UGH more hacking / stabbing. Adding some more parsing to Legilimens, going to finally start tackling commands.

Some things that are bothering me: Maybe I haven't hit this point yet, but soon I'll have to start thinking about fixtures for testing.

Stuff not in log4: add a pre-commit to get rid of pdbs.

OH SNAP yeah eventually I'll need some sort of dev vm, once the basic engine works right.
# next level

AND I'll want a special way to write automated tests for your game. Maybe make it kinda like rspec, in the sense that the tests should read like text almost.

Now is not the time for this, but maybe I'll need to do something like our ApiMessages to interact smoothly with a front-end? meow meow

JULIA IS AWESOME

LinkedIn tech blog is a thing, they have an open source project called Kafka that's kinda like a queue that you can use for event storage. Better for distributed systems.

Talking about db transaction analogy and rollbacks in state changes. I want to easily abort a state change if anything goes wrong. Like how with django, you can make atomic transactions and roll them back if anything goes wrong. How do I do that with my project? Thinking about returning to my idea of AHistoryOfMagic, except slightly different -- record all state changes somewhere. Then if something goes wrong, you can rebuild from initial state.