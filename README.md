#This zabbix template is to do HP 3PAR storage monitoring in Zabbix
#The script is the same which is used in Nagios for 3PAR monitoring with some changes here and there
#You need to put the test3par.sh bash script in the "externalscripts" directory defined in zabbix_server.conf
#For the template to work you need to have read only access - user account to the 3PAR command line
#You must also make the zabbix user as onw which runs bash as default instead of a nologin acc
#For this do the following pre-reqs below,
	#make a copy of /etc/passwd as "root" user
	#edit the /etc/passwd to reflect like below,
		#zabbix:x:498:497:Zabbix Monitoring System:/var/lib/zabbix:/bin/bash - (Earlier it will be /sbin/nologin)
		#create a password for the zabbix user (passwd zabbix)
	#create a directory (mkdir /var/lib/zabbix/) and the directory should be owned by zabbix user
		#chown zabbix /var/lib/zabbix/
#Once this is done you will need to login to the zabbix server as a zabbix user and the password you just created
#Now you need to create a public-private keypair in zabbix server and then copy the public key to 3par machine for authentication
#This will ensure that the zabbix need not enter passwd to ssh and run the commands to get 3par storage status
#From zabbix server do,
	#ssh-keygen and then press enter
	#This will create the private key in /var/lib/zabbix/.ssh/id_rsa
	#The public key will be in /var/lib/zabbix/.ssh/id_rsa.pub
	#Copy the contents of 3PAR id_rsa.pub. You need to use it in 3PAR for auth
#Login to the 3PAR command line (You should have an user with read only access to 3par cli)
#Do the following below in 3PAR CLI
	#ssh-add ~/.ssh/id_rsa
	#Paste the contents of the id_rsa.pub here and then press enter
	#The public key of zabbix server is added to 3PAR - This means that we do not need to authenticate when we ssh as 
	# ssh 3paruser@3parip from zabbix server as a zabbix user
#If you have any doubts aboutthis, please refer
	#http://exchange.nagios.org/components/com_mtree/attachment.php?link_id=2610&cf_id=29

#Inside the test3par.sh script change the username to 3PAR user name of your environment
#After all the changes import the template and then assign the 3PAR storage you discover to that template
#Image attached
#Keep the "Timeout" in zabbix_server.conf sufficiently high. Max 30 secs. This ll ensure the script execution doesn't timeout
#There are 9 items and 18 triggers in this template for monitoring
