---
 
title: Setting up a SaaS app with Django Cookie Cutter and Docker all ready to go
pubDate: 2018-10-04 18:15:49.000000000 +05:30

category:  Uncategorised
tags: []
meta:
  _wpcom_is_markdown: '1'
  _edit_last: '1'
  _yoast_wpseo_content_score: '60'
  _yoast_wpseo_primary_category: ''
  _wpas_done_all: '1'
author:  sharmi
 
canonical: "https://www.minvolai.com/setting-up-a-saas-app-with-django-cookie-cutter-and-docker-all-ready-to-go/"
slug: setting-up-a-saas-app-with-django-cookie-cutter-and-docker-all-ready-to-go
description: "This is a detailed look at setting up a production level django project using cookie-cutter django.  I explore the different choices and project layout of cookiecutter django as we go along setting up the project."
---
<p>This is a detailed look at setting up a production level django project using cookie-cutter django.  I explore the different choices and project layout of cookiecutter django as we go along setting up the project.</p>
<p>A long long time ago, so long time ago, no one knows how long ago (ok maybe a couple of years back), I installed docker on my machine and created a few containers. So, then, my machine had less space and I wanted to remove a few of those containers because I have no use for them. I try to delete them, and lo and behold, that does not work. Docker internally uses a different filesystem structure and it has some issues with deleting containers. There had been open tickets for it for sometime and yet it was not prioritized. There is nothing I could do short of purging docker and all its data from my system. Bump :( .</p>
<p>So you can understand my reluctance to install docker directly on my machine. I always setup a vagrant machine and install docker in that. As an added bonus, I can also ensure the same setup works on any vanilla box. I use the Ubuntu LTE version.</p>
<p>The sequence of steps I follow to install docker.</p>
<p>To Install docker: <a href="https://docs.docker.com/install/linux/docker-ce/ubuntu/#install-docker-ce-1">https://docs.docker.com/install/linux/docker-ce/ubuntu/#install-docker-ce-1</a></p>
<p>To install docker-compose: <a href="https://docs.docker.com/compose/install/#install-compose">https://docs.docker.com/compose/install/#install-compose</a></p>
<p>If you run into issues while installing docker like</p>
<pre><code>docker-ce : Depends: libseccomp2 (&gt;= 2.3.0) but 2.2.3-3ubuntu3 is to be installed
</code></pre>
<p>run the following commands</p>
<pre><code>    sudo apt-get install aptitude #Incase you don't have aptitude installed already
    sudo aptitude install docker-ce
</code></pre>
<p>The aptitude command will give you different options to workaround the dependency problem.</p>
<p>Next, we need to ensure we have pip and python 3.6.<br />
sudo apt-get install libssl-dev</p>
<p><a href="https://tecadmin.net/install-python-3-6-ubuntu-linuxmint/">https://tecadmin.net/install-python-3-6-ubuntu-linuxmint/</a></p>
<p>pip3.6 install cookiecutter</p>
<p>Change the current dir to the one when the code will be developed</p>
<p>cookiecutter <a href="https://github.com/pydanny/cookiecutter-django.git">https://github.com/pydanny/cookiecutter-django.git</a></p>
<p>cookiecutter will ask you a set of questions to setup django.<br />
Use the following url to understand the options</p>
<p>One of the questions will ask for a ‘slug’ for the project. This will be sued for your repo and in other places where a Python-importable version of your project name is needed. Enter your project’s slug without dashes or spaces.</p>
<pre><code>vagrant@ubuntu-bionic:/vagrant$ cookiecutter https://github.com/pydanny/cookiecutter-django.git

You've downloaded /home/vagrant/.cookiecutters/cookiecutter-django before. Is it okay to delete and re-download it? [yes]: no
Do you want to re-use the existing version? [yes]: yes
project_name [My Awesome Project]: Site Fitness
project_slug [site_fitness]: 
description [Behold My Awesome Project!]: Website Availability &amp; Status Checker
author_name [Daniel Roy Greenfeld]: Sharmila Gopirajan Sivakumar
domain_name [example.com]: sitefitness.com
email [sharmila-gopirajan-sivakumar@example.com]: sharmila.gopirajan@gmail.com
version [0.1.0]:

Select open_source_license:
1 - MIT
2 - BSD
3 - GPLv3
4 - Apache Software License 2.0
5 - Not open source

Choose from 1, 2, 3, 4, 5 [1]: 5

timezone [UTC]: IST
windows [n]: n
use_pycharm [n]: y
use_docker [n]: y

Select postgresql_version:
1 - 10.4
2 - 10.3
3 - 10.2
4 - 10.1
5 - 9.6
6 - 9.5
7 - 9.4
8 - 9.3
Choose from 1, 2, 3, 4, 5, 6, 7, 8 [1]: 1

Select js_task_runner:
1 - None
2 - Gulp
Choose from 1, 2 [1]: 2

custom_bootstrap_compilation [n]: y
use_compressor [n]: y
use_celery [n]: y
use_mailhog [n]: y
use_sentry [n]: y
use_whitenoise [n]: y
use_heroku [n]: n
use_travisci [n]: y
keep_local_envs_in_vcs [y]: y
debug [n]: y

 [WARNING]: Docker and Gulp JS task runner working together not supported yet. You can continue using the generated project like you normally would, however you would need to add a JS task runner service to your Docker Compose configuration manually.

 [SUCCESS]: Project initialized, keep up the good work!
</code></pre>
<p>There will be a folder created with the name containing all the generated code.</p>
<pre><code>cd &lt;slug&gt;
</code></pre>
<p>You should find the following files.</p>
<pre><code>README.rst  config  gulpfile.js  locale     merge_production_dotenvs_in_dotenv.py  production.yml  requirements  sitefitness   compose     docs    local.yml    manage.py  package.json                           pytest.ini      setup.cfg
</code></pre>
<ul>
<li>To setup docker locally we need <code>local.yml</code> file. For deployment we need <code>production.yml</code></li>
<li><code>README.rst</code> contains the following setup instructions.</li>
<li>config contains <code>settings</code> folder, <code>wsgi.py</code> and <code>urls.py</code></li>
<li><code>gulpfile.js</code> contains the sass compilation, autoprefixing css, minification, and js minification code</li>
<li>[WARNING]: Docker and Gulp JS task runner working together not supported yet. You can continue using the generated project like you normally would, however you would need to add a JS task runner service to your Docker Compose configuration manually</li>
<li>local.yml and production.yml are configurations files for docker-compose to setup local environment and production environment respectively. Both spin up containers for django, postgres, redis, celeryworker, celerybeat and flower. local.yml also setups up mailhog for local email testing, whereas production.yml additionaly setups up caddy for hosting. The filesystem within docker is not persistent. So we just have to configure the following disk volumes in the yml file that map to host filesystem to enable peristence for data.
<ul>
<li>local_postgres_data: {}</li>
<li>production_postgres_data: {}</li>
<li>local_postgres_data_backups: {}</li>
<li>production_postgres_data_backups: {}</li>
<li>production_caddy: {}</li>
</ul>
</li>
<li>requirements directory contains 3 requrements file. base.txt contains packages common to both local and production environment. local.txt and production.txt include requirements from base.txt and further more add local/production specific packages.</li>
<li>package.json maintains the requirements for gulp.</li>
<li>pytest.ini - configuration file for pytest module</li>
<li>site_fitness - All apps reside here.</li>
<li>I am not sure about merge_production_dotenvs_in_dotenv.py. I have no use for locale yet.</li>
</ul>
<p>If you going to use any ip other than <code>"localhost", "0.0.0.0", "127.0.0.1"</code> to access the server, add that ip to the <code>INTERNAL_IPS</code> or <code>ALLOWED_HOSTS</code> setting in <code>config/settings/local.py</code>. For example, I will be running the server in a Vagrant machine, whose ip will be <code>192.168.0.101</code>. So I will add that IP to <code>ALLOWED_HOSTS</code> setting.</p>
<p>Yay! all the setup is done!<br />
Lets begin building.</p>
<pre><code>docker-compose -f local.yml build
</code></pre>
<p>This starts downloading the docker images for postgres, mailhog, django, redis, celery and setup them up.</p>
<p>To bring up django and postgresql, run</p>
<pre><code>docker-compose -f local.yml up
</code></pre>
<p>Change the volumes params in local.yml. Make them point to the local host directory for persistence.</p>
<h2 id="setup-a-blog">Setup a blog</h2>
<p>The main contention was between Zinnia and Wagtail-Puput.</p>
<h3 id="why-zinnia">Why Zinnia?</h3>
<p>Zinnia is a fairly mature blogging platform based on Django. It replicates most of wordpress features. Wagtail gives a great modular way to setup a CMS and I have been seeing rave reviews for it. But it feels more like Lego blocks than a full-fledged system (maybe this is my lack of understanding, but not because of lack of trying). Zinnia on the other hand can be easily setup in a second. The admin ui is quite lacking compared to Wagtail-Puput but hey, if I do have to migrate to Puput, Puput has a zinna to Puput migrator ;)</p>
<p>To install, add the following line to <code>requirements/base.txt</code></p>
<pre><code>#zinnia blog
django-blog-zinnia
</code></pre>
<p>Then run the following to install <code>zinnia</code> and its dependencies.</p>
<pre><code>sudo docker-compose -f local.yml build
</code></pre>
<p>the following line to <code>config/settings/base.py</code></p>
<pre><code> ZINNIA_APPS = ['django_comments',
               'mptt',
               'tagging',
               'zinnia',
               ]
