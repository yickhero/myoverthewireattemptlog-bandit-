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