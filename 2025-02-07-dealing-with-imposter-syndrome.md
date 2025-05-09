

# Dealing with imposter syndrome as a software engineer

I was recently speaking with a colleague who is earlier in their career journey than me. They were explaining how they often work on tasks that require technical knowledge they don't have; they feel overwhelmed by the amount of knowledge required to complete these tasks, and worried that other people might start noticing that they don't have the skills required for the job.

My colleague was asking me:

1.  Is this normal?
2.  When will this stop happening to me?
3.  How can I deal with these situations?

Of course, my answers to the first two questions were *"yes"*, and *"never"*! These feelings of not having the knowledge others might expect you to have&#x2013;or [imposter syndome](https://en.wikipedia.org/wiki/Impostor_syndrome)&#x2013;is a common experience in software engineering. Along my journey, it has been a frequent companion. Perhaps it happens less frequently as you gain experience, but it certainly still happens.

However, one thing that became clear to me as I was speaking with my colleague was that I do have an answer for (3). I suppose I've become used to identifing the onset of imposter syndrome, and have developed some strategies for dealing with it effectively. Nowadays, I recognise when I'm entering a situation where my lack of technical knowledge might be difficult for me, and I employ a toolkit of techniques to help me navigate it.

I shared these ideas with my colleague, and they found them helpful, so I thought I would share them here in-case others find themselves in similar situations and ask themselves: *"how can I deal with this?"*


## Talk to people

When I feel like I'm the only person who doesn't know *that technology*, my first reaction used be to hide it from those around me. I don't want to get caught out! 😅

However, it became clear that this was counterproductive for two reasons:

1.  Your colleagues are probably in the best position to help you learn.
2.  You can't ask basic questions if you're pretending to be an expert!

In my experience, it's best to be candid with those around you. When you're assigned a ticket to write a Kafka consumer, you say *"that sounds interesting, but by the way I've never worked with Kafka before!"*

That way, you can ask for help when you need it, without having to worry about other people finding out that you're new to a topic; you've already told them! You might not want to go straight to your local subject matter expert, and ask them spend an hour on a Zoom call explaining the basics to you, but you can be honest about what you know you don't know 👍


## Read the docs

Reaching out to your local subject matter expert to help accelerate you learning is certainly a good idea, but before you do I recommend grounding yourself in the official documentation. Normally, any new technology you need to learn will have a good primary source of documentation. This primary source will normally have some high level *concept* documentation, and some detailed *implementation* documentation. Detailed documentation can often compound your feelings of being overwhelmed at this stage, but *concept* documentation can help you frame your lack of knowledge in the right terms. It can give you context, and help you ask the right questions later.

Even if you're not facing a new technology, but are trying to understand the fundamentals of something, I think it can still be really valuable to try to find the *primary source* of documentation early, and refer to it as you learn. This might be an [IETF RFC](https://www.rfc-editor.org/) for internet protocols, or a [man page](https://man7.org/linux/man-pages/index.html) for Linux commands.

Once you've found the documentation, familiarised yourself with their layout, and read about some foundational concepts, you will probably still feel out-of-your-depth, but you'll be able to start asking the right questions of those around you.


## Boil it down

OK, so you've told everyone you're new to this topic. You've found the docs, and read about some big ideas. You've had an intro call with a colleague, who has explained some things you understood (and some things you didn't!).

What's the next step?

For me, the way I really learn about a new technology is to use it. The trouble is, often the easiest way to interact with something is in a real environment. Production? Probably not! Development? Yes, but you're worried you might break things 😅

Once I've got some context, and I can frame the things I need to learn, I try to "boil them down" to a simple, self-contained development environment that I can run *locally*. That way, I can experiment as much as I like without affecting other people. I can say, *"I wonder if it breaks when I do this&#x2026;"*, and I don't have to worry when the answer is, *"oops, yes it does!"*

What this looks like in practice will vary depending on what you're doing, but here are some ideas for inspiration:

-   If you need to run a piece of software, try finding a [public Docker image](https://hub.docker.com/) for it. That way, you don't need to install anything locally, you can just `docker run` it.
-   If you need to run several pieces of software in concert, consider writing a small [Docker Compose](https://docs.docker.com/compose/) file so that you can easily recreate your environment.
-   Try to identify a way for you to interact directly with your new technology; is there a CLI, an HTTP API, or a web front-end you can use?

Ultimately, if you can set-up a local development environment, you can start learning directly through experience and try things out safely that you would otherwise want to run in a real environment.


## Closing thoughts

Whether you're new to software, or an experienced practitioner, feeling overwhelmed by the amount you don't know is a frequent and daunting experience. For me, this is not something that has ever gone away; I'm always learning new things, and frequently coming up against topics that confound me. I think facing new domains, learning about them, and becoming proficient is one of the hallmark experiences of working in software engineering. I hope the tips and tricks in this post help to reassure you, and encourage you to be a lifelong learner 📚

