# cloud-backup-script
This bash script uses rsync, tar, and rclone to create local and cloud backups.

Needed: 
rsync   
rclone  
tar  
find  

This script is designed to do several things. It first creates daily local copies of
all servers and a mysql database. You don't need the mysql part unless you keep your
mysql database in a seperate location from server. In my case for performance and size
reasons my mysql database has it's own disk. I suggest doing the same if you have a large
database. Since the database changes daily, rclone will create a full backup of your 
database each day, and it can start eating a lot of space.

Then once a week it creates a tarball of each backup, then sends it via rclone to your 
cloud account. 

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
