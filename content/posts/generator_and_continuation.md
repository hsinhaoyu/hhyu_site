---
title: "Beautiful ideas in programming: generators and continuations"
date: 2021-07-16T17:30:25+11:00
draft: false
tags: ["programming", "LISP", "python", "art appreciation"]
---

In this post, I'll summarize what I've learned from an attempt to gain a deeper understanding of two important concepts in programming: Python's [generators](https://en.wikipedia.org/wiki/Generator_(computer_programming)) and Scheme's [continuation](https://en.wikipedia.org/wiki/Continuation). The aim is not to teach Python or Scheme programming. Rather, what I want to do is to demonstrate that generators are special cases of a much more powerful construct - continuations. Continuations allow programmers to invent new control structures, and it is the foundation upon which iterators, generators, coroutines, and many other useful constructs can be built. I have found it very useful to understand how generators work from the deeper and broader perspective of continuations. 

Continuation is well-established in programming language theory (it was introduced in the 70's), but its use has not become mainstream. However, I suspect that in the age of cloud computing and big data, where lazy evaluation is becoming increasingly important, continuation will become more prominent, like many other concepts in functional programming. I think it's worthy of your time to reserve a place for this concept in your peripheral vision.

For those who are not familiar with Scheme: [Scheme](https://en.wikipedia.org/wiki/Scheme_(programming_language)) is a relatively modern dialect of one of the oldest programming languages: LISP. Scheme is a minimalist but highly expressive language, which makes it an ideal vehicle for experimenting with advanced concepts in programming. This is why Scheme is often used as a tool for teaching computer science, and as a playground for programming language researchers. Continuation is not unique to Scheme, but Scheme is the first language that treats continuations as first-class entities, thus making them easily manipulatable. It is the most natural venue for exploring continuations.

# Making generators in Python with "yield"

In Python, generators are primarily used to make complex loops easier to write and to maintain. That's why they are special cases of iterators. In a traditional language like c, a `for` loop looks like this:

```c
for(i=0; i<10; i++)
  printf("%i ", i);
```

The first line specifies the sequence of numbers to iterate over with. The second line is what we want to do with those numbers. With generators in Python, we can write this type of loops like the example given below. In it, we use the `squared_ints()` generator to produce a sequence of squared integers one by one, and then use the `sum_of_squares()` function to accumulate them until the sum is greater than a bound specified by the programmer:

```python
def squared_ints():
    ''' Infinite generator
        Return i^2 for i = 1, 2, ...
    '''
    i = 1
    while True:
        yield i*i   # it is this yield that makes this function a generator
        i += 1

def sum_of_squares(bound):
    ''' Return the smallest sum of squared integers greater than the bound
    '''
    g = squared_ints()
    s = 0
    for i in g:       # iterate over generator g
        s += i        # accumulate the sum s
        if s>bound:   # exit the loop if s is larger than n
            return s
```

The definition of `squared-ints()` looks like a regular function, but the presence of the `yield` keyword makes it a function that produces generators [1]. Using the `next()` function on a generator `g`, we can see that the numbers are generated one by one. Generators work nicely with loops in Python. An example can be seen in `sum_of_squares()`, where we iterate over a generator with a `for` loop.

```python
>>> g = squared_ints()
>>> g
<generator object squared_ints at 0x7fcf48143830>
>>> next(g)
1
>>> next(g)
4
>>> next(g)
9
>>> sum_of_squares(100)
140
```

Let's review what we have accomplished so far. In c-style loops, the generation and the consumption of the sequence are tightly coupled. In the Python code above, however, they are decomposed into two independent entities. Because generators can be build on other generators, you can further decompose a complex generator into a chain of simpler generators. This makes complex loops more modular and reusable, and therefore easier to write and maintain. Another improvement is that the generator code does not set a limit on the length of the sequence. It is theoretically infinite. The actual length of the sequence generated is determined by the code that consumes the sequence. This means that the generator code can be as general as possible, leaving computational efficiency to the consumer. This "laziness" is highly desirable in situations where generating the next item in the sequence is computationally expensive. 

All of this is made possible by one single keyword `yield`! It's pretty amazing, isn't it? What happens is that every time `yield` is called, the state of the function is frozen, and the flow of control is yielded to the caller, along with the generated value [2]. The next time the generator is evoked, the control flow is returned to the frozen state of the generator. 

What a simple and elegant syntax for a complex behavior! It's like magic. However, there is a part of me (perhaps the theorist in me) that is uncomfortable with magical behaviors in programming languages. How does `yield` create a generator (i.e., an object of the generator class) from a function definition? Why isn't this magic found in other places in the language? Why can't you call `yield` outside a function? In addition, generators provide some facilities for making coroutines - a topic in concurrent programming. What does concurrency have anything to do with iteration? Since all these behaviors appears to be associated with `yield` in an _ad hoc_ way, I tend to write generators idiomatically, following a memorized recipe without thinking about the heavy machinery behind the curtain. I always have a nagging feeling that there is a bigger picture that I don't see.

This was when I realized that I had read about similar ideas in a different context before. When I learned to program in college, I had a copy of [Scheme and the Art of Programming](https://www.cs.unm.edu/~williams/cs357/springer-friedman.pdf) by Springer and Friedman. There was a chapter on the mysterious concept of "continuation", where all of this stuff was discussed. I skipped the entire chapter because I couldn't understand what continuations were good for. Maybe it's time to revisit this topic. 

# Python-style generators in Scheme

Before we move on to continuations, rest assured that Python-style generators are available in Scheme. They are not part of the language itself, but with the `make-coroutine-generator` function in a quasi-standard library (i.e., [SRFI-158](https://srfi.schemers.org/srfi-158/srfi-158.html)), the `squared-ints` generator discussed previously can be written in Scheme as:

```scheme
(define squared-ints          ;;; squared-ints is a function (lambda) that makes a generator
  (lambda ()
    (make-coroutine-generator
     (lambda (yield)
       (let loop ((i 1))      ;;; define recursive function "loop" which increases i, starting from 1 
	     (yield (* i i))
	     (loop (+ i 1)))))))

(define g (squared-ints))     ;;; make a generator, assign it to "g" 
```

This doesn't look as elegant as the Python code, but logic is almost identical [3]. What is different here is that `yield` in Scheme is not a reserved keyword baked into the interpreter. Rather, the mechanism for yielding control is achieved by manipulating "continuations" - a fundamental building block of the language that represents computational steps that are pending execution. In fact, `make-coroutine-generator` is just a regular Scheme function consisting of ~10 lines of code. It can be implemented by any competent Scheme programmer without too much work. How is it possible? The magic is the use of `call/cc` (which stands for "call with current continuation") in `make-coroutine-generator`. Note that nothing in this code refers to a special class or keyword. `make-coroutine-generator`, `yield`, and `g` are all nothing more than regular functions. This is why we ask for the next value by calling it like a function:

```scheme
> g
#<procedure>
> (g)
1
> (g)
4
> (g)
9
```

The beauty of Scheme generators is that the library does not introduce a new class of entities, so generators can integrate seamlessly with the rest of the language.

# What are continuations?

Simply put, a continuation is a series of computational steps that are pending execution. For example, `0 + 1 + 2` is expressed in Scheme as `(+ 2 (+ 1 0))`. From the perspective of `0`, what will happen is that 1 will be added to it, and then 2 will be added to the result. The continuation of `0` in this context is therefore these two additions. The beauty of Scheme is that a continuation is not an exotic construct. Rather, it is just a function that can be easily manipulated by the programmer. In this example, the continuation is nothing other than `(lambda (x) (+ 2 (+ 1 x)))`. It is equivalent to `lambda x: x + 1 + 2` in Python.


In Scheme, we can use a function named `call-with-current-continuation` (or `call/cc` for short) to capture the current continuation, and then pass it to a different function, which is sometimes called the _receiver_. Most programming languages don't have this construct, so it will take a little bit of time to get used to it.

Consider this simple receiver, which simply stores the continuation in a global variable 'c'.

```scheme
(define c #f)                ;;; initially, global variable c stores nothing

(define store-continuation   ;;; define a function store-continuation
  (lambda (cc)               ;;; which receives a continuation
    (set! c cc)              ;;; store it to the global variable c
    0))                      ;;; and returns 0
```

We can now use it to capture the continuation of `0` in `(+ 2 (+ 1 0))`:

```scheme
> c                 ;; nothing is stored in c
#f 

> (+ 2
   (+ 1
     (call/cc store-continuation)))
3

> c
#<procedure (continuation . results1887)>
```

The returned value of `(+2 (+ 1 (call/cc store-continuation)))` is not important. The important part is that after the `call/cc` call, the `c` variable stores a function, which is the continuation we captured! We can't see the code, because it is compiled, but we can verify that it is effectively `(lambda (x) (+2 (+ 1 x)))`, because when we call `c` with a number, the returned value is the input plus 3.

```scheme
> (c 0)
3
> (c 10)
13
> (c -100)
-97
```

# Controlling the flow of execution with continuations

Let's use `call/cc` to do something useful. Let's use it to implement the logic of the Python function `sum_of_squares()` in Scheme. As before, we iterate over the generator in a loop, accumulate the sum, and stop when the sum is bigger than a certain bound.

```scheme
(define sum-of-squares
  (lambda (bound)                        ;; define (sum-of-squares bound)
      (call/cc
       (lambda (break)                   ;; define a reciever: first capture the continuation into break
	     (let ((g (squared-ints)))       ;; make the generator g
	       (let loop ((s 0))             ;; define the recursive function (loop s), where s is the accumulated value
	         (let ((new-s (+ s (g))))    ;; compute the new accumulated value
	           (if (> new-s bound)       ;; if the new accumulated value is larger than bound
		           (break new-s)         ;; exit the loop
		           (loop new-s)))))))))  ;; otherwise, continue the loop with the new accumulated value

> (sum-of-squares 100)
140
```

Before the `sum-of-square` function does anything, I use `call/cc` to capture the continuation, and assign it to the variable `break`. Notice that in the syntax of Scheme, this is the last step of the function. Therefore, calling `break` will immediately exist the function with a returned value, no matter where it is called. This doesn't seem to be too impressive in this simple example, but what is means is that if you have to work with a big and deeply nested data structure (for example, a tree or a graph), you can program with the elegance of recursion, but with the ability to abandon the recursion as soon as some condition is met. This can dramatically reduce unnecessary computation.

Some people call `call/cc` the `GOTO` statement of functional languages, but a better characterization is that it is a general mechanism for programmers to created new control structures that don't exist in the core language. With `call/cc`, all forms of control, including exceptions, backtracking, and coroutines can be built. Since no magic is involved, you can create your customized versions of these structures if you don't like how they behave. Novel control structures that are specific to the problem domain can also be invented. For example, in web programming, continuations have been used to control the logic of server applications (see [here](https://thelackthereof.org/docs/library/cs/continuations.pdf)).

Note that I wrote the program above to mirror the logic of the Python code. There are better ways to achieve the same without recursion or `call/cc`. For example, in the example below, I used the generator library to create a second infinite generator `acc` to accumulate the sum of elements in the `gen` generator, and then use the `generator-find` function to get the first value in `acc` that is larger than `bound`. This is an example of chaining iterators to make more complex iterators.

```scheme
(define sum-of-squares
	(lambda (bound)
		(let* ((gen (squared-ints))               ;; create a generator gen
		       (acc (make-unfold-generator        ;; create a second generator acc
		            (lambda (s) #f)               ;; no stop condition, make acc an infinite generator
		            (lambda (s) s)                
		            (lambda (s) (+ s (gen)))      ;; accumulate the next value from gen
		            0)))                          ;; accumulate from 0
					
      (generator-find                             ;; find the first value bigger than bound in the acc generator
       (lambda (s) (> s bound))
       acc))))
```

# Creating generators with call/cc

In the previous section, we use `call/cc` to break from a loop that iterates over a generator. Let's implement the generator itself with `call/cc`. To make it happen, we need a mechanism to yield - to exit a function before it ends normally. We have already seen how it is done. Now, we need a mechanism that resumes the function after it has yielded. It's not hard to see that we need two continuations.

```scheme
(define squared-ints
  (lambda ()
    (let* ((break #f)                  ;;; will store a continuation to break out of the function
	       (resume #f)                 ;;; will store a continuation to resume after yielding
	       (yield                      ;;; define the function "yield"
	         (lambda (value)
	          (call/cc                 ;;;   capture the current continuation
	            (lambda (r)
		         (set! resume r)       ;;;   store it in "resume"
		         (break value))))))    ;;;   and break out 
      
      (lambda ()                       ;;; will return a function (a closure)
	    (call/cc                       ;;; capture the current continuation...
	      (lambda (cc)
	       (set! break cc)             ;;; ...and store it in "break"
	       (if resume                  ;;; if this generator has been called before...
	           (resume '())            ;;; ... resume it
	           (let loop ((i 1))       ;;; otherwise, loop through i=1, 2, 3, 4...
		         (yield (* i i))       ;;; yield the square of i
		         (loop (+ i 1))))))))))

(define g (squared-ints))

> (g)
1
> (g)
4
> (g)
9
```

I modified this code slightly from Vasilij Schneidermann's implementation of the SRFI-158 library (see [here](https://depp.brause.cc/srfi-158/) for the full source code). It looks a bit intimidating to those who are not expert Scheme programmers, but the logic is very straightforward: for every number `i` in the loop, first store the future of the computation into an internal variable called `resume`, and then call the `break` continuation as we have done before to exist the function. The next time the generator is called, call the `resume` continuation, which will progress to the next number in the loop.

Note that in the code above, we return a generator as a function with no argument. We can easily add an argument to the function, so that messages can be sent to the generator after the iteration has started. For example, we can modify the code such that `(g 'reset)` will reset the iteration. Since generators are just functions in Scheme, communicating with generators can be done naturally with regular function calls. In Python, you can also send messages to generators, but it requires a special syntax of the `yield` keyword, and the `.send()` method of the generator class. Again, working with generators in Python is idiomatic, with lots of things happening behind the curtain.

# A few words on coroutines

I had planned to go into details about coroutines, because Python's tutorials on generators and Scheme's tutorials on continuations both tend to end with coroutines (How interesting!). However, I have decided against the idea because it will derail the discussion into a different territory.

But very briefly, `yield` in Python is often presented as a simplified method for writing generators, but what it does, suspending the execution of a function with the ability to resume it, is a much general concept than iteration. It enable a routine to do a small amount of work, and then pass the control to another routine, thus allowing them to run concurrently. This enables data to be processed by a network of routines, which do not have to be organized as a linear pipeline. It is more flexible and powerful than iteration. In other words, although generators are used for iterations, `yield` doesn't have to be.

The story is much simpler in Scheme. Since entities in Scheme are not organized hierarchically as classes, you don't have the awkwardness of implementing coroutines with a class that is designed primarily for iterations. In Scheme, coroutines and generators are just normal functions that manipulate continuations in different ways. 

# Beautiful ideas

I wrote this post primarily to summarize something new that I have learned. I also did it as an exercise in art appreciation. A lot of people like to talk about why they love a song, a TV show, or a novel. I would like to see more words spent on articulating why an idea is elegant, powerful, or clever. 

Scheme is an unusual language in its minimalism. Its designers want it to be highly expressive (meaning that complex logic can be expressed succinctly), and yet as simple as possible. This can only be achieved by building the language on a small number of powerful ideas. A powerful idea is not narrow in scope. A powerful idea stays true to itself, but when it is used in combination with other powerful ideas, new functionalities emerge. This is where the expressiveness comes from. A poetic way to express this magic (due to [John McCarthy](https://en.wikipedia.org/wiki/John_McCarthy_(computer_scientist))) is that it almost feel like the language was discovered rather than invented. Once you get it, it's hard to imagine how a language can be designed in any other way, because every component seems to be incomplete without the others.

I am obviously a fan of this philosophy. However, I am not blind to the fact that powerful ideas are much harder to master. Continuations are hard to understand and hard to write. The fact that I am still thinking about continuations so many years after my college programming class should tell you something about the level of devotion needed to reach the blissful state of pure lambda enlightenment. This brings me to Python. As I said before, the minimalist in me is bothered by the hidden machinery behind generators. But with the `yield` keyword, anybody can learn the idioms and start to use generators in 10 minutes. It is perhaps not as powerful an idea as continuations, but it is powerful enough that it blends in nicely with loops, making complex loops dramatically easier to write. It's a beautiful idea.

In the end, I feel compelled to circle back to Scheme. What is beautiful about Python's generators is that `yield` is a minimal interface to a powerful idea. You might say the same about Scheme's continuations and macros. With `call/cc` and `define-macro`, the machinery of the interpreter become accessible to the programmer, thus erasing the boundary between programming and metaprogramming. A lot has been written on this topic so I won't repeat it here (see [here](http://www.paulgraham.com/avg.html)), but parsing (accessible via macros) and execution control (accessible via `call/cc`) are basically what the interpreter does. I am amazed by how minimal the API to the interpreter is. That is beautiful.

# Notes

[1] Using `yield` in a function definition is not the only way in which generators can be constructed in Python. Please consult any general tutorial on generators.

[2] More accurately, `yield` in a function creates a generator, which suspends its execution at the point where `yield` appears in the definition. This characterization is harder to read, so I will write as if `yield` actually does the yielding. The intention of the syntax is to create this illusion.

[3] Note that recursion is Scheme's preferred way for writing loops. It isn't necessary for creating generators. Since this is a form of tail recursion, the Scheme interpreter automatically translates the recursion into a loop. 
