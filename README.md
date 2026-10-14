# OverTheWire: Bandit Wargame Write-Up

> A chronological log of my progression through the OverTheWire Bandit wargame. This log documents my practical problem-solving methods using Linux command-line utilities, SSH configurations, bash scripting, and Git version control.

## Level 0

* Connected to the server using SSH, specifying port 2220 and the username `bandit0`:
```bash
ssh -p 2220 bandit0@bandit.labs.overthewire.org

```



## Level 0 → Level 1

* Used `vim` to read the `readme` file and retrieve the password.

## Level 1 → Level 2

* Discovered that a dashed filename (`-`) causes `cat` to freeze, as the command interprets the dash as a special instruction (reading from standard input).
* Bypassed this by explicitly specifying the current directory path:
```bash
cat ./-

```


* Password: `[REDACTED]`

## Level 2 → Level 3

* Handled spaces in a filename by enclosing the entire name in quotes.
```bash
cat ./"--spaces in this filename"

```


* Password: `[REDACTED]`

## Level 3 → Level 4

* Used `ls -a` to show hidden files.
* Used `cat` to read the revealed file.
* Logged out of the SSH session using `Ctrl + D`.
* Password: `[REDACTED]`

## Level 4 → Level 5

* Used the `file` command to identify human-readable files.
* Because the files in the directory started with dashes, I used a wildcard with the path to search all files safely:
```bash
file ./*

```


* Password: `[REDACTED]`

## Level 5 → Level 6

* Utilized the `find` command with specific flags to filter files based on the given requirements:
* `-type f` to find human-readable (regular) files.
* `-size 1033c` to check for a file size of exactly 1033 bytes (`c` stands for characters/bytes).
* `! -executable` to find a file that is not executable.


```bash
find -type f -size 1033c ! -executable

```


* Password: `[REDACTED]`

## Level 6 → Level 7

* Used `-user` and `-group` flags to filter by ownership.
```bash
find / -user bandit7 -group bandit6 -size 33c

```


* Password: `[REDACTED]`

## Level 7 → Level 8

* Researched `grep`, which searches for a specific word in a file and prints the containing line.
```bash
grep "millionth" data.txt

```


* Password: `[REDACTED]`

## Level 8 → Level 9

* Used the `-u` flag with `uniq` to print lines that appear exactly once.
* Realized the file must be sorted first, because `uniq` only detects duplicates that are adjacent to each other.
```bash
sort data.txt | uniq -u

```


* Password: `[REDACTED]`

## Level 9 → Level 10

* Used the `strings` command, which only prints human-readable characters from a binary file.
```bash
strings data.txt

```


* Password: `[REDACTED]`

## Level 10 → Level 11

* Attempted to run `base64 data.txt`, but realized it was encoding the file instead of decoding it.
* Passed the `-d` flag to decode the text.
```bash
base64 -d data.txt

```


* Password: `[REDACTED]`

## Level 11 → Level 12

* Attempted `tr data.txt 'A-Za-z' 'N-ZA-Mn-za-m'`, but it failed because `tr` only takes two parameters (the set to replace and the replacement set) and reads from standard input.
* Found an efficient way to redirect the file input into `tr`:
```bash
tr 'A-Za-z' 'N-ZA-Mn-za-m' < data.txt

```


* Password: `[REDACTED]`

## Level 12 → Level 13

* Encountered a multi-step decoding process involving a nested archive.
* First, reversed the hex dump to create a binary file:
```bash
xxd -r data.txt > data.bin

```


* Used the `file` command iteratively to check the format of the output.
* Depending on the output, I renamed the extension and decompressed accordingly:
* If gzip: `mv data.bin data.gz` → `gzip -d data.gz`
* If bzip2: `mv data.bin data.bz2` → `bzip2 -d data.bz2`
* If POSIX tar: `tar -xf data.bin`


* Password: `[REDACTED]`

## Level 13 → Level 14

* Faced permission and format issues while trying to use an SSH private key to log into localhost as the next user.
* Discovered the key file was missing empty lines required by the format. I bypassed the format issue by appending a new line to the key and modifying the file permissions.
* Password: `[REDACTED]`

## Level 14 → Level 15

* Attempted to SSH into port 30000, but discovered it was a text reading program, not an SSH daemon.
* Used Netcat to connect and submit the current password:
```bash
nc localhost 30000

```


* Password: `[REDACTED]`

## Level 15 → Level 16

* Switched from `nc` to `ncat` after realizing SSL/TLS encryption was required.
```bash
ncat --ssl localhost 30001

```


* Password: `[REDACTED]`

## Level 16 → Level 17

* Used `nmap` to find which ports were listening in the given range:
```bash
nmap -p 31000-32000 localhost

```


* Found 5 listening ports. Used the `-sV` flag to determine the service versions (SSL/echo).
* Brute-forced the valid ports using `ncat --ssl localhost {portnum}` until one returned an RSA private key.
* Note: Using `openssl` failed because the key started with 'k' (triggering a shortcut). I bypassed this by sticking to `ncat` (or by using the `-quiet` flag with openssl).
* Password: `[REDACTED]`

## Level 17 → Level 18

* Used `diff` to compare two files and isolate the changes.
```bash
diff password.old password.new

```


* This outputted two passwords; the first one was the correct key for the next level.
* Password: `[REDACTED]`

## Level 18 → Level 19

