---
layout: post
title: "WordPress Installation Script"
date: 2010-03-12 22:08:59 -0700
comments: true
categories: WordPress
---

The WordPress installation process is already incredibly simple, but because I often find myself setting up fresh installations of WordPress for various reasons, I decided to make it even easier.

The following script takes a folder name as an argument, creates that folder in the website root (specified in a config variable of the script), downloads and extracts the latest version of WordPress into that folder, and runs the WordPress install. Finally, it sets the admin password to the password specified in another config variable and removes the password change nag.

```sh
#!/bin/bash
#
# configuration
#
rootURL="http://localhost" # your root URL. No trailing slash
rootDIR="/home/anderswc/public_html" # your document root. No trailing slash

dbname="wordpress"
dbuser="username"
dbpass="password"
dbhost="localhost"

wppass="password" #password to use for the admin account
adminemail="email@example.com" #email to use for the admin account
blogtitle=$1 # defaults to the specified directory name. can be anything

if [ -z "$1" ]
then
	# directory name must be specified
	echo "Please specify an installation folder name"
else
	# ideally you shouldn't have to change anything past this point
	echo "Creating a WordPress instance in $rootDIR/$1"
	# create the directory to install WordPress in
	mkdir $rootDIR/$1
	# download the latest version of WordPress into the directory we just created
	wget -o /dev/null -O $rootDIR/$1/wordpress.zip http://wordpress.org/latest.zip
	# unzip the archive containing WordPress. files will be inside a 'wordpress' directory
	unzip -q $rootDIR/$1/wordpress.zip -d $rootDIR/$1
	# move the files out of the 'wordpress' directory into their intended location
	mv $rootDIR/$1/wordpress/* $rootDIR/$1
	# remove the wordpress archive and the wordpress folder
	rm $rootDIR/$1/wordpress.zip
	rmdir $rootDIR/$1/wordpress
	# make WordPress directory writeable by PHP so installation can take place
	chmod -R 777 $rootDIR/$1
	# request the configuration file generation and blog installation webpages.
	postdata="dbname=$dbname&uname=$dbuser&pwd=$dbpass&dbhost=$dbhost&prefix=$1_"
	wget -o /dev/null --post-data "$postdata" "$rootURL/$1/wp-admin/setup-config.php?step=2"
	postdata="weblog_title=$blogtitle&admin_email=$adminemail"
	wget -o /dev/null --post-data "$postdata" "$rootURL/$1/wp-admin/install.php?step=2"
	# WordPress password was randomly generated
	# change it to password in configuration and remove password change nag
	sqlcommand="UPDATE $1_users SET user_pass=MD5('$wppass')"
	sqlcommand="$sqlcommand;DELETE FROM $1_usermeta WHERE meta_key='default_password_nag';"
	mysql -u $dbuser -p$dbpass -e "$sqlcommand" $dbname
	# uncomment the following line if you're running this script on a public server
	#chmod -R 755 $rootDIR/$1
	echo "Done"
fi
```

If you think this might be useful, give it a shot and let me know what you think.