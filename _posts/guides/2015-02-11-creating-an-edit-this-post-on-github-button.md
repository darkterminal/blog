---
layout: post
title: "Creating an \"Edit on GitHub\"-button on your Jekyll site or blog"
date:  2015-02-11
category: guides
tags: 
- guides
- how-to
- jekyll
- github
---
You might have noticed that every post on this page has a button with the text
"Edit post on GitHub" and the Github logo on it.

Clicking the button should take you to the GitHub page in the repository
where I've stored the markdown source for the post you are reading.
This enables readers to easily help you with your content.

In this post (which actually happens to be my first post) I will explain how I
did it.

## Requirements

For this guide I'm assuming that you have:

* a [Jekyll website][jekyll]:
* posts tracked on [Github][github]

## How to:

### Configuration file

In the [root][jekyll-dir] of your blog a file named `_config.yml` exists.
Here, variables are defined which can be used throughout your blog.
Using the standard build of Jekyll, there should exist a line starting with
`github_username:`.

Set the variable to to your github username, mine is for example eeemil.
_In this guide I will add a variable_ called `git_repo`. You should set this
variable to the repository where you store your Jekyll root. If you are using
[GitHub Pages][github-pages] this will be `username.github.io`.
When done, my configuration file contains the following lines:

     github_username: eeemil
     git_repo: blog

We will use these variables in order to build links to your posts later.

When I wrote this, my config file [looked like this][my-config].

### Creating a nice looking button with CSS

In order to create a nice looking button, you may want to use [css][css-wiki].
I wanted my button to look like the buttons on GitHub, so I googled "GitHub
buttons css" and found a nice collection by [Necolas][css-buttons] which also
were [free to use in any way][unlicense].

I am in no way a guru in css, there are probably a number of ways to do this
better than the solution I came up with, but the button I ended up using had
[this css][gitbutton-css].

#### Adding the CSS to your Jekyll site

[The Jekyll directory rationale][jekyll-dir] states that:

     Every other directory and file except for those listed above—such as css and
     images folders, favicon.ico files, and so forth—will be copied verbatim to the
     generated site.

This means that every directory and file you place in your Jekyll root, that is
not a special directory/file (*such as _posts, _layouts, _config.yml etc.*) will
be placed in your site with the same name and path as you have named your
file/directory.

For example, if you place a directory called "foo" in your Jekyll root, with the
file "bar.html", you can access the file (after building your site with `jekyll
build` or `jekyll serve`, [read the documentation for more info][jekyll-doc]) by
going to your site and adding "/foo/bar.html".
On my site the link would be 
<a href="{{ site.baseurl }}/foo/bar.html">{{ site.baseurl }}/foo/bar.html</a>

I believe the standard Jekyll installation places a directory called "css" in
your Jekyll root. It contains a file called main.scss, which is a kind of [css
on steroids][sass-scss]. However, I'm not really into web design and for now we
will manage with an ordinary .css-file.

Take the CSS containing the button design and put it in your /css directory.
I named my file "custom.css" and it had [this content][github-css].

My css directory now contained two files, "main.css" and "custom.css".

    jekyll@diversesaker:~/blog.git$ ls css/
    custom.css  main.scss

Now we only to tell the user to load our .css whenever a page is loaded, and we
are almost there.

#### Tell Jekyll to serve CSS every time a page is loaded

We are almost done with the CSS part now. We only need to tell the user where to
find our CSS.
This is done in the [HTML head][html-head] - a section defining general
information about a site.

In Jekyll, the head for every page is defined in `_include/head.html`. The
_include directory contains information which can be fetched from anywhere using
the following syntax:

`{{ "{{ include stuff-to-include.extension " }}}}`.

Where "*include*" is a keyword telling [Liquid, Jekyll's templating
language][jekyll-templates] to extract the contents of a file called
"*stuff-to-include.extension*" in the _include directory. We will use this later.

For now, we will edit the file `head.html`. By default, this is included as the
html head tag for every page on your site.

You may notice a line with the following text:\\
`<link rel="stylesheet" href="{{ '{{ "/css/main.css" | prepend: site.baseurl ' }}}}">`\\
This is to load all default CSS information, defining how your site will look.

We want to add our own custom CSS to the site. This is done by adding another
line our own CSS. Provided you have added your CSS to the css directory, and
named your file to custom.css, the you added should be\\
`<link rel="stylesheet" href="{{ '{{ "/css/custom.css" | prepend: site.baseurl ' }}}}">`

My finished head [looked like this][gitbutton-html-head].

#### Getting a logo for GitHub

