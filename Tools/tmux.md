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
