---
layout: page
title: Help and Contribution
comments: false
---

**This website is built with Jekyll** (continue reading). It uses a fork of a template called **Mediumish** which was **customized** for the purpose of this blog. It is fully compatible with Github pages, where it is actually hosted. Read below to fully understand. 
{: .text-justify}


## What is Jekyll?

No more databases, comment moderation, or pesky updates to install-just your content. **Markdown, Liquid, HTML & CSS go in**. Static sites come out ready for deployment. Permalinks, categories, pages, posts, and custom layouts are all first-class citizens here.
{: .text-justify}

**GitHub Pages are powered by Jekyll,** so you can easily deploy your site using GitHub for free-custom domain name and all.
**Jekyll is a simple, blog-aware, static site generator.**
{: .text-justify}

You create your content as text files (Markdown), and organize them into folders. Then, you build the shell of your site using Liquid-enhanced HTML templates. **Jekyll automatically stitches the content and templates together, generating a website made entirely of static assets, suitable for uploading to any server.**
{: .text-justify}

Jekyll happens to be the engine behind GitHub Pages, so you can host your project’s Jekyll page/blog/website on GitHub’s servers for free.
{: .text-justify}


## Quick Start Guide

**If you already have a full Ruby development environment** (go to the Bundler section on this page for more information) with all headers and RubyGems installed (see Jekyll’s requirements), you can create a new Jekyll site by doing the following:
{: .text-justify}

```ruby
# Install Jekyll and Bundler gems through RubyGems
gem install jekyll bundler

# Create a new Jekyll site at ./myblog
jekyll new myblog

# Change into your new directory
cd myblog

# Build the site on the preview server
bundle exec jekyll serve

# Now browse to http://localhost:4000
```

## Options for creating a new site with Jekyll

`jekyll new <PATH>` installs a new Jekyll site at the path specified (relative to current directory). In this case, Jekyll will be installed in a directory called `myblog`. Here are some additional details:
{: .text-justify}

- To install the Jekyll site into the directory you're currently in, run `jekyll new` . If the existing directory isn't empty, you can pass the --force option with jekyll new . --force.
- `jekyll new` automatically initiates `bundle install` to install the dependencies required. (If you don't want Bundler to install the gems, use `jekyll new myblog --skip-bundle`.)
- By default, the Jekyll site installed by `jekyll new` uses a gem-based theme called Minima. With gem-based themes, some of the directories and files are stored in the theme-gem, hidden from your immediate view.
- We recommend setting up Jekyll with a gem-based theme but if you want to start with a blank slate, use `jekyll new myblog --blank`
- To learn about other parameters you can include with `jekyll new`, type `jekyll new --help`.


## About Bundler

`gem install bundler` installs the bundler gem through RubyGems. You only need to install it once - not every time you create a new Jekyll project. Here are some additional details:
{: .text-justify}

`bundler` is a gem that manages other Ruby gems. It makes sure your gems and gem versions are compatible, and that you have all necessary dependencies each gem requires.
{: .text-justify}

The `Gemfile` and `Gemfile.lock` files inform `Bundler` about the gem requirements in your site. If your site doesn’t have these Gemfiles, you can omit `bundle exec` and just `run jekyll serve`.
{: .text-justify}

When you run `bundle exec jekyll serve`, `Bundler` uses the gems and versions as specified in `Gemfile.lock` to ensure your Jekyll site builds with no compatibility or dependency conflicts.
{: .text-justify}

For more information about how to use `Bundler` in your Jekyll project, this tutorial should provide answers to the most common questions and explain how to get up and running quickly.
{: .text-justify}


## Features of this template

- Built for Jekyll
- Compatible with Github pages
- Featured Posts
- Index Pagination
- Post Share
- Post Categories
- Prev/Next Link
- Category Archives (this is not yet compatible with github pages though)
- Jumbotron Categories
- Integrations:
    - Disqus Comments
    - Google Analaytics
    - Mailchimp Integration
- Design Features:
    - Bootstrap v4.x
    - Font Awesome
    - Masonry
- Layouts:
    - Default
    - Post
    - Page
    - Archive
    

## How to Use it

**As said above, Jekyll is a static site generator.** It will transform your plain text into static websites and blogs. No more databases, slow loading websites, risk of being hacked...just your content. And not only that, with Jekyll you get free hosting with GitHub Pages! If you are a beginner I recommend you start with [Jekyll's Docs](https://jekyllrb.com/docs/installation/){:target="_blank"}. Now if you know how to use Jekyll, let's move on to using this template in Jekyll:
{: .text-justify}


## Using this template

Download or Fork it: [https://github.com/android10/android10.github.io](https://github.com/android10/android10.github.io). 
- In your local project, open <code>_config.yml</code>. If your site is in root, for <code>baseurl</code>, make sure this is set to <code>baseurl: ""</code>. Also, change your Google Analytics code, disqus username, authors, Mailchimp list etc.
- This site requires 2 plugins: 
    - <code>$ gem install jekyll-paginate</code>
    - <code>$ gem install jekyll-archives</code>.
- Edit the menu and footer copyrights in <code>default.html</code>
- Start by adding your .md files in <code>_posts</code>. 
- YAML front matter
    - featured post - <code>featured:true</code>
    - exclude featured post from "All stories" loop to avoid duplicated posts - <code>hidden:true</code>
    - post image - <code>image: assets/images/mypic.jpg</code>
    - page comments - <code>comments:true</code>
    - meta description (optional) - <code>description: "this is my meta description"</code>
    
YAML Post Example:
<pre>
---
layout: post
title:  "Architecting Android... Reloaded"
author: fernando
categories: [ android, development, mobile, engineering ]
image: assets/images/architecting_android_reloaded.jpg
featured: true
comments: true
---
</pre>

YAML Page Example
<pre>
---
layout: page
title: About Myself
comments: false
---
</pre>


## Contribute

- [Clone the repo](https://github.com/android10/android10.github.io).
- Create a branch off of master and give it a meaningful name (e.g. my-new-feature).
- Open a pull request on GitHub and describe the feature, fix or post.