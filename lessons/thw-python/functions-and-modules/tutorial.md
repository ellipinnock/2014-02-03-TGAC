---
layout: lesson
root: ../../..
github_username: apawlik
bootcamp_slug: 2014-02-03-TGAC
title: Python Functions and Modules
---
**Materials by: John Blischak, Anthony Scopatz, and other Software Carpentry instructors (Joshua R. Smith, Milad Fatenejad, Katy Huff, Tommy Guy and many more)**

Computers are very useful for doing the same operation over and over. When you know you will be performing the same operation many times, it is best to encapsulate this similar code into a function or a method. Programming functions are related to mathematical functions, e.g. f(x), and it is helpful to think of them as abstract operators that produce output given some input. Let's look at some examples to solidify this concept.

*The IPython Notebook file and the sample data files for this tutorial can be found in the course materials:* 

	{{page.bootcamp_slug}}/lessons/thw-python/functions-and-modules

##Built-in string methods

The base distribution comes with many useful functions. When a function is a property of, or owned by, on a specific type (ints, floats, lists, strings, dictionaries, etc.) it is called a method. First let's look at some basic string methods since they are very useful for reading data into Python.


Find the start codon of a gene

	dna = 'CTGTTGACATGCATTCACGCTACGCTAGCT'
	dna.find('ATG')

parsing a line from a comma-delimted file:

	lotto_numbers = '4,8,15,16,23,42\n'	
	lotto_numbers = lotto_numbers.strip()
	print lotto_numbers.split(',')

replacing characters:

	question = '%H%ow%z%d%@d%z%th%ez$%@p%ste%rzb%ur%nz%$%@szt%on%gue%?%'
	question = question.replace('%', '')
	question = question.replace('@', 'i')
	question = question.replace('$', 'h')
	question = question.replace('z', ' ')
	print question

Replacing characters can be concatenated:

	answer = '=H=&!dr=a=nk!c=~ff&&!be=f~r&=!i=t!w=as!c=~~l.='
	print answer.replace('=', '').replace('&', 'e').replace('~', 'o').replace('!', ' ')

**Short Exercise: Calculate GC content of DNA**

