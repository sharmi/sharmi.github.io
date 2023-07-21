---
 
title: Setting up a Secure Single Node Elasticsearch server behind Nginx
publishDate: 2014-08-01 16:07:03.000000000 +05:30

category: Python External Library
tags:
- elasticsearch
- nginx
meta:
  _wpcom_is_markdown: '1'
  _edit_last: '1'
  _wpas_done_all: '1'
  dsq_thread_id: '2893645996'
author:  sharmi
 
canonical: "https://www.minvolai.com/setting-up-a-secure-single-node-elasticsearch-server-behind-nginx/"
slug: setting-up-a-secure-single-node-elasticsearch-server-behind-nginx
---
<p>Elasticsearch by default does not support security features.  They are left to the sole discretion of the developer.  This guide will help you setup a secure elasticsearch single node server.  This is based on days of searching the internet and poring through the alternatives available to seamlessly implement a secure server. As one of the first options, I tried the elasticsearch-jetty plugin but ran into issues like "org.elasticsearch.ElasticsearchIllegalStateException: Can't create an index". Further searching did not turn up any solutions.<br />
The second best solution seemed to be an nginx reverse proxy.  While this is a good solution for a single server, it can potentially become a complicated problem for multi-node servers.  But my usecase warranted only a single node.  So I decided to put Elasticsearch behind an nginx reverse proxy and provide ssl and password based http authentication to secure the server.</p>
<p>We are going to setup Elasticsearch on a Ubuntu server behind nginx as a reverse proxy i.e we route all the traffic to Elasticsearch through nginx.</p>
<h3>Setting up Elasticsearch</h3>
<h4>Setting up JVM</h4>
<p>Elasticsearch requires the java runtime to run.  We have the option of going with OpenJDK or Oracle Java, recommended by Elasticsearch.</p>
<p>To install Oracle Java, run:</p>
<pre><code class="bash">    sudo add-apt-repository ppa:webupd8team/java
    sudo apt-get update
    sudo apt-get install oracle-java7-installer

</code></pre>
<p>or</p>
<p>To install OpenJDK, run:</p>
<pre><code class="bash">    sudo apt-get update
    sudo apt-get install openjdk-6-jre

</code></pre>
<p>####Installing Elasticsearch<br />
The following section guides you to install the Elasticsearch Version 1.1.1 on Ubuntu. For other elasticsearch versions or linux flavours, pl refer http://www.elasticsearch.org/blog/apt-and-yum-repositories/</p>
<p>#####Adding Elasticsearch Repository<br />
First we add the public key. Following which, we will add the repository for the 1.1.x branch</p>
<pre><code class="bash">    wget -O - http://packages.elasticsearch.org/GPG-KEY-elasticsearch | sudo apt-key add -

</code></pre>
<p>Add the following line to the file <code>/etc/apt/sources.list.d/elasticsearch.list</code></p>
<pre><code class="bash">    deb http://packages.elasticsearch.org/elasticsearch/1.1/debian stable main

</code></pre>
<p>Now let us install ElasticSearch by running the following commands.</p>
<pre><code class="bash">    sudo apt-get update
    sudo apt-get install elasticsearch

</code></pre>
<p>Start Elasticsearch with the command</p>
<pre><code class="bash">    sudo service elasticsearch start

</code></pre>
<p>or</p>
<pre><code class="bash">    sudo /etc/init.d/elasticsearch start

</code></pre>
<p>####Testing Elasticsearch<br />
Run the following shell command. Replace "domain.com" by the ip address or domain name where elasticsearch is installed.</p>
<pre><code class="bash">    curl http://domain.com:9200 

</code></pre>
<p>You should get an output similar to the one below</p>
<pre><code class="bash">    {
      "status" : 200,
      "name" : "Richard Parker",
      "version" : {
        "number" : "1.1.2",
        "build_hash" : "e511f7b28b77c4d99175905fac65bffbf4c80cf7",
        "build_timestamp" : "2014-05-22T12:27:39Z",
        "build_snapshot" : false,
        "lucene_version" : "4.7"
      },
      "tagline" : "You Know, for Search"
    }

</code></pre>
<h3>Setting up nginx</h3>
<p>Installing nginx (pronounced Engine-X) is simple. Run the following commands in terminal.</p>
<pre><code class="bash">    sudo apt-get update
    sudo apt-get install nginx

