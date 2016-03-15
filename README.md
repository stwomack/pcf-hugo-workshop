
Building the workshop site
---

We will use Hugo for this website.  

## Github repository

The code for workshop is available on github: [pcf-kroger-workshop](https://github.com/rjain-pivotal/pcf-kroger-workshop).

## Step 1: Getting started with Hugo

Go to [https://github.com/spf13/hugo/releases](https://github.com/spf13/hugo/releases) and download Hugo for your operating system. If you are on Mac, the you can install using `brew` package manager as well.

```bash
$ brew update && brew install hugo
```

Once `hugo` is installed, make sure to run the `help` command to verify `hugo` installation. Below I am only showing part of the output of the `help` command for brevity.

```bash
$ hugo help
```
```
hugo is the main command, used to build your Hugo site.

Hugo is a Fast and Flexible Static Site Generator
built with love by spf13 and friends in Go.

Complete documentation is available at http://gohugo.io/.
```

You can check `hugo` version using the command shown below.

```bash
$ hugo version
```
```
Hugo Static Site Generator v0.15 BuildDate: 2015-11-26T11:59:00+05:30
```

> **In this post, we will use the latest version of hugo i.e. version 0.15**

## Step 2: Clone this Website to get the Workshop website

```bash
$ git clone https://github.com/rjain-pivotal/pcf-kroger-workshop
```

## Step 3: Build the website and test it locally
Hugo has inbuilt server that can serve content so that you can preview it. You can also use the inbuilt Hugo server in production as well. To serve content, execute the following command.

```bash
$ hugo server --buildDrafts
```
```
1 of 1 draft rendered
0 future content
1 pages created
0 paginator pages created
0 tags created
0 categories created
in 6 ms
Watching for changes in /Users/rjain/pcf-kroger-workshop/{data,content,layouts,static}
Serving pages from memory
Web Server is available at http://localhost:1313/ (bind address 127.0.0.1)
Press Ctrl+C to stop
```

## Step 4: Push it to cloud foundry

```bash
cf push
```

## Step 5: Add content
Anytime you make changes in the content, delete the public directory, hugo will rebuild the directory. 
Let's now add a post to our `workshop`. We will use the `hugo new` command to add a post. 

```bash
$ cd pcf-kroger-workshop
$ hugo new post/lab-1.md
```
The above command will create a new directory `post` inside the `content` directory and create `lab-1.md` file inside it.

```bash
$ tree -a content
```
```
content
`-- post
    `-- lab-1.md

1 directory, 1 file
```

The content inside the `lab-1.md` looks like as shown below.

```
+++
date = "2016-02-14T16:11:58+05:30"
draft = true
title = "lab-1"

+++
```

The content inside `+++` is the TOML configuration for the post. This configuration is called **front matter**. It enables you to define about the post along with the content. Every post has three configuration properties shown above.

* **date** specifies the date and time at which post was created.
* **draft** specifies that post is not ready for publication yet so it will not be in the generated site
* **title** specifies title for the post

Let's add some content for **Lab-1** .

```
+++
date = "2016-02-14T16:11:58+05:30"
draft = true
title = "Lab-1 Content"

+++

```



## Step 5: Change theme

Themes provide the layout and templates that will be used by Hugo to render your website. There are a lot of Open-source themes available at [https://themes.gohugo.io/](https://themes.gohugo.io/) that you can use. From the [Hugo docs](https://gohugo.io/themes/overview/),

> **Hugo currently doesn’t ship with a `default` theme, allowing the user to pick whichever theme best suits their project.**

Themes should be added in the `themes` directory inside the website root. Create new directory themes and change directory to it.

```bash
$ mkdir themes && cd themes
```
Now, you clone one or more themes inside the `themes` directory. We will use robust theme.

```bash
$ git clone git@github.com:dim0627/hugo_theme_robust.git
```

Start the server again

```bash
$ hugo server --theme=hugo_theme_robust --buildDrafts
```
```
1 of 1 draft rendered
0 future content
1 pages created
2 paginator pages created
0 tags created
0 categories created
in 10 ms
Watching for changes in /Users/shekhargulati/bookshelf/{data,content,layouts,static,themes}
Serving pages from memory
Web Server is available at http://localhost:1313/ (bind address 127.0.0.1)
Press Ctrl+C to stop
```

> ** If Hugo will not find a specific theme in the `themes` directory then it will throw an exception as shown below.**
```
FATAL: 2016/02/14 Unable to find theme Directory: /Users/shekhargulati/bookshelf/themes/robust
```

To view your website, you can go to http://localhost:1313/. You will see as shown below.

<img src="images/bookshelf-robust-theme.png" width="600">

Let's understand the layout of a theme. A theme consists of following:

* **theme.toml** is the theme configuration file that gives information about the theme like name and description of theme, author details, theme license.

* **images** directory contains two images -- `screenshot.png` and `tn.png`. `screenshot.png` is the image of the list view and `tn.png` is the single post view.

* **layouts** directory contains different views for different content types. Every content type should have two files single.html and list.html. single.html is used for rendering single piece of content. list.html is used to view a list of content items for example all posts with `programming` tag.

* **static** directory stores all the static assets used by the template. This could JavaScript libraries like jQuery or CSS styles or images or any other static content. This directory will be copied into the final site when rendered.

## Step 6: Use multiple themes

You can very easy test different layouts by switching between different themes. Let's suppose we want to try out `bleak` theme. We clone `bleak` theme inside the `themes` directory.

```bash
$ git clone git@github.com:Zenithar/hugo-theme-bleak.git
```


Restart the server using `hugo-theme-bleak`.

```bash
$ hugo server --theme=hugo-theme-bleak --buildDrafts
```

Now, website will use `bleak` theme and will be rendered differently as shown below.

<img src="images/bookshelf-bleak-theme.png" width="600">

## Step 7: Update config.toml and live reloading in action

Restart the server with `robust` theme as we will use it in this blog.

```bash
$ hugo server --theme=hugo_theme_robust --buildDrafts
```

The website uses the dummy values specified in the `config.toml`. Let's update the configuration.

```toml
baseurl = "http://replace-this-with-your-hugo-site.com/"
languageCode = "en-us"
title = "Shekhar Gulati Book Reviews"

[Params]
  Author = "Shekhar Gulati"
```

Hugo has inbuilt support for live reloading. So, as soon as you save your changes it will apply the change and reload the web page. You will see changes as shown below.

<img src="images/bookshelf-updated-config.png" width="600">

The same is reflected in the Hugo server logs as well. As soon as the configuration is changed, it applied the changes.

```
Config file changed: /Users/shekhargulati/bookshelf/config.toml
1 of 1 draft rendered
0 future content
1 pages created
2 paginator pages created
0 tags created
0 categories created
in 11 ms
```

## Step 8: Customize robust theme

Robust theme is a good start towards our online bookshelf but we to customize it a bit to meet the look and feel required for the bookshelf. Hugo makes it very easy to customize themes. You can also create your themes but we will not do that today. If you want to create your own theme, then you should refer to the [Hugo documentation](https://gohugo.io/themes/creation/).

The first change that we have to make is to use a different default image instead of the one used in the theme. The default image used in both the list and single view page resides inside the `themes/hugo_theme_robust/static/images/default.jpg`. We can easily replace it by creating a simple directory structure inside the `static` directory inside the `bookshelf` directory.

Create images directory inside the static directory and copy an image with name `default.jpg` inside it. We will use the default image shown below.

<img src="images/default.jpg" width="600">

Hugo will sync the changes and reload the website to use new image as shown below.

<img src="images/bookshelf-new-default-image.png" width="600">

Now, we need to change the layout of the index page so that only images are shown instead of the text. The index.html inside the layouts directory of the theme refer to partial `li` that renders the list view shown below.

```html
<article class="li">
  <a href="{{ .Permalink }}" class="clearfix">
    <div class="image" style="background-image: url({{ $.Site.BaseURL }}images/{{ with .Params.image }}{{ . }}{{ else }}default.jpg{{ end }});"></div>
    <div class="detail">
      <time>{{ with .Site.Params.DateForm }}{{ $.Date.Format . }}{{ else }}{{ $.Date.Format "Mon, Jan 2, 2006" }}{{ end }}</time>
      <h2 class="title">{{ .Title }}</h2>
      <div class="summary">{{ .Summary }}</div>
    </div>
  </a>
</article>
```

Create a new file li.html inside the `bookshelf/layouts/_default` directory. Copy the content shown below into the li.html. We have removed details of the book so that only image is shown.

```html
<article class="li">
  <a href="{{ .Permalink }}" class="clearfix">
    <div class="image" style="background-image: url({{ $.Site.BaseURL }}images/{{ with .Params.image }}{{ . }}{{ else }}default.jpg{{ end }});"></div>
  </a>
</article>
```

Now, the website will be rendered as shown below.

<img src="images/bookshelf-only-picture.png" width="600">


Next, we want to remove information related to theme from the footer. So, create a new file inside the `partials/default_foot.html` with the content copied from the theme `partials/default_foot.html`. Replace the footer section with the one shown below.

```html
<footer class="site">
  <p>{{ with .Site.Copyright | safeHTML }}{{ . }}{{ else }}&copy; {{ $.Site.LastChange.Year }} {{ if isset $.Site.Params "Author" }}{{ $.Site.Params.Author }}{{ else }}{{ .Site.Title }}{{ end }}{{ end }}</p>
  <p>Powered by <a href="http://gohugo.io" target="_blank">Hugo</a>,</p>
</footer>
```

We also have to remove the sidebar on the right. Copy the index.html from the themes layout directory to the bookshelf layouts directory. Remove the section related to sidebar from the html.

```html
<div class="col-sm-3">
  {{ partial "sidebar.html" . }}
</div>
```

So far we are using the default image but we would like to use the book image so that we can relate to the book. Every book review will define a configuration setting in its front matter. Update the `good-to-great.md` as shown below.


```
+++
date = "2016-02-14T16:11:58+05:30"
draft = true
title = "Good to Great Book Review"
image = "good-to-great.jpg"
+++

I read **Good to Great in January 2016**. An awesome read sharing detailed analysis on how good companies became great. Although this book is about how companies became great but we could apply a lot of the learnings on ourselves. Concepts like level 5 leader, hedgehog concept, the stockdale paradox are equally applicable to individuals.
```

After adding few more books to our shelf, the shelf looks like as shown below. These are few books that I have read within last one year.

<img src="images/bookshelf.png" width="600">


## Step 9: Make posts public

So far all the posts that we have written are in draft status. To make a draft public, you can either run a command or manually change the draft status in the post to True.

```bash
$ hugo undraft content/post/good-to-great.md
```

Now, you can start the server without `buildDrafts` option.

```
$ hugo server --theme=hugo_theme_robust
```

## Step 10: Integrate Disqus

Disqus allows you to integrate comments in your static blog. To enable Disqus, you just have to set `disqusShortname`  in the config.toml as shown below.

```
[Params]
  Author = "Shekhar Gulati"
  disqusShortname = "shekhargulati"
```

Now, commenting will be enabled in your blog.

<img src="images/bookshelf-disqus.png" width="600">

## Step 11: Generate website

To generate Hugo website code that you can use to deploy your website, type the following command.

```bash
$ hugo --theme=hugo_theme_robust
0 draft content
0 future content
5 pages created
2 paginator pages created
0 tags created
0 categories created
in 17 ms
```

> **Make sure to change the baseurl. For my bookshelf on Github pages, url is [https://shekhargulati.github.io/bookshelf](https://shekhargulati.github.io/bookshelf)**

After you run the hugo command, a public directory will be created with the generated website source.


## Step 12: Deploy bookshelf on Github pages

Create a new repository with name `bookshelf` on Github. Once created, create a new Git repo on local system and add remote.

```bash
$ mkdir bookshelf-public
$ cd bookshelf-public
$ git init
$ git remote add origin git@github.com:shekhargulati/bookshelf.git
```

Copy the content of the `public` directory to the `bookshelf-public` directory. Run this command from with in the `bookshelf-public` directory.

```bash
$ cp -r ../bookshelf/public/ .
```

Create new branch `gh-pages` and checkout it.

```bash
$ git checkout -b gh-pages
Switched to a new branch 'gh-pages'
```

Add all the files to the index, commit them, and push the changes to Github.

```bash
$ git add --all
$ git commit -am "bookshelf added"
$ git push origin gh-pages
```

In couple of minutes, your website will be live https://shekhargulati.github.io/bookshelf/.

----

That's all for this week. Please provide your valuable feedback by adding a comment to [https://github.com/shekhargulati/52-technologies-in-2016/issues/10](https://github.com/shekhargulati/52-technologies-in-2016/issues/10).

[![Analytics](https://ga-beacon.appspot.com/UA-59411913-2/shekhargulati/52-technologies-in-2016/07-hugo)](https://github.com/igrigorik/ga-beacon)
