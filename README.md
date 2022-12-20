# AllScan
AllStar Favorites Management &amp; Scanning Web App

See [screenshot.png](https://github.com/davidgsd/AllScan/blob/main/screenshot.png) for an example of the AllScan GUI. AllScan supports the following features:

1. Allow Favorites to be added/deleted simply by entering the node# and clicking an 'Add Favorite' button. The favorites.ini file is then updated with the node# and name, description and location data from the ASL DB.

2. Show all Favorites in a table on the main page, allow the connected node to be changed to a new favorite (automatically disconnecting any currently connected node) with a single click on the GUI.

3. Allow favorites to be sorted by node#, name, description, location, etc.

4. Allow favorites to be easily edited, deleted, and copied/pasted.

5. Allow 'Scanning' of Favorites and other nodes by leveraging the ASL database and info such as Keyed status and Connected Nodes data. AllScan shows what favorites are active and have recently been active, and in a future release will allow connecting to favorites one at a time sequentially and if no activity after a configurable time then disconnecting and moving to the next.

These features essentially give ASL the same memory management and scan capabilities that analog radios have had for decades. AllScan follows the latest web development standards and best-practices, with PHP, JavaScript, HTML, and CSS cleanly partitioned. AllScan is mobile-friendly and optimized for ease of use on both small and large screens. AllScan is intended to work well in both PC browsers and mobile browsers, using the same UI for both.

Currently AllScan is Beta software. It supports showing detailed node status, supporting connect, disconnect and other common commands, supporting one-click add/delete of favorites to favorites.ini, showing the Favorites list and color-coded ASL stats, and allowing the Favorties Table to be sorted by any column.

Prior to installing AllScan it is recommended that you have a working install of SuperMon or AllMon2. AllScan can automatically read their config files and thereby need no additional configuration. If you use AllMon rather than Supermon, because it does not have a favorites.ini file you would want to place a blank favorites.ini file in the allscan directory. (AllMon's controlpanel.ini file may also be supported at some point.)

AllScan is very easy-to-use and can be downloaded and installed in minutes. Currently AllScan supports favorites.ini entries that refer to connecting nodes eg. 'cmd[] = "rpt cmd %node% ilink 3 [node#]"' but will also support other types of commands at some point. The code also saves a backup copy to favorites.ini.bak in case of any issue.

As AllScan receives stats data from the ASL stats server it updates the Favorites Table rows with color coded details showing the following:

Color codes for '#' column:
* Dark Green: Node Active (registered and reporting to AllStarLink Network)
* Medium Green: Node Active, Web-Transceiver enabled (node may be more likely to accept connections)
* Red: Node is Keyed (transmitting audio to the AllStarLink Network)

'Rx%' column: The remote node's reported TxTime divided by its Uptime, provides a general indication of how busy the note tends to be.

'LCnt' column: The reported number of Connected Links (ie. user nodes, hubs, bridges, or other links).

ASL's stats APIs are limited to 30 requests/minute per IP Address. AllScan currently defaults to one stats request per 2.5 seconds but will reduce that frequency if over limit http code (eg. 429) or other http error codes are received during API requests.

# Install
First check that you are using a recent 2.0 Beta version of the ASL Distribution (available [here](http://downloads.allstarlink.org/index.php?b=ASL_Images_Beta)), and that you have AllMon2 or Supermon installed, properly configured and working. If you have Supermon already working, AllScan will then work right away with no additional configuration needed, and will use the favorites.ini file in the supermon directory. See [supermon-install.txt](https://github.com/davidgsd/AllScan/blob/main/docs/supermon-install.txt) or the Supermon groups.io page for details on how to install Supermon.

Make sure your node information is defined in /etc/asterisk/allmon.ini and in the supermon global.inc (specifically your $CALL, $LOCATION, and $TITLE2 (eg. "Node 56789") in global.inc). AllScan uses those same settings. Also make sure you are able to properly execute various functions in Supermon such as connecting and disconnecting remote nodes. AllScan supports automatically using the AllMon2 files if Supermon is not installed/configured, but Supermon is easy to set up and has some nice maintenance/debug features.

You will need SSH access to your node and should have basic familiarity with Linux. This has been tested on AllStarLink 2.0 Beta nodes with Supermon 7, but should also work fine with HamVOIP, pre-2.0 ASL releases, or AllMon.

Once you are logged in by SSH to your node run the following commands:

	cd /var/www/html
	mkdir allscan; sudo chmod 775 allscan; sudo chgrp $USER allscan
	wget https://github.com/davidgsd/AllScan/archive/refs/heads/main.zip
	unzip main.zip; rm main.zip
	cp -rf AllScan-main/* allscan/
	rm -rf AllScan-main

	\# Below needed only if you do not have php-curl installed and get ASL Stats functions errors
	sudo apt-get install php-curl; sudo service apache2 restart

Now open a browser and go to your node's IP address followed by /allscan/ eg. `http://192.168.1.23/allscan/`, and bookmark that URL. You should see a Connection Status table showing your Node number(s), Call Sign and Location, a control form where you can enter node numbers and use the Connect, Disconnect, etc. buttons, and a Favorites table with at least a few favorites listed. If any of these are not showing check your allmon.ini and global.inc files in /var/www/html/supermon/ and make sure they are properly configured. 

# Update
The update process is similar to the install process with exception that you don't need to create the allscan directory. To update AllScan log into your node with SSH and run the following commands:

	cd /var/www/html

	\# Note: To make a backup copy of your existing allscan installation do the following:
	\# cp -a allscan allscan-old

	wget https://github.com/davidgsd/AllScan/archive/refs/heads/main.zip
	unzip main.zip; rm main.zip
	cp -rf AllScan-main/* allscan/
	rm -rf AllScan-main

	\# Below needed only if you do not have php-curl installed and get ASL Stats functions errors
	sudo apt-get install php-curl; sudo service apache2 restart

Now open a browser and go to your node's IP address followed by /allscan/, and <u>be sure to force a full reload by pressing CTRL-[F5] or clearing your browser cache, or in mobile browsers do a long-press of the reload button</u>, so your browser will load the updated JavaScript and CSS files.

# Notes
SECURITY NOTE: User login support has not yet been implemented. If your node webserver is PUBLICLY accessible you should set up password protection on the /allscan/ directory. If you do not know how to do that, it is NOT recommended that you install AllScan at this time. In the next few weeks a login system will be implemented in AllScan, but currently anyone who has access to your node's IP address will have access to the /allscan/ directory (if they know to check that specific url). If you are using your node only on your local home network and do not have an external port mapped in your internet router to port 80 on your node then having a login and password is generally not necessary, but can still be enabled easily with a few simple steps to enable Apache directory authentication, such as described in this [article](https://www.digitalocean.com/community/tutorials/how-to-set-up-password-authentication-with-apache-on-ubuntu-20-04)

Also note: I have not yet tested AllScan on a node with EchoLink enabled but that will soon be fully supported.

# Node Notes
If you do not have a node or if your node is out-of-date, noisy, or unreliable, take a look at the following post: [How To Build a High-Quality Full-Duplex AllStar Node for Under $200](https://forums.qrz.com/index.php?threads/how-to-build-a-professional-grade-full-duplex-allstar-node-for-under-200.839842/).

# Troubleshooting / FAQs
If you get a permissions error when trying to Add a Favorite in AllScan, check that the /var/www/html/supermon dir has 775 permissions and www-data group, and that the /var/www/html/supermon/favorites.ini file exists and has 664 permissions and www-data as the group. These settings should already be that way if your Supermon install is properly working, otherwise it would not be able to edit and save the favorites.ini file. As a test you can go into Supermon, click the Configuration Editor button and try adding a blank line to favorites.ini and see if it saves OK. If not, there was something off with the Supermon install. In that case you might want to check on the Supermon groups.io page to let them know, or just run the following commands to correct those settings:

	cd /var/www/html/supermon/
	sudo touch favorites.ini
	sudo chmod 664 favorites.ini
	sudo chmod 775 .
	sudo chgrp www-data favorites.ini .
	
# Contact
If you have any questions email chc_media at yahoo dot com. 73, NR9V

# Road Map
1. Enhanced config file validation and editing. Estimated completion: Dec. 24
4. Implement user authentication system supporting management of multiple users with Read-only, Limited, or Full permissions. This will use cookies rather than php sessions in order to provide better control over login durations and support "Remember Me" option to prevent frequent re-logging in. Estimated completion: Jan. 5
3. Add support for EchoLink nodes. Estimated completion: Jan. 12
4. Other features that are highly requested or that seem like a good fit.

# Release Notes
**v0.31 2022-12-20**<br>
Use PHP cURL lib for ASL Stats data download if present. Move ASL stats output messages to <p> tag under Favorites Table.

**v0.3 2022-12-19**<br>
Implement ASL Stats functions and color coding of Favorites Table '#' and new 'Rx%' and 'LCnt' columns. Color codes for '#' col: Dark Green: Node Active, Medium Green: Active, WT enabled, Red: Node is Keyed. Rx% refers to node's reported TxTime divided by its Uptime. LCnt is the reported number of Connected Links (ie. nodes/hubs/links). ASL APIs are limited to 30 requests/minute per IP Address. AllScan currently defaults to one stats request per 2.5 seconds but will reduce that frequency if over limit http code eg. 429 or another error is encountered during an API request.

**v0.26 2022-12-19**<br>
Implement ASL Stats API infrastructure. Improve handling of page reload logic after browser JS online event when node is no longer accessible.

**v0.25 2022-12-19**<br>
Enable automatic reading of allmon.ini file also from /etc/asterisk/, and astdb.txt file from allscan's directory or from ../supermon/ or /var/log/asterisk/.

**v0.24 2022-12-18**<br>
Enable automatic reading of allmon.ini file from allscan's directory or from ../supermon/ or ../allmon/allmon.ini.php. Provide detailed messages about any issues found when trying to read the file.

**v0.23 2022-12-18**<br>
When JS online event is received, reload page after 2 Sec delay, to automatically restart server event-stream connection after PC/browser was asleep or offline. Add print of astdb.txt file Last Update time.

**v0.22 2022-12-17**<br>
CSS optimizations. Add Asterisk Restart button. Improvements to log messages.

**v0.21 2022-12-16**<br>
Support Disconnect before Connect feature. This sends AMI an 'rpt cmd ilink 6' (Disconnect all links) command and waits 500mS before executing a Connect request, if 'Disconnect before Connect' checkbox is checked (default val = checked) and nodes are connected. To have the default for this checkbox be unchecked, set a url parm of autodisc=0. Disable PHP notices. Change Node text input field to a number field, and make the font size larger to make use of the field more mobile-friendly. Check for online/offline JS events.

**v0.2 2022-12-15**<br>
Add Asterisk API. Code refactoring. Add Message Stats div, set up JS functions to output detailed status and error messages during all event processing. Add info links and CPU temp display.

**v0.15 2022-12-14**<br>
Enable sortable columns on Favorites Table. GUI Updates.

**v0.1 2022-12-13**<br>
Initial Commit.

# Thanks
Thanks to all ASL Developers. Thanks to KK6QMS for being the first Beta tester, and K6ATC for help in verifying AllScan works on HamVOIP.
