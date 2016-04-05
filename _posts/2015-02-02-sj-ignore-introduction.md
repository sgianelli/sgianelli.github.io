---
layout: post
title: Introduction to sj_ignore
tags: ruby git open-source
---

Last week I threw together a simple little command line utility in ruby called
[sj_ignore](https://github.com/sgianelli/sj-ignore) that I have actually found
really useful since then and was a good excuse to learn the basics of packaging
my own gems.  sj_ignore is really simple, run from the terminal, it makes it
easy to create a custom .gitignore file for a project by using the
[github/gitignore](https://github.com/github/gitignore) repository.

I have found that this repository has always been a good starting point for
adding a .gitignore file to a new project, especially when learning new
languages and platforms.  This utility was geared at making that setup step
just a little bit easier by making the process of creating a .gitignore file
for a new Rails project as simple as:

    sj_ignore vim ruby rails

In a bid to make this project as simple as possible, I initially implemented it
using the GitHub api (via the
[github_api gem](https://github.com/peter-murach/github)) which was incredibly
easy to get working with just a few lines of code.

    github = Github.new
    contents_top = github.repos.contents.get user: 'github', repo: 'gitignore', path: '/'
    contents_global = github.repos.contents.get user: 'github', repo: 'gitignore', path: '/Global'

This grabs a list of all files in the gitignore repository and returns them in
the two arrays `contents_top` and `contents_global`. Simple enough, we can then
use this information to download our desired gitignore files. This is the only
time we call the github api, but using this method we have to make two api calls
during every run. That is pretty much it for the version that uses the github
api and it is super useful for limited use because of this.

The problem, as it turns out, is the github api limits you to only so many
unauthenticated api calls per hour.  At this point I restructured the project a
bit and added support for a more durable solution.

This new solution involves cloning the repository locally and then reading from
the local files instead of downloading them every time. I find this method more
preferable overall because it does not require an internet connection after the
first time you run it and does not need to wait on a download. This method uses
a couple lines of bash to run `git` on the command line to clone the gitignore
repo and the rest is a simple bit of file reading.

Overall, this is an extremely minor command line utility but I have found it to
be incredibly handy and I find myself not lazily creating .gitignore files
anymore.

If you find any issues with it or have any suggestions for me, please open a
new [issue](http://github.com/sgianelli/sj-ignore/issues) and pull requests are
always encouraged!
