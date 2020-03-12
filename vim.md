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
- `gg` = Move to start of file
- `G` = Move to end of file
- `<line-number>G` = Move to \<line-number\> line
- `Ctrl g` = Show info of current line, total line
- `<n>w` = move forward word by word
- `<n>b` = move backward word by word
- `<n>e` = move to end of word

#### Highlight

- `v` = highlight selected area with `h`, `j`, `k`, `l`.
- `V` = highlight current line
- `vap` = highlight current paragraph
- `ggVG` = highlight all file

#### Search
- `/<key-word>` = search down with keyword
- `?<key-word>` = search up with keyword
- `n` = continue searching
- `Ctrl O` = return back to current point
- `%` = to find start/close of character `(`,`)`,`[`,`]`, ... 

#### Search & Replace
- `:s/<word-to-find>/<word-to-replace>/<modifier>`: replace word-to-find to word-to-replace in modifier(eg: g - global, ...) mode 

#### Edit content
- `i` = move to insert mode
- `r` = move to replace mode
- `x` = delete a character from position of pointer
- `Shift A` = move to end of line and move to insert mode

#### Delete content: `d <n>(optional) motion`
motion can be `w`, `e`, `$`
- `dw` = delete a word
- `d$` = delete to end of line from current point
- `dd` = remove a line (like `Ctrl X` a line)

#### Copy/Paste
- `yy` = copy
- `p` = patse (after `dd`: paste a line deleted before)

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
- `:u` = undo 1 action
- `:<n>u` = undo n actions
- `Ctrl R` = redo

#### Delete and move to insert mode `c motion`
- `c w` = delete word and move insert mode
- `c $` = delete to end of line and move insert mode


















