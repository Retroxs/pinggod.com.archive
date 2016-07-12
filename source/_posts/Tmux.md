title: Tmux
date: 2016-02-13 13:09:06
desc: basic knowledge of tmux
---

Recently, I am learning some different programming languages which exciting and a little bit tired. Why tired? Coding, testing code and taking notes always need two or three panes in one shell window for each programming language every time, and this is where tmux comes in.

![tmux-demo](/img/tmux-structure.png)

> Tmux is a terminal multiplexer, and it lets developers switch easily between programs in one termial, detach them and reattach them to a different terminal. 

<!-- more -->

## Key bindings

The GitHub repo [.tmux](https://github.com/gpakosz/.tmux) is a pretty and versatile self-contained tmux configuration which I am using. `C-a` is the prefix key provided by this repo, while we can keep use default `C-b` prefix. The following tables show common key bindings which come into play with prefix key, from session to pane:

**SESSION KEY BINGDINGS**

- `tmux ls`，显示所有 session
- `tmux new -s session-name`，新建 session
- `tmux attach -t session-name`，进入 session
- `tmux kill-session -t session-name`，关闭 session

| Key | Description |
|:---:|:------------|
| C-f | find session |
| d   | hang-up session |
| r   | reload configuration | 

**WINDOW KEY BINGDINGS**

| Key | Description |
|:---:|:------------| 
| c   | create new window |
| ,   | rename current window |
| s   | list all windows |
| f   | find window |
| 0~9 | switch window according to serial number |
| &   | exit tmux and close current window |
| space | adjust layout |

**PANE KEY BINGDINGS**

| Key | Description |
|:---:|:------------| 
| %   | create new pane with horizontal direction |
| "   | create new pane with vertical direction |
| hjkl | switch pane |
| HJKL | adjust layout size of panes |
| o   | cycle panes |
| q   | display serial number of panes |
| x   | close current pane |
| {/} | switch postion of panes |
| Enter | enter copy-mode |
| l   | clear pane and history |
