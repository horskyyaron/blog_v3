+++
date = 2025-11-26T19:34:01+02:00
lastmod = 2025-11-27T10:34:31+02:00
draft = false
title = 'Lastmod'
+++

What the hell is Lastmod? 

## Hugo

> Hugo is one of the most popular open-source static site generators. 
> With its amazing speed and flexibility, Hugo makes building websites fun again.


This is the description of Hugo from the [hugo](https://gohugo.io/) official website.
I couldn't agree more.\
This is also why I use it for this exact site that you're in :).
(Shout out to [congo](https://jpanther.github.io/congo/docs/) for making this beautiful theme!)

## Configuring an article page

The awesome thing about hugo is that it lets me focus on what matter most - writing.\
I don't need to handle that `useEffect`, or choosing which UI library is the best,
or dispatching an action using `Redux` to update the application state, or building that server 
that I be requesting some fancy calculations from, nope.
I just need to write markdown files.

Part of that file is called the **frontmatter**. This is the opening part of the file which
contains metadata about the post.
for example, for this post this is how, roughly, the frontmatter looks like:

```markdown
+++
date = 2025-11-26T19:34:01+02:00
lastmod = 2025-11-26T19:53:23+02:00
draft = true
title = 'Lastmod'
+++
```

That is why you see *26 November 2025* at the top of the page,
and next to it you see *Updated: 26 November 2025*.
The theme is using hugo capabilities to use that field and use it as it wishes, in
this example, it puts it right under the title.


End of background.

## Wasting time?

I found myself changing a post on three different days, each time I needed to change the 
`lastmod` so it will be, well, up to date and true to reality.\
I'm sure you see where this is going.\
"Well", I thought to myself, "Why should I be doing this manually, each time on every post that I change?"\
You are exactly right, me, you shouldn't be wasting your time on such nonsense, you should "waste" your
time on automating it!

## The solution 

One of the best things about neovim is that you can pretty much do whatever you want with it.
So the answer for the question "Can I do this on vim?" is pretty much:
> If you can script it, you can do it

The actual answer is "anything" but lets put it aside for now, that's not the main issue at hand.

So how do we do it? One solution can be: 

```lua
local function update_lastmod()
  -- getting the exact time of when this function is running
  -- then it does some text manipulation so it will have the frontmatter desired format.
  local ts = os.date("%Y-%m-%dT%H:%M:%S%z"):gsub("(%d%d)(%d%d)$", "%1:%2")

  -- scanning the lines of the current buffer (the file which i'm editing currently)
  local lines = vim.api.nvim_buf_get_lines(0, 0, -1, false)
  for i, line in ipairs(lines) do
    if line:match("^lastmod%s=") then
      -- updating the line to use the correct timestamp
      lines[i] = "lastmod = " .. ts
      break -- stop after first match
    end
  end

  -- overrides the current buffer lines with the updated lines which includes our change in the
  -- lastmod line.
  vim.api.nvim_buf_set_lines(0, 0, -1, false, lines)
end

-- This creates an auto command which will run on the event of 
--'BufWritePre'.
-- which means basically, 'just before you will save the changes in 
-- this buffer, run this command and then continue to save'.
-- it will run the callback function 'update_lastmod' 
-- which in turn will update the date, and then will continue to save.
vim.api.nvim_create_autocmd("BufWritePre", {
  pattern = { "*.md", "*.markdown" }, 
  callback = update_lastmod,
})
```

I'm sure there is a better, faster, more efficient, better looking solution. 
I started with trying out `sed` commands with the idea of creating a script, then create a 
keymap that will trigger the script.
I gave up on that and tried out a neovim solution in lua.
Between you and me? I asked the ChatGPT to help me. I changed some of his suggestions of course,
but it helped a ton.\
I know, I'm not proud of it, but it was fast and I always find myself mixing up the 
neovim API commands, since sadly, I stopped changing my neovim config daily just a while ago.

So, What is the purpose of this weird post? In not particular order:

### Know you tools.

By knowing neovim and neovim capabilities (a bit, this piece of software is huge and I'm far from an advanced vim user):

- keymaps
- autocommands
- use of operating system commands inside of neovim (use of the date command)
    - My first thought was to create a script and run it from inside of neovim.
    This is the use of external commands while in neovim. 
    Knowing this will let your imagination go wild. (try out `:read !ls` while in neovim :) 
    if you see it for the first time I'm sure your programmer's brain cogs will start running uncontrollably)

The goal of finding a string, changing it automatically by using operating system commands +
text manipulation, just before saving the file, can be achieved pretty easily.

Another cool example: grammar.

- another cool one is that English is not my native language, so I may trip here and there with grammar and typos.
"So?" you might ask, well, try entering `:set spell`, then start with `]s` and `[s` and `z=` you'll get the idea.
- So, whenever I write these posts, I have this setting turn on and it helps me when I'm typing just too damn fast and
unavoidably sometimes make mistakes.

### Automate 

If you're doing stuff over and over, just automate it man, do it.\
It is also really satisfying when you succeed, and here I'm talking about something small
as changing a line of text right? Think about automating other stuff in you developer workflow
or even your life

Just some ideas that pops in my head

- running `npm run dev` all the time? Create an alias, create a function and bind it to a keymap.
- I sometimes create a "new project" which is same process over and over:
    - create a directory in `~/projects` directory.
    - cd into it 
    - initialize git directory
    - create a `readme.md` file\

    So I created a script for it.

### Have fun

When you know your tools, know how to script, know how to mix different programs
capabilities, you can do a lot of very cool stuff.
