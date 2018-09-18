## Challenges

Some of the following challenges can be done in the labs. Others might be consider work for home or preparation for the next session.

### Build an operational Raspbian system

Start by creating an SD card with Raspbian Stretch Lite.

Setup a serial connection with the Raspberry Pi and determine it's MAC and IP address.

Connect to your Raspberry Pi via SSH.

Update the system.

### Setup a WebServer

Setup a webserver on your Raspberry Pi. You can follow this online tutorial: [https://thepi.io/how-to-set-up-a-web-server-on-the-raspberry-pi/](https://thepi.io/how-to-set-up-a-web-server-on-the-raspberry-pi/).

Do make sure to install the packages `php7.0-fpm php7.0-mysql` instead of `php5-fpm php5-mysql`.

Create a file `/var/www/html/index.php` with the following content:

```php
<?php
// Show all information, defaults to INFO_ALL
phpinfo();
?>
```

The result should be:

![PHPInfo via nGinx](img/php_info.png)