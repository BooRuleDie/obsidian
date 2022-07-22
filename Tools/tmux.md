# Sessions in tmux

**Sessions** in tmux are **persistent**, which means programs that are running on tmux, will continue to run even if you get disconnected.

# Prefix
All commands on tmux starts with a prefix which is `CTRL + B`  by default.

# Starting tmux
```bash
tmux
```

When you enter command above in shell, following processes will be performed:
* A **session** will be created.
* A **window** will be created in the session.
* A **shell** will be initialized in the window.

# Creating a New Session
It's always a good idea to give a descriptive name to your session.
```bash
tmux new -s "session1"
```

![[Pasted image 20220721184303.png]]

# Detaching from tmux
You can always detach your tmux session and go back to your previous shell.
```keyboard
CTRL + B + D 
```
Don't try to press all keys at the same time, just enter **prefix(CTRL + B)** then press **D**.

![[Pasted image 20220721184500.png]]

# Re-attaching Detached tmux Session
In order to do that, first we need to find name of session. And we can figure it out by entering following command:
```bash
tmux ls
```

## Output:
![[Pasted image 20220721185115.png]]

First strings before colon are names of session. Let's attach "**another_session**".
```bash
tmux attach-session -t another_session
```

# Working with Windows and Panes
In order to create a new window with default shell we can type **PREFIX** and **C**:
```keyboard
CTRL + B + C
```

![[Pasted image 20220721185559.png]]

# Switch between Windows and Session
By entering `CTRL + B + W` , you can select any window to switch into. Note that, since all windows are shown here, you can also switch sessions.

![[Pasted image 20220721190231.png]]

You can also use windows' index number to change your curren window:
```keyboard
CTRL + B + <index number of window>

#example CTRL + B + 0 will change window as the very first window
```

# Go to Previous Window
```
CTRL + B + L
```

# Renaming Windows
In order to change current window, we can enter following command:
```
CTRL + B + ,
```

![[Pasted image 20220721191014.png]]
![[Pasted image 20220721191101.png]]

# Managing Panes

### Split pane horizontally:
```
CTRL + B + %
```

### Split pane vertically:
```
CTRL + B + "
```

![[Pasted image 20220721191607.png]]

### Go to next pane:
```
CTRL + B + O
```

### Toggle between previous and current pane:
```
CTRL + B + ;
```

### Close current pane:
```
CTRL + B + X
```

# Killing Sessions-Windows-Panes
In order to kill any of these, we can enter following command:
```bash
tmux kill<TAB>
```

It'll show you available options like so:
![[Pasted image 20220721193058.png]]

### Killing session named 4
![[Pasted image 20220721193141.png]]

# Copy Mode
In copy mode you can search strings in terminal as you do in vi interface. In order to enable copy mode, we need to enter following keybinding:
```keyboard
CTRL + B + [
```

Also we need to specify another configuration to use copy-mode as we use vi. Enter this command into tmux.conf:
```tmux.conf
# Copy-mode key style
setw -g mode-keys vi
```

* `/` : Search a string
* `n`: Go to next found string
* `N`: Go to previous found string

# Customizing tmux
The default configuration file of tmux is **tmux.conf**. This file contains some tmux commands that are executed whenever a tmux server starts. Tmux first look for this file in **/etc/tmux.conf**. If it can't find it, then it looks same file in home directory of user *(It's hidden here **.tmux.conf**)*.

# Reloading Conf File
After making changes we need to reload conf file as we source zshrc. And this is the command we're going to use for it:
```bash
tmux source ~/.tmux.conf
```

If you don't want to enter this command whenever you make changes on file, you can assign a key to do same thing. Open conf file and enter following:
```tmux.conf
# Reload tmux.conf file
bind r source-file ~/.tmux.conf \; display-message "Config Reloaded!"
```

Now we can open tmux, and enter **PREFIX+r** to reload conf file. It'll also display a informative message.
![[Pasted image 20220722104213.png]]

# Unbinding and Binding Keys

### Unbinding splitting panes 
```tmux.conf
# Pane Splitting (PREFIX + h|v)
unbind '"'
unbind %

bind h split-window -h
bind v split-window -v
```

Now in order to split pane horizontally, we need to enter **PREFIX + h**, and for vertically **PREFIX + v**.

# Enable Mouse Scroll
```tmux.conf
# Enabling mouse mode
set -g mouse on
```

After enabling this option, it'll automatically copy selected strings into clipboard.

# Rename Window
```tmux.conf
# Rename Window (PREFIX + n)
unbind ,
bind n command-prompt -I "#W" "rename-window '%%'"
```

# Relocate Status Bar
```tmux.conf
# Location of Status Bar
set-option -g status-position top
```

# Setting Base Indexes
```tmux.conf
# Setting base indexes to 1 instead of 0
set -g base-index 1
setw -g pane-base-index 1
```

# Changing Style of Status Bar
```tmux.conf
# Changing background color to black and font color to red
set-option -g status-style bg=black,fg=red
```

##### Other configurations that I've made can be found on local tmux.conf file or you can visit this [github](https://github.com/BooRuleDie/conf) page.

