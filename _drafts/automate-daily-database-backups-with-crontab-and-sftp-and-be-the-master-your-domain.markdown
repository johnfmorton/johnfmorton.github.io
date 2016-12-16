---
title: Automate daily database backups with crontab and SFTP and be the master your domain.
date: 2013-10-26 16:02:00
layout: post
url: http://supergeekery.com/geekblog/comments/automate-daily-database-backups-with-crontab-and-sftp
---
I recently worked on a site which saved data in it’s local database which needed to be collected and sent each day to a remote server via SFTP for analysis. Needless to say, I didn’t want to manually do this process every day, but I had never fully explored cron jobs, so I had to teach myself about cron jobs while I figured out the problem at hand. Here’s how I ended up automating the job.

## Your friend, the command line

I built the Ubuntu server myself and I had full access to server via the command line. The “root” user was locked down though for safety’s sake. I logged in under a 2nd user I created. To make it as clear as possible, I’ll call that 2nd user “myusername” in this post. “myusername” can use sudo when needed to execute root level commands.

When I log into the server as “myusername”, I land in the directory “/home/myusername”. I didn’t have a “bin” directory yet, so I made one to store the scripts I would write that I would eventually run in my cron job.

After making the “bin” directory, I went into it and made a new file called “dailybackup” by using my default text editor.

	cd /bin
	sudo nano dailybackup

I had to use ‘sudo’ to make this file because of the permissions on the ‘bin’ folder were for ‘root’ instead of ‘myusername’. (This was because of an Ubuntu requirement that the base directory be ‘root’ I don’t fully understand. All I know is that if I made the home directory belong to ‘myusername’, I couldn’t log in.)

I changed ownership of the ‘dailybackup’ file using chown to allow ‘myusername’ full ownership of that file, both the owner and group.

	chown myusername:myusername dailybackup

Then I needed to make the dailybackup file executable.

	sudo chmod 755 dailybackup

The file was now an executable. It was empty though.

I could run this script as I worked on it by typing the following into my command line:

	/home/myusername/bin/dailybackup

I did this frequently along the way to test as I went along.

## Basic script starter needs

The first thing in the file defined which shell to use to interrupt the script. ‘bash’ is the shell I am most familiar with so I went with that.

	#!/bin/bash

Next I needed to define the PATH for the commands in my script. If I didn’t do this, I’d get error telling me that what seem to be the most basic pieces of functionality were missing. Defining the PATH variable solves this problem.

	export PATH=/bin:/usr/bin

## Rotate backups

