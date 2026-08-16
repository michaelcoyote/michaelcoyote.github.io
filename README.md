# Michael's Jekyll GitHub Pages

A somewhat hacked up and cut down version of a standard github pages site.  There's a hardwired "dark" theme for now. It's the rat-rod of Jekyll blogs.

## Usage


`rake post["Title"]` creates a new post in the \_posts directory

When making changes it can be helpful to run a local server using `bundle exec jekyll serve`

To clear the cache to make sure your changes are picked up run:
```bash
rm -rf _site .jekyll-cache
bundle exec jekyll build
```

## The `dark` theme

Again it's just `tom` hacked up to be "dark mode", meaning I manually edited the `screen.css` and `syntax.css` files.  Yes it was tedious but I wanted to know how it worked.

To add the theme, I changed the following
`_layouts/default.html` - Edited the theme name to `dark` and edited the include to `{% include themes/dark/page.html %}`
I also changed the include in `_layouts/post.html` and `_layouts/page.html` to change the include as well.

## Version

This was forked from Jekyll-Bootstrap v0.3.0.  I've hacked it to bits since then and deleted a bunch of things I didn't want. Do not use this as a guide for how to use Jekyll-Bootstrap.  I'm sure people would be wondering why I did it this way.  


## License

Jekyll is [MIT](http://opensource.org/licenses/MIT) licensed.

Anything in \_posts or images in this repo is [CC BY-NC](http://creativecommons.org/licenses/by-nc/4.0/) and you can email me at michael @ guldan dot net for commercial use.