Because the binding strength of guanine (G) to cytosine (C) is different from the binding strength of adenine (A) to thymine (T) (and many other differences), it is often useful to know the fraction of a DNA sequence that is G's or C's. Go to the [string method section](http://docs.python.org/2/library/string.html) of the Python documentation and find the string method that will allow you to calculate this fraction.

	# Calculate the fraction of G's and C's in this DNA sequence
	seq1 = 'ACGTACGTAGCTAGTAGCTACGTAGCTACGTA'
	gc = 

Check your work:

	round(gc, ndigits = 2) == .47

##Creating your own functions!

When there is not an available function to perform a task, you can write your own functions. Ths simplest functions have the following format in Python:

	def <function name>():
    	<function body>

For example:

	def do_nothing():
	    s = "I don't do much"

However, this often isn't very useful since we haven't returned any values from this function. Note: that if you don't return anything from a function in Python, you implcitly have returned the special None singleton. To return vaulues that you computed locally in the function body, use the return keyword.

	def <function name>():
	    <function body>
    	return <local variable 1>      

Functions, may be defined to take parameters or arguments.

	def <function name>(<argument>):
	    <function body>
    	return <local variable 1>  

The function name, arguments, and retun are jointly known as the function signature since the uniquely define the function's interface.

	def square(x):
	    sqr = x * x
    	return sqr

Using a function is done by placing parentheses () after the function name after you have defined it. This is known as calling the function. If the function requires arguments, the values for these arguments are inside of the parentheses

	square(2)

Like mathematical functions, you can compose a function with other functions or with itself!

	square(square(2))

Functions may be defined such that they have multiple arguments or multiple return values:

	def <function name>(<arg1>, <arg2>, ...):
	    <function body>
    	return <var1> , <var2>, ...

For example:

	def hello(time, name):
    	print 'Good ' + time + ', ' + name + '!'

Let's try it:

	hello('afternoon', 'Software Carpentry')



Another example, return both the quotient and remainder

	def quorem(a, b):
	    quo = a / b
    	rem = a % b
	    return quo, rem

Let's call the function:
	
	quorem(42, 16)

Note that when you return multiple values you may unpack these into individual variables or into a single variable which is a tuple of both values:

	q, r = quorem(42, 16)
	print q
	print r

 
	both = quorem(42, 16)
	print both

##Keyword Arguments

In Python, functions also support default values for arguments. Arguments with an associated default are called keyword arguments. If this function is then called without one of these arguments being present the default value is used. All keyword arguments must come after normal arguments in the function definition:

	def <function name>(<arg1>, <arg2>, <arg3>=<arg3 default>, <arg4>=<arg4 	default>, ...):
    	<function body>
    	return <rtn>

For example:

	def add_space(s, t="Mom"):
    	return s + " " + t


	print add_space("Hello")
	print add_space("Morning", "Dad")

You can also call any functions with their arguments, regular and keyword, with their argument names explictly in the call. This uses equal signs in the same way that keyword arguments are defined.

	print add_space(s="Hello")
	print add_space(s="Morning", t="Dad")

If you have many keyword arguments, then they may be out of order in the function call as long as they are explicit.

	def f(x=1, y=2, z=3):
	    return 2*x**3 + 42*y - z

Let's try it:

	f(y=17, z=15, x=2)

Warning: be careful with mutable containers as default values. The container will remember its value from previous function calls.


	def add_to_list(val, seq=[]):
	    seq.append(val)
    	return seq


	add_to_list(42)
	add_to_list(16)
	add_to_list(65)

##Packing & Unpacking Arguments

Say that you have a list or tuple of values that you would like to call a function with. You might be tempted to do the following:

	def myadd(x, y):
    	return x + y

	addme = (42, 65)
	myadd(addme[0], addme[1])

However, this becomes very tedious very quickly. To solve this, Python allows you to call a function with the original list prepended by an asterisk *. This expands, or unpacks, the values of the list into the function call.

	myadd(*addme)
	myadd(*[14, 18])

Not just lists, but any sequence is allowed to be expanded in this way. While this works for normal arguments, keyword arguments are more similar to dictionaries. Therefore any mapping is also able to be expanded into keyword arguments. To distingush keyword argument unpacking from unpacking normal arguments, you must use a double asterisk ** to unpack a dictionary.

	def f(x=1, y=2, z=3):
	    return 2*x**3 + 42*y - z

	v = {'x': 1, 'y': 2, 'z': 3}
	f(**v)

This allows you to build up dictionaries of values with which to call function before calling it.

In addition to calling functions with lists or tuples and dicts, you may also define functions such that any extra arguments are put into a tuple and any extra keyword arguments are placed into a dictionary. These special catch-all containers must come after all other arguments and all other keyword arguments. This is known as argument packing and uses the single and double asterisk as above, but this time in the function definition:

	def <function name>(<arg1>, <arg2>=<arg2 default>, *<other args>, **<other keyword args>):
    <function body>
    return <rtn>

For example:
	
	def what(x, y, *args):
	    print "x = ",  x
    	print "y = ", y
	    print "extra argument = ", args

	what(1, 10, 178.0, "hello", [42, 42])

Another example:

	def classroom(teacher="Anthony", **kwargs):
    	print "Teacher's name is ", teacher
    	print "Teacher has a lot to deal with:", kwargs

	classroom(lesson="functions", lesson="Python")


	def sweet_nothings(x, y, *args, **kwargs):
    	print "Not doing anything with x, y, z, f"
    	print args
    	print kwargs

	sweet_nothings(0, 0.0, 42, "Nothing", zero="zero", z="", f=True, empty="de nada")

So the most general function signature that you can define is:

	def func(*arg, **kwargs):
	    print args
	    print kwargs

	func()  # try me out!

##Recursion

One of the greatest features of functions is that they may call themselves from within their own bodies! This is known as recursion.

	def count_backwards(x):
	    print x
	    count_backwards(x-1)

	count_backwards(10)

Well, that was too much. Thus it is important to ensure that recursive functions have some case which does not call itself. This will terminate the recursion.

	def count_backwards(x):
	    print x
	    if 0 < x:
        count_backwards(x-1)

	count_backwards(10)

One of the most famous recursive sequences is the Fibonacci sequence. This can be defined as a single recursive function.

	def fib(n):
	    if n == 0 or n == 1:
	        return n
	    else:
    	    return fib(n - 1) + fib(n - 2)

	fib(10)

##Decorators

In Python, functions are first class objects. This means that anything any other variable can do, a function can also do. This is because they are normal variables in the language.

	def myfunc(x):
	    return x

	print myfunc

We can assign functions just like we did with other variable types: 

	yourfunc = myfunc
	yourfunc("hello")

And use `dir` method to see what methods are available for our function:

	dir(myfunc)

This allows functions be passed to other functions as arguments:

	def just_one():
	    return 1

	def add_one(f):
    return f() + 1

	add_one(just_one)

Functions may also then define other functions and return them:

	def outer():
	    def inner(*args, **kwargs):
    	    return True
	    return inner

	outer()

When you define a function which takes a function and (usually) returns a function, this is called a decorator. Decorators are used to wrap other functions.

	def loud(f):
	    def newfunc(*args, **kwargs):
	        print "calling with:", args, kwargs
	        rtn = f(*args, **kwargs)
	        print "got", rtn
	        return rtn
	    return newfunc

	def myadd(x, y):
	    return x + y

 	loudadd = loud(myadd)
 	v = loudadd(2, 4)

Most of the time such functions may be used such that the original function name is preserved.

	myadd = loud(myadd)
	myadd(42, 65)

Python also has a shortcut for using decorators which preserve the same name. The 'at' symbol @ followed by the decorator name is placed on the line above the function definition.

	@loud
	def mysub(x, y):
	    return x - y

	v = mysub(42, 65)

For more advanced users, the standard library functools module has some really powerful and great utilities. This includes the functools.wraps() function.

##Lambdas

Lambdas are small, single expression functions that are anonymous (they have no name). They come from functional programming languages and the Lambda Calculus. Since they are so small they may be written on a single line.

	lambda <args>: <expr>

	lambda x: x + 1

Note that just because they are implicitly anonymous, doesn't mean that you can't name them.

	f = lambda x, y: x + y +1

This is much more useful than it might seem at first glance.

**Short exercise: Write a function to calculate GC content of DNA**

Make a function that calculates the GC content of a given DNA sequence. For the more advanced participants, make your function able to handle sequences of mixed case (see the third test case).

	# Calculates the GC content of DNA sequence x.
	# x: a string composed only of A's, T's, G's, and C's.
	
	def calculate_gc(x):

 

Check your work:

	print round(calculate_gc('ATGC'), ndigits = 2) == 0.50
	print round(calculate_gc('AGCGTCGTCAGTCGT'), ndigits = 2) == 0.60
	print round(calculate_gc('ATaGtTCaAGcTCgATtGaATaGgTAaCt'), ndigits = 2) == 0.34

##Modules

Python has a lot of useful data type and functions built into the language, some of which you have already seen. For a full list, you can type dir(__builtins__). However, there are even more functions stored in modules. An example is the sine function, which is stored in the math module. In order to access mathematical functions, like sin, we need to import the math module. Lets take a look at a simple example:

	print sin(3) # Error! Python doesn't know what sin is...yet

We can solve that by importing math module which includes sin function:

	import math # Import the math module
	math.sin(3)

	dir(math) # See a list of everything in the math module

	help(math) # Get help information for the math module

It is not very difficult to use modules - you just have to know the module name and import it. There are a few variations on the import statement that can be used to make your life easier. Lets take a look at an example:


	from math import *  # import everything from math into the global namespace (A BAD IDEA IN GENERAL)

	print sin(3)        # notice that we don't need to type math.sin anymore
	print tan(3)        # the tangent function was also in math, so we can use that too

Let's do it now correctly:

	from math import sin  # Import just sin from the math module. This is a good idea.

	print sin(3)          # We can use sin because we just imported it
	print tan(3)          # Error: We only imported sin - not tan

Here is a different method:

	import math as m      # Same as import math, except we are renaming the module m
	
	print m.sin(3)        # This is really handy if you have module names that are long

Python comes with a huge number of modules available as part of the standard library (batteries included). It has a gargantuan number of third party modules as well. This ecosystem is what makes scientific software development in Python great!

###The General Problem

<img title="I find that when someone's taking time to do something right…ht in the past, they're a master artisan of great foresight." alt="xkcd" src="http://imgs.xkcd.com/comics/the_general_problem.png"></img>

Now that you can write your own functions, you too will experience the dilemma of deciding whether to spend the extra time to make your code more general, and therefore more easily reused in the future.

####Longer exercise: Reading Cochlear implant into Python

For this exercise we will return to the cochlear implant data first introduced in the section on the shell. In order to analyse the data, we need to import the data into Python. Furthermore, since this is something that would have to be done many times, we will write a function to do this. As before, beginners should aim to complete Part 1 and more advanced participants should try to complete Part 2 and Part 3 as well.

**Part 1: View the contents of the file from within Python**

Write a function view_cochlear that will open the file and print out each line. The only input to the function should be the name of the file as a string.

	def view_cochlear(filename):
    """Write your docstring here.
    """       

Test it out:

	view_cochlear('/home/swc/boot-camps/shell/data/alexander/data_216.DATA')
	view_cochlear('/home/swc/boot-camps/shell/data/Lawrence/Data0525')

**Part 2:**

Adapt your function above to exclude the first line using the flow control techniques we learned in the last lesson. The first line is just # (but don't forget to remove the '\n').

	def view_cochlear(filename):
    """Write your docstring here.
    """

Test it out:

	view_cochlear('/home/swc/boot-camps/shell/data/alexander/data_216.DATA')
	view_cochlear('/home/swc/boot-camps/shell/data/Lawrence/Data0525')

**Part 3:**

Adapt your function above to return a dictionary containing the contents of the file. Split each line of the file by a colon followed by a space (': '). The first half of the string should be the key of the dictionary, and the second half should be the value of the dictionary.


	def save_cochlear(filename):
    """Write your docstring here.
    """

Check your work:

	data_216 = save_cochlear("/home/swc/boot-camps/shell/data/alexander/	data_216.DATA")

	print data_216["Subject"]


	Data0525 = save_cochlear("/home/swc/boot-camps/shell/data/alexander/data_216.DATA")
	print Data0525["CI type"]


**Bonus Exercise: Transcribe DNA to RNA**

Motivation:

During transcription, an enzyme called RNA Polymerase reads the DNA sequence and creates a complementary RNA sequence. Furthermore, RNA has the nucleotide uracil (U) instead of thymine (T).
Task:

Write a function that mimics transcription. The input argument is a string that contains the letters A, T, G, and C. Create a new string following these rules:

    Convert A to U
    Convert T to A
    Convert G to C
    Convert C to G

*Hint:* You can iterate through a string using a for loop similary to how you loop through a list.

	def transcribe(seq):
    """Write your docstring here.
    """

Check your work:

	transcribe('ATGC') == 'UACG'
	transcribe('ATGCAGTCAGTGCAGTCAGT') == 'UACGUCAGUCACGUCAGUCA'
	
Previous: [Flow control](../flow-control/tutorial.html)



[Back to main page](../../../index.html)
