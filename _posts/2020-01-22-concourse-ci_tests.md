---
layout: post
title: "concourse-ci testing"
description: "Testing out concourse-ci with fossa"
category: tech 
tags: [coyote,tech,ci]
---
{% include JB/setup %}


## Concourse-CI testing

## Summary of concourse-ci

Concourse is a fairly cleanly designed CI platform with very little extra fluff.  It's pretty minimalist in the UI with not a lot of clicky buttons. 
Most of the configuration is done with the [`fly` CLI](https://concourse-ci.org/fly.html) and by design there are no wizards to help configure the CI platform.  All pipelines are written as YAML files and then saved using `fly` 

### My test environment

Mostly done in Docker.  Grab the `docker-compose` yaml config file and run `docker-compose` against it

```bash
wget https://raw.githubusercontent.com/starkandwayne/concourse-tutorial/master/docker-compose.yml
docker-compose up -d
```

### Examples and helpful snippets from the internet

Example pipeline configuration from the stock concourse tutorial showing a git repo resource and a job with a single task.

```yaml
---
resources:
  - name: node-app
    type: git
    source:
      uri: https://github.com/heroku/node-js-getting-started.git
      branch: master

jobs:
  - name: npm-install
    public: true
    plan:
      - get: resource-tutorial
      - task: npm-install
        file: resource-tutorial/tutorials/basic/task-hello-world/task_hello_world.yml
```

The [NodeJS example](https://concourse-ci.org/nodejs-example.html) hacked up to do what we want.

```


---
resources:
  - name: nodejs.org-git
    type: git
    icon: github-circle
    source:
      uri: https://github.com/nodejs/nodejs.org.git

jobs:
  - name: test
    public: true
    plan:
      - get: nodejs.org-git
        trigger: true
      - task: run-tests
        config:
          platform: linux
          image_resource:
            type: registry-image
            source: { repository: node, tag: "8" }
          inputs:
            - name: nodejs.org-git
          run:
            path: /bin/sh
            args:
              - -c
              - |
                echo "Node Version: $(node --version)"
                echo "NPM Version: $(npm --version)"
                cd nodejs.org-git
                npm install
                npm test
```



### Example job definition from the DO tutorial

In this section, we define two jobs, each of which consist of a name and a plan. Each of our plans, in turn, contain “get” and “task” elements. The **task ** items specify how to execute an action while the **get** items indicate the resource dependencies of the task.

The first job does not have any task statements. This is a bit unusual, but makes sense when we look at what it is doing and how it can be used. The first get statement requires the `hello_hapi`resource and specifies the `trigger: true`option. This tells Concourse to automatically fetch the repository and begin a new build of this job every time a new commit is detected in the `hello_hapi`repository.

The second get statement in the first job (`get: dependency-cache`) requires the resource we defined that downloads and caches the project’s Node.js dependencies. This statement evaluates the requirements found in the `package.json`file and downloads them. With no tasks defined for this job, no other actions are taken, but the downloaded dependencies will be available to subsequent jobs.

**Note**: In this specific example, there is only a single additional job, so the benefits of caching the Node.js dependencies as an independent step aren't fully realized (adding the get statements to the testing job that follows would be enough to download the dependencies). However, almost all work with Node.js require the project dependencies, so if you had separate jobs that could potentially be done in parallel, the benefits of a separate dependency cache would become more clear.

The second job (`name: Run tests`) starts off by declaring the same dependencies with one notable difference. The “passed” constraint causes the get statements to only match resources that have successfully traversed previous steps in the pipeline. This is how dependencies between jobs are formed to chain together pipeline processes.

After the get statements, a task called “run the test suite” is defined. Rather than defining the steps to complete inline, it tells Concourse to pull the definition from a file in the repository it fetched. We will create this file next.

When you are finished, the complete pipeline should look like this:

`ci/pipeline.yml`

```yaml
---
resource_types:
  - name: npm-cache
    type: docker-image
    source:
      repository: ymedlop/npm-cache-resource
      tag: latest

resources:
  - name: hello_hapi
    type: git
    source: &repo-source
      uri: https://github.com/your_github_user/hello_hapi
      branch: master
  - name: dependency-cache
    type: npm-cache
    source:
      <<: *repo-source
      paths:
        - package.json

jobs:
  - name: Install dependencies
    plan:
      - get: hello_hapi
        trigger: true
      - get: dependency-cache
  - name: Run tests
    plan:
      - get: hello_hapi
        trigger: true
        passed: [Install dependencies]
      - get: dependency-cache
        passed: [Install dependencies]
      - task: run the test suite
        file: hello_hapi/ci/tasks/run_tests.yml
```



## [Credential Lookup Rules](https://concoursetutorial.com/basics/secret-parameters/#credential-lookup-rules)

When resolving a parameter such as `((cred-name))`, it will look in the following paths, in order:

- `/concourse/TEAM_NAME/PIPELINE_NAME/cred-name`
- `/concourse/TEAM_NAME/cred-name`

So, if the `((cred-name))`credential is to be shared across all pipelines in the `main`team, then the `credhub set`commands would become:

```shell
credhub delete -n /concourse/main/parameters/cred-name
credhub set -n /concourse/main/cred-name --type value --value credential
```

## Concourse setup experiments and configs

Mostly just quick notes here on how I got to a working config for FOSSA client.

### Experiment 1

This failed because the fossa client never even got installed.  The version of this with the separate jobs wouldn't pass validation because the config section requires a platform and image resource.  My guess is that the environments don't share well.  Might ask around later.

```yaml
---
resources:
  - name: nodejs.org-git
    type: git
    icon: github-circle
    source:
      uri: https://github.com/nodejs/nodejs.org.git
  - name: fossa-cli
    type: github-release
    source:
      owner: fossas
      repository: fossa-cli

jobs:
  - name: test
    public: true
    plan:
      - get: nodejs.org-git
        trigger: true
      - get: fossa-cli
        trigger: true
      - task: run-tests
        config:
          platform: linux
          image_resource:
            type: registry-image
            source: {repository: node, tag: "8"}
          inputs:
            - name: nodejs.org-git
          run:
            path: /bin/sh
            args:
              - -c
              - |
                echo "Node Version: $(node --version)"
                echo "NPM Version: $(npm --version)"
                cd nodejs.org-git
                npm install
                npm test
                echo "current working directory: $(pwd)"
                fossa --version
```



### Experiment 2

Ugly but gets the job done.  Need a better way of handling the install script and environment variable.

```yaml
---
resources:
  - name: nodejs.org-git
    type: git
    icon: github-circle
    source:
      uri: https://github.com/nodejs/nodejs.org.git

jobs:
  - name: test
    public: true
    plan:
      - get: nodejs.org-git
        trigger: true
      - task: run-tests
        config:
          platform: linux
          image_resource:
            type: registry-image
            source: {repository: node, tag: "8"}
          inputs:
            - name: nodejs.org-git
          run:
            path: /bin/sh
            args:
              - -c
              - |
                echo "Node Version: $(node --version)"
                echo "NPM Version: $(npm --version)"
                export FOSSA_API_KEY="8aba13dd49e34ae09c98f7cf62577040"
                curl -H 'Cache-Control: no-cache' https://raw.githubusercontent.com/fossas/fossa-cli/master/install.sh | bash
                cd nodejs.org-git
                npm install
                npm test
                echo "current working directory: $(pwd)"
                fossa init
                fossa analyze
```

### Experiment 3

Here's the whole thing with a parameter for the API key.

Setup works like this:

1. Save the command with the `set-pipeline` command in `fly`:
   `fly -t tutorial set-pipeline -p fossa-example-pipeline -c pipeline.yaml`
2. Set the param with one of the usual parameter setting commands (e.g.): 
   `fly -t tutorial sp -c pipeline.yaml -p fossa-example-pipeline -v fossa_api_key=agreatbigapikey`
   `credhub set -n /concourse/main/fossa_api_key --type value --value agreatbigapikey`
3. Enjoy as your reports flow in..

```yaml
---
resources:
  - name: nodejs.org-git
    type: git
    icon: github-circle
    source:
      uri: https://github.com/nodejs/nodejs.org.git

jobs:
  - name: test
    public: true
    plan:
      - get: nodejs.org-git
        trigger: true
      - task: run-tests
        config:
          platform: linux
          image_resource:
            type: registry-image
            source: {repository: node, tag: "8"}
          inputs:
            - name: nodejs.org-git
          run:
            path: /bin/sh
            args:
              - -c
              - |
                echo "Node Version: $(node --version)"
                echo "NPM Version: $(npm --version)"
                curl -H 'Cache-Control: no-cache' https://raw.githubusercontent.com/fossas/fossa-cli/master/install.sh | bash
                cd nodejs.org-git
                npm install
                npm test
                echo "current working directory: $(pwd)"
                fossa init
                fossa analyze
          params:
            FOSSA_API_KEY: ((fossa_api_key))
```



### Experiment 4

So it looks like you can chain tasks, which would allow me to split out the build and fossa tasks by using the [**`passed`**](https://concourse-ci.org/get-step.html#get-step-passed) attribute containing the name of the build step in the [`get` step](https://concourse-ci.org/get-step.html) of the downstream job (e.g. `passed: [build_and_test]`).  This lets me pull the built project into a second job and run fossa against that.

```yaml
---
resources:
  - name: nodejs.org-git
    type: git
    icon: github-circle
    source:
      uri: https://github.com/nodejs/nodejs.org.git

jobs:
  - name: build_and_test
    public: true
    plan:
      - get: nodejs.org-git
        trigger: true
      - task: run-tests
        config:
          platform: linux
          image_resource:
            type: registry-image
            source: {repository: node, tag: "8"}
          inputs:
            - name: nodejs.org-git
          run:
            path: /bin/sh
            args:
              - -c
              - |
                echo "Node Version: $(node --version)"
                echo "NPM Version: $(npm --version)"
                cd nodejs.org-git
                npm install
                npm test
                echo "current working directory: $(pwd)"
  - name: fossa_run
    public: true
    plan:
      - get: nodejs.org-git
        passed: [build_and_test]
        trigger: true
      - task: run-fossa
        config:
          platform: linux
          image_resource:
            type: registry-image
            source: {repository: node, tag: "8"}
          inputs:
            - name: nodejs.org-git
          run:
            path: /bin/sh
            args:
              - -c
              - |
                echo "Node Version: $(node --version)"
                echo "NPM Version: $(npm --version)"
                curl -H 'Cache-Control: no-cache' https://raw.githubusercontent.com/fossas/fossa-cli/master/install.sh | bash
                cd nodejs.org-git
                echo "current working directory: $(pwd)"
                fossa init
                fossa analyze
                fossa test
          params:
            FOSSA_API_KEY: ((fossa_api_key))
```



### References

- [Concourse Tutorial](https://concoursetutorial.com)
- [Digital Ocean: Concourse-ci Tutorial](https://www.digitalocean.com/community/tutorials/how-to-set-up-continuous-integration-pipelines-with-concourse-ci-on-ubuntu-16-04)
- [Concourse Docs](https://concourse-ci.org/docs.html)
- [Concourse Examples](https://concourse-ci.org/examples.html)




