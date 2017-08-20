---
title: Git Server Remote
---

The whole problem arose when I was trying to figure out to backup a remote server site I just deployed. Not thinking ahead of time, trying to get everything up and running I completely ignored any form of version control. Having been more comfortable with git rather than with perforce I decided that I was to work with this new site through pushing verified local changes to the remote to deploy. After setting up apache, mysql, php and copying all the files to /var/www/html...I realized that I needed to make the folder in /var/www/html my remote origin for this to all work out. 

After intense googling on how to turn existing directory into a remote git origin, I realized that there was no way of initializing a bare repo with existing files easily?

The "fastest" that I ended up going with was to to clone a "remote" repository on the same directory space, then create a post-recieve hook to update the live webpage. 

Setup is as follows: 
1. Live website is located at `/var/www/html/mysite` on ubuntu-14.04/apache2 server. 
2. Setup to make 'local' repo. `cp /var/www/html/mysite ~/mysite` and `cd ~/mysite && git init`
3. Setup to to make 'remote' repo. `mkdir /var/www/html/mysite/mysite.git && cd /var/www/html/mysite/mysite.git`.
4. `git init --bare`. The difference between bare and non-bare is the existence of your worktree, the live files you are working on. Bare git repos are generally used as remote repos such that the worktree cannot be directly edited for safety. 
5. Now to setup the hook such that our live website gets update per new commits we want to add a post-receive hook. To do so, `touch /var/www/html/mysite/mysite.git/hooks/post-receive` then add the following:
> #!/bin/sh
> GIT_WORK_TREE=/var/www/www.example.org git checkout -f
> $ chmod +x hooks/post-receive 

6. Adjust the execution permissions `chmod +x /var/www/html/mysite/mysite.git/hooks/post-receive` 
7. Setup your 'local' repo to push changes to 'remote'. `cd ~/mysite` and `git remote add master /var/www/html/mysite/mysite.git`. 
8. Then finally do everything you normally would, `add`, `commit` and `push` to the live page. 

Now that the setup is complete you can `git clone` and ssh from other computers on the network to directly commit to the live changes. 

--- 

In reality the real reason I did this was because I was lazy. I just migrated a local server to cloud openstack server and did not want to deal with latency and setting up Samba. So smart me, since I already had my old server mounted on my workstation, I just pointed the changes so I can update the mounted drive of the old server on Perforce. 

