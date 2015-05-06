Title: About tests
Date: 2015-05-06 10:20
Category: Talk
Tags: shinken, tests, project
Slug: about-tests
Author: Jean Gabès
AuthorLogin: naparuba
Summary: Quick look about how tests did evoled since the project start ^^


Hi :)


I'm starting a blog post serie about various stats. Let's start with one topic that remove lot of stress in the release: tests :D

## Tests: a long history

Tests are not very fun, but they are so useful that no-one should avoid them

When Shinken code base was less than 5K lines (late 2009), we did not have any tests, it was a hobby project without even a name, and I did launch the daemon to look if all was OK.

But at this time [Gerhard](https://github.com/lausser) did find a regression on the state logic, and it was the automatic test startup :)

So if you start a project, remember that after some few Kilo lines (or even before), you will need tests cases, and it's always harder to add them after coding, so maybe it's better to add them from the begining ;)

I take a little time to thanks again Gerhard for this, because without the test framework, the project should have been full of bugs and not as powerful :)

## Evolution

But how the number of tests is evolving when the project gets bigger and bigger? Do they increase as the same rythme than the code size? Let's look at this :)

I did look at all our previous release since 2010 and count code line, and test lines ones. 

(Note: I did remove the modules code/test line count in the pre-2.0 versions)

<center><img src='/images/about-tests/ratio.png'></center>

There are some period in the project:

  * between 0.1 and 0.5: the tests did increase a lot compared to the code size. 0.6 did saw a refactoring pass
  * between 0.8 and 1.0: it's a "go production stabilization" pass, so lot of new tests to be sure we have everything under control for the production world ^^
  * since the 1.2      : we are still increasing the code base and test, but at the same rythm

The tests code did saw some refactoring phase so they are still maintenable. Thanks a lot to [Gregory Stark](https://github.com/gst) & [Seb](https://github.com/Seb-Solon) for the last refactoring, that allow to easily provide a test framework for the modules too :)

So in the end, it's quite classic, with more and more tests until everyone is used to add tests with the new features or when we find bugs. We did allow some test-less features in the past, but since the 2.0 PRs should be provided with tests (look at [dev rules](https://shinken.readthedocs.org/en/latest/15_development/hackingcode.html#development-rules) for more about it).

Now tests are by default. And thanks to them we can refactor or add new features without fearing regressions :)

Our 60% ratio seems to be a great, and allow us to reach a great stability.

And you, what is the test ratio of your favorite project? Are the higer value the better? Feel free to share your experience in the comments :)

(next article: about ok/critical service state ratio in the real world ^^)


