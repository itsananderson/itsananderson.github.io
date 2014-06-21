---
layout: post
title: "Editing Vim Macros"
date: 2014-06-20 22:37:17 -0700
comments: true
categories: Vim
---

Vim macros are great.
If you're not familiar with them, there's a great [Vim tips article](http://vim.wikia.com/wiki/Macros).

Something cool I've discovered is that you can edit macros.
This is helpful if you accidentally mess up a macro recording, or decide you need to tweak it in some way.

If you recorded a macro to register `a`, you can paste its contents into the current document with `"ap`.
From there, you can edit the macro and then yank it back into the `a` register with `"ay`

This makes it easy to paste your macros into your vimrc for future use.

```vim
" delete first word of line
let @d='0dw'
```