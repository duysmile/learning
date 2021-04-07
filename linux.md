# Linux

## GUI
- `Super` (Start in window) + `ArrowUp`: to maximize screen app.
- Các phím tắt thường sử dụng trong Terminator:
  - `Ctrl+Shift+O` chia terminal theo chiều ngang
  - `Ctrl+Shift+E` chia terminal theo chiều dọc
  - `Ctrl+Shift+Right` di chuyển qua cửa sổ bên phải
  - `Ctrl+Shift+S` Ẩn / hiện thanh cuộn
  - `Ctrl+Shift+N` hoặc `Ctrl+Tab` chuyển cửa sổ theo thứ tự từ trái qua phải từ trên xuống dưới
  - `Ctrl+Shift+P` hoặc `Ctrl+Shift+Tab` chuyển cửa sổ theo tứ tự từ phải qua trái từ dưới lên trên
  - `Alt+UpMove` chuyển qua khung lệnh phía trên khung cửa sổ hiện tại


## Terminal
- `Ctrl W`: remove a word from right to left.
- `Ctrl L`: same `clear` command
- `Ctrl <Left/Right/Top/Bottom>`: move through a word
- `Ctrl Shift W` or `Ctrl D`: close current tab terminal
- `history`: show all used commands
- `Ctrl R`: show history of current command

## Normal
- `/etc/apt/sources.list`: source list apt
- `source <path-file>`: reload that file in terminal (Eg: any configuration file and no need to relaunch terminal)
- `pwd`: print working directory -> print current directory in terminal
- `ls`: list -> list all file and directory in current path
  - `-a`: all
