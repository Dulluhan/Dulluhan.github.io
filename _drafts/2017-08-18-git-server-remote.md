---
published: false
---
The whole problem arose when I was trying to figure out to backup a remote server site I just deployed. Not thinking ahead of time, trying to get everything up and running I completely ignored any form of version control. Having been more comfortable with git rather than with perforce I decided that I was to work with this new site through pushing verified local changes to the remote to deploy. After setting up apache, mysql, php and copying all the files to /var/www/html...I realized that I needed to make the folder in /var/www/html my remote origin for this to all work out. 

After intense googling on how to turn existing directory into a remote git origin, I realized that there was no way of initializing a bare repo with existing files easily? 
