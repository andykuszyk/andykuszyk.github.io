# Decision-making and design in growing engineering organisations
I recently [wrote about](https://andykuszyk.github.io/2023-03-15-bootstrapping-an-engineering-organisation.html) what I consider to be the key ingredients for bootstrapping a new engineering organisation. Many of these ingredients are about *what* you use to build your organisation with. However, this post describes one key element of *how* you do it.

In some senses, the way you make decisions in a small engineering organisation may seem unimportant. Especially when there is a small team involved, decisions can be made synchronously via conversation. Similarly, system design can be conducted at a whiteboard; be it physical or virtual.

However, as an engineering organisation grows, face-to-face decision making and design starts to scale less well for a couple of reasons:

1. The communication of the decisions and designs doesn't scale well; the reasons why things are being done a particular way have to be explained (and remembered!) by every new joiner.
2. The process of actually making decisions becomes less democratised as the size of the team grows; it's harder to keep people involved in the process when you can't share a pizza amongst the team.

If you don't start off making decisions and designing new things in a way that scales well with the size of your engineering organisation, you end up with a "blind spot" where no-one really knows why things were done the way they were when your organisation was started. Of course, this isn't strictly speaking true; much of this initial knowledge might become "tribal"--known by everyone--without the need for any specific practices. However, sooner or later you'll need something better than "face-to-face".

This blog post describes one way of making decisions and designing new changes which scales well with a growing team, and includes everyone in the process.

## Writing, not talking
As an engineering organisation grows, synchronous decision-making in conversation becomes harder to scale, and becomes less inclusive. In my view, the solution to this problem is to start off by making decisions via the medium of writing, rather than talking.

In the beginning, in a small team, it might be very natural for decisions to be made verbally; and that's fine. However, in order for the decision-making and design to be preserved for future team mates, and for it to be easily consumed by the masses of your future organisation, I still think it needs to be written down.

A popular pattern for this is the Architecture Design Record (ADR), in which a short document is written to capture the context, outcome, and consequences of a decision. Provided an ADR is the artifact of the decision-making or design process, then you can rest assured that the decisions made as your engineering organisation was bootstrapped will be easy to share with new colleagues in the future.

## Problems, not solutions
This is probably true at any stage in the growth of an engineering organisation, but especially as it begins to grow beyond the founding members it is important to focus on problems before solutions. As with the early decisions, I think it's helpful to do this in a written form, so that problems and their solutions can be shared with a large number of colleagues in a way that everyone has access to the decision-making material.

My preference is to structure this discourse via two document types:
1. Problem requirements documents (PRDs).
2. Requests for comment (RFCs).

When you start considering a topic that requires a decision, or a new feature that requires a design, the idea is that you start off by clearly describing the problem you're solving in a PRD. A PRD might consist of some background information, the motivation for solving the problem, and the problem description itself. If everyone agrees on the problem statement, then everyone is on the same page when it comes to considering possible solutions, and making a decision.

Having agreed on the problem you're solving, it's possible that you may want to discuss several competing solutions. An RFC provides a structured approach for you to refer to a specific problem, and outline a proposed solution. This is equally useful for making a decision about the way your organisation is run, as it is for deciding on the architecture for a new functional component in your system. An RFC might contain some background information, a reference to the problem, and the proposed solution.

Making decisions and designing changes via PRDs and RFCs has a number of advantages in my view:
- They scale well with the number of people in your organisation; everyone can read them without the need to pass on information verbally.
- They democratise access to the decision-making process; anyone can read and comment on a PRD or RFC and take part in the process.
- They provide a consistent, neutral medium for discussion and decision-making; whether or not someone is a charismatic speaker is less likely to enter into the decision-making process, and people are more likely to consider the facts as presented in the written document.

## A history of design
I would advocate utilising PRDs and RFCs from the very beginning when bootstrapping an engineering organisation. Doing so builds a culture of writing to make decisions and design new systems, and makes your decision-making transparent and accessible from the beginning. Any decisions made outside of a PRD/RFC and be recorded in ADRs, so that they don't get lost in the mythical history of your organisation.

The end result is an iterative, cumulative history of all the decisions you have made in building your team and your product. The design documents describe the evolution of your system, and make ideal reference materials for new team members, or for future conversation. If anyone wants to know "how your system works", you can just give them a curated list of RFCs to read.

In my opinion, using writing to make decisions and design new things is a great way of generating high-quality, living documentation, as well as for democratising and scaling the decision-making process.

## PRDs, RFCs, and ADRs: a summary
I think that using writing to make decisions from early on in the life of an engineering organisation is invaluable. Not only does it set you up for success as your organisation grows, but it also builds a high-quality history of design that will be valuable again and again as your team and product are built.

I've found a good pattern for structuring this written material to be the use of three distinct documents:

1. Architecture decision records (ADRs): a decision that you've made.
2. Problem requirements documents (PRDs): a problem that needs to be solved.
3. Requests for comments (RFCs): a solution you'd like feedback on.

These documents could be in Google Docs, Markdown files in a Git repo, or issues in a Git forge. The main thing is that you establish practices for using them as the medium for your organisation's decision-making and design processes.