</code></pre>
<p>We need to now start nginx, which can be done using either the command</p>
<pre><code class="bash">    sudo service nginx start

</code></pre>
<p>or</p>
<pre><code class="bash">    sudo /etc/init.d/nginx start

</code></pre>
<h4>Testing nginx</h4>
<p>Visiting the page <code>http://domain.com</code> should result in a page with the title <code>Welcome to nginx!</code></p>
<h3>Setup a domain for elasticsearch</h3>
<p>You should setup a domain or subdomain through your Domain Name Registrar to point to the server where elasticsearch is installed.  This is beyond the scope of this tutorial.</p>
<p>From this point on, I will refer to that domain as es.domain.com</p>
<h3>Routing the requests through nginx</h3>
<p>First lets disable Elasticsearch from receiving external requests. In the elasticsearch server, open the file /etc/elasticsearch/elasticsearch.yml for editting.<br />
Comment out the config <code>network.bind_host</code> and <code>network.publish_host</code>.</p>
<pre><code class="bash">    #network.bind_host: #some_value
    #network.publish_host: #some_other_value 

</code></pre>
<p>Now add the following config to the same file.</p>
<pre><code class="bash">    network.host: localhost

</code></pre>
<p>Save the file and close it.  The above config, sets both the bind_host and publish_host to the value 'localhost'.  So Elasticsearch will only bind to the <code>localhost</code> ip address and any request originating at a different ip address will be ignored.</p>
<p>We need to restart Elasticsearch for the above configuration to take effect. Run the following command in the terminal</p>
<pre><code class="bash">    sudo service elasticsearch restart

</code></pre>
<h4>Testing disabling of external requests to elasticsearch</h4>
<p>Running the command</p>
<pre><code class="bash">    curl http://domain.com:9200

</code></pre>
<p>will result in the below error. This is expected as the above is an external request and we have configured the server to not listen at external ip addresses.</p>
<pre><code class="bash">    curl: (7) Failed to connect to domain.com port 9200: Connection refused

</code></pre>
<p>If you login into the elasticsearch server and run the below command in the terminal, it should produce a valid output.</p>
<pre><code class="bash">    curl http://localhost.com:9200

</code></pre>
<p>should result in</p>
<pre><code class="python">    {
      "status" : 200,
      "name" : "Agent",
      "version" : {
        "number" : "1.1.2",
        "build_hash" : "e511f7b28b77c4d99175905fac65bffbf4c80cf7",
        "build_timestamp" : "2014-05-22T12:27:39Z",
        "build_snapshot" : false,
        "lucene_version" : "4.7"
      },
      "tagline" : "You Know, for Search"
    }

</code></pre>
<p>Now lets route the requests to Elasticsearch server through the domain we have setup in the previous section.  The next task is to make nginx capture all the requests to the domain <code>es.domain.com</code> and route it to <code>localhost:9200</code> and send back a response.</p>
<p>To accomplish that, we need to create a file <code>/etc/nginx/sites-available/elasticsearch</code> with the following content.</p>
<pre><code class="bash">    server {
        listen 80;
        server_name es.domain.com;
        location / {
            rewrite ^/(.*) /$1 break;
            proxy_ignore_client_abort on;
            proxy_pass http://localhost:9200;
            proxy_redirect http://localhost:9200 http://es.domain.com/;
            proxy_set_header  X-Real-IP  $remote_addr;
            proxy_set_header  X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header  Host $http_host;
        }
    }

</code></pre>
<p>Now let us try to understand the above configuration. <code>listen 80</code> instructs nginx to monitor port 80 for incoming requests. <code>server_name es.domain.com</code> tells nginx that for any incoming request at port 80, if the  header_field <code>Host</code> is set to <code>es.domain.com</code> then this server will be used to serve the request. <code>rewrite  ^/(.*) /$1 break</code> can help rewrite the url if the incoming and the corresponding elasticsearch url are different.  However, there it is redundant. <code>proxy_pass</code> tells where the incoming request should be forwarded. <code>proxy_redirect</code> instructs nginx to replace the text <code>http://localhost:9200</code> in the “Location” and “Refresh” header fields of a proxied server response to <code>http://es.domain.com</code>. Setting the value <code>proxy_redirect default</code> achieves the same purpose as the replacee and replacer can be inferred from <code>proxy_pass</code> and <code>location</code> directives.</p>
<p>In the above config, we have only created the configuration. To enable it, we need to create a symlink for this in <code>/etc/nginx/sites-enabled</code>. Run the following command in terminal</p>
<pre><code class="bash">    sudo ln /etc/nginx/sites-available/test /etc/nginx/sites-enabled/

