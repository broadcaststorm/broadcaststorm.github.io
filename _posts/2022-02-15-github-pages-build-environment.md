---
layout: post
title:  "GitHub Pages Build Environment for Jekyll"
categories: github-pages jekyll fedora vagrant
---

I love the idea of GitHub Pages and static content websites (and free!).
I loathe the thought of a billion "fixing typo" or "fixing markdown"
commits.  This article describes the local build environment I built
around Vagrant to test the pre-publish content. It's generic enough
that you can use it too!

## GitHub Pages

This article isn't about
[GitHub Pages](https://docs.github.com/en/pages/getting-started-with-github-pages/about-github-pages)
but you need to know just a little bit about it. In short, you can use
GitHub provided themes (which are Jekyll based) or your own custom Jekyll
theme.  There are of course a wide selection of freely available themes
out there that some very kind people have made available^[As a side note,
you can host static content built from other generators, you just have
to have your own workflow to generate them.]

The important concept to keep in mind here is that these sites
usually are static content - meaning that the Jekyll engine processes
the Markdown you've written and merges it with templating to create
static HTML files that will get rendered by your browser. Any change
to content or CSS will result in having to rerun Jekyll to rebuild
the entire site.

## The Challenge

With the [GitHub provided themes](https://pages.github.com/themes/),
there is still a use case for a build environment if you want to be
really particular about your commits. However, when using a
[custom theme](https://docs.github.com/en/pages/setting-up-a-github-pages-site-with-jekyll/adding-a-theme-to-your-github-pages-site-using-jekyll),
any number of details could cause issues with your site, such as:

- Jekyll errors that fail to build your static content.
- Difficulty in troubleshooting those issues (where in the world is
the "error reporting" with GitHub pages?!?!)
- Turnaround times for tuning the layouts (typically via CSS) are
not really feasible.

What turnaround times?  Well, the typical publishing workflow is:

- Edit repo
- Commit changes
- GitHub batch rendering

And, when you've got errors, that introduces a significant overhead.
Keep in mind as well that GitHub does invoke a soft limit of 10 builds
per hour. The amount of troubleshooting in that environment is very limited.

## The Solution

With Jekyll, you can fortunately
[build your site locally](https://docs.github.com/en/pages/setting-up-a-github-pages-site-with-jekyll/testing-your-github-pages-site-locally-with-jekyll)
install the required Ruby and dependent GEMs that GitHub uses to build the
static content locally on any system (laptop, of course).

The challenge is the typical developer conundrum: how to maintain a clean
build environment that doesn't clobber my other work, especially since the
version of Ruby is a bit dated for most up-to-date distributions.

[My solution](https://github.com/broadcaststorm/github-pages-build-jekyll)
was to leverage Vagrant and a Fedora-based image (box) to build a clean
environment into which I'd install the correct versions of Ruby and the
GitHub Pages GEM dependencies.  The
[README](https://github.com/broadcaststorm/github-pages-build-jekyll)
provides all the context and instructions but the short, short version
is:

```bash
git clone https://github.com/broadcaststorm/github-pages-build-jekyll local
cd local
git clone MY_GITHUB_PAGE_REPO blog
vagrant up
vagrant ssh
cd blog
bundle exec jekyll serve --host=${MYHOST}
```

You'd use an editor in the host system to make changes and check those
changes on the website running in the guest system but portmapped to
your laptop via http://localhost:4000.

Pretty neat, eh?  Rocket science - no.  But, in the world of DRY, this
is the best way to do this once and then not have to figure it out
again and again.

## Wrap it up

Yes, it's built on Fedora because that's my heritage. I've slowly been
getting familiar with Ubuntu over the years but still run back to my
roots when doing projects like this.  Want an Ubuntu version?  I'm
happy to take pull requests - simply call it Vagrantfile.ubuntu (or
some other obvious extension).
