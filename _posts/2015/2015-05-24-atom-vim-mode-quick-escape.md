---
layout: post
title:  "Escaping atom vim mode with kj"
date:   2015-05-24 14:10:00
categories: Editors Vim Atom
redirect_from:
  - /editors/vim/atom/2015/05/24/atom-vim-mode-quick-escape
---
I've recently been trying out [Atom](https://atom.io/) and it's [vim mode](https://github.com/atom/vim-mode).  One of the first issues that I ran into was quick escaping from insert mode with 'kj' (otherwise known as 'Smash Escaping'). Unfortunately naively trying to remap 'kj' to return to command mode blocks the event and breaks the 'k' key.

There isn't a solution for this built into vim-mode itself.  However, I discovered a workaround in a few [github issues](https://github.com/atom/vim-mode/issues/334) (credit to [rdlugosz](https://github.com/rdlugosz) for figuring this out).

## The Solution

To get smash escape working you'll need to update your `init.coffee` and `keymap.cson` files like so:

```coffee
#keymap.cson
'.editor.vim-mode.insert-mode':
  'j': 'exit-insert-mode-if-proceeded-by-k'
```

```coffee
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
```

You'll have to restart atom for this change to take effect.  But once you do you should now be able to smash escape again.  While this change is specific to 'kj', changing the key combo should be straightforward.

Big thanks to all of the people working through these issues on
the [vim-mode](https://github.com/atom/vim-mode) repo!
