# Linux

## GUI
- `Super` (Start in window) + `ArrowUp`: to maximize screen app.

## Normal
- `source <path-file>`: reload that file in terminal (Eg: any configuration file and no need to relaunch terminal)

## Process Management
- Run a process foreground -> press `Ctrl + Z` -> type `bg` -> to make it run background.
- Another way to run make a process run background is using the symbol `&`. Eg: `cloundcmd &`
- Use `jobs` command to see list processes sent to background.
- Keeping process after exiting its terminal `disown`
```shell
$ sudo rsync Templates/* /var/www/html/files/ &
$ jobs
$ disown -h %1
$ jobs
```
![Result](https://www.tecmint.com/wp-content/uploads/2016/10/Keep-Linux-Processes-Running.png)

- `nohup` can also keep process to continue running in bg when user exits a shell
```shell
$ nohup tar -czf iso.tar.gz Templates/* &
$ jobs
```

![Result](https://www.tecmint.com/wp-content/uploads/2016/10/Put-Linux-Process-in-Background.png)

- Detach a Linux process from controlling terminal 
> $ firefox </dev/null &>/dev/null &
  
  - In Linux, /dev/null is a special device file which writes-off all data written to it, in the command above, input is read from and output is sent to /dev/null.

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
  - press `d` then type your interval you want to `top` update process (default 3s)
  - press `u` then type `username` you want to track or type `top -u duy21`
  - press `k` then type `PID` of process you want to kill
  - press `Shift + o` to sort processes by field
  - press `z` to highlight running process
  - press `c` to show absolute path of processes
  - press `Shift + p` to sort processes as per **CPU** utilization
  - press `r` to change priority of process also called Renice.
  - press `h` to show help
  - to automatically exit after <time> number of repetition `# top -n 10`
  - to save output of top -> `# top -n 1 -b > top-output.txt`
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

- Set priority to process (value from -20 -> 19): Linux can run a lot of processes at a time, which can slow down the speed of some high priority processes and result in poor performance. To avoid this, you can tell your machine to prioritize processes as per your requirements.
  - A value of -19 is very high priority, while a value of 19 is very low priority. A value of 0 is the default priority.
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
  
- `whereis` to file path of program. Eg: `whereis google-chrome`
- `pgrep` to find PID of process by name. Eg: `pgrep firefox`, options `-l` to show process name
- `pkill` to kill process by name, kill all processes that matches the keyword search, before using pkil you should check all processes matches that keyword by using `pgrep -l <process-name>`
- `killall` to kill all processes by name
- `xkill` to kill graphical program. Run it and your cursor will turn into an x sign. Click a program’s window to kill that program. If you don’t want to kill a program, you can back out of xkill by right-clicking instead. You don’t have to run this command from a terminal — you can also press Alt-F2, type `xkill` and press Enter to use it from a graphical desktop.

- `pidof` to find pid ò a process
- `echo $$` to find PID of current shell
- `echo $PPID` to find parent process ID of current shell
























