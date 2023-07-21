---
 
title: Swapping out Caddy for Nginx in CookieCutter-Django
publishDate: 2019-02-04 00:42:23.000000000 +05:30

category:  Uncategorised
tags: []
meta:
  _wpcom_is_markdown: '1'
  _edit_last: '1'
  _yoast_wpseo_content_score: '30'
  _oembed_054c942a4c9d20924d2b489649295802: "{{unknown}}"
  _yoast_wpseo_primary_category: ''
  _wpas_done_all: '1'
author:  sharmi
 
canonical: "https://www.minvolai.com/swapping-out-caddy-for-nginx-in-cookiecutter-django/"
slug: swapping-out-caddy-for-nginx-in-cookiecutter-django
---
<p>The cokkiecutter django community is moving away from caddy because of it's licensing which evaluates to "Free for personal use". They are contemplating picking up Traefic and it is still in the works as of this moment.</p>
<p>I, while am interested in trying out Caddy and Traefic, do not wish to bring in new tech into my production code unless it is crucial for the product's success. Though with NginX, I need to wrangle with Certificate verification, it's a beast I have wrangled before. I have done custom compilations with extensions that were not in the core NginX and it had gone extremely well and still serving files for my past projects without any issues.</p>
<p>So I would prefer to stick with the tried and tested NginX. This post details my Operation NginX.</p>
<p>So these are the steps as I see it.</p>
<ul>
<li>Pick a reliable source of docker nginx image.</li>
<li>Ensure modules that I need are there, pagespeed, gzip etc</li>
<li>Setup let's encrypt certificate</li>
<li>Setup renewal.</li>
<li>Setup the config to serve the files.</li>
</ul>
<p>I zeroed in on OpenBridge's NginX "https://github.com/openbridge/nginx" as the base.</p>
<p>I am going to use this article <a href="[https://msaizar.com/blog/cookiecutter-django-nginx-route-53-and-elb/]">"cookiecutter-django with Nginx, Route 53 and ELB" by Martin Saizar</a> as reference</p>
<p>Let use the Nginx Docker image as provided by the nginx team. Here we are mounting some folders and files for docroot, logs, default site conf etc. The default site conf is located at /etc/nginx/conf.d/default</p>
<p>Add the following to your production.yml</p>
<pre><code> nginx:
   image: nginx
   restart: always
   ulimits:
     nproc: 65535
     nofile:
         soft: 49999
         hard: 65535
   volumes:
           - /some/path/www/html:/usr/share/nginx/html
           - /some/path/log:/var/log/nginx
           - /some/path/compose/production/nginx/sites-enabled/sitefitnesshq.conf:/etc/nginx/conf.d/default.conf
           - /etc/letsencrypt/live/yourdomain.com/fullchain.pem:/etc/letsencrypt/live/yourdomain.com/fullchain.pem
           - /etc/letsencrypt/live/yourdomain.com/privkey.pem:/etc/letsencrypt/live/yourdomain.com/privkey.pem
           - /etc/letsencrypt/live/yourdomain.com/chain.pem:/etc/letsencrypt/live/yourdomain.com/chain.pem
   ports:
     - "80:80"
     - "443:443"
   depends_on:
     - django
   deploy:
     restart_policy:
       condition: on-failure
       delay: 5s
       window: 60s
</code></pre>
<p>Now we need to obtain ssl certificates from Lets Encrypt to be used with Nginx. There are plenty of tutorials on line. One good resource is https://medium.com/@IgorYanovskiy/installing-the-letsencrypt-ssl-certificate-and-arranging-an-automatic-renewal-b770dafb72e7</p>
<p>At the end, you should have your certificates should be installed and accessible at /etc/letsencrypt/live/yourdomain.com</p>
<p>We also need to create the nginx conf file for your site. Besides setting up the conf, I am also setting up redirects from non-secure to secure and www to non-www.</p>
<p>in compose/production/nginx/yourdomain.conf</p>
<pre><code>server {


    listen 443 ssl;
    server_name yourdomain.com;
    charset utf-8;
    ssl_stapling on;
    ssl_stapling_verify on;

    ssl_certificate           /etc/letsencrypt/live/yourdomain.com/fullchain.pem;
    ssl_certificate_key       /etc/letsencrypt/live/yourdomain.com/privkey.pem;
    ssl_trusted_certificate   /etc/letsencrypt/live/yourdomain.com/chain.pem;


    set $my_host $http_host;
    if ($http_host = "staging.yourdomain.com") {
          set $my_host "yourdomain.com";
    }

    location / {
        proxy_pass http://django:5000;
        proxy_set_header Host $my_host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    }

}
server {
        listen 80 ;
        server_name yourdomain.com;
        return 301 https://yourdomain.com$request_uri;
}
server {
        listen 80 ;
        server_name www.yourdomain.com;
        return 301 https://yourdomain.com$request_uri;
}
server {
        listen 443 ;
        server_name www.yourdomain.com;
        return 301 https://yourdomain.com$request_uri;
        ssl_stapling on;
        ssl_stapling_verify on;

        ssl_certificate           /etc/letsencrypt/live/yourdomain.com/fullchain.pem;
        ssl_certificate_key       /etc/letsencrypt/live/yourdomain.com/privkey.pem;
        ssl_trusted_certificate   /etc/letsencrypt/live/yourdomain.com/chain.pem;
}
</code></pre>
<p>Now just build and run docker compose.</p>
<p>sudo docker-compose -f production.yml build &amp;&amp; sudo docker-compose -f production.yml run</p>
<p>Another resource to help with ssl certificates: https://absolutecommerce.co.uk/auto-renew-letsencrypt-nginx-certbot<br />
https://tmn.io/lets-encrypt-with-nginx-auto-renewal/</p>
