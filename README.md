# myoverthewireattemptlog
This is my log of how I attempted overthewire bandit game

Level 0:
- Used ssh -p 2220 bandit0@bandit.labs.overthewire.org to connect to the server
- where -p 2220 means connect to port 2220
- where bandit0@ is the username

Level0 --> Level 1:
- Used vim to go into the readme file and got the password

Level 1 --> Level 2:
- found out that a dashed filename is literally just a file with only a dash, if you use cat on the file, it will just freeze as the command thinks you are passing a special instruction
- Therefore you need to use cat ./-
- password: PK8fYLZg2hnHSz83plBL1iEPKdD3QToB

Level 2 --> Level 3:
- when there is spaces you can use "" for the whole filename
- I did cat ./"--spaces in this filename" and it worked
- password: 7ZZ2LFrykP2zEyvBl4m3clcL7tGYJPME

Level 3 --> Level 4:
- when a file is hidden use ls -a to show hidden files
- then use cat to access the file
- password: xzTXq1rDJQVVAzdv5cHq1TQytTWufAMq
- ctr + D to log out of connected ssh

Level 4 --> Level 5:
- to find only human readable files use file command
- then because there are dashes at the beginning so use file ./* to search all file in the directory
- password: 6C7h9GD8M6ai5nr7wo1RonrzFjj9yIrG

Level 5 --> Level 6:
- use find command to find a file 
- use -size to check the size of a file
- use ! -executable to find a file that is not executable
- use -type f to find file that is human-readable
- so use find -type f -size 1033c ! -executable (c stands for characters/bytes)
- -size, -type are flags, to fiter something you have to use flags
- password: pXa26xhMWaC2SvDotA4r9EgZkulOeSBW

Level 6 --> Level 7:
- use -user and -group flags to filter it out
- so do find / -user bandit7 -group bandit6 -size 33c
- password: Bmnnvf82KzQlfxgAI2d1zYbr1u9pr3E3

Level 7 --> Level 8:
- I searched up what grep does and it finds the word in a file then print the line where it contains the word
-I did grep "millionth" data.txt
-password: VR1ljMayciFxbnUokuQmJFw6QC9VKtub

Level 8 --> Level 9:
- -u flag in uniq only print lines that appear exactly once
- you need to sort the file before using uniq because it can only detect lines that are next to each other
- so I did sort data.txt | uniq -u
- password: EjmOSvuAu7sGAHqHVcBDPirRe9T03kxl

Level 9 --> Level 10:
- strings command only prints out human readable character, which I've found the password by reading it
- so I did strings data.txt
- password: B0s2khmbT9u0geKuOoVGW3JZKhndE3BG

Level 10 --> Level 11:
- I only did base64 data.txt which it didn't work because it encodes the file into base64
- so I have to decode it by doing base64 -d data.txt
- password: pYfOY6HwUsDj5rL9UvyhU7MCmv8vN5Ro

Level 11 --> Level 12:
- I tried to do tr data.txt 'A-Za-z' 'N-ZA-Mn-za-m' but didn't work due to it only takes 2 parameters
- so I have to do cat data.txt | tr 'A-Za-z' 'N-ZA-Mn-za-m'
found a more efficient way which is 
tr 'A-Za-z' 'N-ZA-Mn-za-m' < data.txt
- password: GROozWPO8QyN0mGrjUkID0WCYkZiQxrN

Level 12 --> Level 13:
- I tried copying the data file to the temp file then xxd reverse it but it didn't work
- apparently it is a multistep decoding process
- where first you have to reverse the hex dump by doing 
xxd -r data.txt > data.bin
- then use file to check what's the format of the file
- if it is gzip then reformat the file to .gz then gzip -d data.gz
- if it is bzip2 compressed, then you have to reformat the file to .bz2 then decode by doing bzip2 -d data.bz2
- if it is POSIX tar then you have to do tar -xf data.bin
- password: qQYQiHOBPR8zR61qxYqX45quvihF2uzk

Level 13 --> Level 14:
- this level I have absolutely no idea what to do
- I tried to just copy it and use local host in the server and try to access level 14 but didnt work
- I used youtube and it still wont work due to some permissions issue
- I think its something because of it requires empty lines
- but I eventually got in it 
- I used ((Get-Content -Raw sshkey.private) -replace "`r`n", "`n") | Set-Content -NoNewline sshkey.private this command
- and Add-Content -NoNewline -Value "`n" sshkey.private
- password: aaWecNkG4FhxJQxz07uiwzVP6bJiYS65
#note! apparently i just had to put a new line below the sshkey to bypass the format issue, then change the permission to make it work

Level 14 --> Level 15:
- I tried to use ssh bandit14@localhost -p 30000
- apparently port 30000 is just a text reading program
- so I just had to use nc localhost 30000
- and put in the password
- password: pbLYuZtTg4MgaqfJx8jbA9gKKGqM68A7

Level 15 --> Level 16
- Used ncat instead of nc 
- as I've realised that only ncat can do SSL/TLS encryption
- So I did ncat --ssl localhost 30001
- password: kS0Hf0u5HiXFwKMKFqXvPdOTNGGa0X8V

Level 16 --> Level 17
- I found out which port is listening using nmap 
- I did nmap -p 31000-32000 localhost
- It gave out 5 ports that are listening
- I simply brute force everyone of them with ncat --ssl localhost {portnum}
- and one of them gave out the openssh private key
- found out from online that you could use -sV flag to make it give out service so it shows ssl/echo
- I found out that using openssl won't work but using ncat works bacause the key started with k which is a shortcut
- if I want to use openssl I need to use -quiet flag to make it ignore shortcut
- password: RL3QgeKo8FFveuQpIwkGsxCVTfqxXYes

Level 17 --> Level 18
- I did diff password.old password.new 
- I got two passwords I assume the second password is the password to the next one and the first one is the password for the previous level
- nope the first one is the right one apparently
- password: OQxXZjELndr90zuhOTDYBEomI0SZITXI

Level 18 --> 19:
- I use scp command to download the readme file from bandit18, even though I am pretty sure thats not what you're supposed to do
- I did scp -P 2220 bandit18@bandit.labs.overthewire.org:./readme .
- so I searched it up you can just do the ssh command then put "cat ./readme", then it will output the password
- password: KpsOfPkcP7i1FlIExk2QEjyt6dw8dxZI

Level 19 --> Level 20:
- I executed the setuid by doing ./bandit20-do
- then i realised when you do that you basically becomes bandit20 so you can do the command you want as bandit 20 
- so i did ./bandit20-do cat /etc/bandit_pass/bandit20
- password: 4pIjcunZ0fK2vmp3IwfG8Vf7VhxD6pOA

Level 20 --> Level 21:
- i executed suconnect and it tells me "This program will connect to the given port on localhost using TCP. If it receives the correct password from the other side, the next password is transmitted back."
- then i tried to just connect it with 2220
- but it requires the password immediately and just shuts off immediately
- so I tried to do ./suconnect 2220 4pIjcunZ0fK2vmp3IwfG8Vf7VhxD6pOA, and didn't work
- so I had to put the password to a temp file
- then do nc so that it listens to a random port and i feed it with the file with my password in it
- nc -l -p 4444 < /tmp/hero_pass & (-l means listens, & do the job in background and give me my terminal back)
- then I connect the suconnect with the random port
- ./suconnect 4444
- password: bW9kBv5WC3P4yoDyf12LSdGuNz5ka6hY

Level 21 --> Level 22:
- I went to /etc/cron.d as specified in the title
- I saw whats inside cronjob_bandit22
- it pointed me to /usr/bin/cronjob_bandit22.sh
- so I cat whats inside that
- then i saw it has a file in /tmp/t7O6lds9S0RqQh9aMcz6ShpAoZKF7fgv
- then i cat whats inside and the password is inside 
- password: RYVux2rHEm9tiXHmLFzuR7Vhx6AZQMEz

Level 22 --> Level 23:
- I read the file inside cronjob_bandit23.sh
- found out that it is basically just doing echo I am user $myname | md5sum | cut -d ' ' -f 1 and thats the file name of the password I am looking for
- so I did exactly the same as what that command do 
- and replace myname with bandit23
- so I did echo I am user bandit23 | md5sum | cut -d ' ' -f 1
- then i go into the file with that name on it
- md5sum means a hash technique
- and the cut -d ' ' -f 1 just means cur the empty spaces at the end
- password: gKXDTAXnIz3OBxiPjRZ2uqutUlPZrBsw

Level 23 --> Level 24:
- Here is the code that was in /usr/bin/cronjob_bandit24.sh
#!/bin/bash

shopt -s nullglob

myname=$(whoami)

cd /var/spool/"$myname"/foo || exit
echo "Executing and deleting all scripts in /var/spool/$myname/foo:"
for i in * .*;
do
    if [ "$i" != "." ] && [ "$i" != ".." ];
    then
        echo "Handling $i"
        owner="$(stat --format "%U" "./$i")"
        if [ "${owner}" = "bandit23" ] && [ -f "$i" ]; then
            timeout -s 9 60 "./$i"
        fi
        rm -rf "./$i"
    fi
done
- It basically executes all scripts in /var/spool/"$myname"/foo meaning all scripts in /var/spool/"bandit24"/foo as bandit 24
- so I created a .sh file in /var/spool/bandit24/foo
- in it I basically copied the password from /etc/bandit_pass/bandit24 to /tmp/hero_pass24
- so i did cat /etc/bandit_pass/bandit24 > /tmp/hero_pass24
- then I cat /tmp/hero_pass24
- and it gave me the password
- password: hVQMk3lJNsmQ7VF3ubyrNNBom7BOgVXv

Level 24 --> Level 25:
- I was tring to do nc localhost 30002 | echo "hVQMk3lJNsmQ7VF3ubyrNNBom7BOgVXv" from 0000 - 9999 
- but I dont know how to do the last part
- I found out that I need to put the nc command on the bottom of the pipeline
- and to echo all the numbers I had to do
- for i in {0000..9999}: do echo "password $i"; done
- so the whole command is in {0000..9999}; do echo "hVQMk3lJNsmQ7VF3ubyrNNBom7BOgVXv $i"; done | nc localhost 30002
- password: SoHfqMOEqIX2IYKVciZxvgpR9a2Djx4P

Level 25 --> Level 26:
- I got the sshkey for bandit26 so I tried to log in it
- by putting it in my own machine and change the permission using icacls sshkey5.private /grant "%USERNAME%:(R)"
- also fix the format by putting an empty line under the key
- then do ssh -i sshkey5.private bandit26@bandit.labs.overthewire.org -p 2220
- then I got instantly kicked 
- apparently it doesnt give you a shell when you log on, it prints an art logo then kicks you out
- So you have to shrink your window so it doesn't show the whole artwork yet
- then you press v to get into the vi text editor
- then you have to change the editor's default shell by doing :set shell=/bin/bash
- then do :sh to get your shell back
- after that it gives you another setuid which you can use it to get access to /etc/bandit_pass/bandit27

Level 26 --> Level 27:
- it shows you the password from last level
- password: STJLJBRRphMxKB392CT4iOr5CbzPU9ER

Level 27 --> Level 28:
- I tried to just git clone -p 2220 ssh://bandit27-git@bandit.labs.overthewire.org/home/bandit27-git/repo
- but you have to specify the port number
- to specify you put the port behind the website
- so you do git clone ssh://bandit27-git@bandit.labs.overthewire.org:2220/home/bandit27-git/repo
- then you go inside the repo and cat the file
- password: y8Yd2ssKcpHpud7UvOSOxwamRMzIGIeQ

Level 28 --> Level 29:
- I did exactly the same thing as the last level and got the file but the password is just xxxxxxxx
- so apparently you have to go back into previous commits
- to do that you have to do git log
- then copy the hash that is shown e.g. 
commit de09b8205d8d115e2749670df21bf1882bc98e34 (HEAD -> master, origin/master, origin/HEAD)
Author: Morla Porla <morla@overthewire.org>
Date:   Sat Sep 26 21:54:43 2026 +0000

    fix info leak

commit ad8a5f812f24e793d2657f926f503a2fd1c5e256
Author: Morla Porla <morla@overthewire.org>
Date:   Sat Sep 26 21:54:43 2026 +0000

    add missing data

commit {{e4337f154bd5cc106d3d047fc72f90b187ffcfab}} --> this is the hash
Author: Ben Dover <noone@overthewire.org>
Date:   Sat Sep 26 21:54:43 2026 +0000
- then you do git show ad8a5f812f24e793d2657f926f503a2fd1c5e256
- password: Em7eGtqaMySwNFjCpwzzHhLhospOcdt0

Level 29 --> Level 30:
- I did the same thing as last level, I got the file and did the git log
- I found out that there are different branches and I probably has to go to different branch
- To list out branch you do git branch -a
- then you can do use switch command to go to different branch
- do git switch dev to go to the dev branch
- then you can cat the file for the password
- password: jq9Dfg2rXsfYsWMgFuKlXhphjdH7USgX

Level 30 --> level 31:
- I did the same thing as the last few level and got the file
- I did git log and git branch -a nothing shows up
- I found out about tags, where it shows the hidden database for existing bookmarks
- so you do git tag, and there is a file calles secret
- so i did git show secret
- password: 82NkymblpGBYmIXG6ZQ8YldBYstHpfUf

Level 31 --> Level 32:
- I searched up how to commit repo and you have to do add --> commit --> push (if its a remote server)
- this is in the README.md file 
This time your task is to push a file to the remote repository.

Details:
    File name: key.txt
    Content: 'May I come in?'
    Branch: master
- so I wrote 'May I come in?' in my txt file 
- then I did git add key.txt
- git commit -m "commiting"
- git push origin master
- password: pWuj5jBQ6IgV0NXwiH6g1pXRF8S1YvbT

Level 32 --> Level 33:
- I found out that once you connected to the remote machine it is an uppercase shell, which basically makes every command I do to uppercase
- to break out of it, I need to use a regular shell, which i had to do $0 which gives me my regular shell back
- so I just had to cat /etc/bandit_pass/bandit33
- password: u4P2CyPOwPGLe94RdD9Uo2FxFwvnFswM

##That is the end of my attempt on OverTheWire Level 0 - Level 34