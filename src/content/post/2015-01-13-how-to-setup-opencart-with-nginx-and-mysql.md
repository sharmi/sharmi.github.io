---
 
title: How to setup Opencart with Nginx and MySql
publishDate: 2015-01-13 16:18:39.000000000 +05:30

category:  Nginx
tags:
- opencart
- php
meta:
  _wpcom_is_markdown: '1'
  _edit_last: '1'
  _wpas_done_all: '1'
  dsq_thread_id: '3783282929'
author:  sharmi
 
canonical: "https://www.minvolai.com/how-to-setup-opencart-with-nginx-and-mysql/"
slug: how-to-setup-opencart-with-nginx-and-mysql
---
<p>In recent years, nginx is fast overtaking apache as the default web server due to it's smaller footprint and easier configuring ability.</p>
<p>Yet, nginx does not seem to be mainstream yet in the OpenCart community.  When I had to create an opencart setup, I had to look up multiple blog posts to create a setup that would work.  Interestingly, once opencart was up and running, the whole setup worked like a charm.</p>
<p>Since I worked on an debian based server, I have used the synaptic package manager.  Kindly adapt it to the needs of your own package manager.</p>
<p>So here are the steps to get an working opencart setup from scratch.</p>
<p>###System update<br />
Since it is php we are dealing with here, it is always advisable to pick up the latest packages to not miss out on any of the security updates and fixes.</p>
<pre><code class="bash">sudo apt-get update
</code></pre>
<p>###nginx<br />
Before installing nginx, we should add the appropriate software sources and update.</p>
<pre><code class="bash">echo "deb http://ppa.launchpad.net/nginx/stable/ubuntu $(lsb_release -sc) main" | sudo tee /etc/apt/sources.list.d/nginx-stable.list
sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-keys C300EE8C
sudo apt-get update
sudo apt-get install nginx
</code></pre>
<p>nginx will not start automatically. To start nginx, run</p>
<pre><code class="bash">sudo service nginx start
</code></pre>
<p>To check if nginx is running, run</p>
<pre><code class="bash">ps ax | grep nginx
</code></pre>
<p>You can check further by directing your browser to the server ip address.</p>
<p>###MySQL Installation<br />
MySQL is one of the best open source databases available.  It is often preferred by startups for their speed and ease of use.  Opencart is tightly integrated with MySQL and integrating with any other database management system will require an OpenCart expert to do a lot of tuning in the core. So let us close our eyes and type</p>
<pre><code class="bash">sudo apt-get install mysql-server
</code></pre>
<p>The installation will prompt for a password for root.  Enter a secure password.<br />
The mysql server should start running automatically. Test it  by running</p>
<pre><code class="bash">ps ax | grep mysql
</code></pre>
<p>If there are is no process by the name 'mysqld', run the following command to start mysql.</p>
<pre><code class="bash">sudo service mysql start
</code></pre>
<p>###PHP Installation</p>
<p>Let us install php5 first in case it is not present already.</p>
<pre><code class="bash">sudo apt-get install php5 
</code></pre>
<p>Next, let us install the php extensions needed by Opencart</p>
<pre><code class="bash">sudo apt-get install php5-mysql php5-curl php5-gd php5-mcrypt php5-zlib 
</code></pre>
<p>What is PHP-FPM?</p>
<blockquote><p>
  PHP-FPM (FastCGI Process Manager) is an alternative PHP FastCGI implementation with some additional features useful for sites of any size, especially busier sites.
