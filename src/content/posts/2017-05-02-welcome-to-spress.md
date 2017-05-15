---
layout: post
title: "The technology behind this Blog"
subtitle:   "Spress build, hosting and deploy"
author:     "Gerardo"
categories: [tech]
header_img:
  url: "assets/img/pete-wright-105201.jpg"
  author: Pete Wright
  author_url: http://rashpixel.photo
---

I've been working on web development for a few years now, I specialize mostly on Drupal/Symfony sites, and even 
liking Drupal as much as I do, enjoying all of the improvements that have been done to Drupal with it's
latest version, this very same experience tells me that Drupal is not for everything, I could've
built this site using D8, but I decided against it mainly because I wanted to learn something new, and 
because D8 would've been pretty much over-kill for a blog.

## Research
I started looking for a technology that might suit my needs for this Blog, I did not want to have to do 
a lot of maintenance, I wanted to keep it as simple as possible, and I wanted to be able to add content to
the site quickly.

This path eventually led me to Static HTML generators, I tried [Hugo](https://gohugo.io/) and 
[Jekyll](https://jekyllrb.com/), which are the most used ones, I even created a pre-alpha version of this
blog using Hugo, but I spent a lot of time tweaking the styling for the site and dealing with other issues
I did not want to deal with.

After a few while I found [Spress](http://spress.yosymfony.com/), which is a PHP based static site generator,
this was particularly attractive to me because I was already very used to PHP, Composer and Twig templates, So
I took it for a ride.

## Spress
The basic setup is pretty much straight forward: [Getting Started](http://spress.yosymfony.com/docs/getting-started/),
I decided to install a custom theme and tweak it a little bit to get the result I wanted, the biggest change was
the video banner I added to the homepage, and some minor tweaks on a couple of twig templates, and styling changes on 
fonts and sizes, 
nothing too major, after a few hours I was very happy with the result, and I wanted to deploy the beta version of it
somewhere on the internet.

## Hosting
In terms of hosting, I did not want an expensive hosting solution with over complicated build steps, so my first
option was to try [Github pages](https://pages.github.com/), this solution is setup pretty much so you use Jekyll,
but other static HTML generators are also supported, after a few tries, I could not get Github pages to work 
with my site following their instructions on their page, so I started to search for an alternative that would 
allow me to host my Blog.

I found an excellent platform that promised everything I wanted: [Netlify](https://www.netlify.com/), the basic
tier is free, so I decided to try and host it there.

## Build process
My build process is very simple:

1. Add new content to my blog usually by running `spress new:post` to get the basic structure
2. Add some content
3. Commit my changes to a git repository (Github, Bitbucket, Gitlab, etc).
4. Build the Static HTML `spress site:build`
5. Deploy: `cd build` and `netlify deploy`

And my site would be online as soon as I deploy it, `netlify` is a CLI tool provided by them to make this deploys
easier and faster you can install it using this [Netlify Command Line Tools](https://www.netlify.com/docs/cli/).

## CDN
And last but not least, I created an account on [CloudFlare](https://www.cloudflare.com) in order to have some traffic 
mitigation and SSL certificate automatically. The basic free tier was more than enough for my needs.

## Conclusion
I really like Spress, however, one of it's main downsides is that not a lot of people are using it, the documentation
on the Spress site is kind of basic, and help outside of the site is very scarce, I would recommend using it only 
if you feel comfortable with PHP, Composer, and Twig, if you think you might need more community help, 
you might be better using hugo or jekyll.


