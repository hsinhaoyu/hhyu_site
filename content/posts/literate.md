---
title: "Literate programming as a medium of experimental literature"
date: 2020-10-06T14:34:22+11:00
draft: true
tags: ["programming", "whimsical", "literature"]
---
[Literate programming](https://en.wikipedia.org/wiki/Literate_programming) is a method of software engineering that emphasizes the narrative aspect of programming. The concept was first introduced by Donald Knuth in the 80's. I don't use it at all in my work, but can I do something interesting with it? How about we take the "literate" part literally, and try to imagine a way to write some sort of novel with this method?

### Code like writers do
Let's first review the concept of literate programming. Programming is traditionally seen as an exercise that translates human thoughts into a format that computers can understand. As a result, the ideas behind the program can become obscured and fragmented in the source code. Reading a source code is a lot of mental work. It often feel like solving a puzzle, or watching Tarantino's [Pulp Fiction](https://en.wikipedia.org/wiki/Pulp_Fiction). 

Why do I have to do so much mental work, jumping around different routines in multiple files, just to orient myself to the flow of logic? 



Literate programming turns the table around, and insists that a program should be written in a format that we use to communicate to each other - as a *narrative*. In this view, the fact that this narrative can be executed by a computer is more like a byproduct. Literate programs are typically presented like articles in technical journals, with beautiful typography and layout, and the ideas are described in prose. Fragments of computer code are interspersed in the article in a format that computer scientists used to describe algorithms: they are written in human readable form that hides low-level details, and they are presented in the order dictated by the narrative, rather than by the programming language. Using literate programming tools such as [WEB](https://en.wikipedia.org/wiki/Web_(programming_system)), these code fragments can be extracted from the article, and reassembled into a valid program. Using Knuth's term, this process is called *tangling*.

### Can you read a program like a novel?
Knuth's website has a section called [Programs to Read](https://cs.stanford.edu/~knuth/programs.html) to drive home the idea that his programs are written with human readers' pleasure in mind. How cool is that! Let me skip his literal programs for solving mathematical puzzles and try to read something more fun... How about his reimplementation of [ADVENT](https://en.wikipedia.org/wiki/Colossal_Cave_Adventure) - the classic text adventure game? [Here](http://www.literateprogramming.com/adventure.pdf) is the pdf. I found it surprisingly fun to read. It's like an epic told in 200 verses. There are two intertwined plots: a creation story about constructing a world inside a computer, and an adventure set inside a colossal cave. The former is abstract. To create a universe out of nothing (well, nothing except the programming language), you start with ontology. 



We read about the definitions of ontological entities and how they interact. What is a 



In the former, we learn about locations, objects, animals, and how these ontological entities interact with each other in a simple language; and in the latter, we learn about , characters, treasures, puzzles, and all the fun things that an adventurer can expect to do in the cave. 



All sections are short and easy to digest, but together they define a small but complex universe that can actually come alive, once the source code is tangled, compiled, and executed on a computer. As a bonus, Knuth threw in some personal comments, anecdotes, and even references to classic literature in the program. I probably wouldn't call it a novel (the first couple of pages seem too dry for a novel, but have you read Tolkien's [The Silmarillion](https://en.wikipedia.org/wiki/The_Silmarillion), or the appendices of The Lord of the Rings?), but it was an enjoyable red, and I wouldn't mind reading it again. It's very obvious that I wouldn't have enjoyed, or understood, the original implementation of ADVENT in Fortran.

### Or maybe a parody of a novel?
