5/24/16
=======

##### My DigitalOcean droplet development workflow
*	There are 3 locations: my local dev environment(local), github or bitbucket repo(github), DigitalOcean droplet LAMP box(remote), remote has 2 separate directory(environment): dev and production.

*	local can directly sync with remote's dev environment through sublime's sftp tool, make it easier to test & debug.

*	when a new feature is developed and tested, it is pushed to github's master branch, and this master branch has a webhook to push the changes to remote's production environment.

*	add and enable dev subdomain through `etc/apache2/`, create a matching directory under `/var/www` for dev, and add the dev subdomain name to `jinsongli.com` through `namecheap.com`.

##### misterious bug that `http://devblog.jinsongli.com/` still not working correctly.
*	bug1: it still points to the orignal site, not this dev one.
*	Solution1: restart apache! `sudo service apache2 restart` will do. to check apache2 status, do `service apache2 status`
*	bug2: now when I refresh the page, it just show 500 server error, what could possibly be wrong?
*	Solution2: I am sure all paths are set correctly, I've created a devblog site in apache2 and enabled it, pointed to the `/var/www/devblog/public/` folder, then what might be wrong? maybe file permissions? then I went to check apache log files, in `/var/log/apache2/error.log`, I found that apache could not write to `storage` folder. So I found it! solution is simple, just `sudo chown -R www-data:www-data storage/` will fix the bug.