---
layout: lesson
root: ../../..
title: Testing
---

**Based on materials by Mike Jackson, Katy Huff, Rachel Slaybaugh, and Anthony Scopatz. With special thanks to Gordon Webster, the [Digital Biologist](http://www.digitalbiologist.com), for kindly allowing use of his [Python DNA function](http://www.digitalbiologist.com/2011/04/code-tutorial-getting-started-with-python.html).**

## Some questions for you

Let's open with some questions. How many of you test your code or scripts...

* With a set of input data and check that this gives an expected set of output data?
* With multiple sets of input data?
* With input data you know to be incorrect and check that your code or scripts behave appropriately e.g. giving a warning, or exiting, or anything that doesn't involve producing output data that *seems* OK but is not?
* After every change you've made to fix a bug or optimise your code or to add a new feature?
* Using some form of automation e.g. a set of testing scripts or a test framework?

## What is testing?

Software testing is exercising and confirming expected behaviours and results from code. It allows us to check that,

* Our code behaves as expected and produces valid output data given valid input data.
* Our code does this using *any* set of valid input data.
* Our code fails gracefully if given invalid input data - it does not just crash or behave mysteriously or unpredictably but, for example, exits politely with a message as to why the input data is invalid.
* Our code can handle extreme boundaries of input domains, output ranges, parametric combinations or any other edge cases.
* Our code's existing behaviour is still the same after we've changed it (this is called *regression testing*).

It also gives us the confidence to:

* Add new features.
* Optimise our code.
* Parallelise our code.
* Fix bugs.

...all without introducing bugs. Nothing is worse than fixing a bug only to introduce a new one.

Tests also help us remember what all the parts of our code does. If we are working on a large project over three years and end up with 100s of functions, it may be hard to remember what each function does in detail. If we have a test that checks all of the function's functionality, we can look at the test to remember what it's supposed to do.

## Why isn't testing done?

Do any of these sound familiar?

* "I don't write buggy code", well, we are naturally very protective of our work and may refuse to accept that our code has bugs. Unfortunately, almost all code has bugs.
* "It's too hard", but, if it's hard to write a test for some code, then this is a good sign that the code is not well designed.
* "It's not interesting", sometimes, testing is just viewed as not being interesting which means...
* "It takes too much time and I've research to do"

And, this is a fair point. So, why should we care about testing?

## Why we should do testing?

Testing allows us, and others, to trust our code and trust it enough to answer in the affirmative to at least a few of the following questions:

* Does your code work?
* Always?
* Does it do what we think it does?
* Does it continue to work after changes are made, for example optimisations or bug fixes?
* Does it continue to work after system configurations or libraries are upgraded?
* Does it respond properly for a full range of input parameters?
* Does it handle about edge or corner cases?

As a cautionary tale, consider Ariane 5 which used Ariane 4 software. Ariane 5 had new and improved engines which caused the code to produce a buffer overflow...and Ariane 5 blew up! So, some forgotten tests led to millions of pounds down the drain and some very red faces.

Or, consider [Geoffrey Chang](http://en.wikipedia.org/wiki/Geoffrey_Chang) who had to [retract](http://www.sciencemag.org/content/314/5807/1875.2.long) 3 papers from [Science](http://www.sciencemag.org), due to a flipped sign! Or, McKitrick and Michaels' [Climate Research 26(2) 2004](http://www.int-res.com/abstracts/cr/v26/n2/p159-173/) paper, which drew the attention of a blogger Tim Lambert who noted a [problem](http://crookedtimber.org/2004/08/25/mckitrick-mucks-it-up/) which led to their subsequent [erratum](http://www.int-res.com/articles/cr2004/27/c027p265.pdf).

Do this too regularly and people may not trust our research, which could affect our chances for collaborations, publications or funding.

But if this is not compelling, then, if nothing else, writing tests is an investment in time that saves us time in future,

* We can automate the checking of outputs from our software to ensure they're valid.
* We can detect more quickly whether refactoring, optimisation or parallelisation has introduced bugs.
* We can run our tests while doing other, more interesting, things.

## Fixing things before we test...

Before we test our code, it can be very productive to get a colleague to look at it for us...why?

> **What we know about software development - code reviews work** 

> Fagan (1976) discovered that a rigorous inspection can remove 60-90% of errors before the first test is run. 
> M.E., Fagan (1976). [Design and Code inspections to reduce errors in program development](http://www.mfagan.com/pdfs/ibmfagan.pdf). IBM Systems Journal 15 (3): pp. 182-211.

> **What we know about software development - code reviews should be about 60 minutes long** 

> Cohen (2006) discovered that all the value of a code review comes within the first hour, after which reviewers can become exhausted and the issues they find become ever more trivial.
> J. Cohen (2006). [Best Kept Secrets of Peer Code Review](http://smartbear.com/SmartBear/media/pdfs/best-kept-secrets-of-peer-code-review.pdf). SmartBear, 2006. ISBN-10: 1599160676. ISBN-13: 978-1599160672.

## Contents

* [Let's start writing some tests](#Writing)
* [Testing in practice](#RealWorld)
* [Test-driven development](#TDD)
* [Conclusions and further information](#Conclusion)


<a name="Writing"></a>
## Let's start writing some tests

In the file [dna.py](python/dna/dna.py) we have a Python dictionary that stores the molecular weights of the 4 standard DNA nucleotides, A, T, C and G, 

    NUCLEOTIDES = {'A':131.2, 'T':304.2, 'C':289.2, 'G':329.2}

and a Python function that takes a DNA sequence as input and returns its molecular weight, which is the sum of the weights for each nucelotide in the sequence,
 
    def calculate_weight(sequence):
        """
        Calculate the molecular weight of a DNA sequence.
        @param sequence: DNA sequence expressed as an upper-case string. 
        @return molecular weight.
        """
        weight = 0.0
        for ch in sequence:
            weight += NUCLEOTIDES[ch]
        return weight

We can calculate the molecular weight of a sequence by,
 
    weight = calculate_weight('GATGCTGTGGATAA')
    print weight

We can add a test to our code as follows,

    def calculate_weight(sequence):
        """
        Calculate the molecular weight of a DNA sequence.

        @param sequence: DNA sequence expressed as an upper-case string.
        @return molecular weight.
        """
        weight = 0.0
        try:
            for ch in sequence:
                weight += NUCLEOTIDES[ch]
            return weight
        except TypeError:
            print 'The input is not a sequence e.g. a string or list'

If the input is not a string, or a list of characters then the `for...in` statement will *raise an exception* which is *caught* by the `except` block. For example,

    print calculate_weight(123)

This is a *runtime test*. It alerts the user to exceptional behavior in the code. Often, exceptions are related to functions that depend on input that is unknown at compile time. Such tests make our code robust and allows our code to behave gracefully - they anticipate problematic values and handle them.

Often, we want to pass such errors to other points in our program rather than just print a message and continue. So, for example we could do,

    except TypeError:
        raise ValueError('The input is not a sequence e.g. a string or list')

which raises a new exception, with a more meaningful message. If writing a complex application, our user interface could then present this to the user e.g. as a dialog box.

Runtime tests don't test our functions behaviour or whether it's implemented correctly. So, we can add some tests,

    print "A is ", calculate_weight('A')
    print "G is ", calculate_weight('G')
    print "GA is ", calculate_weight('GA')

But we'd have to visually inspect the results to see they are as expected. So, let's have the computer do that for us and make our lives easier, and save us time in checking,

    assert calculate_weight('A') == 131.2
    assert calculate_weight('G') == 329.2
    assert calculate_weight('GA') == 460.4

`assert` checks whether a condition is true and, if not, raises an exception.

We explicitly list the expected weights in each statement. But, by doing this there is a risk that we mistype one. A good design principle is to define constant values in one place only. As we already have defined them in `nucleotides` we can just refer to that,

    assert calculate_weight('A') == NUCLEOTIDES['A']
    assert calculate_weight('G') == NUCLEOTIDES['G']
    assert calculate_weight('GA') == NUCLEOTIDES['G'] + NUCLEOTIDES['A']

But this isn't very modular, and modularity is a good design principle, so let's define some test functions,

    def test_a():
        assert calculate_weight('A') == NUCLEOTIDES['A']
    def test_g():
        assert calculate_weight('G') == NUCLEOTIDES['G']
    def test_ga():
        assert calculate_weight('GA') == NUCLEOTIDES['G'] + NUCLEOTIDES['A']

    test_a()
    test_g()
    test_ga()

And, rather than have our tests and code in the same file, let's separate them out. So, let's create

    $ nano test_dna.py

Now, our function and nucleotides data are in `dna.py` and we want to refer to them in `test_dna.py` file, we need to *import* them. We can do this as,

    from dna import calculate_weight
    from dna import NUCLEOTIDES

Then we can add all our test functions and function calls to this file. And run the tests,

    $ python test_dna.py

## `nose` - a Python test framework

`nose` is a test framework for Python that will automatically find, run and report on tests written in Python. It is an example of what has been termed an *[xUnit test framework](http://en.wikipedia.org/wiki/XUnit)*, perhaps the most famous being JUnit for Java.

To use `nose`, we write test functions, as we've been doing, with the prefix `test_` and put these in files, likewise prefixed by `test_`. The prefixes `Test-`, `Test_` and `test-` can also be used.

Typically, a test function,

* Sets up some inputs and the associated expected outputs. The expected outputs might be a single number, a range of numbers, some text, a file, a set of files, or whatever.
* Runs the function or component being tested on the inputs to get some actual outputs.
* Checks that the actual outputs match the expected outputs. We use assertions as part of this checking. We can check both that conditions hold and that conditions do not hold.

So, we could rewrite `test_a`, as the more, verbose, but equivalent,

    def test_a():
        expected = NUCLEOTIDES['A']
        actual = calculate_weight('A')                     
        assert expected == actual

Python `assert` allows us to check,

    assert should_be_true()
    assert not should_not_be_true()

`nose` defines additional functions which can be used to check for a rich range of conditions e.g..

    from nose.tools import *

    assert_equal(a, b)
    assert_almost_equal(a, b, 3)
    assert_true(a)
    assert_false(a)
    assert_raises(exception, func, *args, **kwargs)
    ...

`assert_raises` is used for where we want to test that an exception is raised if, for example, we give a function a bad input.

To run `nose` for our tests, we can do,

    $ nosetests test_dna.py

Each `.` corresponds to a successful test. And to prove `nose` is finding our tests, let's remove the function calls from `test_dna.py` and try again,

    $ nosetests test_dna.py

nosetests can output an "xUnit" test report,

    $ nosetests --with-xunit test_dna.py
    $ cat nosetests.xml

This is a standard format that that is supported by a number of xUnit frameworks which can then be converted to HTML and presented online. 

## Write some more tests

Let's spend a few minutes coming up with some more tests for `calculate_weight`. Consider,

* What haven't we tested for so far? 
* Have we covered all the nucleotides? 
* Have we covered all the types of string we can expect? 
* In addition to test functions, other types of runtime test could we add to `calculate_weight`?

Examples of tests we could add include,

* `calculate_weight('T')`
* `calculate_weight('C')`
* `calculate_weight('TC')`
* `calculate_weight(123)` 

The latter requires us to check whether an exception was raised which we can do as follows:

    try:
        calculate_weight(123) 
        assert False
    except ValueError:
        assert True

This is like catching a runtime error. If an exception is raised then our test passes (`assert True`), else if no exception is raised, it fails. Alternatively, we can use `assert_raises` from `nose`,

    from nose.tools import assert_raises

    def test_123():
        assert_raises(ValueError, calculate_weight, 123)

The assert fails if the named exception is *not* raised.

One other test we could do is `calculate_weight('GATCX')` for which we can add another runtime test,

        ...
    except KeyError:
        raise ValueError('The input is not a sequence of G,T,C,A')

<a name="RealWorld"> </a>
## Testing in practice

The example we've looked at is based on one function. Suppose we have a complex legacy code of 10000s of lines and which takes many input files and produces many output files. Exactly the same approach can be used as above - we run our code on a set of input files and check whether the output files match what you'd expect. For example, we could,

* Run the code on a set of inputs.
* Save the outputs.
* Refactor the code e.g. to optimise it or parallelise it.
* Run the code on the inputs.
* Check that the outputs match the saved outputs. 

This was the approach taken by EPCC and the Colon Cancer Genetics Group (CCGG) of the MRC Human Genetics Unit at the Western General as part of an [Oncology](http://www.edikt.org/edikt2/OncologyActivity) project to optimise and parallelise a FORTRAN genetics code.

The [Muon Ion Cooling Experiment](http://www.mice.iit.edu/) (MICE) have a large number of tests written in Python. They use [Jenkins](), a *continuous integration server* to build their code and trigger the running of the tests which are then [published online](https://micewww.pp.rl.ac.uk/tab/show/maus).

## When 1 + 1 = 2.0000001

Computers don't do floating point arithmetic too well. This can make simple tests for the equality of two floating point values problematic due to imprecision in the values being compared. 

    $ python
    >>> expected = 1 + 1 
    >>> actual = 2.0000001
    >>> assert expected == actual

We can get round this by comparing to within a given threshold, or delta, for example we may consider *expected* and *actual* to be equal if *expected - actual < 0.000000000001*.

Test frameworks such as `nose`, often provide functions to handle this for us. For example, to test that 2 numbers are equal when rounded to a given number of decimal places,

    $ python
    >>> from nose.tools import assert_almost_equal
    >>> assert_almost_equal(expected, actual, 0)
    >>> assert_almost_equal(expected, actual, 1)
    >>> assert_almost_equal(expected, actual, 3)
    >>> assert_almost_equal(expected, actual, 6)
    >>> assert_almost_equal(expected, actual, 7)
    ...
    AssertionError: 2 != 2.0000000999999998 within 7 places

What do we consider to be a suitable threshold for equality? That is application-specific - for some domains we might be happy to round to the nearest whole number, for others we may want to be far, far more accurate.

## When should we test?

We should test,

* Always!
* Early, and not wait till after we've used it to generate data for our important paper, or given it to someone else to use.
* Often, so that we know that any changes we've made to our code, or to things that our code needs (e.g. libraries, configuration files etc.) haven't introduced any bugs.

But, when should we finish writing tests? How much is enough? 

> **What we know about software development - we can't test everything**

> "It is nearly impossible to test software at the level of 100 percent of its logic paths", fact 32 in R. L. Glass (2002) [Facts and Fallacies of Software Engineering](http://www.amazon.com/Facts-Fallacies-Software-Engineering-Robert/dp/0321117425) ([PDF](http://citeseerx.ist.psu.edu/viewdoc/download?doi=10.1.1.94.2037&rep=rep1&type=pdf)).

We can't test everything but that's no excuse for testing nothing! How much to test is something to be learned by experience, so think of it as analogous to when you finish proof reading a paper, over and over, before sending it to a conference. If you find bugs when you use your code, you did too little, so consider what you might have done and how to address this next time.

Tests, like code, should ideally be reviewed by a colleague which helps avoid tests that,

* Pass when they should fail, false positives.
* Fail when they should pass, false negatives.
* Don't test anything. 

For example,

    def test_critical_correctness():
        # TODO - will complete this tomorrow!
        pass

Yes, tests like this *do* occur on projects!

<a name="TDD"></a>
## Test-driven development

Traditionally, we'd write our code, then write the tests. [Test driven development](http://www.amazon.com/Test-Driven-Development-By-Example/dp/0321146530) (TDD), proposed by Kent Beck, is a philosophy that turns this on its head - we write code by *writing the tests first*, then write the code to make the tests pass. If a new feature is needed, another test is written and the code is expanded to meet this new use case. This continues until the code does what is needed. This can be summarised as red-green-refactor:

 * Red - write tests based on requirements. They fail as there is no code!
 * Green - write/modify code to get tests to pass.
 * Refactor code - clean it up.

By writing tests first, we're forced to think about what our code should do. In contrast, in writing our code then tests, we risk testing what the code actually does, rather than what it should do.

TDD operates on the YAGNI principle (You Ain't Gonna Need It) to avoid developing code for which there is no need.

## TDD of a DNA complement function

Given a DNA sequence consisting of A, C, T and G, we can create its complementary DNA, cDNA, by applying a mapping to each nucleotide in turn,

* A => T
* C => G
* T => A
* G => C

For example, given DNA strand GTCA, the cDNA is CAGT. 

So, let's write a `complement` function that creates the cDNA strand, given a DNA strand in a string. We'll use TDD, so to start, let's create a file `test_cdna.py` and add a test,

    from cdna import complement

    def test_complement_a():
        assert_equals complement('A') == 'T'

And let's run the test,

    $ nosetests test_cdna.py

Which fails as we have no function! So, let's create a file `cdna.py`. Our initial function to get the tests to pass could be,

    def complement(sequence):
        return 'T'

This is simplistic, but the test passes. Now let's add another test,

    def test_complement_c():
        assert complement('C') == 'G'

To get both our tests to pass, we can change our function to be,

    def complement(sequence):
        if (sequence == 'A'):
            return 'T'
        else:
            return 'G'

Now, add some more tests. Don't worry about `complement` just now.

Let's discuss the tests you've come up with.

Now update `complement` to make your tests pass. You may want to reuse some of the logic of `calculate_weight`!

When we're done, not only do we have a working function, we also have a set of tests. There's no risk of us leaving the tests "till later" and then never having time to write them.


