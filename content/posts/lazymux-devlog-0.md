+++
date = '2025-12-06T22:15:54+02:00'
draft = false
title = 'Lazymux DevLog 0'
lastmod = 2025-12-06T22:58:18+02:00
tags = ["Tui", "Devlog"]
+++

## Background

I love using the terminal.\
If possible, and I have a found a neovim plugin for a certain task, that would be even better!\
Using the same tool without context switch is faster and more efficient solution, at least that's what works for me.\
And that is something I try to optimize for.

### Example 1: PR

I can go onto github, and manually open a pr, but that would be boring, of course 😃.\
That is why I use `gh pr create` which uses github cli tool and allows me to:
1. open a pr from my comfy and cozy terminal in seconds.
2. I can write the pr description in neovim, my editor of choice, which is cool.

### Example 2: storage management

I ran low on storage in my computer, so I needed to delete some stuff to make space.\
I started the traditional way, Apple -> setting -> storage.\
And that's when I found the horror of the **"calculating..."** message, which kept on going for several minutes.

This is unacceptable.

Solution: a TUI named [gdu](https://github.com/dundee/gdu) a fast disk usage analyzer written in Go.

Soon after running this program (forgive me, I didn't measure this but it was blazingly fast as the saying goes, I invite you to try out for yourself ⏲️) 
I knew the state of my storage, and quickly started deleting some unused files.

### Conclusion

I appreciate good TUI applications which allows me to stay in my terminal\
while achieving a non trivial task, otherwise achieved using a gui or a web browser.

Great examples for some glorious TUIs are [lazygit](https://github.com/jesseduffield/lazygit) and [lazydocker](https://github.com/jesseduffield/lazydocker), I
highly advise you to check them out.

## Lazymux

So, This is a project I'm building which is a TUI for managing tmux sessions for your projects.
This came to be from three main reasons:

1. [Opencode](https://opencode.ai/docs) and [OpenTui](https://github.com/sst/opentui)
2. The desire to build a TUI application 
3. Apply system architecture principles building the project.

The first point is how far you can go building a TUI. Which made me more curious than ever to build a TUI and now there is a 
library using typescript and react for building it, which is really cool.

The second point is that I've been wanting to build a TUI for some time now, and with the rise of this framework, it seems like a good time to do that.

I've been learning about software architecture and have been wanting to apply the principles I learn.
Now I have the project to do just that 👨‍🎨 




