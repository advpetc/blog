---
title: "How Do I Create This Site"
date: 2021-02-14T21:15:58-06:00
draft: false
author: "Peter"
categories: ["tech"]
tags: ["blogging"]
---

I have browsed through a lot beautify sites which are created by github page, and I want to try a new statics site
framework, so I started this site using **Hugo**.


Here are the step by step guide that reproduced the entire process.

## Install Hugo through Homebrew

Just follow what [Hugo Documentation](https://gohugo.io/getting-started/installing/#homebrew-macos) said here.

## Try out the rudimentary theme

Before you install other themes, first run `$ hugo new site blog`. You will now have a folder called "blog", open this directory.

I try out [ga-hugo-theme](https://github.com/giraffeacademy/ga-hugo-theme) from Graffeacademy by Mike Dane. 
He also has a wonderful series of youtube videos showing you how to create the site from step zero, 
refer here: {{< youtube WvhCGlLcrF8 >}}

To install a theme, you go to the theme folder and run `$ git clone <Github URL of the theme>`. Now if you dive into the themes folder,
you can find the theme has the same structure as we have already.

## Writing posts
Now you can try create some posts under "content" directory. Do the following will create a post named "a.md" under "content/posts/" directory.


`$ hugo new posts/a.md`

If you check the "a.md" file from the "posts/" directory, you can see there are several lines at the top of the file:


```
---
title: "a"
date: 2021-02-14T13:38:33-06:00
draft: true
---
```

This is called [Front Matter](https://gohugo.io/content-management/front-matter/). Hugo has a very smart mechanism for using front matter, and you can
check out the documentation.

Run `$ hugo server -D` to check out the new post you just created. Note the `-D` flag means it will publish the post even if it's a draft version (check draft front
matter, we set it to true now).


## Content structure: _index.md

Each folder you created will by default has a "index page" showing a summary of all the files under that directory. Ideally, it should be:

```
Title: some summary

* __link1 for post1__
* __link2 for post2__
```

You could also override this layout or content by creating a new file called "_index.md" under that directory. Just remember this filename has to be exactly "_index.md".
"_index.md" is just like another post file which should include the front matter at the top.

## Design your layouts

If you are using others' themes (which I do), you just need to know two different layouts for your blog: 
1. single.html
2. list.html

These two layouts essentially define how will your post be shown. To override them, create your own layout html files under "layouts/_default/".

Here is what single.html looks like:


```html
{{ define "main" }}
	<main>
		<article>
			<h1>{{ .Title }}</h1>
			<b><time>{{ .Date.Format (default "2006-01-02 15:04:05" .Site.Params.dateFmt) }}</time></b>
		       {{ range .Params.tags }}
		           <a href="{{ "/tags/" | relLangURL }}{{ . | urlize }}">{{ . }}</a>
        	       {{ end }}

			<div>
				{{ .Content }}
			</div>
		</article>
	</main>
{{ partial "sidebar.html" . }}
{{ end }}
```


In the block `{{ define "main" }}`, it defines a template called "main". In main, you can see `{{ .Title }}`, which is
called [variables](https://gohugo.io/variables/site/). ".Title" (short for ".Params.Title") is also a site variable, which means it could be accessed
site-wide. You can also find `{{ range .Params.tags }}`, and `range` is a [function](https://gohugo.io/templates/introduction/#functions).

You don't need to create your function in most of cases, and you can check the refereces table from hugo documentation.

However, there is one special layout I created: [shortcode](https://gohugo.io/content-management/shortcodes/#what-a-shortcode-is).
I use it for inserting some embedded contents to my post, for example, a linkedin post. I create a file called "linkedin.html"
under "layouts/shortcodes/". A shortcode is just a simple function that you could add it to your markdown file to show some nice
embedded content like:

```
 {{</* youtube irhR5SkqswE */>}}
```

This will be embedded as: {{< youtube irhR5SkqswE >}}

Isn't it cool :)

Creating a shortcode is also very simple, here is what I create:

```html
<div>
<iframe src="https://www.linkedin.com/embed/feed/update/urn:li:share:{{ index .Params 0 }}" height="1332" width="504" frameborder="0" allowfullscreen="" title="Embedded post"></iframe>
</div>
```

--all these are not written by me, but by Linkedin. I just create `{{ index. Params 0 }}` to grab the first argument of the input from our markdown file. Now I have

```
{{</* linkedin 6765427141384380416 */>}}
```
shown as: 
{{< linkedin 6765427141384380416 >}}.

Yay!


## Deployment with Github Page

As the final step, I use Github action to build my website and host it through Github Page.

I follow the [this](https://gohugo.io/hosting-and-deployment/hosting-on-github/#build-hugo-with-github-action) script to build my website.

The only thing keeps in mind is that never forget to change all the draft to false, so that they will be shown correctly.


I also use smol theme, so I need to set up the submodule for that Github repo under my "themes/". Create a ".gitmodules" under the root, and
write:

```
[submodule "themes/smol"]
    path = themes/smol
    url = https://github.com/colorchestra/smol.git
```

Finally, commit and push, you are all set now!