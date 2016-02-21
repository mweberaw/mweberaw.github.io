---
title: Backing up a linux vserver using a Synology Diskstation
layout: post
date: 2013-09-08
categories:
  - Configuration
  - Linux

---
If you have your own linux vserver or root server, you most probably also have information on this server that you do not want to lose. Backing up the data of the server is the most crucial part in avoiding information loss by anything that can happen to your server. For those who have a Synology Diskstation, this task is not too complex.

First make sure *rsync* is installed on the server you have to backup. This handy tool is designed to transfer huge amount of data over an encrypted link. The tool uses *ssh* to secure the transfer. The biggest advantage you gain is that rsync only transfers parts of your files that have been changed after the last run.

The next step is to configure *ssh* (which should be available on almost all linux servers out there) such that your diskstation can log into your server without password. This can be done using public key authentication. Log into your diskstation using *ssh* (you may have to enable ssh access to your box first under `Control Panel -> Terminal -> Enable SSH Service`). I logged in as user admin. Next you have to generate a new ssh public key using the following command:

```
DiskStation> ssh-keygen -t rsa
```

Do not add a password for the key since you would have to enter the password each time the diskstation connects to your server. Since we want to use the key to connect at a specific time at night when no user interaction is possible, adding a password would make this technique not work. The **public** part of this new ssh-key has to be transferred to your server and added to the `authorized_keys` file under the home directory of the user you want to log in.

```
DiskStation> scp /var/services/homes/admin/.ssh/id_rsa.pub backupuser@example.com:diskstation.pub
```

Log onto your server using ssh.

```
Server> cat diskstation.pub >> .ssh/authorized_keys
```

Now your admin user of your diskstation can log into your server without entering a password. The next step is to create a folder where to backup your vserver data. You can do this using the Shared Folder item in the control panel. I created a shared folder *backup* on volume 1 in this case.

The last part is to finally write the script that backups your servers data to your diskstation. You can use the following code as a starting point:

```
#!/bin/sh
/usr/syno/bin/rsync -avz -e '/usr/syno/bin/ssh -i /var/services/homes/admin/.ssh/id_rsa' backupuser@example.com:/data /volume1/backup/vserver
```

Replace *backupuser* with the user of your vserver that has access to the data you want to backup. Also replace *example.com* with your domain. In the example above, I want to backup everything under */data* on my server into the folder vserver in my backup shared folder. Do not forget to adapt the ssh private key you have generated in one of the first steps of the post. The path should be the same as for the ssh public key dropping the *.pub* suffix. Save the script under some file name and make it executable.

```
DiskStation> chmod 755 /path/to/your/script.sh
```

Now go to the control panel of your diskstation and into the Task Scheduler. Create a new task and choose *User-defined script*. Choose a name for the new task, for example “Backup VServer”. As user choose the user that should run the task, in my case *admin*. As user-defined script you can use something like the following snippet:

```
/bin/sh /path/to/your/script.sh > /volume1/backup_vserver.log 2>&1
```

This will run the script your have created earlier and write a log file of the last run to the file `/volume1/backup_vserver.log`. In the Schedule tab you can define, when the script will be executed. When done, try to run the script for the first time and check the log file for errors. If all looks fine, your diskstation will pull a backup of the data on your vserver in the specified interval.