</code></pre>
<p>Now we need to reload the nginx configuration for the new site to take effect.</p>
<pre><code class="bash">    sudo service nginx reload

</code></pre>
<h4>Test Nginx forwards the requests properly</h4>
<p>Run the following command in terminal to check if nginx routes the requests properly to Elasticserver.</p>
<pre><code class="bash">    curl http://es.domain.com/

</code></pre>
<p>should return something similar to</p>
<pre><code class="python">    {
      "status" : 200,
      "name" : "Richard Parker",
      "version" : {
        "number" : "1.1.2",
        "build_hash" : "e511f7b28b77c4d99175905fac65bffbf4c80cf7",
        "build_timestamp" : "2014-05-22T12:27:39Z",
        "build_snapshot" : false,
        "lucene_version" : "4.7"
      },
      "tagline" : "You Know, for Search"
    }

</code></pre>
<h3>Adding Basic HTTP Authentication</h3>
<p>To setup basic HTTP authentication, we need to create a password file. The easiest way to do it is through apache-utils. We need to install it.</p>
<pre><code class="bash">    sudo apt-get install apache2-utils

</code></pre>
<p>Now lets create a password file with the command <code>htpasswd</code>.  By default htpasswd encrypts the password using MD5 encryption.  We can also do bcrypt encryption(-B), SHA encryption(-s) or plaintext(-p) using the switches specified in the brackets. Please check the man pages for more details (<code>man htpasswd</code>).</p>
<pre><code class="bash">    sudo htpasswd -c /etc/elasticsearch/user.pwd username

</code></pre>
<p>htpasswd will prompt you for a password.</p>
<pre><code class="bash">    New password: 
    Re-type new password: 
    Adding password for user username

</code></pre>
<p>Now a file <code>/etc/elasticsearch/user.pwd</code> will be created with the username and password specified in the following format.</p>
<pre><code class="bash">    login:password

</code></pre>
<p>If the password is encrypted, the encrypted password will be prefixed with a <code>$code$</code> where the code identifies the encryption used.</p>
<p>Now we need to add this to our nginx's es.domain.com configuration. We will add the following lines to <code>/etc/nginx/sites-available/elasticsearch</code>.</p>
<pre><code class="bash">    auth_basic "Elasticsearch Authentication";
    auth_basic_user_file /etc/elasticsearch/user.pwd;

</code></pre>
<p><code>auth_basic</code> is the message that the browser displays when prompting for an username &amp; password. <code>auth_basic_user_file</code> is the path to the password file.</p>
<p>The file <code>/etc/nginx/sites-available/elasticsearch</code> should look like this.</p>
<pre><code class="bash">    server {
        listen 80;
        server_name es.domain.com;
        location / {
            rewrite ^/(.*) /$1 break;
            proxy_ignore_client_abort on;
            proxy_pass http://localhost:9200;
            proxy_redirect http://localhost:9200 https://es.domain.com/;
            proxy_set_header  X-Real-IP  $remote_addr;
            proxy_set_header  X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header  Host $http_host;
            auth_basic "Elasticsearch Authentication";
            auth_basic_user_file /etc/elasticsearch/user.pwd;
    }
    }

</code></pre>
<p>Now lets reload nginx for the added configuration to take effect.</p>
<pre><code class="bash">    sudo service nginx reload

</code></pre>
<h4>Testing HTTP authentication</h4>
<p>Trying to access Elasticsearch without authentication should cause an error.</p>
<pre><code class="bash">    curl http://es.domain.com

</code></pre>
<p>should result in</p>
<pre><code class="html">    &lt;html&gt;
    &lt;head&gt;&lt;title&gt;401 Authorization Required&lt;/title&gt;&lt;/head&gt;
    &lt;body bgcolor="white"&gt;
    &lt;center&gt;&lt;h1&gt;401 Authorization Required&lt;/h1&gt;&lt;/center&gt;
    &lt;hr&gt;&lt;center&gt;nginx/1.4.1 (Ubuntu)&lt;/center&gt;
    &lt;/body&gt;
    &lt;/html&gt;

</code></pre>
<p>Now let us try the same command with authentication. It should succeed this time.</p>
<pre><code class="bash"><br />    curl -u username http://es.domain.com

