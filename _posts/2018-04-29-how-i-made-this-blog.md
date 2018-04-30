---
layout: post
title: How I Made This Blog with Jekyll and GitHub Pages
---

I have built my fair share of Wordpress sites over the last few years. Despite services like Digital Ocean making setup a breeze, I find Wordpress to be clunky and unintuitive. For a CMS focussed on content creation/blogging I am astounded by how flaky the editor is. So I couldn't bring myself to use Wordpress for my own simple dev blog. Instead I settled on using [Jekyll](https://jekyllrb.com/) with [GitHub Pages](https://pages.github.com/) to hack together something simple, fast and free that suits my needs perfectly. And so far I love it!

GitHub Pages is a free service offered by GitHub to host static websites from a git repository. It has built in support for Jekyll, a static site generator that takes markdown files and converts them into static web pages. It has become a useful tool for many open source groups that use it for hosting documentation, for example Facebook use it for hosting the React documentation.

Many of the tutorials for GitHub Pages and Jekyll focus on getting the site running locally using Ruby. As somebody that has never used Ruby and only wanted to hack together something simple I couldn't be bothered with getting set up and learning about gem files. So I ended up doing most of my site modifications using the Github editor.

The commits are a little messy, but as it didn't matter if I wrecked the site at any point I don't really care! For larger changes and writing posts I have the whole repository cloned to my dev machine. But I don't run it locally at all. I lose some functionality around drafts but at the end of the day it's a markdown file so I know roughly what it will look like.

Even if there are problems, the workflow for updating is so quick thanks to GitHub Pages auto building the site within seconds, that making fixes is quick and easy.

I'm not going to write out a tutorial for setting up a Jekyll site on GitHub Pages (not right now anyway) as the official documentation is pretty good and there are thousands of tutorials already covering the process in painstaking detail.

INSERT TUTORIALS HERE

Instead I'd like to highlight a couple of cool things I found. Firstly the **Liquid Templating Engine** used by Jekyll. The syntax reminded me a lot of Jinja2 used by Flask which I've had a decent amount of experience with.

Liquid has many built in functions for processing the content it displays. For example on my homepage the below snippet of code is used render each post.

```Liquid
{{ post.date | date: "%-d %B %Y" }}</br>
{{ post.content | number_of_words | plus: 250 | divided_by: 180 | append: " minutes to read" }}</br>
{{ post.excerpt }}
```