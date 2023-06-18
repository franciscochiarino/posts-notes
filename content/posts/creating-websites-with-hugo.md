---
title: "Creating Websites With Hugo"
date: 2023-06-18T13:37:21+02:00
summary: A step by step guide on how to create static websites using Hugo.
draft: false
tags: [front-end, hugo]
---

Creating a website with Hugo can be really easy and enjoyable. I hope you have fun reading these notes and developing your own site!

If you come across any issue or want to get deeper into Hugo, here's the [documentation](https://gohugo.io/documentation/).

### Contents
* [Intro](#intro)
* [Installation](#installation)
* [Create a Website](#create-a-website)
* [What is What](#what-is-what)
* [Add a Theme](#add-a-theme)
* [Create a Post](#create-a-post)
* [Create a Menu](#create-a-menu)
* [Modify Themes](#modify-themes)
* [Deploy](#deploy)

## Intro
Hugo works with templates. You don't build websites from scratch. The overall flow is as follows:

* You generate a website using a command.
* You add a template to that generated structure.
* You generate pages for that template according to the templates docs (it's simpler than it sounds)
* You modify stuff you don't like about the template (if you want to)

## Installation

Install Hugo on macOS homebrew:
```
brew install hugo
```
That's it.

## Create a Website

Create your website by running this command in your terminal:
```
hugo new site <site_name>
```
Done. You have a website now. You can open it in VS Code by running:
```
code <site_name>
```
Or just manually opening your project folder.

## What is What

You'll notice a bunch of directories and a file in your project folder and you might be thinking "What's all this?". Let's go through them, but don't worry if you don't understand everything right away. You'll know them better as you use them.

### hugo.toml
In this file you'll manage the overall configuration of your project. I like to change this file to _hugo.yml_ **and I will do so for this tutorial** because I'm more familiar with the yml syntax. Feel free to do so if you want to. You'll also write the them configuration here. Well get to that later.

### archetypes
This handles the `hugo new` command, which we'll use to create new content files. Hugo will create a new content file with the `title`, `date` and `draft` properties automatically. You can control of these properties in this directory by changing _default.md_.

### assets
This is where CSS and JavaScript files **that you want to modify from the template** live. If you want to add something on top of it, do it on the static directory.

### data
This directory is used to store configuration files that can be used by Hugo when generating your website. I just literally pasted that from the docs.

### layouts
Very important folder! Here you will store HTML files that you want to modify from the template. It's sounds weird now, but you'll see what I mean.

### static
Put your own images, JavaScript and stylesheets here.

### resources
Caches some files to speed up generation.

## Add a Theme
On [Hugo's theme website](https://themes.gohugo.io) you'll find a lot of cool themes for free. You can choose the one you like, but for this example we're gonna use [PaperMod](https://themes.gohugo.io/themes/hugo-papermod/). Under the PaperMod link you'll find a download button which takes you to [this GitHub page](https://github.com/adityatelange/hugo-PaperMod). Here you'll find instructions on [how to install it and how to configure it](https://github.com/adityatelange/hugo-PaperMod/wiki/Installation).

This is pretty much how themes work with Hugo. You select one you like and they have their own configuration. Now, "configuration" sounds like something complicated is about to happen, but these are just going to be key-value pairs in your _hugo.yml_ file like:
```
params:
  description: This is the description of the website.
  keywords: [Keywords, For, Your, Website]
```

So to install the PaperMod theme we have to run:
```
git clone https://github.com/adityatelange/hugo-PaperMod themes/PaperMod --depth=1
```
If you have a closer look to that command you'll see it's just cloning the theme repo in your _themes_ directory. If you run this and it complains there's not repo, just run `git init`.

Activate this theme by adding this to _hugo.yml_:
```
theme: PaperMod
```

Finally open a terminal in your project directory and run:
```
hugo server
```
You should be able to see your site on `localhost:1313`

## Create a Post
Now that you have your site running, you can run this command to create a new post for your site:
```
hugo new posts/my-post.md
```

This will create the _post_ directory and the _my-post.md_ file inside the _content_ director. If you run your server again, you'll notice that you can't see any posts on your site. This is because in _my-post.md_ you have the `draft` attribute set to `true`. This little section in between three dashes is called the _"front matter"_. Set `draft` to `false`, run your server and you'll see your post!
```
title: "My Post"
date: 2023-06-18T13:37:21+02:00
draft: false
```

### Add an image
Drag and drop an image into your static folder. Let's pretend it's called `example.jpg`.

Then just add the image path along with some other attributes to your front matter inside _my-post.md_:
```
title: "My Post"
date: 2023-06-18T13:37:21+02:00
draft: false
cover:
  image: 'example.jpg'
  alt: 'example'
  caption: 'This is just an example'
```

Keep in mind these configurations vary from theme to theme, but as you can see it's pretty simple.

### Add tags
We can also easily add tags to our post by modifying the front matter:
```
# ...
tags: [front-end, hugo]
```

## Create a Menu
Inside your _hugo.yml_ file you can create a menu like so:
```
menu:
  main:
    - identifier: tags
      name: Tags
      url: /tags/
      weight: 20
```
So now we have a clickable menu that takes us to the route we specified.

## Modify Themes
"I'm cool with themes, but what if I want to have my own thing?"

You can add whatever layout/style you want on top of your theme. Let's say you want to change the footer. You'll see the footer in  _themes/PaperMod/layouts/partials/footer.html_. Instead of modifying it there directly, copy the file and paste it in your layouts directory, following the same structure:
_layouts/partials/footer.html_. Now your project is going to grab this file instead, and you can modify it however you want, without touching your theme files.

If you want to change a stylesheet instead of a markup file, the approach is the same. First: find the stylesheet you want to change in your theme folder, for example _themes/PaperMod/assets/css/common/main.css_. Copy it and create the same file with the same file structure in your own assets directory: _assets/css/common/main.css_. Change whatever you want in your file.

## Deploy
For this deploy we're gonna use Netlify (which is awesome).

Add your theme as a sub-module:
```
git submodule add --force <url> <path>
```

Then push your repo to GitHub.

Login to Netlify, select Add new site > Import an existing project, select GitHub and select your repo. Change the build command to `hugo`, fill `public` in Publish Directory and under Advanced Settings add the `HUGO_VERSION` variable and set it to your version (check using `hugo version` on your terminal).

Finally, get your site's url from netlify and add it to the hugo.yaml file, so that it can work as expected:
```
baseURL: 'https://whatever-url.netlify.app'
```