</code></pre>
<p>will prompt you for the password.</p>
<pre><code class="bash">    Enter host password for user 'username':

</code></pre>
<p>Following the correct password, you should get the status message</p>
<pre><code class="python">    {
      "status" : 200,
      "name" : "Steel Spider",
      "version" : {
        "number" : "1.2.1",
        "build_hash" : "6c95b759f9e7ef0f8e17f77d850da43ce8a4b364",
        "build_timestamp" : "2014-06-03T15:02:52Z",
        "build_snapshot" : false,
        "lucene_version" : "4.8"
      },
      "tagline" : "You Know, for Search"
    }

</code></pre>
<p>###Setting up HTTPS connection<br />
The problem with basic HTTP authentication is that the password is sent out in plaintext.  Even if we use the digest authentication, there is the opportunity for man-in-the-middle attacks. So a better option is to make an HTTPS connection to ensure no eavesdropping. For that we need an SSL Certificate. Since I the whole setup was not meant for any external entity I chose to use a self-signed SSL certificate.  You could also get a SSL certificate signed by Certificate Authorities.</p>
<h4>Generating the certificate</h4>
<p>Let us store the certificates in elasticsearch's config folder.</p>
<pre><code class="bash">    sudo mkdir /etc/elasticsearch/ssl
    cd /etc/elasticsearch/ssl

</code></pre>
<p>Now let us generate the private server key.</p>
<pre><code class="bash">    sudo openssl genrsa -des3 -out es_domain.key 1024

</code></pre>
<p>The tool will prompt you for a passphrase. This is important as we will use it again later.</p>
<pre><code class="bash">    Generating RSA private key, 1024 bit long modulus
    ............++++++
    .....++++++
    e is 65537 (0x10001)
    Enter pass phrase for es_domain.key:
    Verifying - Enter pass phrase for es_domain.key:

</code></pre>
<p>Next, we need to generate a Certificate Signing Request(CSR) using the key we already generated.</p>
<pre><code class="bash">    sudo openssl req -new -key es_domain.key -out es_domain.csr

</code></pre>
<p>This will prompt for the passphrase.  We need to enter the same passphrase we entered before.  The most important field is the <code>Common Name</code> where we need to provide the domain for which this will be used.  The <code>Challenge Password</code> and <code>Company Name</code> can be left blank.</p>
<pre><code>
Country Name (2 letter code) [AU]:IN
State or Province Name (full name) [Some-State]:State
Locality Name (eg, city) []:City
Organization Name (eg, company) [Internet Widgits Pty Ltd]:Company
Organizational Unit Name (eg, section) []:Data
Common Name (e.g. server FQDN or YOUR name) []:<strong><em><u>es.domain.com</u></em></strong>
Email Address []:addr@server.com
Please enter the following 'extra' attributes
to be sent with your certificate request
A challenge password []:
 An optional company name []:
</code></pre>
<p>Now we need to remove the passphrase from the key. Else, everytime nginx starts we would have to manually enter the passphrase before nginx can work.</p>
<pre><code class="bash">    sudo cp es_domain.key es_domain.key.bk
    sudo openssl rsa -in es_domain.key.bk -out es_domain.key

</code></pre>
<p>Now we are finally ready to generate the certificate.</p>
<pre><code class="bash">    sudo openssl x509 -req -days 3650 -in es_domain.csr -signkey es_domain.key -out es_domain.crt

</code></pre>
<p>Here the days parameter specifies for how many days the certificate is valid. We have set it to 10 years :)</p>
<p>Now we need to edit our virtual host file <code>/etc/nginx/sites-available/elasticsearch</code> to display the certificate and accept HTTPS connections.</p>
<p>We need to change the port from 80 to 443.</p>
<pre><code class="bash">    listen 443;

</code></pre>
<p>We need to turn on ssl and add the ssl certificates.</p>
<pre><code class="bash">    ssl on;
    ssl_certificate /etc/elasticsearch/ssl/es_domain.crt;
    ssl_certificate_key /etc/elasticsearch/ssl/es_domain.key;

</code></pre>
<p>Let us also add a new virtual host configuration to ensure all HTTP traffic to our system is redirected to HTTPS.</p>
<pre><code class="bash">    server{
        listen 80;
        server_name es.domain.com;
        return 301 https://$host$request_uri;
    }

