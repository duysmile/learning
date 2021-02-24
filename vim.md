### Basic:
#### Terminal
- `Ctrl + Shift + '+'` = increase font-size
- `Ctrl + W + '-'` = decrease font-size

#### Move
- `h` = (ArrowLeft)
- `j` = (ArrowDown)
- `k` = (ArrowTop)
- `l` = (ArrowRight)

- `0` = Move to start of line
- `$` = Move to end of line
- `gg` or `0` = Move to start of file
- `G` = Move to end of file
- `<line-number>G` = Move to \<line-number\> line
- `Ctrl g` = Show info of current line, total line
- `<n>w` = move forward word by word
- `<n>b` = move backward word by word
- `<n>e` = move to end of word
- `Ctrl O` = goes backward
- `Ctrl I` = goes forward
- `CTRL-W CTRL-W` = to jump from one window to another.

#### Highlight

- `v` = highlight selected area with `h`, `j`, `k`, `l`.
- `V` = highlight current line
- `vap` = highlight current paragraph
- `ggVG` = highlight all file

#### Search
- `/<key-word>` = search down with keyword
- `?<key-word>` = search up with keyword
- `n` = continue searching
- `%` = to find start/close of character `(`,`)`,`[`,`]`, ... 

#### Search & Replace
- `:s/<word-to-find>/<word-to-replace>/<modifier>`: replace word-to-find to word-to-replace in modifier(eg: g - global, ...) mode 
- To change every occurrence of a character string between two lines,
  - type   :#,#s/old/new/g    where #,# are the line numbers of the range
                               of lines where the substitution is to be done.
  - Type   :%s/old/new/g      to change every occurrence in the whole file.
  - Type   :%s/old/new/gc     to find every occurrence in the whole file,
                               with a prompt whether to substitute or not.

#### Edit content
- `i` = move to insert mode
- `a`= move to insert mode with cursor after current cursor
- `r` = move to replace mode
- `R` = replace multiple character
- `x` = delete a character from position of pointer
- `Shift A` = move to end of line and move to insert mode

#### Delete content: `d <n>(optional) motion`
motion can be `w`, `e`, `$`
- `dw` = delete a word
- `d$` = delete to end of line from current point
- `dd` = remove a line (like `Ctrl X` a line)

#### Copy/Paste
- `y` = copy
- `p` = patse (after `dd`: paste a line deleted before) below cursor
- `:r <command or file>`: to retrieve output to current file

#### Save and exit
- `ESC` = exit mode insert or replace
- `:q` = exit Vim
- `:q!` = force exit Vim and don't save
- `:w` = save file
- `:w!` = force save file (override)
- `:wq` = save and exit

#### Open multiple files
- `vim <file1> <file2> ...` = open multiple files
  - `:next` = move to next file
  - `:previous` = move to previous file
- `vim -o/-O <file1> <file2> ...` = open multiple files in vertical/horizontal split
- `:split <file2>` or `:sp <file2>` = split file2 in horizotal when in working mode
- `:vsplit <file2>` or `:vsp <file2>` = split file2 in vertical when in working mode
  - `<line-vertical/line-horizontal>sp/vsp <file2>` = split file2 in a number of line in vertical or horizontal
  - `set splitbelow/splitright` = open split panes in bottom/right
- `Ctrl w =` =  Normalize file size panes resizing terminal
- `Ctrl w r` = Swap top/bottom left/right split
- `Ctrl w t` = break out current window into new tabview
- `Ctrl w o` = close all windows in current view but the current one
#### Navigate between file
- `Ctrl W <direction: H - left, J - down, K - up , L - right`: move to split file according direction

#### Undo/Redo
- `u` = undo 1 action
- `U` = undo the whole line
- `<n>u` = undo n actions
- `Ctrl R` = redo

#### Delete and move to insert mode `c motion`
- `c w` = delete word and move insert mode
- `c $` = delete to end of line and move insert mode

#### Combine count
 operator   [number]   motion
     where:
       operator - is what to do, such as  d  for delete
       [number] - is an optional count to repeat the motion
       motion   - moves over the text to operate on, such as  w (word),
                  $ (to the end of line), etc.


#### Execute command
- `:!<command>`: to execute an external command

#### set options
- Typing ":set xxx" sets the option "xxx".  Some options are:
        'ic' 'ignorecase'       ignore upper/lower case when searching
        'is' 'incsearch'        show partial matches for a search phrase
        'hls' 'hlsearch'        highlight all matching phrases
     You can either use the long or the short option name.

#### Show suggest for command
- Type the start of a command:  :e
- Press  CTRL-D  and Vim will show a list of commands that start with "e".







