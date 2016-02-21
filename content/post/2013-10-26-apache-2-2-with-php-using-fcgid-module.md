---
title: Apache 2.2 with PHP using FCGID module
layout: post
date: 2013-10-26
categories:
  - Configuration
  - Linux

---
When using the <a title="Apache Webserver" href="https://httpd.apache.org/" target="_blank">Apache</a> webserver, you have multiple possibilities to integrate <a title="PHP" href="http://php.net/" target="_blank">PHP</a>.

One is to use mod_php. This module integrates PHP directly into the server itself. PHP is started when the server starts and runs under the same user as the Apache server itself.

An alternative configuration is to start a new PHP process for each request that serves a PHP script. This is know as CGI and is quite well supported by PHP and Apache alike. The problem in this scenario is that starting a PHP process per request means a lot of overhead. This slows down the overall performance. On the other hand, PHP can run as what ever user you like.

A tradeoff between the before mentioned alternatives is FastCGI or also called FCGI. This module starts a PHP process for a specific number of requests. When these requests are processed, the PHP process is terminated and a new one is started. This method avoids most of the overhead of CGI and the PHP process can also be started as a different user. Additionally, the PHP process and the web server process are separated.

The configuration of Apache for the PHP-FCGI method looks the following:

First install the Apache module and the PHP-CGI module. Under Ubuntu Linux this can be done using apt:

```
apt-get install libapache2-mod-fcgid php5-cgi
```

The PHP configuration (also known as php.ini) can be found under `/etc/php5/cgi/php.ini`. There you can adapt things like the maximum upload size. The next step is to configure Apache to use the PHP-CGI module.

First check that the module is activated by running the command `a2enmod fcgid`. Edit the main Apache configuration file found under `/etc/apache2/apache2.conf`. Add the following lines to the configuration:

```
FcgidMaxRequestsPerProcess 10000
AddHandler fcgid-script .php
FcgidWrapper /usr/bin/php5-cgi
```

The first line tells the FCGI-module the restart the PHP process after 10000 requests have been processed. The second line registers the FCGI-module to process file the ending `.php`. The last line tells the module which executable to use the start the PHP-process. The executable `php5-cgi` is installed by the package `php5-cgi` package we installed in the first step. Do not forget to restart the web server using `service apache2 restart`.

The PHP-Apache configuration is complete. In the site configurations you have to add the option `ExecCGI`, if PHP scripts should be executed for this site. My configuration looks like this:

```
...
DocumentRoot /data/www/www
<Directory />
    Options Indexes FollowSymLinks MultiViews ExecCGI
    AllowOverride All
    Order allow,deny
    allow from all
</Directory>
...
```

Test if the configuration is working by adding a PHP info file to the DocumentRoot:

```
<?PHP
phpinfo();
?>
```

If the site loads forever, check if you added the `ExecCGI` option in the `Directory` section of your site configuration.

Small remark at the end: This configuration does not start PHP as a different user. For this task you can use the <a title="SuExec module homepage" href="https://httpd.apache.org/docs/2.2/mod/mod_suexec.html" target="_blank">SuExec</a> Apache module.
