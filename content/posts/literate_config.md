---
title: "Writing the Emacs configuration script in org-mode: a simple example of literate programming"
date: 2021-01-05T14:16:59+11:00
draft: false
tags: ["tutorial", "emacs", "literate programming", "orgmode", "lisp"]
---
### Program like writers do
Programming is traditionally seen as an exercise that translates human thoughts into a format that computers can understand. As a result, the ideas behind the program can become obscured and fragmented in the source code. This is why reading code requires so much mental effort. For me, reading code often feels like solving a puzzle, or reading modernist/postmodernist novels where events are narrated out of sequence. The concept of [literate programming](https://en.wikipedia.org/wiki/Literate_programming) was introduced by Donald Knuth in the 80's to address this issue. In literate programming, the source code is seen as a medium to communicate ideas. The fact it can be compiled and executed by a computer is more of a byproduct (that is, an enormously useful byproduct!). Literate programs are presented like articles in technical journals, with small chunks of code scattered among the paragraphs to illustrate the narratives.  On Knuth's website, there is a section called [Programs to Read](https://cs.stanford.edu/~knuth/programs.html) to drive home the idea that his programs are written with human readers' pleasure in mind. How cool is that! In fact, the [source code of TeX](https://mirror.aarnet.edu.au/pub/CTAN/systems/knuth/dist/tex/tex.web) *really* reads like a book. Take a look and prepare to be amazed.

In a sense, literate programming is already mainstream. It's now commonplace to mix texts and code freely in Jupyter notebooks when coding in Python and R (_Mathematica_ has a notebook interface designed by [Theodore Gray](http://home.theodoregray.com) since the 80's). However, Knuth's idea is more ambitious and radical than the notebook interface. For him, the essence of literate programming is to write code that follows of the flow of human thoughts, which is very often in conflict with the requirements of programming languages. To achieve this, Knuth designed a program called [WEB](https://en.wikipedia.org/wiki/Web_(programming_system)) to extract the code fragments from the document, and re-assemble them into a valid program. He calls this process *tangling*.

