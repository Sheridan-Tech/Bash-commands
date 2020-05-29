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
| `psaux` |  |
| `bg` |  |
| `fg` |  |
| `cd` |  |
| `alias` |  |
| `htop` |  |
| `dd` |  |
| `xxd` |  |
| `find . -name <filename>` |  |
| `rm -rf <relative path to filename>` |  |
| `grep` |  |
| `cut -d ":" -f1` | `<executable_path> \| grep "TextEndingWith:" \| cut -d ":" -f1` |
| `tee` |   `<executable_path> 2>&1 \| tee ~/outputfile.txt` |
| `history -d <command_number_listed_in_history>` | This is useful when you ran a command with a plaintext password as a parameter. |
| `$?` | Result=$?|

| `unbuffer <executable command> \| & tee ~/build.txt` | |



### Shortcuts

| Command | Description |
| ------- | ----------- |
| `Ctrl-W` |  |
| `Ctrl-U` |  |
| `Ctrl-C` |  |
| `Ctrl-X` |  |
| `Ctrl-R` |  |


### Misc

| Command | Description |
| ------- | ----------- |
| `. or source` |  |
| `Ctrl-C` |  |
| `Ctrl-X` |  |
| `Ctrl-R` |  |
