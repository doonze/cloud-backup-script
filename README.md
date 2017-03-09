# cloud-backup-script
This bash script uses rsync, tar, and rclone to create local and cloud backups.

Needed:   
rsync   
rclone  
tar  
find  

This script is designed to do several things. In my case as writen it 
backs up my main server, my Mysql database, and my PI server. Then sends
it all up to the cloud. The PI backs itself up to a disk my main server
and the PI share. Then my main server does the cloud backups from there.
You have to of course modify the script for your needs.

It first creates daily local copies of all servers and a mysql database. 
You don't need the mysql part unless you keep your mysql database in a 
seperate location from server. In my case for performance and size
reasons my mysql database has it's own disk. I suggest doing the 
same if you have a large database. Since the database changes daily, 
rclone will create a full backup of your database each day, and it can 
start eating a lot of space.

Then once a week it creates a tarball of each backup, then sends it via rclone to your 
cloud account. I do this becuase my IPS has a montly bandwidth limit, if you have no limit
or don't get close to hitting your limit you could remove the Day of the Week logic in the
script and have it do all task each day. Just keep in mind moving all this to the cloud each
day could really eat up a lot of data, why I only do it once a week.

The tarballs are to keep all permissions and links intact in the cloud, since most (all?) cloud
providers don't offer a way to perserve permissions. 

If the script is ran every day via crontab, it will keep daily backups of the local server, and
send weekly snapshots to the cloud. Along with the server changes that should give you a save to 
restore your servers back to exactly where they were at any point in time. Worst case would be
if your local storage dies 6 days after the last cloud sync, then you might end up 6 days behind.
I stongly suggest you have the local backups on a seperate disk from your server(s), if you do that you
will have your current server, a daily current copy on a seperate local disk, and a 3rd copy in the 
cloud. Should be safe enough.

I've included a copy of the rsync exclude file I use in this repo.
 
Here's the entry I use in crontab:

0 9    * * *     root    /home/justin/scripts/cloud-backup-script >> /var/log/rsync/main_server/cloud-backup-script.log 2>&1

you'll likely have to create the log file location and give it correct permissions 
for the cron to write to it. Since this script is pretty chatty (lots of output) I 
suggest going through the trouble of creating the log file. Then of course use 
whatever means you like to keep your logfiles in check.  
