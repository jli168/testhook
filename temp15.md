temp15: how does it work out?
===============

##### misterious bug that `http://devblog.jinsongli.com/` still not working correctly.
*	bug1: it still points to the orignal site, not this dev one.
*	Solution1: restart apache! `sudo service apache2 restart` will do. to check apache2 status, do `service apache2 status`
*	bug2: now when I refresh the page, it just show 500 server error, what could possibly be wrong?
*	Solution2: I am sure all paths are set correctly, I've created a devblog site in apache2 and enabled it, pointed to the `/var/www/devblog/public/` folder, then what might be wrong? maybe file permissions? then I went to check apache log files, in `/var/log/apache2/error.log`, I found that apache could not write to `storage` folder. So I found it! solution is simple, just `sudo chown -R www-data:www-data storage/` will fix the bug.

tags: javascript, php, yii2