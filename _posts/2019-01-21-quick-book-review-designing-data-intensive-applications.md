---
title: "Quick Book Review: <em>Designing Data-Intensive Applications</em>"
---

I wish I read *[Designing Data-Intensive Applications](https://dataintensive.net)* years ago when I first started building web services and deciding which databases and processing systems to use.[^1] It's a well-written overview of the distributed systems landscape, exploring the underlying ideas behind different offerings and the tradeoffs they make when ingesting and storing data.

![Designing Data-Intensive Applications book cover](/images/designing-data-intensive-applications.png)

The profusion of options remains bewildering --- the practical difference between ActiveMQ and RabbitMQ still escapes me --- but I now at least have a better understanding of two-phase commit, where stream processors with durable storage like Kafka fit in, and when you might want to use a more exotic database like Voldemort (other than the cool name which alone makes me want to use it for *something*).

After learning about a new technology it's always tempting to use it even when it's a poor fit for the problem at hand. Just observe the stack of blog posts from a few years back bemoaning the choice to use MongoDB for inherently relational data. In that respect the book does a good job to emphasize that no one-size-fits-all solution exists and that most real-world systems are composed of multiple components --- databases, message queues, caches, batch processing frameworks, etc. --- all working in concert. Much of the difficulty in building the titular data-intensive applications is getting all these disparate pieces working together while guaranteeing integrity, fault tolerance, scalability, and so forth.

I found the last chapter in which the author proposes using an immutable event log as the single source of truth, with other databases and caches effectively materialized views derived from it, particularly compelling. It's that loose coupling from programming that we all love but applied to entire systems. Tasty food for thought.

If nothing else, *Designing Data-Intensive Applications* is an excellent springboard to further explore the topics it covers, with extensive links to relevant academic papers (which, let's be real, I won't read) and blog posts (some of which at least made it to my Instapaper queue). Two thumbs up, highly recommended.

---

[^1]: One of my toughest architecture challenges was building Lumos, a game analytics startup I founded when this sort of business seemed like a good idea. Hoo boy did we made poor choices early on that came back to bite us. Any time a game's popularity spiked our service's availability took a nosedive. We mitigated this problem after a major refactor, though the larger challenge of making money continued to elude us.
