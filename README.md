# Pro Bash Command List
A practical, battle-tested Bash cheat sheet with workflows you will actually use. Copy, fork, and adapt.

## Table of contents
- [Shell fundamentals](#shell-fundamentals)
- [Navigation](#navigation)
- [Files and directories](#files-and-directories)
- [Viewing and inspecting](#viewing-and-inspecting)
- [Search and replace](#search-and-replace)
- [Text processing](#text-processing)
- [Find, xargs, and bulk operations](#find-xargs-and-bulk-operations)
- [Archives and compression](#archives-and-compression)
- [Permissions and ownership](#permissions-and-ownership)
- [Disk, filesystem, and mounts](#disk-filesystem-and-mounts)
- [Processes, jobs, and signals](#processes-jobs-and-signals)
- [Networking](#networking)
- [HTTP, APIs, and downloads](#http-apis-and-downloads)
- [SSH and remote workflows](#ssh-and-remote-workflows)
- [Logs and troubleshooting](#logs-and-troubleshooting)
- [Time, scheduling, and cron](#time-scheduling-and-cron)
- [Git productivity](#git-productivity)
- [tmux productivity](#tmux-productivity)
- [Docker quick hits](#docker-quick-hits)
- [Safe scripting patterns](#safe-scripting-patterns)
- [Everyday “recipes”](#everyday-recipes)

---

## Shell fundamentals

### Know your shell, path, and environment
```bash
echo "$SHELL"
echo "$PATH" | tr ':' '\n'
env | sort
export VAR="value"
unset VAR
```

### History, completion, and quality-of-life
```bash
history | tail -n 50
history | grep -i "ssh"
!!          # repeat last command
!$          # last argument of previous command
^old^new    # quick substitution in previous command
```

### Safer defaults in scripts
```bash
set -euo pipefail
IFS=$'\n\t'
shopt -s nullglob globstar
```

### Redirects and pipes
```bash
cmd > out.txt              # stdout to file
cmd 2> err.txt             # stderr to file
cmd > out.txt 2>&1         # stdout + stderr
cmd 2>&1 | tee out.log     # view + save
cmd < input.txt            # file to stdin
cmd1 | cmd2 | cmd3         # pipeline
```

---

## Navigation
```bash
pwd
ls -lah
cd /path/to/dir
cd -              # previous directory
pushd /tmp
popd

# Quick folder sizes in current dir
du -sh ./* 2>/dev/null | sort -h
```

---

## Files and directories

### Create, copy, move, remove
```bash
mkdir -p a/b/c
touch file.txt
cp file.txt copy.txt
cp -a src/ dest/            # preserve attributes
mv old new
rm file.txt
rm -r dir/
rm -rf dir/                 # dangerous, double-check paths
```

### Links
```bash
ln file hardlink
ln -s /absolute/path symlink
readlink -f symlink
```

### Compare files and directories
```bash
diff -u a.txt b.txt | less
cmp a.bin b.bin
comm -3 <(sort a.txt) <(sort b.txt)
rsync -avn src/ dest/       # dry-run directory diff style
```

---

## Viewing and inspecting

### Read files safely
```bash
cat file
less -RF file               # better than cat for big files
head -n 50 file
tail -n 50 file
tail -f app.log
```

### File metadata
```bash
stat file
file file
wc -l file
nl -ba file | head
```

### Quick hex / binary inspection
```bash
xxd -g 1 file.bin | head
hexdump -C file.bin | head
strings file.bin | head
```

---

## Search and replace

### Grep (fast searching)
```bash
grep -RIn "needle" .
grep -RIn --exclude-dir=.git --exclude='*.min.js' "needle" .
grep -RInE "foo|bar|baz" .
grep -RIn "TODO" . | head
```

### Ripgrep (recommended if installed)
```bash
rg -n "needle" .
rg -n --hidden --glob '!.git/*' "needle"
rg -n "TODO|FIXME|HACK" .
```

### Sed (stream edits)
```bash
sed -n '1,120p' file
sed 's/old/new/g' file
sed -i.bak 's/old/new/g' file    # macOS safe pattern
# GNU sed:
# sed -i 's/old/new/g' file
```

### Perl for complex edits
```bash
perl -pe 's/old/new/g' file
perl -0777 -pe 's/foo.*?bar/baz/gs' file
```

---

## Text processing

### Columns and fields
```bash
cut -d',' -f1,3 file.csv
awk -F',' '{print $1,$3}' file.csv
awk 'NR==1{print; next} {sum+=$2} END{print sum}' data.txt
```

### Transform
```bash
tr '[:lower:]' '[:upper:]' < file
tr -d '\r' < windows.txt > unix.txt
```

### Sort, uniq, count
```bash
sort file
sort -u file
sort file | uniq -c | sort -nr | head
```

### Join-like operations
```bash
join <(sort a.txt) <(sort b.txt)
paste -d',' a.txt b.txt
```

### JSON (jq)
```bash
jq '.' data.json
jq -r '.items[] | .id' data.json
jq -r '.items[] | [.id,.name] | @csv' data.json
```

---

## Find, xargs, and bulk operations

### Find by name, time, size
```bash
find . -name "*.log"
find . -type f -mtime -7
find . -type f -size +100M
```

### Safe bulk delete (preview first)
```bash
find . -type f -name "*.tmp" -print
find . -type f -name "*.tmp" -delete
```

### xargs patterns
```bash
find . -type f -name "*.txt" -print0 | xargs -0 wc -l
rg -l "needle" . | xargs -I{} sed -i.bak 's/old/new/g' "{}"
```

### Rename in bulk (examples)
```bash
# Replace spaces with underscores (Linux rename variant differs by distro)
find . -depth -name "* *" -print0 | while IFS= read -r -d '' f; do
  mv -v "$f" "${f// /_}"
done
```

---

## Archives and compression

```bash
tar -czf archive.tgz dir/
tar -xzf archive.tgz
tar -tf archive.tgz | head

zip -r archive.zip dir/
unzip archive.zip

gzip -k file        # keep original
gunzip file.gz
```

---

## Permissions and ownership

```bash
chmod 644 file
chmod 755 script.sh
chmod -R u+rwX,go+rX dir/

chown user:group file
chown -R user:group dir/

umask
umask 022
```

ACLs (if enabled):
```bash
getfacl file
setfacl -m u:alice:rw file
```

---

## Disk, filesystem, and mounts

```bash
df -h
du -sh ./* 2>/dev/null | sort -h
lsblk
mount | column -t
```

Largest files and folders:
```bash
# Largest files
find . -type f -printf '%s\t%p\n' 2>/dev/null | sort -nr | head -n 20

# Largest directories (top-level)
du -sh ./* 2>/dev/null | sort -h | tail -n 20
```

---

## Processes, jobs, and signals

```bash
ps aux | less
top                       # or htop if installed
pgrep -af "python|node"
pkill -f "pattern"

kill -TERM <pid>
kill -KILL <pid>          # last resort

cmd &                     # background
jobs -l
fg %1
bg %1
disown -h %1
```

Resource checks:
```bash
free -h                   # Linux
vm_stat                   # macOS
uptime
```

---

## Networking

### Interfaces, routes, DNS
```bash
ip a                      # Linux
ip r
ifconfig                  # macOS (legacy)
route -n                  # Linux
netstat -rn               # macOS

dig example.com +short
nslookup example.com
```

### Ports and listeners
```bash
lsof -i :8080
ss -lntp                  # Linux
netstat -anv | grep LISTEN   # macOS
```

### Connectivity
```bash
ping -c 4 1.1.1.1
traceroute example.com
mtr example.com           # if installed
```

---

## HTTP, APIs, and downloads

### curl essentials
```bash
curl -I https://example.com
curl -sS https://example.com | head
curl -sS -o file.bin https://example.com/file.bin
curl -L -o file.bin https://example.com/redirected

# JSON API call
curl -sS https://api.example.com/items | jq '.'

# POST JSON
curl -sS -X POST https://api.example.com/items \
  -H 'Content-Type: application/json' \
  -d '{"name":"test"}'
```

### Verify checksums
```bash
sha256sum file.bin
shasum -a 256 file.bin    # macOS
```

---

## SSH and remote workflows

### Basics
```bash
ssh user@host
ssh -p 2222 user@host
scp file user@host:/path/
rsync -avP dir/ user@host:/path/dir/
```

### Keys and agent
```bash
ssh-keygen -t ed25519 -C "you@example.com"
ssh-add ~/.ssh/id_ed25519
ssh-copy-id user@host      # Linux (macOS may need brew install)
```

### SSH config (~/.ssh/config)
```sshconfig
Host mybox
  HostName 203.0.113.10
  User ubuntu
  IdentityFile ~/.ssh/id_ed25519
  ServerAliveInterval 30
  ServerAliveCountMax 3
```

### Tunneling
```bash
# Local port 5432 to remote 5432
ssh -L 5432:localhost:5432 user@host

# Remote port 8080 back to your machine 8080
ssh -R 8080:localhost:8080 user@host
```

---

## Logs and troubleshooting

```bash
tail -n 200 app.log
tail -f app.log

# Follow logs, filter for errors
tail -f app.log | grep -iE "error|warn|fail"

# systemd (Linux)
journalctl -u nginx --since "1 hour ago"
journalctl -xe
```

Core diagnostics:
```bash
uname -a
whoami
id
hostname
date
```

---

## Time, scheduling, and cron

```bash
date
date -u
time cmd

# crontab
crontab -l
crontab -e
```

Cron examples:
```cron
# Every day at 02:30
30 2 * * * /path/to/script.sh >> /var/log/script.log 2>&1

# Every 5 minutes
*/5 * * * * /path/to/healthcheck.sh
```

---

## Git productivity

```bash
git status
git log --oneline --decorate --graph -n 20
git diff
git diff --staged

git checkout -b feature/my-thing
git add -A
git commit -m "Describe change"
git push -u origin feature/my-thing

git fetch --all --prune
git rebase origin/main
git stash push -m "WIP"
git stash pop
```

Search history:
```bash
git grep "needle"
git log -S "needle" --oneline
git blame -L 10,60 file
```

---

## tmux productivity

```bash
tmux new -s work
tmux ls
tmux attach -t work
```

Common keybinds (default prefix Ctrl+b):
- `c` new window
- `"` split vertical
- `%` split horizontal
- `d` detach
- `[` scroll mode

---

## Docker quick hits

```bash
docker ps
docker ps -a
docker images

docker logs -f <container>
docker exec -it <container> bash

docker build -t myimg:dev .
docker run --rm -it -p 8080:8080 myimg:dev
docker compose up -d
docker compose logs -f
docker compose down -v
```

Cleanups (use with care):
```bash
docker system df
docker system prune
docker volume prune
```

---

## Safe scripting patterns

### Robust argument handling
```bash
#!/usr/bin/env bash
set -euo pipefail
IFS=$'\n\t'

if [[ $# -lt 1 ]]; then
  echo "Usage: $0 <path>" >&2
  exit 1
fi

path="$1"
[[ -e "$path" ]] || { echo "Missing: $path" >&2; exit 1; }
```

### Logging helpers
```bash
log() { printf '%s %s\n' "$(date +'%Y-%m-%d %H:%M:%S')" "$*"; }
die() { log "ERROR: $*"; exit 1; }
```

### Temporary files
```bash
tmp="$(mktemp)"
trap 'rm -f "$tmp"' EXIT
```

### Read lines safely
```bash
while IFS= read -r line; do
  echo "Got: $line"
done < input.txt
```

---

## Everyday “recipes”

### 1) Find what is taking disk space
```bash
du -sh ./* 2>/dev/null | sort -h | tail -n 20
find . -type f -size +200M -print
```

### 2) Search code, open the file list, then edit
```bash
rg -n "needle" .
rg -l "needle" . | sort
rg -l "needle" . | xargs -I{} sed -i.bak 's/old/new/g' "{}"
```

### 3) Kill whatever is holding a port
```bash
lsof -i :3000
kill -TERM <pid>
```

### 4) Download, verify, and unpack
```bash
curl -L -o tool.tgz https://example.com/tool.tgz
shasum -a 256 tool.tgz
tar -xzf tool.tgz
```

### 5) Quick local backup with rsync
```bash
rsync -avP --delete ~/Documents/ /mnt/backup/Documents/
```

### 6) Watch a command continuously
```bash
watch -n 1 'ss -lntp | head'     # Linux
# macOS alternative:
while true; do date; netstat -anv | grep LISTEN | head; sleep 1; done
```

### 7) Parse logs into a top offenders list
```bash
grep -RIn "ERROR" /var/log/app.log | awk -F: '{print $1}' | sort | uniq -c | sort -nr | head
```

### 8) Extract a column and compute a sum
```bash
awk -F',' 'NR>1 {sum += $3} END {print sum}' data.csv
```

### 9) “Poor man’s” HTTP server
```bash
python3 -m http.server 8000
```

### 10) One-liner JSON to CSV
```bash
jq -r '.items[] | [.id,.name,.status] | @csv' data.json > out.csv
```

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
| `sudo dpkg -i <package_name.deb>` | Install a deb package on debian-based systems |
| `sudo dpkg -r <package_name` | Remove installed package on debian-based systems |
| `sudo dpkg -S <package_name>` | Query installed package on debian-based systems |
| `sudo dpkg -l` | List all installed package on debian-based systems |
| `alias` |  |
| `htop` |  |
| `dd` |  |
| `xxd` |  |
| `hexdump` |  |
| `find . -name <filename>` |  |
| `rm -rf <relative path to filename>` |  |
| `grep -F -x -v -f new.txt old.txt` | Find the lines are you missing from the old file great for includes  |
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



---

## Contributing
PRs welcome. If you add a command:
- include a short explanation,
- include a safe variant or preview-first pattern when destructive,
- keep examples copy-paste ready.
