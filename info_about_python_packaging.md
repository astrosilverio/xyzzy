# A Reference for Python Packaging

### A small caveat

I started writing this document as a reference for my own personal use, because I reached a stage where I had managed to internalize some concepts about Python packaging, like the general directory structure, but repeatedly forgot other key concepts, like the fact that you can build a package locally to test your build. While researching some terminology, I stumbled on https://packaging.python.org/. I had seen it before but somehow failed to internalize that it contains [an excellent guide for distributing projects](https://packaging.python.org/tutorials/distributing-packages/) that covers the vast majority of what I'll explain below. 


## Vocabulary, Part 1

* _package_: from Python's perspective, a package is just any directory that contains a file named `__init__.py`, which from a practical perspective allows you to namespace modules under the name of the directory. If you're reading this document, however, you're probably specifically interested in publicly downloadable packages. For the remainder of this document, I'll use the word package specifically for Python code that can be uploaded to PyPI[^PyPI_def] and downloaded with pip[^pip_def] or easy_install[^easyinstalldef] and run on other people's computers.

## General Directory Structure, Part 1

Most packages (without tests, tsk tsk) at their core look something like this:

```
braga/
    braga/
        __init__.py
        very_important_module.py
```
That's because Python packages are, at their core, directories containing Python code. When you install a Python package, that directory gets added to your Python path. That means that the code that lives in modules and subdirectories of the installed package is now importable. Say `very_important_module.py` contains a function called `my_function`. To import `my_function`, you'd use `from braga.very_important_module import my_function`.

There's no reason why the outer directory, which should be the root directory of the repository, has to share the same name as the inner directory, or code package. However, it seems a little rude to make your users `pip install braga` and then in their python code `import notbraga`. It's entirely possible to encase two products in the same repo, in which case you'd end up with at least one inner directory that does not share the outer directory's name, but other than that I don't see any reason for flaunting this particular convention.

In terms of where to put tests, I've read arguments suggesting [they should go at top level](http://docs.python-guide.org/en/latest/writing/structure/#test-suite) and that [they should go in the code package](https://coderwall.com/p/lt2kew/python-creating-your-project-structure). I've come to lean towards nesting test directories so that all Python code-containing top-level directories are something that an end user would want to import into their code. Whatever you choose, the most important thing is probably to do that consistently, so that anyone installing one of your packages knows where to go to find the tests ([do as I say](https://github.com/astrosilverio/hogwarts), [not as I do](https://github.com/astrosilverio/braga)).

I usually try import code that I expect users to import into the `__init__.py` in the code package. In the example above, I'd import `my_function` in `braga/braga/__init__.py` so that importing my function would be cleaner: `from braga import my_function`. Besides saving my users some typed characters, this practice makes the `__init__.py` a sort of unofficial document of the project's public API: everything in the init is designed to be public, and if it's not in the init, maybe you should think twice about importing it.

For more information, [this introductory tutorial](https://python-packaging.readthedocs.io/en/latest/index.html) has a good, thorough example of directory structure for a package.

## Vocabulary, Part 2:

* _library_: a project that is intended to be imported and used by other projects. Examples: django, flask, requests, braga (shameless plug)
* _application_: a project that is *not* intended to be imported and used by other projects (it is deployed somewhere and those installing it are end users). Examples: ipython

## Difference in use cases between specifying dependencies in `setup.py` and `requirements.txt`

Specifically, `setup.py` lets you specify packages that yours depends on, without specifying a version or source for them[^dist url]. Any version of those packages can be used, from any source (including forks or local versions). Using `requirements.txt`, on the other hand, pins the version and source of the dependency.

Practically, these two ways of specifying dependencies correspond to different use cases. Pinned versions and sources are useful when you are building an application that you deploy and is not depended on, where your main concern is that the app runs and therefore using only tried and tested versions is preferable. However, if your project is a library designed to be imported and developed with, having pinned versions can be cumbersome, e.g. if a user needs to fix a bug in or add a feature to one of your library's dependencies for their project to work, pinning your requirement will prevent them from using their desired version.

A more eloquent discussion can be found [here](https://caremad.io/posts/2013/07/setup-vs-requirement/).

## Vocabulary, Part 3:

* _semver_: short for semantic versioning, semver is a standard for version numbering in which version changes convey meaning about changes between releases. Semver version numbers are of the form A.B.C, where A, B, and C are all ints. A is the major version, B is the minor version, and C is the patch version. Full spec of the semver standard can be found [here](http://semver.org).

## When to Bump Versions

Not all commits to your package need a version change, but any time you wish to publish a release, you need to bump your version. The semver standard, in brief, dictates that you should: increment the patch version if the code changes since the last release fix a bug without introducing new functionality; increment the minor version if you're introducing new functionality that is backwards compatible; and increment the major version if you're releasing a breaking change. That's all pretty intuitive for mature projects, but the problem that I and my young projects run into is that almost every version changes the public API. The solution to that problem is to [bump the minor version for every release when your major version is 0](http://semver.org/#how-should-i-deal-with-revisions-in-the-0yz-initial-development-phase) (unless it's a bug fix). [Only go to 1.0.0 when you are ready for your code to be used in production](http://semver.org/#how-do-i-know-when-to-release-100).

## Develop Mode to QA New Releases

`pip install -e <path to home dir of project>` builds a project **locally** so you don't have to upload a distribution to PyPI. You will frequently make builds that don't work, and PyPI won't let you delete and reupload a build for a version, so you'll burn through version numbers really quickly if you upload your builds before checking if they work.

## Differences between source distributions and built distributions

## Actually, terminology in general

## My Suggested Workflow

## Open Questions

Is there a standard procedure for commit version changes, tagging releases on Github, and uploading a dist to PyPI? Right now I do all of this manually, usually by:

1. Commiting the version change in its own commit, with message something like "Bump version to <blah>";
2. Pushing the version change commit to GH and making a tag at that commit;
3. Uploading to PyPI.

This process is more manual than I'd like. That's easily fixable, however. I already wrote [a little tool](https://gist.github.com/astrosilverio/57ddef2486dd7c073b53) so that I can edit version numbers from the command line, and I could extend it so that the entire process I described above is automated.

[^PyPI_def]: PyPI is a repository (but not in the git sense of the word) to which you can upload Python software and from which you can install Python software.
[^pip_def]: pip is a package manager for Python.
[^easyinstalldef]: Another, less used package manager for Python.
[^dist url]: You _can_ specify urls in the `setup` command via the `dependency_links` kwarg, but it is optional behavior compared to the intrinsic url specification in `requirements.txt`.