</p></blockquote>
<p>Since we don't use apache, and nginx performs as a reverse proxy (i.e it forwards all request from the user to the fastcgi process seamlessly) we need a FastCGI process at the other end of nginx to process the php requests.</p>
<pre><code class="bash">sudo apt-get install php5-fpm
</code></pre>
<p>###PHP Configuration<br />
The following changes need to be made in the php configuration file. Open up the php-fpm's php.ini file.</p>
<pre><code class="bash">sudo vim /etc/php5/fpm/php.ini
</code></pre>
<p>Find the line, cgi.fix_pathinfo=1, and change the 1 to 0.</p>
<pre><code class="bash">cgi.fix_pathinfo=0
</code></pre>
<p>This is a security risk, since if the fix_pathinfo is 1, then, in case of a request for a missing file, the php interpreter will find the file closest in path to the requested file and execute it.  On the other hand, setting the number to 0 ensures that only php files that match the exact requested path will be executed.</p>
<p>We need to make another small change in the php5-fpm configuration.Open up www.conf:</p>
<pre><code class="bash">sudo nano /etc/php5/fpm/pool.d/www.conf
</code></pre>
<p>Find the line, listen = 127.0.0.1:9000, and change the 127.0.0.1:9000 to /var/run/php5-fpm.sock.</p>
<pre><code class="bash">listen = /var/run/php5-fpm.sock
</code></pre>
<p>Now, php-fpm will only handle requests originating within the host operating system, a much safer option.</p>
<p>Restart php-fpm:</p>
<pre><code class="bash">sudo service php5-fpm restart
</code></pre>
<p>###Nginx Configuration<br />
We need to configure nginx to say how the php files need to be handled.  It needs to be pass the php requests to PHP-FPM for execution and return the results back to the client.  Basically in the context of php files, nginx will be a reverse proxy for PHP-FHM.</p>
<p>We will be changing the example.com's config file. But this can be made in the default nginx site file too.</p>
<p>sudo vim /etc/nginx/sites-available/example_com.config</p>
<pre><code class="config">server {
    listen 80;
    server_name www.example.com;
    access_log /var/log/nginx/example_opencart.log;               
    root /usr/share/nginx/html/example/;
    index index.php index.html;
    location /image/data {
        autoindex on;
    }
    location /admin {
        index index.php;
    }
    location / {
        try_files $uri @opencart;
    }
    location @opencart {
        rewrite ^/(.+)$ /index.php?_route_=$1 last;
    }

    # Make sure files with the following extensions do not get loaded by nginx because nginx would display the source code, and these files can contain PASSWORDS!
    location ~* \.(engine|inc|info|install|make|module|profile|test|po|sh|.*sql|theme|tpl(\.php)?|xtmpl)$|^(\..*|Entries.*|Repository|Root|Tag|Template)$|\.php_ {
        deny all;
    }
    # Deny all attempts to access hidden files such as .htaccess, .htpasswd, .DS_Store (Mac).
    location ~ /\. {
        deny all;
        access_log off;
        log_not_found off;
    }
    location ~*  \.(jpg|jpeg|png|gif|css|js|ico)$ {
        expires max;
        log_not_found off;
    }
    location ~ \.php$ {
        try_files $uri =404;
        fastcgi_pass unix:/var/run/php5-fpm.sock;
        fastcgi_index index.php;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        include fastcgi_params;
    }
}


</code></pre>
<p>Let us test it by adding a php info file.</p>
<pre><code class="bash">sudo vim /usr/share/nginx/html/example/info.php
</code></pre>
<p>Add the following code to the file and save.</p>
<pre><code class="php">&lt;?php
phpinfo();
?&gt;
</code></pre>
<p>Restart nginx</p>
<pre><code class="bash">sudo service nginx restart
</code></pre>
<p>You can see the nginx and php-fpm configuration details by visiting http://example.com/info.php</p>
<p>###Setting up MySQL Database<br />
We need to setup a mysql database for opencart.</p>
<p>Invoke mysql from the commandline</p>
<pre><code class="bash">mysql -u root -p
</code></pre>
<p>Create the database for opencart by running the following command at the mysql prompt.</p>
<pre><code class="sql">create database opencartdb;
</code></pre>
<p>Create the user for opencart app by running the following command at the mysql prompt.</p>
<pre><code class="sql">CREATE USER 'opencart_user'@'localhost' IDENTIFIED BY 'somepassword';
</code></pre>
<p>Now we need to ensure the opencart_user has all privileges on opencartdb.</p>
<pre><code class="sql">GRANT ALL ON opencartdb.* TO 'opencart_user'@'localhost';
</code></pre>
<p>Please note that the above configuration assumes that mysql and opencart run on the same server.</p>
<p>###Installing Opencart<br />
Upload the opencart files to the directory you want to install it in. Navigate to the directory you have copied the files to and setup the config pages.</p>
<pre><code class="bash">cp config-dist.php config.php
cp admin/config-dist.php admin/config.php
</code></pre>
<p>The server needs write permissions on the following files. If mode 755 does not work, try with 0777</p>
<pre><code class="bash">chmod 0755 image/
chmod 0755 image/cache/
chmod 0755 cache/
chmod 0755 download/
chmod 0755 config.php
chmod 0755 admin/config.php
</code></pre>
<p>Once the install is over, change the config files to 0444 or 0644 as that is more secure. The folders can be 0755.</p>
<p>Next, visit the store homepage e.g. http://www.example.com or http://www.example.com/store/</p>
<p>You should be taken to the installer page. Follow the on screen instructions.</p>
<p>After successful install, delete the /install/ directory from ftp.</p>
<p>Thats it! You are done!</p>
