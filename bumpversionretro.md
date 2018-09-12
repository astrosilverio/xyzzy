# Things I have learned while writing `bumpversion`:
* I can do `sed` a little better, I think? would def benefit from exercises
* `git log ref1..ref2` gives you the commits between the two refs, including everything merged in, e.g. in this situation:

```
> git log --oneline --graph --all --decorate
*   3ad73ae (HEAD, origin/master, master) Merge branch 'try_this_again'
|\
| * cde58b2 (try_this_again) Remove threading until I use it properly
| * 637374f Decorate system methods to run callbacks before and after methods
* | 312da19 Bump version to 0.8.0
|/
* 962c808 WIP description system
* 2f86c98 rough cut at signals
* 9f148bb fix assemblage kwarg bug
```

if you wanted to get all commits between the version bump and HEAD:

```
> git log 312da19..HEAD --oneline
3ad73ae Merge branch 'try_this_again'
cde58b2 Remove threading until I use it properly
637374f Decorate system methods to run callbacks before and after methods
```

you'd get all the commits that were in the `try_this_again` branch before it was merged. Which is not actually all that surprising on second thought, but it surprised my brain.

* the `head` command prints the first lines or bytes of a file (`-n` for lines and `-c` for bytes)
* `${var#Pattern}` Remove from `$var` the shortest part of `$Pattern` that matches the front end of `$var`.
* `${var##Pattern}` Remove from `$var` the longest part of `$Pattern` that matches the front end of `$var`.

Hence, `${PWD##*/}` where `PWD` is "some/path/to/project" is `project`.

* `printf` exists in bash and [is better than echo??](https://unix.stackexchange.com/questions/65803/why-is-printf-better-than-echo/65819#65819)
* 