INSTALLED_APPS = DJANGO_APPS + THIRD_PARTY_APPS + LOCAL_APPS + ZINNIA_APPS
</code></pre>
<p>Zinnia should come after LOCAL_APPS because one of the local apps extends the User Model. Adding Zinnia before registering the User Model will result in the error <code>LookupError: Model 'users.User' not registered.</code>.</p>
<p>Zinnia needs a little more setup. Please refer to <a href="http://docs.django-blog-zinnia.com/en/develop/getting-started/install.html">http://docs.django-blog-zinnia.com/en/develop/getting-started/install.html</a></p>
<p>To setup bootstrap theme for Zinnia</p>
<ul>
<li>Add the following line to <code>requirements/base.txt</code>
<pre><code>django-app-namespace-template-loader
zinnia-theme-bootstrap
</code></pre>
</li>
<li>Include the following line in config/settings/base.py before the line <code>zinnia</code> in ZINNIA_APPS
<p><code>'zinnia_bootstrap',</code></li>
<li>Once installed, add app_namespace.Loader to the TEMPLATE_LOADERS setting of your project.</li>
</ul>
<p>```TEMPLATE_LOADERS = [<br />
‘app_namespace.Loader’,<br />
… # Other template loaders<br />
]<br />
With Django &gt;= 1.8 app_namespace.Loader should be added to the ‘loaders’ section in the OPTIONS dict of the DjangoTemplates backend instead. Note: With Django 1.8, app_namespace.Loader should be first in the list of loaders.</p>
<pre><code>TEMPLATES = [
    {
        'BACKEND': 'django.template.backends.django.DjangoTemplates',
        'OPTIONS': {
            'loaders': [
                'app_namespace.Loader',
                'django.template.loaders.filesystem.Loader',
                'django.template.loaders.app_directories.Loader',
            ],
        },
    },
]
</code></pre>
<ul>
<li>Run
<p>sudo docker-compose -f local.yml build<br />
sudo docker-compose -f local.yml run</li>
</ul>