- `man <command>`: show all guide of this command
- `tee`: copy standard input to each file, and also to standard output
  - `[command] | tee -a [file]`
  - Eg: `ping google.com | tee output.txt`
  
  ![tee](https://www.howtoforge.com/images/command-tutorial/ping-with-tee.png?ezimgfmt=rs:500x327/rscb1/ng:webp/ngcb1)
- `wc -l`: count entries
  - Combine with `tee`: `ls | tee | wc -l` -> show number of files in current dir
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

- `pidof` to find pid of a process
- `echo $$` to find PID of current shell
- `echo $PPID` to find parent process ID of current shell


### Debug script
- options `-x` to debug script. Eg: `bash -x hello.sh`

### Listing
- `ls` command
Option	Description
- -a, --all	Liệt kê tất cả các mục bao gồm những mục . và ..
- -A, --almost-all	Liệt kê tất cả các mục trừ mục . và ..
- -c	Sắp xếp các file theo thời gian thay đổi
- -d, --directory	Danh sách chỉ gồm các folder
- -h, --human-readable	Hiển thị kích thước ở định dạng con người có thể đọc được (tức là K, M) (đi kèm option s ví dụ -sh)
- -H	Tương tự như trên nhưng với tỉ lệ quy đổi là 1000 thay vì 1024
- -l	Hiển thị nội dung với định dạng long-listing
- -o	Long -listing format nhưng không có thông tin group
- -r, --reverse	HIển thị nội dung nhưng với sự sắp xếp ngược lại
- -s, --size	Danh sách cách tệp với kích thước của từng thành phần
- -S	Sắp xếp theo kích thước file
- --sort=WORD	Sắp xếp danh sahcs file theo từ khóa, ví dụ: size, version,status
- -t	Sắp xếp theo thời gian sửa đổi
- -u	Sắp xếp theo thời gian truy cập
- -v	Sắp xếp theo version
- -1	Danh sách nhưng với 1 file một dòng

- `ls -l` -> `drwxr-xr-x 3 root root 4096 Apr 21 03:44 acpi`
Explain:
- d	File type (xem ở bảng dưới đây)
- rwxr-xr-x	Permission string
- 3	Number of hard links
- root	Owner name
- root	Owner group
- 4096	File size in bytes
- Apr 21 03:44	Modification time
- acpi	File name

File type:
- -	Regular file
- b	Block special file
- c	Character special file
- C	High performance ("contiguous data") file
- d	Directory
- D	Door (special IPC file in Solaris 2.5+ only)
- l	Symbolic link
- M	Off-line ("migrated") file (Cray DMF)
- n	Network special file (HP-UX)
- p	FIFO (named pipe)
- P	Port (special system file in Solaris 10+ only)
- s	Socket
- ?	Some other file type

### Command:
- `tr A-Z a-z <file.txt`: replace all uppercase to lowercase
- `grep`: search file (line) include word
  - `-v`: to search file exclude word


### Job handling
Creating jobs

Để tạo một job, chỉ cần thêm một kí tự & duy nhất và command:

```
$ sleep 10 &
[1]20024
```
Bạn cũng có thể biến một process đang chạy thành một job bằng cách nhấn Ctrl + z :

```
$ sleep 10
^Z
[1]+  Stopped                 sleep10
```
Background và Foreground một process Để đưa Process thành foreground, command fg được sử dụng cùng với %

```$ sleep 10 &
[1] 20024
$ fg %1
sleep 10
```
Bây giờ bạn có thể tương tác với process. Để đưa nó trở lại background, bạn có thể sử dụng bg. Do terminal session được cài đặt sẵn, trước tiên bạn cần dừng process bằng cách nhấn Ctrl + z.

```
$ sleep 10
^Z
[1]+ Stopped       sleep 10
$ bg %1
[1]+ sleep 10 &
```
Nếu bạn chỉ có một proces hoặc đối với process đầu tiên trong danh sách, bạn chỉ cần % là đủ:

```
$ sleep 10 &
[1] 20024
$ fg %
 \# to bring a process to foreground 'fg %' is also working.
sleep 10
```
hoặc chỉ cần

```
$ %
\# laziness knows no boundaries, '%' is also working.
sleep 10
```
Ngoài ra, chỉ cần nhập fg hoặc bg mà không có bất kỳ đối số nào sẽ xử lý job cuối cùng:

```
$ sleep 20 &
$ sleep 10 &
$ fg
```

```sleep^C
$ fg
sleep10
20
```
Killing jobs đang chạy

```
$ sleep 10 &
[1] 20024
$ kill %1
[1]+ Terminated     sleep 10
```
Kill một process cụ thể Có lẽ cách dễ nhất để kill một process đang chạy là chọn nó thông qua tên của nó như trong ví dụ sau, bằng cách sử dụng pkill:

```
pkill -f test.py
```
(hoặc) một cách nữa là sử dụng pgrep để tìm kiếm id process:

```
kill $(pgrep -f 'python test.py')
```
### Kiểm tra xem process nào đang hoạt động ở cổng bất kì
Để kiếm tra process đang chạy ở cổng 8080 bạn làm cách này:

```
lsof-i :8080
```

### List ra các job hiện tại
```
$ tail -f /var/log/syslog > log.txt
[1]+ Stopped       tail -f /var/log/syslog > log.txt
$ sleep 10 &
$ jobs
[1]+ Stopped       tail -f /var/log/syslog > log.txt
[2]- Running       sleep 10 &
```

### System signal
- Shell sử dụng cơ chế giao tiếp của UNIX gọi là signal để truyền thông tin tới process. Khi process nhận một signal nó sẽ dừng việc thực thi, xử lí signal đso và có khả năng sẽ thay đổi luồng thực thi dựa trên thông tin của signal nhận được. Vì vậy, signal được gọi là software interrupts (ngắt phần mềm).
- Một số signal có thể được handle bởi application nên có thể sẽ hoạt động khác nhau với mỗi process khác nhau
- Vd: như khi nhấn `Ctrl C` để thoát chương trình, nhưng nhiều khi nó ko hoạt động như chúng ta mong muốn. Lí do là vì khi nhấn `Ctrl C` thì shell sẽ gửi `SIGINT` signal tới process, nên nếu process có cài đặt phần xử lí signal này thì có thể bỏ qua nó và ko ngắt chương trình, thay vào đó phải dùng `SIGQUIT` signal bằng cách gõ `Ctrl \` để stop process.
- `SIGKILL` là một signal đặc biệt vì nó ko thể được capture bởi process và nó luôn huỷ process ngay lập tức. Nhưng nó cũng có những side effect ko tốt đi kèm như là để những process con "bơ vơ".
- Có thể xem thêm những signal khác tại [đây](https://en.wikipedia.org/wiki/Signal_(IPC))