Next I needed to start getting the daily file export from my database, a MySQL database in my case. There are many resources, [like this](http://www.seanbehan.com/backup-and-rotate-mysql-databases-simple-bash-script "Backup and Rotate MySQL Databases Simple Bash Script") where I picked different pieces of this up.

	# modify the following to suit your environment
	export DB_BACKUP="/home/myusername/backup"
	export DB_USER="dbusername"
	export DB_PASSWD="dbpassword"
	export THEDATE='date +"%A, %B %-d, %Y"'

	# title and version
	echo ""
	echo "Daily Tabbed Report: " $THEDATE
	echo "----------------------"
	echo "* Rotating backups…"
	rm -rf $DB_BACKUP/04
	mv $DB_BACKUP/03 $DB_BACKUP/04
	mv $DB_BACKUP/02 $DB_BACKUP/03
	mv $DB_BACKUP/01 $DB_BACKUP/02
	mkdir $DB_BACKUP/01

The script starts by defining some variables. The first is the location where I wanted to keep the backup files, DB_BACKUP. The next 2 variables are the username and password for the MySQL database.

The ‘export’ part of these commands should make the variable available to sub-shells, from what I understand. I think this means if I separated out the various parts of this script into their own files, these exported variables would be able to be used in those scripts.

The variable called “THEDATE” holds the current date. The echo commands you see will print out helpful information into your command line if you run it from there or in an email if you have your cron job to notify you by email when it’s run.

The rest of the commands above rotate the backup files from the previous days. (The first time you run this, there aren’t any data files to rotate, but I made placeholder directories in my backup folder to get things going.)

There are 4 daily back ups at any one time. To rotate the backup file, the ‘04’ directory is first erased, and the remaining 3 directories are renamed (or, actually “moved”) down the line. Finally, a new ‘01’ directory is created to hold the new data export that we create in the next step.

## Export the daily data

This section starts by echoing out what I’m trying to accomplish in each step. This helps out in debugging. Believe me, I didn’t do this correctly on the first attempt.

	echo "* Creating new backup..."
	mysql --password=$DB_PASSWD -h 'localhost' -e "SELECT * FROM databasenaem.tablename WHERE datecreated >= DATE_SUB(NOW(), INTERVAL 1 DAY)" > $DB_BACKUP/01/exporteddata-`date +%Y-%m-%d`.txt
	echo "* File: " $DB_BACKUP/01/exporteddata-`date +%Y-%m-%d`.txt
	echo "----------------------"

Since I run this script every 24 hours, I want each export to include the previous 24 hours data. In the database, I have a field in the table called “datecreated” which I populate with the date automatically the moment it’s created. In the ‘mysql’ line above, I query for data where the “datecreated” value is greater than or equal to “now” minus 1 day. The “>” in the ‘mysql’ line exports whatever was found in a tab delimitated file name “exported data” with a date attached to it. This tab-delimitated file is readable by Excel and Numbers.

## Transfer the newly exported file via SFTP

With the script so far, I was making the file that I wanted, but it was only on my server in the “/home/myusername/backup/01/exporteddata–2013-10-26.txt” file. I still needed to get the data off the server and to the “big data analytics” company.

Getting the data off my server onto the server I’ll call bigdatacompany.com was a little tricky. If I were able to get my SSH public key set up on their server would have made this much easier. Due to circumstances beyond my control, that wasn’t happening.

Doing SFTP via the command line isn’t that difficult, but you need to enter your password each time. You can’t simply add in a password parameter to the ‘sftp’ command unfortunately. It doesn’t work that way.

I found 2 options: “sshpass” and “expect”. I ended up using “sshpass”, but I think using “expect” would have also worked. If you don’t have sshpass, [read this post about how to install sshpass.](http://www.cyberciti.biz/faq/noninteractive-shell-script-ssh-password-provider/"") If you want to check out the link that looked most promising for how to use the **expect** approach, [check it out here](http://stackoverflow.com/questions/5386482/how-to-run-the-sftp-command-with-a-password-from-bash-script "Using expect for a cron job at StackExchange"). Look for the 3rd answer on that page.

Below is the script I ended up with using “sshpass”. I’ll describe it after the code block.

	echo "* Logging into external big data SFTP server"

	FILE=$DB_BACKUP/01/exporteddata-`date +%Y-%m-%d`.txt

	sshpass -p 'mysftppassword' sftp -oBatchMode=no -b - -oStrictHostKeyChecking=no externalusername@ftp.bigdatacompany.com <<_EOF_
	cd /incoming
	put $FILE
	bye
	_EOF_

	echo "* Logging off external bigdatacompany server"
	echo "----------------------"
	exit 0

Like before, the echo commands let me know what I was trying to execute in the script.

I made a variable defining the name of the file that was created in the previous section of code in a variable called “FILE”. You can see where I use it later in the script where you see $FILE.

In my example code, I’m showing that the big data company gave me an SFTP username of ‘externalusername’ and a password of ‘mysftppassword’. The server’s address is ‘ftp.bigdatacompany.com’. I used these values in the ‘sshpass’ command.

‘sshpass’ accepts the -p parameter with your password as a string followed by the actual ‘sftp’ command you would use. I have the “-oStrictHostKeyChecking=no” in there because I thought it would help me avoid error that warns against an unknown host. (It ended up working for me when I ran the script directly from the command line, but it failed when I had it run in the cron job. I’ll show you how I dealt with that later in this post.)

Once my script logged onto the external server, I had to execute the commands that would have done if I were doing it manually. First I changed into the directory with the ‘cd’ command. Then I ‘put’ the file there. Lastly, I say ‘bye’ to leave sftp.

I wrapped all of those commands in a “here script” section. What’s here script? It a way of including a bunch of commands, a stream of them, in your script. In my example, the __EOF__ marks the beginning and end of the here script. [Here’s an excellent tutorial that I learned about here scripts from myself](http://linuxcommand.org/wss0030.php "Here Scripts lesson at Linux Command").

The final “exit 0” command is there to tell your cron job that you completed the script. It’s like saying “The End”.

## Know your crontab

The script may be over, but we’re not done yet because we haven’t talked about cron jobs yet.

All was going well with the script. I could run the command from my command line and it made the data export and copied it to the external server. As mentioned earlier, I’d run the command like this:

	/home/myusername/bin/dailybackup

Now I needed to add that command to my cron job list. You do this by using ‘crontab -e’ command to edit the current crontab using the default editor which is nano in my case.

	crontab -e

This showed my current crontab file for ‘myusernam’.

I edited the default crontab file to include the following lines:

	SHELL=/bin/bash
	PATH=/sbin:/bin/usr/bin
	HOME=/
	MAILTO='notifyme@domain.com'

These lines set the shell, path, and home for the crontab jobs. The “MAILTO” line let me override the email address I had set up as the admin user email address on my server.

I wanted my ‘dailybackup’ job to run every day at 12:01am so I added the following command to the end of my crontab file.

	1 0 * * * /home/myusername/bin/dailybackup

The easiest way is to figure out the right syntax for your crontab command is to use the web-based [Corntab utility](http://www.corntab.com/pages/crontab-gui "Visit Corntab: a visual crontab utility"). (Yes, it’s “Corntab” not “Crontab” because corn is funnier than cron.)

Of course I tested this initially by not having it run only at 12:01am every day. I tested it by having it run every 5 minutes or so until I got the whole thing debugged properly.

## Failed host error

I had an error that would appear only when I executed my ‘dailybackup’ script via crontab. I could see the error in the email reports that the cron job was sending to the email address I’d specified. The error was:

	Failed to add the host to the list of known hosts (/home/myusername/.ssh/known_hosts).

I already had an .ssh folder but the ‘known_hosts’ file wasn’t there and my cron job didn’t seem to be able to create it. I made the file manually instead and made sure ‘myusername’ was the owner of the file.

	/home/myusername/.ssh/known_hosts
	chown myusername:myusername /home/myusername/.ssh/known_hosts

I then logged into the external ftp server manually again from within my Ubuntu server. I was asked if I wanted to add this host to my known hosts file. I said ‘yes’ and that solved the problem.

## Now do this, computer!

After this daily backup was debugged and worked, I felt like I had a new power that I hadn’t had before. I added other jobs, like doing a SQL backup of my database regularly as well. I guess this is what Superman feels like.