## Consulting services ##

If your company is interested in getting help leveraging Scala and functional programming, please [contact me](mailto:paul.chiusano@stage-n.com) about working together. Here's a broad summary of services I offer:

* Scala programming for hire, with an eye toward writing compositional, testable, and performant code.
* Functional design and implementation, at all levels, including system architecture, libraries and DSLs. I've been doing these things professionally for 6+ years.
* Scala and FP training and/or tech talks, tailored to the interests and experience of your team.
* Mentorship, code and design reviews, pair programming, and coaching, especially for growing Scala teams looking to make sure code quality stays high.
* Support for scalaz or scalaz-stream
* Advice and help with growing Scala capacity on your team. At Capital IQ I introduced Scala and functional programming to the organization and helped grow and train an amazing team of developers successfully applying FP to a variety of problems within the company.

### Tech talks and training sessions

If you're interested in taking better advantage of Scala and FP but aren't sure where to begin, I can offer short tech talks and training sessions tailored to the background and experience of your team. Here are a list of some possible ideas for topics. If any of these sound interesting or useful, [get in touch with me](mailto:paul.chiusano@stage-n.com) and we'll try to work something out.

* _Compositional stream processing and incremental I/O:_ The outer layers of an application that interact with the outside world often consists of messy imperative code which is difficult to test and which is tightly coupled to various external systems. A stream processing library like scalaz-stream reifies these interactions with the outside world as a first-class object, an effectful stream, which can then be manipulated using a rich set of operations (including many of the familiar operations defined for the Scala collections, such as map, filter, etc). This helps regain testability and modularity, and allows for surprising (and useful) separation between the core program logic and interactions with the outside world.
* _State machines as stream processing:_ Dealing with the real world often involves writing messy or complex state machines. (E.g. a node in a cluster may first accept a job request, which transitions it to a state where it expects a handshake message within 10 seconds, followed by a payload of job data, at which point it begins processing this data, with periodic checking of a cancellation signal). These sorts of interactions and protocols can be recast as stream processing, giving us a nice combinator library for assembling these behaviors, with the usual benefits of compositionality and testability.
* _Designing domain specific languages and their interpreters:_ Introductory talk on designing DSLs in Scala, using a worked example. This is more to introduce the basic vocabulary and concepts of DSL design. Shallow vs deep embeddings, GADT-style representations vs "finally tagless" representations, HOAS and other approaches to variable bindings.
* _Worked DSL / functional library design for a domain of your choosing:_ You tell me a little bit about some domain that is relevant to what you're doing, I prep some material and walk through designing a library for that domain.
* _Designing a serializable DSL for time series manipulation and numeric processing:_ Exploration of design space around a DSL for expressing streaming numeric and analytic computations, and its associated interpreter. Topics: representation of sliding window computations, multi-input computations without explicit tupling or zipping, multi-pass computations, grouping and aggregation, recovering sharing and minimizing passes, and distributed execution. Recommended prerequisites: Designing domain specific languages and their interpreters.
* Any chapters that your company is interested in from the book I am coauthoring, [Functional Programming in Scala](http://manning.com/bjarnason), could be turned into a session, and I can also do regular sessions, working sequentially through the book's content, tailored to the background and experience of members of the team.

This is not meant to be an exhaustive list, so if you have other ideas for topics, please let me know and I'll see what I can do.
