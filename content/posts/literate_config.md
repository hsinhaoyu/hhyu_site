---
title: "A simple example of literate programming: the Emacs configuration file"
date: 2021-01-05T14:16:59+11:00
draft: false
tags: ["tutorial", "emacs", "literate programming", "orgmode", "lisp"]
---
I like Donald Knuth's idea of [literate programming](https://en.wikipedia.org/wiki/Literate_programming), but unfortunately, I don't have too many opportunities to write literate programs. A couple of months ago, I was inspired by [this article](https://blog.thomasheartman.com/posts/configuring-emacs-with-org-mode-and-literate-programming), and decided to rewrite my Emacs configuration file using [org-mode](https://orgmode.org)'s literate programming tool [Babel](https://orgmode.org/worg/org-contrib/babel/intro.html). I was quite pleased by the [result](https://github.com/hsinhaoyu/.emacs.d), but I must admit that it was an exercise in literate programming only in the most superficial sense of the word. What I did was adding some marked-up texts around LISP code blocks. The result was similar to the notebooks of Python and _Mathematica_. The texts became easier to read, but they were really just glorified code comments. Knuth's literate programming is a deeper concept. The point is to present the code in a format that reflects the flow of human thoughts and narrative, which is often not in the same order as demanded by the programming language. 

I came up with a simple example that significantly improves the readability of my configuration file. Originally, I had a block of LISP code that reads like this:

```sh
(setq org-capture-templates
	'(("t" "TODO inbox"
           entry
           (file "~/.deft/capture-todo.org")
           "* TODO %?
		   SCHEDULED: %t")
      ("n" "notes inbox"
           entry
           (file "~/.deft/capture-notes.org")
		   "* %T\n%i%?")
	  ("j" "Journal entry"
           plain
           (function org-journal-find-location)
           "** %(format-time-string org-journal-time-format)%^{Title}\n%i%?"
           :jump-to-captured t
           :immediate-finish t)))
```

What this does is that it sets up three templates for [org-capture](https://orgmode.org/manual/Capture.html) (org-mode's mechanism for quickly jotting down notes). I use indentation to make it more obvious that `org-capture-templates` is assigned to a list of three templates, but I find this type of deeply nested code difficult to read and edit. When I tried to add a new template earlier today, I broke up the wrong parentheses, and had to spend a couple of minutes trying to figure out which `(` paired with which `)`, after the structure was already messed up. It's a common problem in LISP programming.

I made this code easier to work with using org-mode's implementation of [noweb](https://en.wikipedia.org/wiki/Noweb) - the literate programming system that `Babel` is based on. `Noweb` is a minimalist literal programming system that is much similer than Knuth's [WEB](http://www.literateprogramming.com/cweb_download.html). Here's the revised version:

```
#+begin_src emacs-lisp :noweb yes
  (setq org-capture-templates
      '(
        <<ORG_CAPTURE>>
       )
  )

Capture ad hoc todos in a special file
#+begin_src emacs-lisp :tangle no :noweb-ref ORG_CAPTURE
("t" "TODO inbox"
     entry
     (file "~/.deft/capture-todo.org")
     "* TODO %?
        SCHEDULED: %t")
#+end_src

Capture ad hoc notes in a special file
#+begin_src emacs-lisp :tangle no :noweb-ref ORG_CAPTURE
("n" "notes inbox"
     entry
     (file "~/.deft/capture-notes.org")
     "* %T\n%i%?")
#+end_src

Capture org journal
#+begin_src emacs-lisp :tangle no :noweb-ref ORG_CAPTURE
("j" "Journal entry"
     plain
     (function org-journal-find-location)
     "** %(format-time-string org-journal-time-format)%^{Title}\n%i%?"
     :jump-to-captured t
     :immediate-finish t)
#+end_src
```

In this version, `org-capture-templates` is assigned to a `noweb` macro called `ORG_CAPTURE`. The `:noweb yes` attribute was inserted to tell `Babel` to expand the macro, which is defined by the three code blocks marked by `:noweb-ref ORG_CAPTURE`. The three templates became easier to read and edit, and more importantly, they are now decoupled, so I am free to move them around. I am planning to move the third code block to a different section about `org-journal`, because it is about journaling. With literate programming, I can now explain everything about my journaling setup in one section. 

It's possible to achieve the same effect without literate programming (for example, we can assign the three templates to three LISP symbols), but I like the literate approach better because what I want to do is to talk about the code out of order. I would rather not introduce additional LISP symbols for this purpose.

It's unlikely that I will write entire programs in the literate style, because it's a bit esoteric. But I'm glad that it can be very helpful in small places.


