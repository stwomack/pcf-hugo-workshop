
Building the workshop site for a customer
---

We will use Hugo for this website.  
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


## Step 2: Clone this Website to get the Workshop website

```bash
$ git clone https://github.com/rjain-pivotal/pcf-kroger-workshop
```

## Step 3: Build the website and test it locally
Hugo has inbuilt server that can serve content so that you can preview it. You can also use the inbuilt Hugo server in production as well. To serve content, execute the following command.

```bash
$ cd pcf-kroger-workshop
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
$cf push
```

## Step 5: Add content
Anytime you make changes in the content, delete the public directory, hugo will rebuild the directory. 

Let's now add a post to our `workshop`. We will use the `hugo new` command to add a post. 

```bash
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


## Step 6: Make posts public

New posts we have written are in draft status. To make a draft public, you can either run a command or manually change the draft status in the post to True.

```bash
$ hugo undraft content/post/lab-1.md
```

Now, you can start the server without `buildDrafts` option.

```
$ hugo server --theme=material-design
```

## Step 7: Generate website in public folder

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

After you run the hugo command, a public directory will be created with the generated website source.


## Step 8: Deploy to Cloud foundry

Create a Staticfile in public folder before pushing to Cloud Foundry. 

```bash
$touch public/Staticfile
$cf push
```

Make sure you commit all your changes back to the git repo. 

```bash
$ git add --all
$ git commit -am "New Content Added"
$ git push origin master
```

In couple of minutes, your website will be live https://kroger-workshop.cfapps.io

