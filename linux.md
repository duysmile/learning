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
  - Detail table of process:

![Detail Process](https://i.ibb.co/YRN8rRY/Screenshot-from-2020-04-03-17-52-29.png)

- Task Manager: `ps ux` - ps stands for 'process status'

![Task Manager](https://www.guru99.com/images/ps.png)
  
  - `ps PID`

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

















