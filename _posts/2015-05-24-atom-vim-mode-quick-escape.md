---
layout: post
title:  "Escaping atom vim mode with kj"
date:   2015-05-24 14:10:00
categories: Editors Vim Atom
---

I've been using [Sublime](http://www.sublimetext.com/) with vim-mode and [vintageous](https://github.com/guillermooo/Vintageous) for a few years now.
However, I've decided to invest more time in customizing my editor for productivity and I don't feel comfortable doing that with a closed source editor.

I decided to give [Atom](https://atom.io/) a try since its OSS and is close to Sublime in terms of features and look and feel.  One of the first issues that I ran into when setting up [vim mode](https://github.com/atom/vim-mode) was quick escaping from insert mode with 'kj' (some people refer to this as 'Smash Escape').

There isn't a solution for this built into vim-mode itself.  However, I discovered a workaround in a few [github issues](https://github.com/atom/vim-mode/issues/334) (credit to [rdlugosz](https://github.com/rdlugosz) for figuring this out).

## The Solution

To get this working you simply need to update your `init.coffee`
and `keymap.cson` files like so:

{% highlight cson %}
#keymap.cson
'.editor.vim-mode.insert-mode':
  'j': 'exit-insert-mode-if-proceeded-by-k'
{% endhighlight %}

{% highlight coffee %}
#init.coffee
atom.commands.add 'atom-text-editor', 'exit-insert-mode-if-proceeded-by-k': (e) ->
  editor = @getModel()
  pos = editor.getCursorBufferPosition()
  range = [pos.traverse([0,-1]), pos]
  lastChar = editor.getTextInBufferRange(range)
  if lastChar != "k"
    e.abortKeyBinding()
  else
    editor.backspace()
    atom.commands.dispatch(e.currentTarget, 'vim-mode:activate-command-mode')
{% endhighlight %}

You'll have to restart atom for this change to take effect.  But once you do you should now be able to smash escape again.

Big thanks to all of the people working through these issues on
the [vim-mode](https://github.com/atom/vim-mode) repo!
