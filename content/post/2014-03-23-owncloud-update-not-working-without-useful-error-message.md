---
title: ownCloud update not working without useful error message
layout: post
date: 2014-03-23
categories:
  - Configuration

---
Today, the most annoying thing happened to me. I wanted to update my ownCloud instance from version 6.0.0a to the newest version 6.0.2. In principle, this should be an easy task. Simply login as an admin user, go to Settings (top right) -> Admin -> Update Center. Afterwards, click on the update tab and simply click update.

But after waiting a few seconds, the process died with the most useless error message I ever saw: &#8220;Please fix this and retry!&#8221;. Well, fix what??

The solution was to look into the error log of the web-server. A cryptical message of the php-fpm daemon (I&#8217;m running nginx) revealed, that PHP simply died with the message &#8220;Allowed memory size of 134217728 bytes exhausted&#8221;. This can simply be fixed by modifying the `php.ini` file. For my setup, it was enough to raise the limit specified by the `memory_limit` variable to `256M`. The file `php.ini` can be located in one of the following paths

  * `/etc/php5/fpm/php.ini`
  * `/etc/php5/apache2/php.ini`
  * `/etc/php5/cli/php.ini`

depending on your configuration.
