Level 0:
- ssh bandit0@bandit.labs.overthewire.org -p 2220

Level 0 -> Level 1:
- open the readme to get password
- ssh into bandit1 using that password
- bandit1 password: ZjLjTmM6FvvyRnrb2rfNWOZOTa6ip5If

Level 1 -> Level 2:
- "cat < -" to open a dashed filename
- bandit2 password: 263JGJPfgU6LtdEvgfWU1XP5yac29mFx

Level 2 -> Level 3:
- "\ " for spaces in filenames
- command: cat Spaces\ in \filenames
- bandit3 password: MNk8KNH3Usiio41PRUEoDFPqfxLPlSmx

Level 3 -> Level 4:
- ls -a: find hidden files
- bandit4 password: 2WmrDFRmJIq3IPxneAaMGhap0pFhF3NJ

Level 4 -> Level 5:
- grep -v [text] [file] : returns the files that don't contain that particular text
- inhere/-file07
- bandit5 password: 4oQYVPkxZOOEOO5pTW81FB8j8lxXGUQw

Level 5 -> Level 6:
- find -size 1033c ! -executable | xargs grep -L ?: gives you the directory where the password is hidden
- find -size 1033c ! -executable: it finds a file that is exactly 1033 bytes and is not executable. 
- xargs grep -L ?: takes the files from find and inputs them into grep then -L finds the files that don't match.
- bandit6 password: HWasnPhtq9AVKe0dmk45nxy20cvUa6EG

Level 6 -> Level 7:
- find -size 33c -user bandit7 -group bandit6 -readable | xargs  ls -als: only one file has permission and that's ./var/lib/dpkg/info/bandit7.password
- bandit7 password: morbNTDkSW6jIlUc0ymOdMaLnOlFVAaj

Level 7 -> Level 8:
- grep millionth data.txt
- bandit8 password: dfwvzFQi4mU0wfNbFOe9RoWskMLg7eEc

Level 8 -> Level 9:
- sort data.txt | uniq -u: sorts the text which makes it easier to find duplicate lines
- bandit9 password: 4CKMh1JI91bUIZZPXDqGanal4xvAg0JM

Level 9 -> Level 10:
- strings -e s data.txt | grep ^"=": prints the strings that use ascii characters and are preceded by an equal sign. Adding a \+ in the "" would mean the equal sign shows up 1 or more times.
- bandit10 password: FGUW5ilLVJrxX9kMYMmlN4MgbpfMiqey

Level 10 -> Level 11:
- base64 -d data.txt: decodes the base64 data in the file into UTF-8
- bandit11 password: dtR173fZKb0RRsDFSGsg2RWnpNVj3qRr

Level 11 -> Level 12:
- cat data.txt | tr 'NOPQRSTUVWXYZABCDEFGHIJKLMnopqrstuvwxyzabcdefghijklm' 'ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz' : replaces the letters and unscrambles the ROT13 code
- bandit12 password: 7x16WNeHIi5YkIhWsfFIqoognUTyj9Q4

Level 12 -> Level 13:
- /tmp/tmp.xWGm5cxfjv
- copy the data file over into a temporary directory
	- cd /tmp
	- mktemp -d : makes a temporary directory with a random name
- reverse the hexdump so that you can determine what kind of zip it was
	- xxd -r [filename] > [output file]: reverse hex and output to file
	- file [output file]: to figure out what type of zip
- change the extension and unzip
- use file to check what new compression has been applied
- repeat the last two steps until file outputs ASCII
- bandit13 password: FO5dwFsc0cbaIiH0h8J2eUks2vdTDwAn


Level 13 -> 14:
- ssh -i ~/sshkey.private
- bandit14 password: MU4VWeTyJk8ROof1qqmcBPaLh7lDCPvS

Level 14 -> Level 15:
- telnet localhost 30000: opens up a connection to port 30000 on localhost
- send the password for the current level through
- bandit15 password: 8xCjnmgoKbGLhHFAZlGE5Tmu4M2tKJQo

Level 15 -> Level 16:
- openssl s_client [host]:[port]: opens up a TLS/SSL connection to the specified host and port. You can then send messages to that port and it encrypts them for you with SSL/TLS.`
- bandit16 password: kSkvUpMQ7lBYyCM4GBPvCvT1BfWRy0Dx

Level 16 -> Level 17:
- first scan the ports using nmap -sV -p 31000-32000 to find the active port: 31790 in this case
- then run openssl s_client -ign_eof localhost:[port] to open up an SSL/TLS connection
- put in the password for the previous level: 
- you should get a private key in response.
- put the key in /tmp/sshkey.private
- then ssh -i /tmp/sshey.private -p 2220 
- now you have access to bandit17
- bandit17 password: EReVavePLFHtFlFsjn3hyzMlvSuSAcRD
   - located in /etc/bandit_pass/bandit17

Level 17 -> Level 18
- use diff to find the line that differs between passwords.old and passwords.new
- diff passwords.old passwords.new
   - <: lines in file 1 (passwords.old)
   - >: lines in file 2 (passwords.new)
   - the password is the line associated with passwords.new
- bandit18 password: x2gLTTjFwMOhQ8oWNbMN362QKxfRqGl

Level 18 -> Level 19
- ssh is blocked by .bashrc but scp isn't ;)
- scp -P 2220 bandit18@bandit.labs.overthewire.org:/home/bandit18/readme [destination]
- bandit19 password: cGWpMaKXVwDUNgPAVJbWYuGHVn9zl3j

Level 19 -> Level 20
- euid: effective uid
   - this is the user id you are currently running as
- run ~/bandit20-do cat /etc/bandit_pass/bandit20
   - because you are running "effectively" as bandit20, you can read it to get the password
-bandit20 password: 0qXahG8ZjOVMN9Ghs7iOWsCfZyXOUbYO
