# Linux

## Process Management
- Run a process foreground -> press `Ctrl + Z` -> type `bg` -> to make it run background.

![Foreground to background](https://www.guru99.com/images/bg.jpg)

- Turn a process to foreground: `fg jobname`
  Example:
  - Launch 'banshee' music player
  - Stop it with the 'ctrl +z' command
  - Continue it with the 'fg' utility.
  
![Turn to foreground](https://www.guru99.com/images/fg.png)

- List all process: `top`

![List process](https://www.guru99.com/images/top.png)

  - press `q` to quit
  - press `d` then type your interval you want to `top` update process
  - press `u` then type `username` you want to track
  - press `k` then type `PID` of process you want to kill
  - Detail table of process:

![Detail Process](https://i.ibb.co/YRN8rRY/Screenshot-from-2020-04-03-17-52-29.png)

- `htop`: it's a newer version of `top`

![htop](https://miro.medium.com/max/1400/1*lAAYOlCkrmhYkbXLQ05Ovg.png)

  - some options can be used with `htop`:
    - `-d` Delay between updates, in tenths of seconds
    - `-u` USERNAME to show only specific user processes
    - `-t` show processes in tree view

- Task Manager: `ps ux` - ps stands for 'process status'

![Task Manager](https://www.guru99.com/images/ps.png)
  
  - `ps PID`
  - `pstree`

- Terminates a running process: `kill PID`
- Find a PID of a process: `pidof <process name>`

- Set priority to process (value from -20 - 19): Linux can run a lot of processes at a time, which can slow down the speed of some high priority processes and result in poor performance. To avoid this, you can tell your machine to prioritize processes as per your requirements. Default value of process is 0.
  - Set nice value to process `nice -n 'Nice value' process name`
  
![Nice](https://www.guru99.com/images/changing_niceness.png)
  
  - If there is some process already running on the system, then you can 'Renice' its value using syntax: `renice 'nice value' -p 'PID'`
  
![Top](https://www.guru99.com/images/renicing.png)

- Reports the free disk space(Hard Disk) on all the file systems: `df` or `df -h`

![Hard disk](https://www.guru99.com/images/df.png)

- Shows the free and used memory (RAM) on the Linux system: `free`

![Free memory](https://www.guru99.com/images/free.png)

  - `free -m` to display output in MB

  - `free -g` to display output in GB

- `who` to list all current users logged in Linux system.
  - `whoami` to show your username
  - `w` to show more info about you
















