Swagshop
====

<h2>
Getting to User
</h2>


HackTheBox box Swagshop. This is the second box I'm doing. This box wasn't that bad, but I did have some troubles finding an exploit to getting root. Learnt alot with this box.


First things first we will run an nmap scan of the IP address. We find that there are two ports open, http and ssh.

![](https://i.imgur.com/k9HOnvy.png)

We visit the site. I start to do a google search on what is Magento, and also trying to find where the admin panel is.

![](https://i.imgur.com/BzzDhj3.png)

After some research I ended up finding the admin panel which is located at ``` 10.10.10.140/index.php/admin ``` and also that research has led to me finding out that Magento is vulnerable to RCE (Remote Code Execution) attacks. Knowing this we can just go on searchsploit and download a script to get admin rights.

![](https://i.imgur.com/ikiqFzt.png)

So I use searchsploit and download the python script I want to use.

![](https://i.imgur.com/91OjJkQ.png)

So the script I downloaded bascially goes into the directory and adds in a new admin user for you to log into, with the credentials of forme:forme. 

So all I have to do is to add the target url for the script to exploit to get it to work. I just target the homepage. 

![](https://i.imgur.com/piisA4c.png)

Run the script, and it should work. Just go onto the admin panel and login. 

![](https://i.imgur.com/zP7Xxq4.png)

![](https://i.imgur.com/18xV1Bv.png)

![](https://i.imgur.com/vO4Nnkz.png)

Now, I just need to create a reverse tcp shell, so I can connect to the server. I looked around to find a way to edit the php pages so I can upload my shell. But it doesn't seem the case. 

So I did some research and found some videos where you can upload an extension which adds in an IDE. The plugin I am using is linked: https://pluginarchive.com/magento/magpleasure_filesystem


So to add an extension in Magento, we need to go to the Mangento Connect Manager Tab. After that we just need to upload the extension which you can do. Just go to the Direct Package file upload, and just upload the extenstion. 

![](https://i.imgur.com/XKfCOz9.png)


After uploading the extenstion, we just go into the system tab in the dashboard and click IDE. 

![](https://i.imgur.com/0A2p7YM.png)

Now we can edit any page we want. I don't want to edit the index.php page since it send the home page down. So we can edit any other page that they aren't using. I decided to edit the get.php page. I just download a reverse_tcp script from pentester monkey. The link to download it is here: http://pentestmonkey.net/tools/web-shells/php-reverse-shell

![](https://i.imgur.com/TGZg3VR.png)


isten to the port. After doing this save the file, and open the get.php page. We run the command ``` nc -nvlp 1337``` with the port name you inputed in the reverse shell (in my case the port is 1337).

![](https://i.imgur.com/qWnV4xI.png)

Once doing this you should get a connection with the shell. From here we can grab the user.txt which is located in: ``` /home/haris/user.txt ```. Lets cat user.txt and we got user!

``` a448877277e82f05e5ddf9f90aefbac8 ```

<h2>
Getting to Root
</h2>

So now we just need to spawn a fully interactive shell.

Even though we created a shell from the reverse shell we uploaded onto the website. By default its non-interactive, and we can't pass through keyboard shortcuts or specical characters.

This post will go through on how to spawn a tty shell. Link: https://forum.hackthebox.eu/discussion/142/obtaining-a-fully-interactive-shell




We need to figure out what commands we can use. To do so we just write the command ``` sudo -l ```. 

![](https://i.imgur.com/Mu6g5dv.png)


So we have access to vim and to the directory, /var/www/html/* . So know we know what commands we can run. But we need to get higher privlages, since we don't have user privlages we are just www-data. 

Lets cd into the directory (/var/www/html/) and vim into any file. I use vim on install.php

```bash
sudo /usr/bin/vi /var/www/html/install.php
```

From here we just spawn a shell in vi, to do this we just need to write the following two lines

```bash 
:set shell=bin/bash
```
```bash
:shell
```

Doing this gets us root. We now need to get the root flag, which is located in the directory ```/root/root.txt```. Cat the root.txt file and we get our flag.

```flag: c2b087d66e14a652a3b86a130ac56721```
