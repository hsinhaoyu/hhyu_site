---
title: "Heh, Emacs LISP function! Lemme give you a piece of advice!"
date: 2021-08-22T10:11:05+10:00
draft: false
tags: ["programming", "hack", "emacs", "LISP"]
---
Did you know that in some programming languages, you can give a function [a piece of advice](https://en.wikipedia.org/wiki/Advice_(programming))? The basic idea is this: if you are using an application or a library written by somebody else, what can you do if you need to modify the behavior of a particular function? You could modify its source code, if your version improves it for everybody. However, if you only want to customize it for your personal needs, a more lightweight solution might be desirable. Some dynamic languages allow you to give the function "a piece a advice" (that's an amusing terminology), which means that they allow you to wrap a piece of code around the function, giving you an opportunity to modify the input and the output. Everything will run as usual, except that your advice (i.e., the wrap around code) is evoked every time the advised function is called. You can advice a function even if you don't have access to its source code. 

This is a powerful mechanism for extending the functionality and lifespan of software. It is perhaps not surprising that Emacs, an editor famous for its extendibility, supports advising. In fact, the [advising facility](https://www.gnu.org/software/emacs/manual/html_node/elisp/Advising-Functions.html) for Emacs LISP (the scripting language of Emacs) is quite sophisticated. There are multiple APIs for advising Emacs LISP functions.

I recently ran into a situation where advising came in handy. I use the Emacs package [Org Roam](https://www.orgroam.com) to track the relationships among my notes, todo's, and journal entries. On top of that, I use [Deft mode](https://github.com/jrblevin/deft) to browse and find the Org Roam documents that I need. What Deft does is that it extracts a title and a summary from each of the documents, and display them in a pleasant interface. Deft had worked well for me... until the recent release of Org Roam V2. What happened is that to make tracking more efficient, Org Roam V2 inserts a "property block" at the beginning of the documents. A Org Roam document now begins with something that looks like this:

```
:PROPERTIES:
:ID: BA3FA15B-4C83-4C61-8A2B-0855DA8E26D8
:END:
```

Unfortunately, Deft extracts the title of a document from its first line. So after I upgraded to Org Roam V2, the titles of all my documents were displayed as `:PROPERTIES:`. This behavior is implemented in a function called  `deft-parse-title`. It's easy to make it skip the property block, but I didn't want to fork Deft. What I did instead was to give `deft-parse-title` an advice, which removes the block from the content of the document passed to `deft-parse-tiltle`:

```lisp
(defun hhyu/deft-title-preprocess (orig-func &rest args)
    (let* ((file (car args))
           (contents (cadr args))
           (m (string-match "^:END:$" contents)))
      (if m
          (let ((new-contents
                 (substring contents
                            (match-end 0)
                            (length contents))))
               (funcall orig-func file new-contents))
          (funcall orig-func file contents))))

(advice-add 'deft-parse-title :around #'hhyu/deft-title-preprocess)
```

As mentioned, there are multiple ways to advice a function in Emacs LISP. I use the `(advice-add ... :around ...)` construct because it is most intuitive to me. My advice to `deft-parse-title` is defined in `hhyu/deft-title-preprocess`. Whenever `deft-parse-title` is called, Emacs LISP first evokes `hhyu/deft-title-preprocess`, with the `args` argument assigned to the arguments of `deft-parse-title`, which are `title` (a file name) and `contents` (the content of the document as a string). I use a regular expression to locate the `:END:` tag, and use the match (`match-end`) to remove the `:PROPERTIES:` block. After the removal (stored in `new-contents`), the first line contains the title, so we can pass it to the original function (`orig-func`, which is assigned to `deft-parse-title`). So, I have managed to add preprocessing code to Deft without modifying its source code. I think it's pretty cool.
