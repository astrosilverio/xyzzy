thoughts: last log post *could* be a blog post maybe? too large in scope.

so here's where I'm at:
I'm taking some time to set up, because I'm done with feeling janky and unprofessional on this project
so far today I've:
* made a new repo for the blog
* added my work laptop ssh key to github so no password input
* moved business_rules method to a feature branch
* set up a global .gitignore

And now I'm looking into setting up multiple github profiles

Design decision: do I have *one* base stateful thing or split them up?

i.e. how strongly typed do I go?

Right now I have three base classes: Room, Player, Thing
idea is to... subclass them?
or I could just not type at all. have one Object class. that way things can talk and players can contain things, etc?

this is maybe the more pythonic architecture.

POTENTIAL RABBIT HOLES:
* learn about factories
* learn about git hooks # done
* setup dev vm for hogwarts
* modify Rachel's boilerplate repo to include git hooks # actually dunno if that's wise
* make git hooks work # done
* bash colors


Git Hooks

git repos come automatically stocked with some git hooks # investigated
Q: why do I want to delete .pyc's? they're not *actually* the devil
A: don't want them carrying over between branches. make it a post-checkout hook.

baby rabbit hole: http://stackoverflow.com/questions/9765453/gits-semi-secret-empty-tree

anyway, $number gives you that number argument in a bash script
stmt -a stmt is AND
stmt -o stmt is OR
; seems to be some sort of "execute this now"

hm maybe I have to do more setup -- not working even with simple if statement
simon says run an install script for venmo's, maybe I have to do that?

also I want bash colors so my symlinks are pretty

UGH ok I needed to make my script executable <- that fixed everything


