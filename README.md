Welcome to my blog 👋

I'm Andy Kuszyk, and I work as a Software Engineer. I'm based on the South coast of the UK, and enjoy writing about my experiences.

I currently work with Golang, Terraform, AWS, and Kubernetes. I also have experience in .NET, Clojure, and Python. And that's not to mention [Elisp](https://github.com/andykuszyk/noman.el)! 🤓

📓 If you're looking for my CV, you can find it [here](./cv/README.md).

💬 If you'd like to get in touch, please reach out on [LinkedIn](https://www.linkedin.com/in/andy-kuszyk/).

🛜 You can subscribe to updates on this blog using the [RSS feed](./feed.xml).

---
## [How I write technical documents](2025-04-11-how-i-write-technical-documents.md)
I was recently talking with a colleague about how to write technical documents, and they asked me if I had any tips. Although I enjoy writing technical documents (e.g. design documents), I'm by no means an expert. I shared my thoughts with my colleague, and they found them valuable, so here they are in written form&#x2013;my favourite medium!

*Published: 11th April 2025*

---
## [Dealing with imposter syndrome as a software engineer](2025-02-07-dealing-with-imposter-syndrome.md)
I was recently speaking with a colleague who was explaining how they often work on tasks that require technical knowledge they don't have. I told them that this was a familiar experience to me, and that I frequently feel "out of my depth"! In this post, I explain some of the ways I deal with this, and remain confident and productive when learning new technologies.

*Published: 14th February 2025*

---
## [Entering GPG passphrases with Emacs when signing commits with GPG](./2025-01-10-commit-signing-with-gpg-and-emacs.md)
I recently set-up commit signing with Git for the first time since I've been using Magit in Emacs as my daily-driver Git client. It turns out, if you use a passphrase for your GPG keys, it's a little tricky to setup Emacs as the pinentry client for entering passphrases. I've written down what I did to get this working in the hopes that it will save my future self (and perhaps you!) the effort of finding out how to do it.

*Published: 13th January 2025*

---
## [Running parameterised Jenkins jobs using the Jenkins CLI](./2024-12-05-jenkins-cli.md)
I have used Jenkins on-and-off for many years, but have never really been a power-user. I recently started using it a lot more, and needed to trigger parameterised jobs regularly with a variety of different parameter configurations. Doing this through the UI quickly became cumbersome, and I thought to myself: *there must be a better way!*

It turns out, there is! In this post I explain how to set-up and use the Jenkins CLI, to make working with your builds from a terminal nice and ergonomic.

*Published: 6th December 2024.*

---
## [Hypothesis-driven debugging](./2024-11-29-hypothesis-driven-debugging.md)
Several years ago, I spent some time investigating performance problems in a large distributed system. After a while, I noticed that I was using something akin to the scientific method to track down the root causes. In this post, I explain how to use hypotheses--specifically *falsifiable* hypotheses--to debug problems in software systems.

*Published: 29th November 2024.*

