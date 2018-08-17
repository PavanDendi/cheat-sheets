# tmux cheat sheet

All commands start with ctrl+b.  This will be further referenced as "cmd".  A common modification of the default config is to change this to ctrl+a for easier typing and not conflicting with vim.

`cmd b` - denotes that cmd should be fully released before entering 'b'\
`cmd+b` - denotes that cmb should be held down while entering 'b'

## Session management

Sessions are persistent so that they remain running.  For example, you can connect remotely using ssh, start a tmux session, detach the session, and disconnect from ssh.  Upon reconnecting with ssh, reattaching to the existing tmux session will bring back all previously running programs.  It is also possible for two users to concurrently join the same session.

New session - `tmux`\
New named session - `tmux new -s named_session`\
**Reattach to last session - `tmux a`**\
Reattach to a named session - `tmux a -t named_session`\
**List sessions - `tmux ls`**\
**Detach from session - `cmd d`**\
Kill session - `tmux kill-session -t named_session`\
Rename session - `cmd $`\
Switch session\
&emsp;Show selectable list - `cmd s`\
&emsp;Previous session - `cmd (`\
&emsp;Next session - `cmd )`

## Window management

**New window - `cmd c`**\
**Rename window - `cmd ,`**\
**Previous window - `cmd p`**\
**Next window - `cmd n`**\
Select window by number - `cmd 0-9`\
Show selectable list - `cmd w`\
Kill window - `cmd &`

## Pane management

**Split horizontally - `cmd "`**\
**Split vertically - `cmd %`**\
**Change pane - `cmd arrow`**\
Change pane clockwise - `cmd o`\
Toggle last active - `cmd ;`\
**Resize pane - `cmd+arrow`**\
Kill pane - `cmd x`\
Convert pane to window - `cmd !`

## Configuration changes

Create/modify ~/.tmux.conf

### For tmux < 2.1

```
# Make mouse useful in copy mode
setw -g mode-mouse on

# Allow mouse to select which pane to use
set -g mouse-select-pane on

# Allow mouse dragging to resize panes
set -g mouse-resize-pane on

# Allow mouse to select windows
set -g mouse-select-window on
```

### For tmux > 2.1

```
set -g mouse on
```
