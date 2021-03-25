# Bash-commands
_A list of some of my favorite Bash commands and tricks_

___

### stop

| Command | Description |
| ------- | ----------- |
| `sudo killall <executable name>` | kill application |
| `sudo pkill <executable name>` | kill application |
| `sudo kill $(pidof <executable name>)` | kill application |

### applications

| Command | Description |
| ------- | ----------- |
| `cd -` | Return to previous directory |
| `alias` |  |
| `htop` |  |
| `dd` |  |
| `xxd` |  |
| `hexdump` |  |
| `find . -name <filename>` |  |
| `rm -rf <relative path to filename>` |  |
| `grep` |  |
| `cut -d ":" -f1` | `<executable_path> \| grep "TextEndingWith:" \| cut -d ":" -f1` |
| `tee` |   `<executable_path> 2>&1 \| tee ~/outputfile.txt` |
| `history -d <command_number_listed_in_history>` | This is useful when you ran a command with a plaintext password as a parameter. |
| `less -R  ~/build.txt` |  |
| `unbuffer <executable command> \| & tee ~/build.txt` | |
| `tree` | |
| `ln -s source_file_or_dir symbolic_link` | Create a symbolic link to a file or directory |
| `readlink -f symbolic_link` | Print full path to symbolic link |
| `$?` | Equal to the return of the previously executed command, result=$? is useful in bash scripting|
| `!!` | "BangBang" rerun the previous command |
| `!string` |  Print the last command beginning with string |

     



https://ss64.com/bash/bang.html
### GNU Readline Shortcuts

| Command | Description |
| ------- | ----------- |
| `Ctrl-A` | Moves the cursor to the line start equivalent to HOME |
| `Ctrl-W` | Clears the word before the cursor and copies it into the clipboard |
| `Ctrl-U` | Clears the line content before the cursor and copies it into the clipboard |
| `Alt-D` | Clears the line content after the cursor and copies it into the clipboard |
| `Ctrl-Y` | (yank) adds the clipboard content from the cursor position |
| `Ctrl-X` |  |
| `Ctrl-R` | Reverse search through command history, continued presses move up or those altered by SHIFT move down through the history |

### Process control

| Command | Description |
| ------- | ----------- |
| `psaux` |  |
| `bg` |  |
| `fg` |  |
| `Ctrl-C` | Sends the signal SIGINT to the current task, which aborts and closes it |
| `Ctrl-Z` | Sends the signal SIGTSTP to the current task, which suspends it. To execute it in background one can enter bg. To bring it back from background or suspension fg ['process name or job id'] (foreground) can be issued. |

### Misc

| Command | Description |
| ------- | ----------- |
| `. or source` |  |
