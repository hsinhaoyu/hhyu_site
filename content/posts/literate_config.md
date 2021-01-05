---
title: "Literating the Emacs configuration file"
date: 2021-01-05T14:16:59+11:00
draft: false
tags: ["tutorial", "emacs", "literate programming", "orgmode", "lisp"]
---
I really like Donald Knuth's idea of [literate programming](https://en.wikipedia.org/wiki/Literate_programming), but unfortunately, I don't have too many opportunities to write literate programs. A couple of months ago, I was inspired by [this article](https://blog.thomasheartman.com/posts/configuring-emacs-with-org-mode-and-literate-programming), and decided to rewrite my Emacs configuration file using [org-mode](https://orgmode.org)'s literate programming tool [Babel](https://orgmode.org/worg/org-contrib/babel/intro.html). I was quite pleased by the [result](https://github.com/hsinhaoyu/.emacs.d), but I must admit that it was an exercise of literate programming only in the most superficial sense of the word. What I did was adding some marked-up texts around LISP code blocks (similar to the notebooks of Python and _Mathematica_). The literate part was prettier and easier to read, but it was really just a glorified version of programmer's code comments. Knuth's literate programming is a deeper concept. The point is to present the code in a format that reflects the flow of human thoughts and narrative, which is often not in the same order as prescribed by the programming language. 

I came up with a simple example that significantly improves the readability of my configuration file. Originally, I had a block of LISP code that reads like this:

```lisp
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

What this does is that it sets up three templates for [org-capture](https://orgmode.org/manual/Capture.html) (org-mode's mechanism for quickly jotting down notes). I use indentation to make it more obvious that `org-capture-templates` is assigned to a list of three things, but I find this type of deeply nested code difficult to read and edit. When I tried to add a new template earlier today, I broke up the wrong parentheses, and had to spend a couple of minutes trying to figure out which `(` paired with which `)`, after the structure was already messed up. It's a common problem in LISP programming.

I made this code easier to work with using org-mode's implementation of [noweb](https://en.wikipedia.org/wiki/Noweb) - a literate programming tool that removes some of the complexities of Knuth's [WEB](http://www.literateprogramming.com/cweb_download.html). Here's the new version:
```lisp
#+begin_src emacs-lisp :noweb yes
  (setq org-capture-templates
      '(
        <<ORG_CAPTURE>>
       )
  )
#+end_src

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

In the literate version of the same code, `org-capture-templates` is assigned to a macro called `ORG_CAPTURE`. I use `:noweb yes` to tell `Babel` to expand the macro. This is followed by three code blocks all marked with `:nweb-ref ORG_CAPTURE`, which defines the macro. The three templates are easier to read and edit, because they are defined in their own blocks. Most importantly, the three templates are now decoupled in the literate program. They can be moved around. I am planning to move the definition of the journal entry template (the third one) to another place, where other aspects of `org-journal` are configured. 

It's unlikely that I will write entire programs in the literate style, because it's a bit esoteric. But in small places, it can be very helpful.