I have been fascinated by this idea for a long time, but I hadn't tried to program literally until recently, because WEB appeared to be too complicated to be practical for me. It was, after all, designed to write serious programs and big books.  About half a year ago, a colleague showed me his Emacs configuration script on GitHub, and I was amazed that the source code was displayed like an article. This is because [org-mode](https://orgmode.org), a markup language popular among Emacs users, is supported by GitHub. If you pay attention, you'll see many `README.org` rather than `README.md` files on Github, especially in Emacs-related repositories. Org-mode is similar to Markdown, except that it is designed to be a markup language for documents _and_ for code. Org-mode documents can be edited in a notebook-like interface under Emacs, and they can embed code blocks with [Babel](https://orgmode.org/worg/org-contrib/babel/), which is derived from a lightweight literate programming system called [Noweb](https://www.cs.tufts.edu/~nr/noweb/). Noweb is a lot simpler than WEB. It's perfect for small projects.

### The poetics of Emacs configuration

Inspired, I followed [this article](https://blog.thomasheartman.com/posts/configuring-emacs-with-org-mode-and-literate-programming) and rewrote [my Emacs configuration script](https://github.com/hsinhaoyu/.emacs.d) in org-mode. I did it primarily as an excuse to learn org-mode. At that time, I felt that literate programming something as trivial as a configuration script was like setting up a Rube-Goldberg contraption. I had suspected that I'd be switching back to the normal `init.el` in a month. After all, what's the grand narrative in loading themes and remapping keys? If I turn my `init.el` into an epic, would anyone care to read it? I was pleasantly surprised that this experiment was successful. It has increased by enjoyment and productivity when I use Emacs. I don't see why I would switch back ever again.

Emacs configuration scripts are not simple key-value pairs. They can be long and complicated (I'm not an Emacs expert but my script is already +600 lines) and they almost always embed programming logic expressed in LISP. I use Emacs primarily as a productivity tool, so a big part of my script is about how Emacs-based productivity packages (e.g., calendar, notes, todo and agenda) interact with each other. My Emacs configuration script is not just about how my editor looks -  it's also about how I work. I find myself _reading_ my `config.org` on GitHub from time to time, because I can't always remember how I am supposed to do something. Interestingly, I have become a semi-regular reader of my script.

In addition, GitHub's rendering makes the document pleasant to read. When I edit the script in Emacs, the ability to fold sections makes navigating the script a breeze. My org-mode rewrite turned out to be a productive exercise that has transformed my messy configuration script into something enjoyable to work with.

### Tangling to untangle

However, I was reluctant to call what I did literate programming, because all I did was writing fancy code comments. How about Knuth's _tangling_? I thought it had no place for me, because an Emacs configuration script is mostly a collection of self-contained sections that set up various packages. There were no larger-scale narratives, so there was no need to re-arrange code. Or was there?

I quickly realized that in small places, literate programming can still be useful. For example, I originally had a block of LISP code that reads like this:

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

What this does is that it sets up three templates for [capture](https://orgmode.org/manual/Capture.html) - a mechanism for quickly jotting down notes. No matter what I am doing with Emacs, I can always evoke capture, choose a template, and start to write. When I'm done, `org-capture` files it away according to the instructions in the template. It is a hub that dispatches texts to different components of Emacs.

In the code above, I use indentation to make it obvious that `org-capture-templates` is assigned to a list of three templates, but I find this type of deeply nested code difficult to read and edit. When I tried to add a new template earlier today, I broke up the wrong parentheses, and had to spend a couple of minutes figuring out which `(` paired with which `)`, after the structure had already been messed up. It's a common hazard in LISP programming.

I rewrote it like what's shown below. Note words like `tangle` and `noweb` - that's where we are really literate programming:
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
In this version, `org-capture-templates` is assigned to a `noweb` macro called `ORG_CAPTURE`. The `:noweb yes` attribute was inserted to tell `Babel` to expand the macro, which is defined by the three code blocks marked by `:noweb-ref ORG_CAPTURE`. The three templates became easier to read and edit, and more importantly, they are now decoupled, so I am free to move them around. The third code block is a template for writing journal entries, so it makes more sense to move it to a different section, where my journaling workflow is defined.

Another place where tangling is useful involves regular expressions. For example, I use the Emacs package [Deft](https://github.com/jrblevin/deft) as an interface for browsing org-mode documents (it's similar to the popular [Notational Velocity](https://notational.net) or [nvALT](https://brettterpstra.com/projects/nvalt/) apps on the Mac). With Deft, one line of each file is displayed for browsing, so I had to tell Deft to filter out customized meta information (such as tags) at the beginning of each file, which I set up in the capture templates. This was done by a messy regular expression. With `Noweb`, I replaced the regular expression with a series of macros, which are defined in sections where the capture templates are set-up. This way, if I change the capture template, I'd remember to edit the regular expression as well.

A third example is that I wrote a couple of shell scripts to run Emacs in client-server mode (see [this post](https://www.hhyu.org/posts/emacs_clientserver/)). They are not LISP scripts for Emacs, but I embed them in my script anyway. This way, if I have to install these scripts on a different computer, I can always use `Babel` to extract them.

### Emacs, the future of Jupyter?
Because Emacs ships with org-mode, it is a very accessible literate programming system that anyone can start using immediately. I wrote this post to demonstrate that you don't have to buy in the entire philosophy to do useful things with it on the small scale. But `Babel` is a lot more powerful than that. Babel allows the output of a code block to be embedded in the document (like Jupyter notebooks do)... and it can be fed into another code block, _written in a different programming language_. This is where the name Babel comes from: what it really is, is a meta-programming system for integrating multiple programming languages. That sounds too baroque to be useful in practice... but wait! I actually mix Python and R in the same project quite often. So it's a very useful feature that appears to have been under-used. I'd like to see the idea adopted into more mainstream use.

