# RFC: A new Python package release managing tool

## Problem

I'm currently irritated by my process (or lack thereof) for releasing new versions of Python projects. As far as I can tell, the process for releasing a new version of a Python package should be something like this:

1. If you store the version somewhere in your project (I do, so people can check the package version in Python), commit the version change as a separate commit (at least that's what Django seems to do);
2. QA the build. This step is separate from "running tests" because there are things that I've botched re: builds that don't surface in tests. Most commonly, I forget to add a new code object to the toplevel `__init__.py`. I ~~could~~ should probably write linters or automated tests to catch these problems, but it seems more prudent to just always QA the build. The easiest way of doing that is by pip installing the package from your local version with `pip install -e .` (assuming you are building from the package dir).;
3. Push that commit and tag it on Github (I don't know the difference between GH's `Releases` and `Tags`, but the version change commit should be tagged as one of those;
4. Create a distribution and upload that to PyPI by running `python setup.py sdist upload` for a source distribution or `python setup.py bdist upload` for a built distribution.

I have problems with this process because:

* It's very manual
* Sometimes I forget steps 1, 2, or 3. If I forget to commit the version number change, my build for version X of a project claims to be version Y instead.
* QAing is the hardest step for me to remember, which has resulted in me uploading a bunch of bad builds to PyPI. And PyPI doesn't let you replace a build once you've uploaded it, for good reason, which essentially has meant that I've burned through version numbers on more than one personal project.
* I also frequently forget to tag the release on Github, which means if someone is looking at my project on Github, they won't be able to easily tell what the code looked like at a particular release.
* **_EXTREMELY_** frequently, I have large gaps between sessions working on a project, which means I forget if I had released the changes I made in the last session. And because I tend to forget tagging releases on GH, making separate commit messages, etc, it takes more effort than I'd like to reconstruct what my status is in terms of when the project was last released.

This problem seems like a prime target for tooling. If such tooling exists I really want to know about it. As of now, I don't, and I would like it to exist, so:

## Proposal

I'd like to write a tool for managing my release process. There will be a command that changes and commits the version number in your project, if you have one, a command that wraps all of the publishing steps together (tagging a release on GH and publishing to PyPI), a command to rollback your version changes, and most importantly, a command that summarizes the release status of the project. Git is already part of my workflow, so I'm imagining some details of the tool as [being similar to Git][Concerns]--for example, there might be some pre-publish hooks that I can optionally use to disallow me from publishing a build if I haven't QAed it.

I've already written a small bash script that bumps the version number of a python project and named it, creatively, `bumpversion`, so that's the name I'm using below for the whole tool.

## Commands

### status
* gets current version number
* gets latest published version
* gets commits added since last release
* (perhaps?) checks if you have built your project locally recently?

### bump [-m, --major] [-n, --minor], [-b, --bugfix], [--alpha], [--beta]
* updates version number
* commits that change
* mostly already exists (sans git integration) [here](https://gist.github.com/astrosilverio/57ddef2486dd7c073b53)

### build
* for python packages, runs `pip install -e <path to package, usually .>`
* may be overkill?
* maybe sets a flag somewhere that you've QA'ed the build
* MAYBE runs some automated tests? compares your new build to your old build and complains if there are functionally no changes?

### rollback
* resets to last release, meaning reverts version change commit
* can't decide if it should squash version change commit + revert commit or not
* untags on GH if you've already managed to do that (by not using the `publish` command lol)

### publish
* tags version change commit as a release on GH
* runs `python setup.py sdist upload`
* (perhaps?) has optional pre-publish hooks that bug you if you haven't run `build` since your last `bump`?

I want all those steps in one command because I never want to push a version change commit without tagging it as a release; I never want to publish to PyPI without tagging the release on GH; and I think it would be a little weird to tag a release on GH that is not published on PyPI. All of these steps feel connected, and to me constitute a complete release.

Update: TRAVIS-CI DOES THIS. Maybe I should not.

## Usage

```
> bumpversion status
Last published version: 1.0.0

Commits since last published version:
    d5e6f7g Rearranges directory structure a bit
    1a2b3c4 Adds CoolNewFeature

Current local version: 1.0.0

# hmmm CoolNewFeature is pretty cool, I should cut a new release
# It is a new feature that doesn't break existing functionality or fix a bug, which makes it a minor bump
# (as opposed to major or bugfix)

> bumpversion bump -n  # for minor, -m would be major and -b bugfix
Local version bumped to 1.1.0
Last published version: 1.0.0

> git log
    h8i9j10 Changes version to 1.1.0
    d5e6f7g Rearranges directory structure a bit
    1a2b3c4 Adds CoolNewFeature

# hmm I should QA this before pushing to PyPI

> bumpversion build

# test a bit
# whoops I forgot to add CoolNewFeature to __init__.py! don't wanna publish this

> bumpversion rollback

# commit fix

> git log
    13n14o1 Adds CoolNewFeature stuff to __init__, this commit should be rebased out probably
    k11l12m Revert "Changes version to 1.1.0"
    h8i9j10 Changes version to 1.1.0
    d5e6f7g Rearranges directory structure a bit
    1a2b3c4 Adds CoolNewFeature

> bumpversion bump -n
Local version bumped to 1.1.0
Last published version: 1.0.0

> bumpversion build

# QA again, yay everything is fine

> bumpversion publish

# go check GH, oh hey there's a new tagged release
# go check PyPI, oh hey there's my build

> bumpversion status
Last published version: 1.1.0

Commits since last published version:
    none

Current local version: 1.1.0
```

## Assumptions
* user is using git for version control
* only python packaging flow for now. This might be easily extendable if other languages share a similar release flow.

## Concerns

* When I started designing this flow, I was thinking about it in terms of Git. Git is a notoriously bad interface. Patterning new tooling on Git seems like a poor design choice. However, the worst part of git to me is the naming of the commands and the fact that you can do the same thing with multiple commands, and that many commands can do vastly different things. I'm intentionally designing this API without those particular antipatterns. Maybe that makes it all better?
* I really find it hard to believe that no one else has done this, and while I'm usually happy to write tools for the sake of writing tools, this is not the hill I want to die on [(the hill exists, and is this project)](https://github.com/astrosilverio/hogwarts). tl;dr I won't rewrite this if it already exists.