While looking around in the files in the Jekyll standard build, I found that it
contained some [vector graphics][svg-wiki] for the GitHub logo:

    <span class="icon  icon--github">
      <svg viewBox="0 0 16 16">
        <path fill="#828282" d="M7.999,0.431c-4.285,0-7.76,3.474-7.76,7.761 c0,3.428,2.223,6.337,5.307,7.363c0.388,0.071,0.53-0.168,0.53-0.374c0-0.184-0.007-0.672-0.01-1.32 c-2.159,0.469-2.614-1.04-2.614-1.04c-0.353-0.896-0.862-1.135-0.862-1.135c-0.705-0.481,0.053-0.472,0.053-0.472 c0.779,0.055,1.189,0.8,1.189,0.8c0.692,1.186,1.816,0.843,2.258,0.645c0.071-0.502,0.271-0.843,0.493-1.037 C4.86,11.425,3.049,10.76,3.049,7.786c0-0.847,0.302-1.54,0.799-2.082C3.768,5.507,3.501,4.718,3.924,3.65 c0,0,0.652-0.209,2.134,0.796C6.677,4.273,7.34,4.187,8,4.184c0.659,0.003,1.323,0.089,1.943,0.261 c1.482-1.004,2.132-0.796,2.132-0.796c0.423,1.068,0.157,1.857,0.077,2.054c0.497,0.542,0.798,1.235,0.798,2.082 c0,2.981-1.814,3.637-3.543,3.829c0.279,0.24,0.527,0.713,0.527,1.437c0,1.037-0.01,1.874-0.01,2.129 c0,0.208,0.14,0.449,0.534,0.373c3.081-1.028,5.302-3.935,5.302-7.362C15.76,3.906,12.285,0.431,7.999,0.431z"/>
      </svg>
    </span>

In order to not [not repeat myself][d-r-y] I extracted the svg-part and put it
into my `_include` directory and named the file
[git-logo.html][github-logo]. This way I can write:\\
`{{ " {% include git-logo.html " }}%}` instead of having to copy and paste the information
whenever I need it.

The `<span class=icon icon--github">` shrunk the SVG to 16\*16 px. However, I
thought this was to small for me. So in my [custom.css][github-css] I included
formatting for a medium sized icon with 24\*24 px instead.

Now, if I write `<span class=icon medium">` I get a larger icon.

### Adding a link to every post - editing the post layout

We are now going to put everything together into a nicely formatted button.

In `_layout/post.html`, the layout of the content for every post is defined.

If you want every post to end with "HELLO WORLD", you can add 
`<p>HELLO WORLD</p>` after the </article> tag.

We want to add a link with the style of a button which we have defined in our
css.
We will do this by writing:

    <a href=https://www.github.com/{{ "{{ site.github_username " }}}}/{{ "{{ site.git_repo " }}}}/blob/master/{{ "{{ page.path " }}}} class="gitbutton pill">
      <span class="icon medium">
        {{ "{% include git-logo.html " }}%}
      </span>
      Edit on GitHub!
    </a>

The variables `site.github_username` and `site.git_repo` are defined in
`_config.yml`. The variable `page.path` is specific to the page being loaded.

The included file [git-logo.html][github-logo] should reside in `_include/`.

My `_layout/post.html` looked [like this][github-post] when I was done.

## Summary:

1. Create a CSS button in a style you like
2. Place the CSS in css/custom.css (*or any other place or name*)
3. Edit _include/head.html to include your css
4. Edit _layout/post.html to include a link to the post by utilizing liquid
style templates such as: \\
  * `{{ "{{ page.path " }}}}` - relative path to the post
  * `{{ "{{ site.whatevervariable " }}}}` - any variable defined in `_config.yml`
  (*in this guide I defined a variable for the path to my GitHub repo*)
  * `{{ "{% include fileToInclude " }}%}` - injects the content of a file in `_include/`

## Notes:

A Jekyll site can be hosted by GitHub using [GitHub Pages][github-pages]. Adding
a edit-button is perfect if you are hosting your page on GitHub pages.

I have chosen not to host my page on GitHub as I want to have complete control
over the server. But for someone who only wants to have a page up and running,
GitHub Pages is perfect!

[d-r-y]: http://en.wikipedia.org/wiki/Don%27t_repeat_yourself
[svg-wiki]: http://sv.wikipedia.org/wiki/Scalable_Vector_Graphics
[html-head]: http://www.w3schools.com/html/html_head.asp
[sass-scss]: http://thesassway.com/editorial/sass-vs-scss-which-syntax-is-better
[gitbutton-css]: https://gist.github.com/Eeemil/e93ad054a73037f5bea3#file-custom-css
[gitbutton-html-head]: https://gist.github.com/Eeemil/e93ad054a73037f5bea3#file-head-html
[github-logo]: https://gist.github.com/Eeemil/e93ad054a73037f5bea3#file-git-logo-html
[github-post]: https://gist.github.com/Eeemil/e93ad054a73037f5bea3#file-post-html
[unlicense]: http://unlicense.com
[css-wiki]: http://en.wikipedia.org/wiki/Cascading_Style_Sheets
[css-buttons]: https://github.com/necolas/css3-github-buttons
[github-pages]: https://pages.github.com/
[github]: https://github.com
[jekyll]: https://jekyllrb.com
[jekyll-doc]: http://jekyllrb.com/docs/home/
[jekyll-dir]: http://jekyllrb.com/docs/structure/
[jekyll-templates]: http://jekyllrb.com/docs/templates/
[my-config]: https://gist.github.com/Eeemil/e93ad054a73037f5bea3#file-_config-yml