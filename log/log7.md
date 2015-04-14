Looked at Django source code for transaction inspiration, didn't really find any.

New plan: write some code, send it out for review.

Watching Maniac Mansion postmortem. Feature idea: command that shows you the names of everything in the room currently, lets you rename things.

Flow testing -- is there a way to do it?

ok, so transactions, maybe I can go back to Django again, but in a very brief sense:
* `transaction,commit_manually` opens a connection to the db, without with no writes can happen
* then you muddle with the db
* `commit` or `save` or whatever saves the changes and closes connection
* `rollback` returns previous stable version

Can I recreate this without Django?

ok, here's what I really want: change the state, and then save everything together if everything looks right
so I want a memory-version of the state, and then some committed db version

yep that's a transaction -- make the whole shebang succeed or fail together
I could learn about that or I could write some code
https://realpython.com/blog/python/transaction-management-with-django-1-6/

