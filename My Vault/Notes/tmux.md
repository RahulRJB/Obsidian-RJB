
# tmux


DATE:  05-06-24


Tags: [[Linux]]


# References:



## Intro:


Tmux is a terminal multiplexer, a fancy term for a tool that lets you manage multiple workspaces within a single terminal window. Here's how it works:

**Creating a Workspace:**

- Imagine your terminal window as a big canvas. Tmux lets you split this canvas into sections called **panes**. Each pane acts like a separate terminal, allowing you to run independent commands and applications side-by-side.
- You can further organize these panes by grouping them into containers called **windows**. Think of windows as tabs on your web browser, where you can switch between different sets of panes.

**Benefits of Tmux:**

- **Multitasking:** Run multiple commands or programs simultaneously without needing to open separate terminal windows.
- **Persistence:** Tmux sessions are persistent. Even if you close your terminal window or disconnect from a remote server, your tmux session with all your running programs remains active. You can simply re-attach to it later to pick up where you left off.
- **Improved Workflow:** Quickly switch between different tasks within your tmux session using keyboard shortcuts. This eliminates the need to constantly open and close new terminal windows.


## CODE:

Tmux usually uses prefix key combination (usually `Ctrl+b`) followed by specific commands. Here's how to create and switch between them:

**Creating Panes:**

- **Vertical Split:** Split the current pane vertically into two by pressing `prefix` followed by `%`.
- **Horizontal Split:** Split the current pane horizontally into two by pressing `prefix` followed by `"`.

**Creating Windows:**

- **New Window:** Create a new window altogether by pressing `prefix` followed by `c`. Tmux assigns a number to each window for easy identification.

**Switching Panes:**

- **Within Window:** Use the arrow keys (`Up`, `Down`, `Left`, `Right`) to navigate between panes within the current window.
- **Previous Pane:** Quickly switch to the previously active pane using `prefix` followed by `p`.

**Switching Windows:**

- **By Number:** Switch to a specific window by pressing `prefix` followed by `w` and then the window number (e.g., `prefix` followed by `w1` to switch to window 1).
- **Previous/Next:** Cycle through windows sequentially using `prefix` followed by `n` (next) or `prefix` followed by `p` (previous).


**Detach from tmux session (recommended):**

- This is the most common way to exit tmux. It allows you to resume the same session later without losing any work. 
	- Press the prefix key (by default, it's `Ctrl+b`).T
	- Then, press `d`.

- This will detach you from the current tmux session and return you to your terminal prompt. The tmux session will continue to run in the background.

**Kill the tmux session:**

- This option completely terminates the tmux session and any programs running within it. 
	- Press the prefix key (by default, it's `Ctrl+b`).
	- Then, type `:`. This enters the tmux command prompt.
	- Type `kill-session` and press Enter.

**Additional notes:**

- You can check for any running tmux sessions with the command `tmux ls`.
- To re-attach to a detached tmux session, use the command `tmux attach`.
- To see the current pane and window layout along with their corresponding numbers, press `prefix` followed by `i` (info).
- You can resize panes within a window by placing your cursor on the dividing border and dragging it left or right with the mouse.