</code></pre>
<p>It is also better to designate seperate log files for elasticsearch subdomain access, else, if nginx has other virtual hosts, which will often be the case, the common log files will be flooded with elasticsearch access requests.<br />
First lets create the log directory.</p>
<pre><code class="bash">    sudo mkdir -p /var/log/nginx/elasticsearch/
    sudo chown www-data:www-data /var/log/nginx/elasticsearch/

</code></pre>
<p>Lets add the following lines to the server config.</p>
<pre><code class="bash">    access_log /var/log/nginx/elasticsearch/access.log;
    error_log /var/log/nginx/elasticsearch/error.log debug;

</code></pre>
<p>Your <code>/etc/nginx/sites-available/elasticsearch</code> should look like this</p>
<pre><code class="bash">    server {
        listen 443;
        server_name es.domain.com;
        ssl on;
        ssl_certificate /etc/elasticsearch/ssl/es_domain.crt;
        ssl_certificate_key /etc/elasticsearch/ssl/es_domain.key;
        access_log /var/log/nginx/elasticsearch/access.log;
        error_log /var/log/nginx/elasticsearch/error.log debug;

        location / {
            rewrite ^/(.*) /$1 break;
            proxy_ignore_client_abort on;
            proxy_pass http://localhost:9200;
            proxy_redirect http://localhost:9200 https://es.domain.com/;
            proxy_set_header  X-Real-IP  $remote_addr;
            proxy_set_header  X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header  Host $http_host;
            auth_basic "Elasticsearch Authentication";
            auth_basic_user_file /etc/elasticsearch/user.pwd;
    }
    }
    server {
        listen 80;
        server_name es.domain.com;
        return 301 https://$host$request_uri;
    }

</code></pre>
<h4>Testing HTTPS Connection</h4>
<p>As this is a self-signed certificate, most browsers/tools will not recognize it. So we have to copy the certificate file to our local machine.</p>
<pre><code class="bash">    sudo scp server_user@server.com:/etc/elasticsearch/ssl/es_domain.crt /local/path/to/store/cert
</code></pre>
<p>Now let us test it.</p>
<pre><code class="bash">    curl --cacert es_domain.crt --tlsv1 -u username https://es.domain.com

</code></pre>
<p>Once you enter the password, you will be able to see the familiar status message.</p>
<p>You can also point the browser to the url.  The browser will flash a warning about unknown certificate. You need to add an exception for the elasticsearch site. Then You will be able to see the status message.</p>
<h3>Accessing the Protected Elasticsearch Programmatically</h3>
<p>I'm going to show an example of how to access Elasticsearch from python as that is the language I'm currently using.</p>
<p>First we need to install python-elasticsearch and python-requests.  It is advisable to set up a virtual environment specific to this project.  Please refer to <a href="http://docs.python-guide.org/en/latest/dev/virtualenvs/">Python Guide for VirtualEnv</a> on how to set it up.</p>
<pre><code class="bash">    pip install elasticsearch requests

</code></pre>
<p>Now we need to provide the certificate, the domain where elasticsearch is running, the port and flag to indicate ssl should be used for communication.  By setting a environment variable, we can enable requests to pick up the certificate to be used for verification.</p>
<pre><code class="bash">    export REQUESTS_CA_BUNDLE=/local/path/to/certificate.crt

</code></pre>
<p>Now comes the python program <code>elasticsearch_test.py</code> with connection class et all.  There are four different connection classes but RequestsHttpConnection provides the easiest way to specify the certificate to use for verification.</p>
<pre><code class="python">    from elasticsearch import Elasticsearch as ES, RequestsHttpConnection as RC 
    host_params = {'host':'es.domain.com', 'port':443, 'use_ssl':True}
    es = ES([host_params], connection_class=RC, http_auth=('username', 'password'),  use_ssl=True)
    post = {
                "title": "My Document",
                "body": "Hello world."
            }
    print es.index(
                index="blog",  
                doc_type="post",
                body=post
            )

</code></pre>
<p>Running it</p>
<pre><code class="bash"><br />    python elasticsearch_test.py

</code></pre>
<p>produces the output below indicating successful connection.</p>
<pre><code class="python"><br />    {u'_type': u'post', u'_id': u'iWRKzIKdTBmrpK-CgAn9mg', u'created': True, u'_version': 1, u'_index': u'blog'}

</code></pre>
<p>Congratulations! You have successfully setup a secure elasticsearch server!</p>
