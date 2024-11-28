# cacti

### installation
nano /var/www/html/cacti/include/config.php
...
$database_username = 'cacti';
$database_password = 'cacti';
...

nano /etc/systemd/cactid
...
User=www-data
Group=www-data
EnvironmentFile=/etc/default/cactid
...

config mysql   heure, cache ...
nano /etc/mysql/my.cnf


sudo apt install libapache2-mod-php

chown -r www-data:www-data /var/www/html/cacti/plugin/weathermap
chown -r www-data:www-data /var/www/html/cacti/plugin/sources

