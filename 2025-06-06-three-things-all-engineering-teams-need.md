# Three things all engineering teams need

Over the last few years, I've seen a handful of different engineering organisations from three perspectives:

1.  An individual contributor (Senior Engineer)
2.  An organisational leader (Head of Engineering)
3.  A technical leader (Staff Engineer)

From each of these perspectives, I've noticed that among the many and varied best practices each team has had, there have been three fundamental ingredients in common. Interestingly, I've seen all three materialised in quite different ways, but I think the essence of each is important no matter where you are. These three things are:

1.  A way to manage the configuration of your source code estate at scale
2.  A way to monitor the health of your source code estate at scale
3.  A central forum for technical discussion and decision making

The first two are quite related, and the third one less so, but I've noticed that all three seem to be buried at the heart of the engineering organisations I've worked in. In this post, I'm going to explain what I mean by each of these things, and provide some practical examples of how to bring them to life in a real engineering team.


## 1. A way to manage the configuration of your source code estate at scale

Assuming you use an enterprise git forge like GitHub, when you have hundreds of users and thousands of repos, managing the configuration of your source code estate becomes a challenge. Some configuration can be managed centrally through your forge's tooling (e.g. who can access which repos), but much of it is bespoke to your needs. Let me give you some examples of what types of configuration I'm taking about:

-   Using a standardised way to tag and categories all your repos.
-   Managing a standard set of files (CI workflows, CODEOWNER files, etc.) in all of your repos.
-   Ensuring your branch protection rules are uniform across your estate, or even dynamic depending on the use case (e.g. documentation repos vs. production repos).

In my experience, a good way to manage this type of custom configuration at scale is by using Terraform. Just as you might use Terraform to manage your AWS infrastructure, you can use the excellent [GitHub Terraform provider](https://registry.terraform.io/providers/integrations/github/latest/docs). It has support for managing [repo topics](https://registry.terraform.io/providers/integrations/github/latest/docs/resources/repository_topics), [branch protection rules](https://registry.terraform.io/providers/integrations/github/latest/docs/resources/branch_protection), and even [files](https://registry.terraform.io/providers/integrations/github/latest/docs/resources/repository_file).

Some of the best examples of using Terraform this way have been to maintain a declarative YAML file of all repos, and use this as an input to a Terraform module:

```yaml
andykuszyk.github.io:
  tags:
    - cool
    - interesting
    - emacs
  branch_protection:
    branch: main
    allow_force_push: false
  include_standard_workflows: true
```

You can then write a Terraform module which has some combination of options from the YAML file and organisation-wide defaults, which controls the underlying Terraform resources:

```terraform
module "repos" {
  source = "modules/github-repos"
  repos = yamldecode(file("repos.yaml"))
}
```


## 2. A way to monitor the health of your source code estate at scale

Managing the *configuration* of your source code estate is all well and good, but it doesn't tell you much about what's *in* your repos. I think it's also important to have a way to assess the health of what's stored within your repos, and have a global way of evaluating your estate, and imposing rules on it. Some examples might be:

-   Using standard linters for specific languages, or monitoring which repos aren't linted.
-   Checking for the use of deprecated tools or libraries, and warning about their use.
-   Assessing which repos meet certain standards (e.g. correct signal handling, or HTTP authentication).

This list could be endless, because these are mostly organisation-specific concerns. However, one way or another it's valuable to be able to assess the health of your source code estate against your own set of metrics.

I've seen two effective ways of achieving this:

1.  A standard CI workflow that runs in all repos either on PRs, or on a schedule (or both).
2.  A custom program which responds to GitHub events, or scans repos on a schedule.

The second option can be more flexible, but is also a lot more effort than the first. The first is quite versatile, but depends on a mechanism to ensure that all your repos have the same CI files installed in them.

Luckily, if you're using Terraform to manage your source code estate, you get this for free! 🎊

This standard CI file could run on every PR, or on a schedule every night, and:

-   Enforce certain rules to make sure only compliant PRs can be merged.
-   Provide advisory notices about engineering procedures.
-   Report back to a central datastore metrics which you think are interesting.

Between Terraform management, and a standard CI workflow, you have a way to standardise *static repo configuration* and *dynamic repo health*.


## 3. A central forum for technical discussion and decision making

I have [written about this topic before](https://akuszyk.com/2023-05-17-decision-making-and-design.html), but I think one of the most important ingredients for a successful engineering team is to have a central forum for discussion. This discussion can be about ceremonies (*what time shall we have stand-ups?*), technologies (*I think we should start using Clojure!*), or design (*Proposed architecture for feature X*). Actually, it can be about anything! The most important thing for me is that people have a central place where they can write their ideas, share them with their colleagues, and collect feedback.

I've seen this work as an RFC (request for comments) process, or a TDD (technical design document) forum, or informally as GitHub issues labeled `proposal`. The important thing is that there is a sign-posted, accepted way for people to share their ideas. Too many of these forums can dilute their impact, so my preference is for a single forum which can accommodate anything from a short proposal, to a fully-fledged technical design.


## Closing thoughts

There aren't *only* three important things for engineering teams, and teams aren't necessarily successful if they have all these things. However, I think a team of any size needs to be able to control their source code, monitor its health, and discuss changes to it. I think if you get those three things right, you have a solid foundation for building great things 🙂
