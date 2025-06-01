[Reference here](https://ctoomey.com/mastering-the-vim-language-slides.pdf),
[Video here](https://youtu.be/wlR5gYd6um0)

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
- `gg` - to the first line of the file
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
- `y` - copy
- `yy` - copy current line
- `p` - paste text *after* the cursor
- `P` - paste text *before* the cursor
- `>` - increase indent/tab
- `<` - decrease intent/tab

Nouns/Motions
- `w` - word/forward
- `b` word/back
- `f` + *symbol* (`f`+`(`) goes to the *next* symbol in the current line
- `F` + *symbol* (`F`+`(`) goes to the *previous* symbol in the current line
- `t` + *symbol* (`t`+`(`) goes before *next* symbol in the current line
- `t` + *symbol* (`T`+`(`) goes after the *previous* symbol in the current line
  - symbols - alphanumeric, brackets, math symbols, slashes, etc.
- `2j` - down 2 lines (use relative line numbers)
- `1k` - up 1 line

Nouns/Text objects
- `w` - change text from letter under cursor to the end of the word (*d_id*, `cw` *done* -> *done*)
- `iw` - inner word/entire word, no withespace (*d_id*, `ciw` *done* -> *one*)
- `aw` - entire word and whitespace
- `it` - inner tag/*HTML* tags for example
- `i'` - inner single quotes, text only
- `i"` - inner double quotes, text only
- `a'` - inner single quotes, text and quotes
- `a"` - inner double quotes, text and quotes
- `i{`, `i}`, `a{`, `a}` - `i` inner text only, `a` includes *{* or *}*
- `i[`, `i]`,`a[`, `a]` - inner text only, `a` includes *[* or *]*
- `i(`, `i)`, `a(`, `a)` - inner text only, `a` includes *(* or *)*
- `ip` - inner paragraph without whitespaces around it
- `ap` - the paragraph and trailing newlines and whitespaces
- `is` - the sentence the cursor is in, without extra space or punctuation
- `as` - a sentence including punctiation and spacing

File/buffer management
- `:e` *filename* to open a file
- `:e` to create an empty buffer/file
- `:w` *filename* saves to a different file
- `:w!` to overwrite existing file
- `:badd` *filename* to add it to the buffer list without opening it
- `:ls` list open files/buffers
- `:b` *buffer number* switches to it
- `:b` *text* switches to file with *text* in its name
- `:bn` and `:bp` to switch to the next or previous file
- `Ctrl+^` switches between the two most recent files/buffers
- `:bd` closes the current buffer/file
- `:bd` *number* closes buffer/file with *number*

File tabs
- `:tabs` shows open tabs
- `:tabe` *filename* opens it in a new tab
- `:tabc` closes the current tab
- `:tabonly` closes all other tabs
- `gt`/`:tabnext` and `gT`/`:tabprevious` to to go to the next/previous tab
- 

Splits/creation
- `:sp` or `:split` splits the current window into two panes, top and bottom
- `:sp` *filename* opens *filename* in new split
- `:vs` or `:vsplit` splits the current window into two panes, left and right
- `:vs` *filename* does the same by opening *filename*
Splits/navigation
- `Ctrl+w` + `h` - move left
- `Ctrl+w` + `l` - move right
- `Ctrl+w` + `k` - move up
- `Ctrl+w` + `j` - move down
- `Ctrl+w` + `up`/`down`/`left`/`right` arrows does the same
Splits/resize
- `Ctrl+w` + `>`/`<` to increase/decrease witdth
- `Ctrl+w` + `+`/`-` to increase/decrease height
- `Ctrl+w` + `=` to make windows of equal size
Splits/closing
- `:q` to close current window
- `:only` to close other windows/splits, except the current one
Splits/other
- `Ctrl+w` + `r` to swap splits
- `:10sp` *filename* to open a horizontal split of *filename* with height *10*
- `:30vs` *filename* for a vertical split with *30* colums of *filename*
- `:help split` to get info on the command

Terminal
- `:term` launches a terminal/shell session as a window split
- use `Ctrl+w` + `h`/`j`/`k`/`l` to move between the splits
