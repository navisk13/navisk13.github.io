---
layout: post
title: DerpNStink - Vulnhub
date: 2024-04-11 00:01:00
description: A walkthrough of the machine “DerpNStink” on Vulnhub.
tags: ctf-walkthrough exam-prep pentesting
categories: ctf
---

This is a walkthrough of the machine DerpNStink on Vulnhub. It’s an interesting one, which took me down quite a long path but was fun to complete.

Link to the machine: [https://www.vulnhub.com/entry/derpnstink-1,221/](https://www.vulnhub.com/entry/derpnstink-1,221/)

I set the machine up on my VirtualBox, using a bridged network connection. First, let’s find the machine’s IP address using netdiscover. The command I used is as given below.

<div style="text-align: center; font-size: larger;">
  <code style="font-family: monospace;">sudo netdiscover -i eth0</code>
</div>

I got the machine’s IP as 192.168.1.38.

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/ctf/derpnstink/derpnstink_1.png" class="img-fluid rounded z-depth-1" %}
    </div>
</div>

Let’s do an nmap scan on the IP.

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/ctf/derpnstink/derpnstink_2.png" class="img-fluid rounded z-depth-1" %}
    </div>
</div>

Ftp port 21, ssh port 22 and http port 80 were open on the machine. Let’s check the website out.

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/ctf/derpnstink/derpnstink_3.png" class="img-fluid rounded z-depth-1" %}
    </div>
</div>

It’s a basic webpage with not many details. However, the source code of the website revealed some interesting details.

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/ctf/derpnstink/derpnstink_4.png" class="img-fluid rounded z-depth-1" %}
    </div>
</div>

I found the first flag of the challenge just lying there in the source code. Also, upon closer inspection, I also found a link to another page.

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/ctf/derpnstink/derpnstink_5.png" class="img-fluid rounded z-depth-1" %}
    </div>
</div>

This link led me to a note intended for stinky, one of the characters in the challenge storyline.

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/ctf/derpnstink/derpnstink_6.png" class="img-fluid rounded z-depth-1" %}
    </div>
</div>

Okay, this means there’s a blog somewhere which could be interesting to find, and to access it we need to add the domain name of the website to our local /etc/hosts file. However, we don’t know the domain name yet. So I checked the /webnotes directory, and sure enough I got the domain name as derpnstink.local.

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/ctf/derpnstink/derpnstink_7.png" class="img-fluid rounded z-depth-1" %}
    </div>
</div>

I added this to my /etc/hosts file.

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/ctf/derpnstink/derpnstink_8.png" class="img-fluid rounded z-depth-1" %}
    </div>
</div>

We still don’t know where the blog lives, though. So I ran a directory enumeration scan using dirb and found some juicy information. The command I used for dirb is the following:

<div style="text-align: center; font-size: larger;">
  <code style="font-family: monospace;">dirb http://derpnstink.local</code>
</div>

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/ctf/derpnstink/derpnstink_9.png" class="img-fluid rounded z-depth-1" %}
    </div>
</div>

I got the link to the blog, and also to two admin panels; phpmyadmin and wp-admin. I visited the blog page, but there wasn’t much there of interest to me. I tried to login to phpmyadmin but could not find any valid credentials. So I focused on the wp-admin page, and I was able to login with credentials admin:admin.

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/ctf/derpnstink/derpnstink_10.png" class="img-fluid rounded z-depth-1" %}
    </div>
</div>

I explored the admin panel, and found that the slideshow tab allows file uploads.

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/ctf/derpnstink/derpnstink_11.png" class="img-fluid rounded z-depth-1" %}
    </div>
</div>

Immediately, I tried to upload a php reverse shell to try and get a reverse shell connection. The place to go for this is PentestMonkey’s PHP reverse shell: [https://pentestmonkey.net/tools/web-shells/php-reverse-shell](https://pentestmonkey.net/tools/web-shells/php-reverse-shell).

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/ctf/derpnstink/derpnstink_12.png" class="img-fluid rounded z-depth-1" %}
    </div>
</div>

I downloaded it, and changed the IP to my local machine’s IP.

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/ctf/derpnstink/derpnstink_13.png" class="img-fluid rounded z-depth-1" %}
    </div>
</div>

Next, I set up a netcat listener on port 1234 to catch the reverse connection.

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/ctf/derpnstink/derpnstink_14.png" class="img-fluid rounded z-depth-1" %}
    </div>
</div>

Now, all I had to do was upload the reverse shell and trigger it. I uploaded it into a new slide.

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/ctf/derpnstink/derpnstink_15.png" class="img-fluid rounded z-depth-1" %}
    </div>
</div>

As soon as I saved it, I got the reverse shell connection on my netcat listener. I did not have to trigger it any other way. I upgraded it to a tty shell using `python -c 'import pty;pty.spawn("/bin/bash")'` and also ran `export TERM=xterm` to be able to use the clear function.

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/ctf/derpnstink/derpnstink_16.png" class="img-fluid rounded z-depth-1" %}
    </div>
</div>

I enumerated a bit on the machine, and was able to find database credentials inside WordPress’ wp-config file at /var/www/html/weblog.

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/ctf/derpnstink/derpnstink_17.png" class="img-fluid rounded z-depth-1" %}
    </div>
</div>

I logged in to MySQL to see what I could find. The command I used is given below.

<div style="text-align: center; font-size: larger;">
  <code style="font-family: monospace;">mysql -uroot -pmysql</code>
</div>

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/ctf/derpnstink/derpnstink_18.png" class="img-fluid rounded z-depth-1" %}
    </div>
</div>

Inside the wordpress database, in the wp_users table, I found two hashes.

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/ctf/derpnstink/derpnstink_19.png" class="img-fluid rounded z-depth-1" %}
    </div>
</div>

I knew that admin’s password was admin itself, so I focused on cracking unclestinky’s hash. Online tools like crackstation did not work, so I tried using john to crack the hash using the rockyou.txt wordlist which comes with Kali Linux. First, I pasted the hash into a file called hash and then ran the following command.

<div style="text-align: center; font-size: larger;">
  <code style="font-family: monospace;">sudo john hash --wordlist=/usr/share/wordlists/rockyou.txt</code>
</div>

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/ctf/derpnstink/derpnstink_20.png" class="img-fluid rounded z-depth-1" %}
    </div>
</div>

I got unclestinky’s password as “wedgie57”. But rather than login to wordpress with this, I tried my luck to see if I could get stinky’s shell. In the /home folder, there were two users mrderp and stinky. I tried switching user to stinky using the command `su stinky` and this password and I was able to get stinky’s shell.

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/ctf/derpnstink/derpnstink_21.png" class="img-fluid rounded z-depth-1" %}
    </div>
</div>

I enumerated a bit, and found a flag on stinky’s desktop.

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/ctf/derpnstink/derpnstink_22.png" class="img-fluid rounded z-depth-1" %}
    </div>
</div>

I also found a .pcap file inside stinky’s Documents folder.

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/ctf/derpnstink/derpnstink_23.png" class="img-fluid rounded z-depth-1" %}
    </div>
</div>

A .pcap file contains packet captures, that can be analysed using a tool like Wireshark. But for that, I had to transfer the .pcap file from the machine into my local machine. To do so, I set up a python server in the directory where I found the .pcap file using the following command:

<div style="text-align: center; font-size: larger;">
  <code style="font-family: monospace;">python -m SimpleHTTPServer 8080</code>
</div>

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/ctf/derpnstink/derpnstink_24.png" class="img-fluid rounded z-depth-1" %}
    </div>
</div>

Then, I used wget on my local machine to transfer the file over. The wget command I used is:

<div style="text-align: center; font-size: larger;">
  <code style="font-family: monospace;">wget http://derpnstink.local:8080/derpissues.pcap</code>
</div>

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/ctf/derpnstink/derpnstink_25.png" class="img-fluid rounded z-depth-1" %}
    </div>
</div>

Now, I could open the file using Wireshark and analyse it. I found derp’s password in the file. It was derpderpderpderpderpderpderp.

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/ctf/derpnstink/derpnstink_26.png" class="img-fluid rounded z-depth-1" %}
    </div>
</div>

Using this, I logged into derp’s shell using ssh.

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/ctf/derpnstink/derpnstink_27.png" class="img-fluid rounded z-depth-1" %}
    </div>
</div>

I checked what permissions Derp has using `sudo -l`, and I found that he could run a binary called derpy as root user without a password.

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/ctf/derpnstink/derpnstink_28.png" class="img-fluid rounded z-depth-1" %}
    </div>
</div>

The interesting part is that neither the binaries directory nor the derpy file exists on the machine. This means we can create our own derpy file with /bin/bash as its contents and execute it using sudo to get the root shell. To do so, I created a binaries directory inside /home/mrderp, and then ran the following command to create the file with the desired contents.

<div style="text-align: center; font-size: larger;">
  <code style="font-family: monospace;">echo "/bin/bash" > derpy.sh</code>
</div>

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/ctf/derpnstink/derpnstink_29.png" class="img-fluid rounded z-depth-1" %}
    </div>
</div>

Then, I made the file executable using `chmod +x derpy.sh` and then ran it using `sudo ./derpy.sh`. Sure enough, I got the root shell.

Now I just had to find the root flag, which was on the Desktop of the root user.

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/ctf/derpnstink/derpnstink_30.png" class="img-fluid rounded z-depth-1" %}
    </div>
</div>

And that’s it! I hope you enjoyed this walthrough. Happy hacking!