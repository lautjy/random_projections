# Tmux cheatsheet

```
tmux list-sessions
# lists out every session, window, pane, its pid, etc.
tmux info


# creates a new tmux session named session_name
tmux new -s session_name

# attaches to an existing tmux session named session_name
tmux attach -t session_name

# switches to an existing session named session_name
tmux switch -t session_name

tmux detach (prefix + d)

# For example, if you see prefix + d below,
# that means you would first hit (and release)
# Control + b and then type d.


tmux new-window (prefix + c)
tmux select-window -t :0-9 (prefix + 0-9)
tmux rename-window (prefix + ,)

# splits the window into two vertical panes
tmux split-window (prefix + ")

# splits the window into two horizontal panes
tmux split-window -h (prefix + %)

# swaps pane with another in the specified direction
tmux swap-pane -[UDLR] (prefix + { or })

# selects the next pane in the specified direction
tmux select-pane -[UDLR]

# selects the next pane in numerical order
tmux select-pane -t :.+

# lists out every bound key and the tmux command it runs
tmux list-keys
# lists out every tmux command and its arguments
tmux list-commands

# reloads the current tmux configuration (based on a default tmux config)
tmux source-file ~/.tmux.conf
```

These are some of my must-haves in my tmux config:

```
# remap prefix to Control + a
set -g prefix C-a
unbind C-b
bind C-a send-prefix

# force a reload of the config file
unbind r
bind r source-file ~/.tmux.conf

# quick pane cycling
unbind ^A
bind ^A select-pane -t :.+
```
