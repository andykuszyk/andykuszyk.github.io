# Bootstrapping a successful engineering organisation
For the past 4 years, I have been working for a very mature engineering organisation (Form3). Despite its relatively small size when I joined (around 35 engineers), it was very well established with lots of excellent practices in place. Over these last few years, the engineering team has grown significantly (to over 200 engineers) with little change to these working practices.

I've been thinking about why there has been so little material change, and I think it's because the engineering organisation was very well bootstrapped in its infancy. The founding team did a great job of knowing what we would need as the organisation grew, and they got a lot of it right first time. There have obviously been improvements and enhancements along the way, but most of the underlying approaches haven't changed.

In this blog post, I'm going to try to identify the main themes in this bootstrapping process by reflecting on the elements of Form3's makeup which are so successful. There are lots of ways of thinking about this, and grouping different components of a functioning engineering organisation. I have grouped what would otherwise be a long list of themes into three categories: Day One, Day Two, and Considerations. I will write subsequent blog posts about most of the Day One and Two themes to discuss them in detail, whereas the Considerations are briefly discussed towards the end of this post.

The themes are as follows:

1. **Day One:** themes that I think are most important in the early bootstrapping phase of an engineering organisation.
	1. [Secrets management in build and deployment pipelines](#secrets-management-in-build-and-deployment-pipelines).
	1. [Distributed management, DevOps, and hiring](#distributed-management-devops-and-hiring).
	1. [Decision making and design](#decision-making-and-design).
	1. [Infrastructure as code](#infrastructure-as-code).
	1. [Environment access](#environment-access).
	1. [Documentation](#documentation).
	1. [Testing](#testing).
2. **Day Two:** themes that I think need to be tackled as engineering organisation begins to grow.
	1. [Secrets management in applications](#secrets-management-in-applications).
	1. [Incident response](#incident-response).
	1. [Observability](#observability).
3. **Considerations:** themes that need to be addressed when starting and growing an engineering organisation, but which I think are issues that need to be addressed on a case-by-case basis.
	1. [Building and managing compute capacity](#building-and-managing-compute-capacity).
	1. [Service communication and messaging](#service-communication-and-messaging).
	1. [Orchestrating containers](#orchestrating-containers).
	1. [Security considerations](#security-considerations).
	1. [Programming languages](#programming-languages).
	1. [Database engines](#database-engines).

Overall, I hope this series of blog posts serves as a useful set of examples of how to successfully bootstrap an engineering organisation from the beginning.

*Disclaimer: this is based on my experience working in business-to-customer and business-to-business SAAS environments, where the product is web-based. Your mileage may vary!*

## Day One
### Secrets management in build and deployment pipelines
Secrets are required to build and deploy software. Normally when you start out, there are some simple, secure ways to inject secrets into your build and deployment pipelines. For example, with Travis you can use the CLI to commit an encrypted file to a repo, or add an encrypted environment variable. In GitHub actions, you can add a secret to a repo, which you can safely use later in your workflows.

However, it doesn't take long before you've got lots of secrets in lots of different places. When this happens, the secrets themselves become hard to manage, and hard to maintain. You can't easily revoke or rotate them, or roll out new secrets automatically.

Ideally, when bootstrapping an engineering organisation, you want to establish a good way of injecting secrets into your entire build and deployment pipeline estate from day one.

I've written a [dedicated blog post](./2023-05-03-secrets-management-in-build-and-deployment-pipelines.md) about this topic, with some practical ideas for doing this well on day one.

### Distributed management, DevOps, and hiring
You might be wondering what management, DevOps, and hiring have in common at Form3 such that I have grouped them together! One of the philosophies underpinning the organisation's foundation was the distribution and delegation of responsibility to a largely self-organising team.

This means that, as we have grown, our large team has crystallised into multiple independent, and self organising units. Each unit is organised around a particular functional area, and has complete autonomy to run and improve this area.

The entire engineering organisation was begun as a DevOps team, with a single engineering population responsible for building, deploying, and supporting our platform. This has led to a culture of ownership and pride, and means that each team has everything it needs to operate autonomously.

This same approach can be seen in many other areas of the engineering organisation at Form3, but it is most notable in our hiring process. There is no hiring manager in charge of engineering hiring at Form3, nor is there a fixed panel of interviewers. Instead, every engineer who has been with the team for longer than 6 months is involved in the process. This means that most of our engineers are also interviewers, and are involved in recruitment activities. This might sound like a lot of non-engineering work for members of our team, however each of interviews is split into three 30 minute segments, each of which is conducted by a different engineer. In practice, an engineer can expect to do a couple of these per month, which means the burden of hiring is greatly reduced, and is distributed across our engineering team.

This hiring example just serves to demonstrate how the founding team decided to distribute and democratise a range of responsibilities across the entire engineering organisation as it grew. Rather than keeping control of these important activities in the hands of a select few, they have become part of the fabric of our engineering community, and not a bottleneck beholden to a small group of decision makers.

### Decision making and design
With a small team, it's easy to quickly make decisions and act on them. Decisions can be made over a cup of coffee, and system architecture can be roughed out on a whiteboard. However, when more people join your team this doesn't scale well, and it isn't very inclusive. Furthermore, the further in the past that you made decisions, the more times people will ask, "why do we do it that way?".

Having a scalable process in place for distributed decision making and design is essential--especially in a remote setting--to making sure everyone is included in shaping the trajectory of your product. It also ensures that future generations of your team can look back on the fruits of your labour, and understand why things are the way they are.

I've written a [dedicated blog post](https://andykuszyk.github.io/2023-05-17-decision-making-and-design.html) about this topic with some ideas I've found useful for structuring the decision-making and design process.

### Infrastructure as code
Although taken for granted now, infrastructure as code was in its infancy when Form3 was founded. Despite its novelty, codified infrastructure (using Terraform) is completely ubiquitous at Form3. Everything from our AWS infrastructure to the repos in our GitHub organisation are managed through Terraform. Anything you can imagine with an API is managed via Terraform (apart from perhaps Slack channels!).

Beyond the obvious benefits this brings, it means that access to a wide variety of systems is democratised. Everyone can inspect, understand, and contribute to our PagerDuty configuration, network infrastructure, Grafana dashboards, Logz alerts, etc. via a uniform tool chain.

This has scaled very well with the size of the team, and has maintained a culture that we all own everything equally.

### Environment access
You'll probably start with a development environment. Then you might need a testing environment. Soon enough you'll have a production environment, and in no time at all you'll have a fleet of environments to manage. For each environment, your engineers will need a variety of different means of access. For example:

- SSH access to compute nodes.
- Control plane access (e.g. via `kubectl`) to Kubernetes.
- Database access to your database servers.
- Command line or web console access to your cloud provider.

For some environments, you'll want engineers to have unrestricted access. In others, you'll want access to be tightly controlled, possibly with some kind of privilege escalation and auditing.

Access to your environments is a fundamental component to your engineering organisation, and getting this right at the beginning will allow your team to grow and build out a platform in a secure, safe way.

I've written a [dedicated blog post](https://andykuszyk.github.io/2023-11-07-securing-environment-access.html) on this topic with some examples of how to secure your environments at scale.

### Documentation
When there are only two engineers in your team, it hardly seems necessary to maintain documentation. Often knowledge is "tribal", and is passed on by word of mouth. This scales well in the beginning, when you add a modest number of people to your organisation, but sooner or later it breaks down. At this point, if you never built a culture of written documentation, you have two problems:

1. You have no documentation.
2. No-one wants to (or is practised at) writing it.

Starting your organisation with a healthy attitude towards written documentation will pay dividends as your engineering team, and product, grows.

*Blog post to follow!*

### Testing
Something Form3 does very well, and has done well from the beginning, is automated testing of the software the team builds. This is thanks to a relatively simple formula consisting of:

- Mainly component-based integration tests.
- The use of real dependencies where possible, or external mocks where required (e.g. [`localstack`](https://github.com/localstack/localstack)).
- Continuous soak tests in pre-production environments.
- Few or no unit tests.

This last point can be a bit controversial, but it has led to a platform that is routinely delivered into production with very rare functional defects. Functional problems are almost always found during development, thanks to the very robust nature of full-stack integration tests. This technique is employed across Form3, to great effect.

Non-functional problems relating to concurrency or load, are generally picked up by the continuous soak tests we run using [`f1`](https://github.com/form3tech-oss/f1).

As a result, from day one, we've been able to build and deploy our platform with confidence, with little operational burden on the engineering team, and no manual testers.

*Blog post to follow!*

## Day Two
### Secrets management in applications
Secrets are required to run your applications. You might start off by using your deployment pipeline to deliver secrets into a runtime environment (via environment variables), although that's obviously not very secure. You might use secret deployment tools like [Sealed Secrets](https://github.com/bitnami-labs/sealed-secrets) to conveniently manage secrets from source control. Both of these approaches, whilst simple, don't allow you to manage a fleet of secrets en-masse.

Pretty soon you will want to start rotating secrets, just like you'll need to in your CI pipelines, and eventually you might want to start using shorter-lived secrets to secure your environment. Building in something that can issue secrets to your applications at runtime, from the very beginning, will set your engineering organisation up for success in the future.

*Blog post to follow!*

### Incident response
Your software and platform will fail, and it will probably fail often! You want to be prepared for this from the beginning, so that everyone on the team understands how to respond to incidents, and has the tools and experience to do so.

Part of this is tooling, but a greater part is your organisation's attitude towards incident response, and how it is organised internally.

*Blog post to follow!*

### Observability
Building applications without an observability platform leaves your engineers blind to how their software is performing, and makes it almost impossible to manage your product in production. Establishing a means of observing your software from day 1 is essential.

*Blog post to follow!*

## Considerations
### Building and managing compute capacity
There are lots of ways to do this, and you'll probably have a preference. As long as you make a sensible choice for your organisation, I don't think you can get this wrong at the beginning. You can always change it later too.

### Service communication and messaging
Again, this is probably dependent on your preferences and domain. I don't think the choice of these technologies is critical to your success.

### Orchestrating containers
There are also lots of ways of doing this. It's probably harder to change this later if you do get it wrong in the beginning, but this a technology choice you can make based on the tools available on the market.

### Security considerations
These will become very important! However, when you're starting a new engineering organisation I don't think you need to solve every problem all at once. If you can get secrets management and environment access right from the beginning, then I think you'll have solved some of the larger challenges, and you can fill in the blanks as your organisation grows.

### Programming languages
Choosing a set of programming languages to use is probably one of the more visible choices that need to be made when starting an engineering organisation. Form3 started with Java, and later moved to Go. I don't think a bad choice can really be made here, because it's always possible to move to a different language later. Ultimately, this choice is probably based on your preferences, your experiences, and the type of software you're planning to build.

### Database engines
Whilst this is an important consideration, I think it's specific to your domain and preferences.

## Conclusion
Based on my observations and reflections of working for a successful engineering organisation, some of the biggest challenges to get right at the beginning are:

1. Secrets management (in CI, and your environments).
2. Environment access.
3. Observability.
4. Incident response.
5. Decision making and design.
6. Documentation.
7. Testing.

In my future posts, I will address each of these themes in more detail to try to provide some practical examples of how you can achieve success.

I think these are the essential ingredients to bootstrapping a successful engineering organisation, and I see signs of these themes everywhere at Form3.
