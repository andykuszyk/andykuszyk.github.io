# A LaTeX Deployment Pipeline
Outside of my day job, I dabble in writing short science-fiction stories. I recently decided to start publishing these stories on a small site in order to share them with my friends and family and solicite feedback.

During this process, I needed to come up with a good way of transforming my raw LaTeX files into a working site that could be updated automatically. This article takes you through the process of:

* How I structured my LaTeX projects to make automation easy.
* How I automatically updated my static site on every push to a LaTeX project.
* How I regenerated my static site from the original LaTeX sources.

## The structure of my LaTeX projects
After a bit of experimentation, I structure my projects following the template at https://gitlab.com/akuszyk/template.

This repo consists of a simple LaTeX book layout, with a main file (`template.tex`) which is used to configure the layout of the document and include any other files (e.g. chapters). For my shorter work, I simply write directly in this file.

Included in the repo is a Makefile, with plenty of handy targets for building PDF, HTML and EPUB files. Most notable, however, is the default target. Simply running `make` will build a PDF of the document using the Docker image defined in https://github.com/andykuszyk/latex.

Building the PDF in Docker container means that I don't need a local installation of LaTeX on the machine where I'm working, just Docker, and it also means I get reproducable output files whether I'm building the PDF locally or on a CI platform.

## The CI pipeline for my LaTeX projects
Whenever I push to any of my writing projects, the CI will build a PDF, stamp it with part of the commit hash and artifact the PDF. This makes it easy to quickly take a look at previous builds in PDF format and access PDFs on the go.

Some of my projects also publish directly to my static fiction site, https://akuszyk.com, and this involves a couple of additional steps. Once the PDF is built, I use the targets in the Makefile to also build a HTML and EPUB files (which is simple enough, using `pandoc`). I then clone my static site (https://github.com/andykuszyk/akuszyk.com), copy the files in and run a site generation make target which transforms these raw files into the static site which is served.

Once this process is complete, I commit and push the changes and let the CI for the static site take care of publishing the new version.

## Regenerating my static site from raw LaTeX files
As mentioned above, whenever I push to a LaTeX project that is configured to be published, I generate an HTML file and then re-generate my static site. This process involves using some template files along with a bit of Python to take the HTML file converted by `pandoc` (from the original LaTeX sources) and transform it into a static site that is a bit more user friendly (with a touch of Bootstrap and Javascript). You can check out the site generator I've written at https://github.com/andykuszyk/akuszyk.com.

A key part of the generated site is the ability for readers to leave comments against each story, which I'll cover in the next section.

A commit to the static site repo results in the site being packaged up into a new Docker image and deployed out to my web server.

## Static site comments - the GitLab way
Part of the point of publishing my work in the first place was to solicit feedback from readers. I wanted people to be able to leave anonymous comments easily and quickly, without having to sign-up to any third party services, or sign-in with any social logins.  It was also important for the comments to end up somewhere useful, where I could be notified about them, review them and work on them.

Since I store my writing projects in GitLab, the natural place for my to store the comments was as issues in each GitLab project. As a result, I wrote a small project (https://github.com/andykuszyk/gitlab-issue-comments) that could create and list issues from a GitLab project, but expose an unauthenticated HTTP interface suitable for use with a public facing blog (or fiction site!). The idea is that this service has a personal access token that it uses to access the GitLab API and simply forwards on comment-style messages to and from GitLab.

## Conclusion
With a version of this comments/issues service deployed alongside my static site, I was now in a position to publish my LaTeX projects at the push of a commit in a fully automated way, with the capability for readers to leave comments at the end of it. I hope this helps other budding authors in a similar position to me!

## Links
* A template LaTeX project layout, complete with hand make targets: https://gitlab.com/akuszyk/template
* My static site generator, demonstrating the conversion from `pandoc` HTML files to a working site (including comments): https://github.com/andykuszyk/akuszyk.com
* A service to allow comments to be created and fetched using GitLab issues: https://github.com/andykuszyk/gitlab-issue-comments
* My finished fiction site: https://akuszyk.com