---
## [My three phases of onboarding as a staff engineer](./2024-11-12-my-three-phases-of-onboarding.md)
I recently joined [Typeform](https://www.typeform.com/) as a staff engineer, which marked the third time in as many years that I've either joined a new company, or joined a new team, in this type of role. It's my experience that onboarding as a staff engineer is different to onboarding in other types of engineering roles, and I've noticed that I go three distinct phases of onboarding when I start out in this type of role. In this post, I describe my three phases, and offer some practical examples of how I navigate each one.

*Published: 22nd November 2024.*

---
## [Debugging phantom 400 responses](./2024-10-11-debugging-phantom-400-responses.md)
I recently spent an entire week debugging an unusual situation involving unexplained, intermittent 400 responses to seemingly well-formed HTTP requests. Throughout the week, I tried a variety of things to debug the situation, pulled out more than a little of my hair, and finally stumbled across the solution.

*Published: 11th October 2024.*

---
## [Where do all the senior engineers go?](2024-10-04-where-do-all-they-senior-engineers-go.md)

Earlier in my career, I wondered where all the senior engineers went in technology companies. There didn't seem to be that many in their 40s, 50s, and 60s. Now I'm a little more experienced, I think I've spotted where a lot of them end up.

*Published: 4th October 2024.*


---
## [Editing encrypted files in Emacs](./2024-08-29-editing-encrypted-files-in-emacs.md)
I routinely store note files in Git repos, and use Emacs' native GPG support to encrypt and decrypt sensitive files. In this post I explain how I create and distribute GPG encryption keys, and how I configure Emacs to make the process as ergonomic as possible.

*Published: 3rd September 2024*.

---
## [A practical checklist for building distributed systems](./2024-06-28-practical-checklist-for-distributed-systems.md)
During a period when I was designing a new payments system, I worked with several teams of engineers to design new components in a highly distributed system. As we iterated on our design process, we created a checklist of ideas to consider when designing a new part of the system. In this blog post, I talk through each of the items on our checklist, and why we thought it was an important topic to consider when designing new distributed systems.

*Published: 2nd September 2024*.

---
## [A simple guide for writing good acceptance criteria](./2024-06-28-guidance-for-writing-acceptance-criteria.md)
I recently shared some tips for writing acceptance criteria with a team I'm working with, so I thought I would re-post them here in case anyone else finds them useful.

*Published: 28th June 2024.*

---
## [Securing environment access from day one (4/8)](./2023-11-07-securing-environment-access.md)
This is the third of my follow-up posts about [bootstrapping a successful engineering organisation](./2023-03-15-bootstrapping-an-engineering-organisation.md). In this post, I go into more detail about one way to secure engineering access to your environments from day one; in a way that will scale with the size of both your team, and your estate.

*Published: 29th May 2024*.

---
## [Using multiple SSH keys for git authentication](./2023-11-23-using-multiple-ssh-keys-for-git-authentication.md)
Although I've been vaguely aware this was possible for many years, I only recently had the need to authenticate with multiple GitHub accounts via SSH from the same machine. In this post, I summarise how to make use of multiple git SSH identities from the same host, with some easy-to-manage configuration tips.

*Published: 27th November 2023*.

---
## [Visualising data analysis in Emacs org-mode](./2023-11-18-using-emacs-org-mode-as-a-jupyter-notebook.md)
I recently wanted to perform some data analysis and visualise the results in a similar way to using a Jupyter Notebook. This post shows a couple of tricks for doing so directly from within Emacs org-mode.

*Published: 23rd November 2023*.

---
## [Decision-making and design in growing engineering organisations (3/8)](./2023-05-17-decision-making-and-design.md)
This is the second of my follow-up posts about [bootstrapping a successful engineering organisation](./2023-03-15-bootstrapping-an-engineering-organisation.md). In this post, I discuss the importance of documenting decisions from early-on, and describe a framework I've found useful for structuring problem-solving and design discussions as engineering teams grow.

*Published: 17th May 2023*.

---
## [Yet another guide to Mermaid diagrams in GitHub pages](./2023-05-03-yet-another-mermaid-in-github-pages-guide.md)
I've recently published my first blog post on GitHub pages using Mermaid diagrams as plain text in the markup. It was surprisingly difficult to find the annoyingly simple way to do this, so here is yet another guide on how to include Mermaid in GitHub pages.

*Published: 3rd May 2023*

---
## [Secrets management in build and deployment pipelines (2/8)](./2023-05-03-secrets-management-in-build-and-deployment-pipelines.md)
This is the first of my follow-up posts about [bootstrapping a successful engineering organisation](./2023-03-15-bootstrapping-an-engineering-organisation.md). In this post, I discuss some practical ideas for managing secrets in build and deployment pipelines which will scale well as an engineering organisation begins to grow.

*Published: 3rd May 2023*

---
## [Bootstrapping a successful engineering organisation (1/8)](./2023-03-15-bootstrapping-an-engineering-organisation.md)
I've been reflecting on the most important things to get right when bootstrapping a new engineering organisation, particuarly by thinking about what must have gone well when [Form3](https://www.form3.tech) was founded. This is the first of several posts describing what some of the major challenges might be, and how I think they can be solved.

*Published: 15th March 2023*

---
## [Remapping modifier keys on a Mac, for Linux](./2023-02-19-xmodmap-for-mac.md)
A description of how I remapped some modifier keys on a Macbook Air running Linux. I swapped `cmd` and `ctrl`, fixed right `alt`, and made `h``j``k``l` behave like arrow keys when caps lock is held.

*Published: 19th February 2023*

---
## [Migrating project v2 boards in GitHub](./2023-02-01-migrating-projectv2-boards.md)
Migrating issues from one project v2 board to another in GitHub can be a little painful. In this blog post I explain how I did it recently using the GitHub GraphQL API, and a smattering of Bash and Python.

*Published: 1st February 2023.*

---
## [What I learned from starting a new project](./2022-10-19-what-i-learned-from-a-new-project.md)
I have spent almost the last two years working on a large, greenfield project first as the lead engineer, and now as the head of engineering. I've learnt a lot about how to grow engineering teams, and build complex systems in this time. This blog post summarises some of my key learnings.

*Published: 19th October 2022.*

---
## [PKI certificate management](./2022-07-06-pki-certificate-management.md)
I have a rough understanding of PKI certificates, how they work, and what TLS is
in general. However, I've always struggled to understand the details,
particularly from the point of view of an operator. How do I check if a
certificate is valid? How do I check who issued it? What does it even mean to
"issue" a certificate? In this blog post I try to cover some of these details,
and lift the shroud of confusion that has always surrounded these topics for me.

*Published: 6th July 2022.*

---
## [Linux fundamentals](./2022-04-20-linux-fundamentals.md)
The Linux kernel has always held a mystical place in my mind. It's the inner sanctum of computer magic which makes programs work. Somehow. In this post I try to suummarise some of the big ideas about the Linux kernel, including what the kernel does, user/kernel space, and the syscalls API surface.

*Published: 20th April 2022.*

---
## [Network Address Translation and Proxies](./2022-03-16-nat-and-proxies.md)
I've always found NAT, forward proxies, and reverse proxies to be somewhat mystical, and I've never understood the fundamentals of how each technique works. This post tries to distil each idea into a simple form, and provides Go code examples where possible.

*Published: 16th March 2022.*

---
## [Tracking time and TODOs using Git](./2021-05-26-git-track.md)
I've recently finished a small project that allows me to track my working time and keep track of a simple TODO list using a Git repo's history. Here's how I did it.

*Published: 26th May 2021.*

---
## [Understanding Prometheus Histograms](2020-07-24-prometheus-histograms.md)
Prometheus histograms have always left me feeling a little confused. I recently used them to instrument a distributed system and this post covers a few of the things I learnt in the process.

*Published: 15th August 2020.*

---
## [A LaTeX Deployment Pipeline](2020-05-19-latex-deployment-pipeline.md)
A run-down of how I organise, build and deploy my personal fiction projects, written in LaTeX.

*Published: 19th May 2020.*

---
## [De-mystifiying i3](2020-02-18-demystifying-i3.md)
I've been using Linux for a couple of years but have always struggled to understand i3, Compton and how some Linux users make me feel like I'm still using Windows! Having finally made the switch from Gnome to i3, this post explains each of the technologies in turn as I understand them - in simple terms!

*Published: 18th February 2020.*

---
## [Running the ELK stack locally](2019-07-03-local-elk.md)
I recently needed to run the ELK stack locally in order to analyse some application specific logs. This post describes how I went about doing it.

*Published: 3rd July 2019.*

---
## [Investigating memory leaks with `jemalloc`](2019-06-14-jemalloc-memory-leak-investigation.md)
Investigating a memory leak using `jemalloc` proved to be difficult, so I've documented the steps I followed to use `jemalloc` with my application.

*Published: 14th June 2019.*

---
## [Heist Planning](2018-07-10-heist-planning.md)
After a recent, challening project, myself and the team I work with tried some novel ideas to help technical people from all disciplines plan a new project's architecture.

*Published: 10th July 2018.*

---
## [Jenkins as a Data Science platform](./2018-06-20-jenkins-as-a-data-science-platform.md)
In this post I discuss how we approach the problem of automating "small-data" Data Science pipelines, using Jenkins as an automation platform.

*Published: 6th June 2018.*

---