* Logging in directly forced an exit due to the remote `.bashrc` configuration.
* Solved by passing the `cat` command directly through the SSH connection before the session could be killed:
```bash
ssh -p 2220 bandit18@bandit.labs.overthewire.org "cat ./readme"

```


* Password: `[REDACTED]`

## Level 19 → Level 20

* Executed a setuid binary (`./bandit20-do`).
* Realized the binary executes commands with the privileges of the `bandit20` user.
```bash
./bandit20-do cat /etc/bandit_pass/bandit20

```


* Password: `[REDACTED]`

## Level 20 → Level 21

* The `./suconnect` binary required connecting to a given port on localhost and feeding it the current password to receive the next one.
* Put the current password into a temporary file (`/tmp/hero_pass`).
* Set up `nc` in the background (`&`) to listen (`-l`) on a random port and feed it the password file:
```bash
nc -l -p 4444 < /tmp/hero_pass &

```


* Connected the `suconnect` binary to my listening port:
```bash
./suconnect 4444

```


* Password: `[REDACTED]`

## Level 21 → Level 22

* Inspected the cron jobs in `/etc/cron.d`.
* Read `cronjob_bandit22`, which pointed to the script `/usr/bin/cronjob_bandit22.sh`.
* Looked inside the bash script and found the target output file located in `/tmp`. Read that file to get the password.
* Password: `[REDACTED]`

## Level 22 → Level 23

* Read `/usr/bin/cronjob_bandit23.sh` and reverse-engineered how it generated the password file name.
* It used `echo I am user $myname | md5sum | cut -d ' ' -f 1`.
* Executed the exact same logic but replaced the variable with the target user (`bandit23`):
```bash
echo I am user bandit23 | md5sum | cut -d ' ' -f 1

```


* Used `cat` on the resulting hash filename to retrieve the password.
* Password: `[REDACTED]`

## Level 23 → Level 24

* Analyzed the script `/usr/bin/cronjob_bandit24.sh`, which executes and deletes all scripts placed in `/var/spool/bandit24/foo` with `bandit24` permissions.
* Created a malicious shell script inside that directory to copy the password to a temp file I could access:
```bash
cat /etc/bandit_pass/bandit24 > /tmp/hero_pass24

```


* Waited for the cron job to execute, then read my temp file.
* Password: `[REDACTED]`

## Level 24 → Level 25

* Required brute-forcing a 4-digit PIN via Netcat.
* Wrote a bash loop to iterate from 0000 to 9999, appending each pin to the current password, and piped the entire loop output into the Netcat connection:
```bash
for i in {0000..9999}; do echo "[PREVIOUS_PASSWORD] $i"; done | nc localhost 30002

```


* Password: `[REDACTED]`

## Level 25 → Level 26

* Logging in with the provided SSH key immediately kicked me out after displaying ASCII art.
* Discovered the default shell was forcing an exit. To bypass this, I shrank my terminal window vertically so the ASCII art wouldn't fit, causing the `more` pager to pause the output.
* Pressed `v` to break out of the pager and enter the `vi` text editor.
* Changed the editor's default shell to bash: `:set shell=/bin/bash`
* Spawned the shell: `:sh`
* Used a provided setuid binary in the new shell to read `/etc/bandit_pass/bandit27`.

## Level 26 → Level 27

* Displayed the password obtained from escaping the shell in the previous level.
* Password: `[REDACTED]`

## Level 27 → Level 28

* Cloned a remote Git repository over SSH. Had to explicitly specify the custom port within the SSH URL:
```bash
git clone ssh://bandit27-git@bandit.labs.overthewire.org:2220/home/bandit27-git/repo

```


* Explored the cloned repo and read the file.
* Password: `[REDACTED]`

## Level 28 → Level 29

* Cloned the repository, but the password in the `README.md` was redacted (`xxxxxxxx`).
* Used `git log` to review the commit history.
* Found an older commit with the message "add missing data" and copied its hash.
* Extracted the password by inspecting the old commit state:
```bash
git show ad8a5f812f24e793d2657f926f503a2fd1c5e256

```


* Password: `[REDACTED]`

## Level 29 → Level 30

* Cloned the repository and checked `git log`, but nothing obvious was there.
* Listed all branches using `git branch -a`.
* Discovered a `dev` branch. Switched to it:
```bash
git switch dev

```


* Read the file in the new branch to get the password.
* Password: `[REDACTED]`

## Level 30 → Level 31

* Cloned the repository. Both `git log` and `git branch -a` yielded nothing.
* Researched Git tags (hidden bookmarks for specific points in history).
* Listed tags using `git tag` and found one named `secret`.
* Extracted the data:
```bash
git show secret

```


* Password: `[REDACTED]`

## Level 31 → Level 32

* The repository's `README.md` requested a file named `key.txt` with the content `May I come in?` to be pushed to the `master` branch.
* Created the file and pushed the changes back to the remote server:
```bash
git add key.txt
git commit -m "commiting"
git push origin master

```


* The remote server responded with the password upon a successful push.
* Password: `[REDACTED]`

## Level 32 → Level 33

* Connected to the server and was trapped in an "uppercase shell" (a script converting all input to uppercase, breaking standard Linux commands).
* Escaped the restricted shell by calling the positional parameter `$0`, which respawns the default shell.
```bash
$0

```


* Once in a standard shell, retrieved the password:
```bash
cat /etc/bandit_pass/bandit33

```


* Password: `[REDACTED]`