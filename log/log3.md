https://www.python.org/doc/essays/list2str/
    Thoughts about speed.
    Implied loops = Python built-ins that do loops in C, like map and reduce.
    Implied loops are faster than loops in Python.
    Loading globals takes a while. Fastest ways to do things involve using C and locals.

http://www.jeffknupp.com/blog/2013/02/14/drastically-improve-your-python-understanding-pythons-execution-model/
    This is a really great article in general, but mostly served to uncover an error in my understanding of how Python works
    Mutability -- when you concatenate strings, you're actually binding to a new object (slow)
    ^ affects behavior of copies.
    ```
    a = 'hello'
    b = a
    a += ' world' # <-- binds the name a to a new object 'hello world'
    print a # returns 'hello world'
    print b # returns 'hello' because b is still pointed to 'hello'

    a = ['hello', 'world']
    b = a
    a[0] = 'goodbye' # here we're actually changing the object that a points to
    print a # returns ['goodbye', 'world']
    print b # returns ['goodbye', 'world']
    ```

    I used to assume that Python 'by default' made ___ copies, where ___ = deep or shallow depending on what type of object you were copying. I usually only thought about default copies of one object type at a time, and so was never really perturbed that it was deep copies of mutable objects and shallow copies of immutable objects.

    I suppose that you could argue that the above statement accurately represents the observed behavior. Describing binding simply in terms of deep/shallow copying is shortsighted, however. Python's doing the same thing for all copies -- binding the new name to the object that the original name is bound to at time of copy. It's not really copying in the strict sense of the word.

    There's also some good stuff about scope and objects which was mostly old news to me, but well summarized.

Things about patches:
    You have to be careful about the namespace in which you patch
    

