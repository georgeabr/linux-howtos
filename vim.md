[Reference here](https://ctoomey.com/mastering-the-vim-language-slides.pdf)

### In command/normal mode

Basic movement:
- `h` - move left
- `l` - move right
- `k` - move up
- `j` - move down

Word navigation:
- `w` - to start of the next word
- `b` - to the start of the previous word
- `r` - to the end of a word

Line navigation:
- `0` - to the beginning of the current line
- `^` - to the first non-blank character of a line
- `$` - to the end of the line

File navigation:
- `gg` - to thhe first line of the file
- `G` - to the last line of the file
- `nG` - to line number (`21G` goes to line *22*)

Scrolling:
- `Ctrl+F` - scroll down one screen
- `Ctrl+B` - scroll up one screen
- `zz` - centre current line on screen

Edit commands:
Delete:
- `x` - delete character under the cursor
- `dd` - delete entire line
- `dw` - delete from cursor to the start of new word
- `D` - delete from cursor to the end of line

Undo/redo/change case
- `u` - undo last change
- `Ctrl+R` - redo the undone change
- `.` - repeat last command
- `~` - toggle case of character under cursor

Visual mode/selection
- `v` - select one character at a time
- `V` - select one line at a time

### Command-line mode
- `:` - enter command-line mode
- `:w` - save the file
- `:q` - quit Vim
- `:q!` - quit without saving
- `:wq` - save and quit
- `:%s/old/new/g` - replace all matches of *old* with *new* in the file

Search and jump:
- `/pattern` - search down for *pattern*
- `?pattern` - search up for *pattern*
- `n` - repeat last search in the same direction
- `N` - repeat last search in the opposite direction
- `%` - jump to matching bracket

Other commands:
- `:!command` - run an external command
- `:help keyword` - help for Vim keyword


### Another approach
Verbs in Vim
- `d` - delete  
- `c` - change (delete and enter insert mode)  
- `>` - increase indent
- `<` - decrease intent

Nouns/Motions
- `w` - word/forward
- `b` back/word
- `2j` - down 2 lines

Nouns/Text objects
- `iw` - inner word/inside a word
- `it` - inner tag/*HTML* tags for example
- `i"` - inner quotes
- `i{`, `i}` - inner *{* or *}*
- `i[`, `i]` - inner *[* or *]*
- `i(`, `i)` - inner *(* or *)*
- `ip` - inner paragraph
- `as` - a sentence
