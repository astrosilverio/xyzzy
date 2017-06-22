# Testing in Python

## This is a long blog post. How is it organized?

My goal was to provide a resource that, if read through, would allow someone who had never written a test in Python before to write and run a test suite. To that end, I tried to address issues in the order I felt they would come up. However, you may want to follow a different order of questioning, or may not have need for particular sections. Therefore I have attempted to make them as independent as possible. For quick reference:

[1. Why write tests?](# No but really, should I?)

[2. Where should I write tests?](# Where should I write tests?)

[3. What tests should I write?](# What tests should I write?)

2. How to run tests?
	* `nose` is a great package for running tests. why is it better than unittest, idk but it's what I've always used.
	* also there's a lot of nose plugins that can do fun things, like tell you what % of your code is covered by your tests
4. How to structure tests?
	* I make my test modules mirror my code modules
	* One test class per code ...either object, if your object is small enough, or behavior...?
	* One test case per concept
6. How do you write a single test?
	* Arrange-Action-Assert
	* Name it something super specific
	* Should only test ONE THING
	* Watch it fail first. Sometimes tests pass for unexpected reasons! http://mathamy.com/tag/testing.html
	* assertEqual(actual, expected) more performant than assertTrue(some expression to be evaluated, expected)
	* Try to test behaviors rather than implementation
	* assertEquals is deprecated in py3
7. Vocab?
8. How to write a test class?
9. What about TDD?
10. Mocks?!??
11. More resources
	* http://docs.python-guide.org/en/latest/writing/tests/

## So, should I even write tests?

Yes.

## No but really, should I?

Yes.

Beyond validating that your code behaves the way you expect it to, tests:

* Are an investment in the future of your project, because they make it easy to check the impact of new features on existing code.
* Can give you insight into your design choices. Frequently, the code that you write in tests is similar to code you would write in your application, so if it is difficult to write tests, then perhaps the code you are testing could be refactored to be more usable.
* Reading tests is a good way for people new to a project to familiarize themselves with it. If you have a test suite, someone looking to understand your code can read your tests to learn what its expected behavior is and what potential edge cases are. That someone could even be you, returning to a project after a significant time away!

The argument against writing tests that I hear most often is "My code isn't complicated enough for tests, anyone can read it and understand it, and any failure points will be obvious and easy to find." If you tell yourself this, take a moment to remind yourself not to make assumptions about what other people should be able to understand by visual inspection, especially if you're an experienced developer. And if you are new to Python, considering writing a test anyway, just for the experience.

## Where should I write tests?

There are a couple different solutions to this question, and which one is right for you is dependent on how you've organized your project. In most situations, the standard practice is to make a directory called `tests/` at the top level of your project. Within the `tests/` directory, I usually make a `test_<module_name>.py` for every module of code in the project. If your project is set up like a typical Python package, it might look something like this:

```
my_project/  # top level
    my_project/  # code goes here
        __init__.py  # important for python
        module_one.py  # code in here
        module_two.py  # more code in here
    tests/  # tests go here
        __init__.py  # important for python
        test_module_one.py  # test module one code in here
        test_module_two.py  # test module two code in here
```

Note: The Python Packaging User Guide [suggests](https://python-packaging.readthedocs.io/en/latest/testing.html) putting the `tests/` directory under the inner `my_project/` directory; however a few big-name open source projects I've looked at, as well as the Python Packaging User Guide's sample package, have tests at the top level. /shrug Choose whichever you like!

## What tests should I write?

This question can be surprisingly sticky to answer. There are two main classifications of test:

1. integration tests, which test start-to-finish flows, guaranteeing that a particular user input results in a particular user output;
2. unit tests, which test individual components, guaranteeing that a particular part of your code behaves in the desired way.

For example, perhaps you would like to simulate feeding a cat. You refill your cat's food bowl every day at 10PM.



If you wanted to write integration tests for this scenario, you would probably write a test that asserts that if the cat's food bowl is empty and you refill it fully, the cat will purr. You would write another test that asserts that if you empty the food bowl, the cat meows.

If you wanted to write unit tests, on the other hand, you would write a test that asserts that if you put food in the bowl, the food level in the bowl increases. You would also write a test that if the food level in the bowl is above a certain threshold, the cat will purr. The difference is that in the unit test situation, you are treating your behavior and the cat's behavior as totally separate.

Of course, there can be varying levels of integration testing and unit testing, depending on how you define a component and how many components a test will execute.

You'll probably come across passionate advocates for each kind of test. Personally, I like to write both. I need to know that each component of my project works as I expect, so I can quickly pinpoint failures. However, I accept that it is possible to write beautiful components that pass all their individual tests, but don't work together (due to API inconsistencies, perhaps--maybe, in the cat example, your bowl-measuring system or sense of time is different from your cat's). Therefore, I write thorough unit tests for my modules and a few integration tests to make sure that the project works.

## Getting started with unittest

Hopefully you have a place to write tests and thoughts about what tests you have to write. Getting from here to tests that requires (or at least is made much, much easier) by using a testing framework. Testing frameworks provide many services, including automated execution of tests (you type a command and your tests run!), granular setup before each test runs (so you can guarantee that each test starts to run in a specified environment), and granular teardown before each test runs (so you can guarantee that any state changes made in one test will not impact the next test to run).`unittest` is a widely used Python testing framework and we'll use that for convenience, althought if it is not intuitive for you there are many other testing frameworks, like pytest or doctest.

### Package Setup

For example, I try to structure most of my Python projects as packages, so that I can ultimately distribute them. There is [a thorough guide](https://packaging.python.org/distributing/) for how to structure a Python package; the short version is that the basic structure looks something like this:

```
my_project/  # top level directory of package
    various config files,  including setup.py
    my_project/  # directory containing code
```
