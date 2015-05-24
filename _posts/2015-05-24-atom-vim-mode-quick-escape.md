---
layout: post
title:  "Escaping atom vim mode with kj"
date:   2015-05-24 14:10:00
categories: Editors Vim Atom
---
I've recently been trying out [Atom](https://atom.io/) and it's [vim mode](https://github.com/atom/vim-mode).  One of the first issues that I ran into when getting set up was quick escaping from insert mode with 'kj' (otherwise known as 'Smash Escaping').

Unfortunately naively trying to remap 'kj' to return to command mode blocks the event incorrectly and keeps you from being able to use the k key correctly.

There isn't a solution for this built into vim-mode itself.  However, I discovered a workaround in a few [github issues](https://github.com/atom/vim-mode/issues/334) (credit to [rdlugosz](https://github.com/rdlugosz) for figuring this out).

## The Solution

To get this working you simply need to update your `init.coffee` and `keymap.cson` files like so:

{% highlight coffee linenos %}
#keymap.cson
'.editor.vim-mode.insert-mode':
  'j': 'exit-insert-mode-if-proceeded-by-k'
{% endhighlight %}

{% highlight coffee linenos %}
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
