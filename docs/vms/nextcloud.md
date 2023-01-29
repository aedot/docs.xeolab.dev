---
title: Nextcloud on Ubuntu 22.04
outdated: false
---

## What is Nextcloud?

Nextcloud advertises itself as a safe home for user data and documents. Developed with an open source philosophy in mind, Nextcloud provides users with much more control and flexibility than the alternatives. Some of the main advantages of Nextcloud include:

- Users can host documents on their own server and do not have to rely on other vendors for hosting.
- Finely-grained file access and sharing controls, along with workflow and audit logs.
- Included full-text search engine that can query the entire collection of files.
- Able to monitor and record all data exchanges and communications.
- Offers end-to-end client side encryption and key handling.
- Includes real-time notifications, comments, and multi-user editing.
- Prioritizes security through third-party reviews and a well-funded Security Bug Bounty program.
- Works together with the developer and user communities to develop, optimize, and test new features.
- Has an [app store](https://apps.nextcloud.com/) where users can download additional extensions and customizations.

See the [Nextcloud feature comparison](https://nextcloud.com/compare/) for a more complete analysis. For complete information on how to use Nextcloud, consult the [Nextcloud product documentation](https://docs.nextcloud.com/server/24/admin_manual/contents.html).

## Prerequisites

### Installing Nextcloud Prerequisites

Nextcloud requires a LAMP stack to work properly. This section provides instructions on how to install the Apache web server, MariaDB RDBMS, and the PHP programming languages. While these instructions are geared towards Ubuntu 22.04 users, they are also broadly applicable to Ubuntu 20.04.

!!! Notes
    Nextcloud only recently added support for PHP 8.1 in version 24. PHP 8.1 is the default PHP library package in Ubuntu 22.04. Earlier versions of Nextcloud must use PHP 7.4. This might require a downgrade of the local PHP packages.

#### Installing Apache Web server

Install and test Apache web server on Ubuntu 22.04:

1. Update and upgrade the Ubuntu packages:

        sudo apt update && sudo apt upgrade

2. Install the Apache web server using `apt`:

        sudo apt install apache2 ufw

3. Configure the `ufw` firewall to allow the `Apache Full` profile. This permits HTTP and HTTPS connections, enabling web access. `OpenSSH` connections must also be allowed. Enable `ufw` when all changes are complete.

        sudo ufw allow OpenSSH
        sudo ufw allow in "Apache Full"
        sudo ufw enable

4. Verify the firewall settings using the `ufw` status command:

        sudo ufw status

    ![](/assets/nextcloud/ufw_status.png){width="527"}

5. Enable the `mpm_prefork` Apache module and disable `mpm_event`:

        sudo a2dismod mpm_event
        sudo a2enmod mpm_prefork

6. Restart Apache using the `systemctl` utility:

        sudo systemctl restart apache2

7. Ensure the web server is still active using `systemctl`:

        sudo systemctl status apache2

8. Visit the IP address of the web server and confirm the server is working properly:

        http://your_IP_address/

!!! Info
    The default Ubuntu/Apache2 welcome page appears in the browser. The page features the message “It works” and details some basic information about the server:

<INSERT APACHE2 DEFAULT PAGE PIC>

### Installing MariaDB RDBMS

Nextcloud stores its information inside an RDBMS, such as MySQL or MariaDB. This guide uses MariaDB. MariaDB is very similar to MySQL, but it more closely follows the open source philosophy. MariaDB provides more features than MySQL does. It also has better performance and usability.

To install MariaDB on Ubuntu 22.04, follow the steps in this example.

1. Install MariaDB using `apt`:

        sudo apt install mariadb-server

2. Verify the status of MariaDB to ensure it is installed correctly:

        sudo systemctl status mariadb

3. Enable MariaDB in `systemctl` so it automatically activates upon server boot up:

        sudo systemctl enable mariadb

4. Configure and secure MariaDB using the `mysql_secure_installation` utility:

        sudo mysql_secure_installation

Enter your password. It is not necessary to switch to Unix socket authentication or change the root password. Answer `Y` to the following questions:

???+ Info inline end
    For further information about MariaDB, consult the [MariaDB Server Documentation](https://mariadb.com/kb/en/documentation/).

```
    Remove anonymous users?
    Disallow root login remotely?
    Remove test database and access to it?
    Reload privilege tables now?
```

### Creating Nextcloud database in MariaDB

When MariaDB is installed, create a new database for Nextcloud to use. It is also necessary to create a user for the database and grant this user additional permissions. To configure the database, follow these instructions.

1. Log into MariaDB as the `root` user. If you added a root password, provide it when requested. The MariaDB prompt appears.

        sudo mysql -u root

    ![](/assets/nextcloud/mariaDB_monitor.png){width="810"}

2. Create the `nextcloud` database. For this and all remaining commands, MariaDB should reply with `Query OK`.

        CREATE DATABASE nextcloud;

3. Use the `SHOW DATABASES` command to ensure the database has been properly created:

        SHOW DATABASES;

    ![](/assets/nextcloud/mariadb_database.png){width="352"}

4. Create a user and grant them all rights to access the database. In place of `password`, provide a more secure password.

        GRANT ALL PRIVILEGES ON nextcloud.* TO 'nextcloud'@'localhost' IDENTIFIED BY 'password';

5. Flush the privileges to apply the recent changes:

        FLUSH PRIVILEGES;

6. Exit the database:

        quit

### Installing PHP and Other Components

Nextcloud uses the PHP programming language. Nextcloud version 25 supports PHP release 8.1. This is also the default release of PHP in the Ubuntu packages, so the regular `php` package can be installed. However, PHP 7.4 is required to use earlier versions of Nextcloud.
!!! info
    To install PHP 7.4, substitute `php7.4` in place of php for all packages. For example, `php-cli` becomes `php7.4-cli`.

To install PHP and the other required packages, use these commands.

1. Install the core PHP package using `apt`:

        sudo apt install php

2. Confirm the PHP release number:

        php -v

    ![](/assets/nextcloud/php_version.png){width="792"}

3. Install the remaining PHP components:

        sudo apt install php-apcu php-bcmath php-cli php-common php-curl php-gd php-gmp php-imagick php-intl php-mbstring php-mysql php-zip php-xml

4. Enable the necessary PHP extensions:

        sudo phpenmod bcmath gmp imagick intl

5. Install the `unzip` utility. This utility might already be installed on the system.

        sudo apt install unzip

6. Install the libmagic package:

        sudo apt install libmagickcore-6.q16-6-extra

## Download, Install & Configure Nextcloud

Nextcloud can be downloaded using wget. After unzipping the downloaded file, a virtual host must be created for Nextcloud. Some additional configurations and optimizations must also be applied to the system.

### Downloading and Installing Nextcloud

To download and install Nextcloud, follow these steps.

1. Download Nextcloud using `wget`. To find the URL for the latest stable release of Nextcloud, visit the [Nextcloud installation page](https://nextcloud.com/install/). This page provides a link to the latest Nextcloud zip file. To locate a particular release of Nextcloud, consult the [Nextcloud changelog and archive](https://nextcloud.com/changelog/). The following example demonstrates how to download the Nextcloud latest release (25.0.2 at time of documentation).

        wget https://download.nextcloud.com/server/releases/latest.zip

2. Unzip the archive. This creates a `nextcloud` folder in the same directory as the zip file.

        unzip latest.zip

3. Change the folder permissions for the `nextcloud` directory:

        sudo chown -R www-data:www-data nextcloud

4. Move the new directory to the server directory. The server directory usually defaults to `/var/www/html` on most servers.

        sudo mv nextcloud /var/www/html

5. Disable the default Apache landing page:

        sudo a2dissite 000-default.conf

!!! Warning
    ignore the advisory to reload Apache at this time. Apache should be reloaded later when all configuration is complete.

### Creating a Virtual Host File for Nextcloud

This section explains how to configure a virtual host file for the Nextcloud application. The virtual host tells Apache how to handle and serve requests for the Nextcloud domain.

1. Create a new file in the `etc/apache2/sites-available` directory and name the file `nextcloud.conf`:

        sudo nano /etc/apache2/sites-available/nextcloud.conf

2. The file must include the following information. The `DocumentRoot` is the name of the server directory followed by `/nextcloud`. For the `ServerName` attribute, enter the actual name of the domain instead of `example.com`. Save the file when all changes have been made.

        <VirtualHost *:80>
            DocumentRoot "/var/www/html/nextcloud"
            ServerName example.com

            <Directory "/var/www/html/nextcloud/">
                Options MultiViews FollowSymlinks
                AllowOverride All
                Order allow,deny
                Allow from all
            </Directory>

            TransferLog /var/log/apache2/nextcloud_access.log
            ErrorLog /var/log/apache2/nextcloud_error.log

        </VirtualHost>

3. Enable the site. Do not reload Apache yet.

        sudo a2ensite nextcloud.conf

### Optimizing PHP for Nextcloud

The default PHP implementation is fine for most applications. But certain PHP settings must be adjusted to allow for peak Nextcloud performance and operations. To make the necessary adjustments, follow these steps.

1. Edit the `php.ini` file and make the following changes. In some cases, the parameter might be commented out and must be uncommented. To uncomment a parameter, delete the `;` character at the start of the line. Leave the remaining lines unchanged.
!!! Note
    To locate the correct timezone for the `date.timezone` parameter, consult the [PHP timezone documentation](https://www.php.net/manual/en/timezones.europe.php).

    If the server is running an earlier PHP release, substitute the actual release number in place of `8.1` in the filename. For example, to configure PHP 7.4, the filename is `/etc/php/7.4/apache2/php.ini`

```
    sudo nano /etc/php/8.1/apache2/php.ini
```

```
max_execution_time = 360
memory_limit = 512M
post_max_size = 200M
upload_max_filesize = 200M
date.timezone = Europe/London
opcache.enable=1
opcache.memory_consumption=128
opcache.interned_strings_buffer=8
opcache.max_accelerated_files=10000
opcache.revalidate_freq=1
opcache.save_comments=1
```

2. Enable some additional Apache modules:

        sudo a2enmod dir env headers mime rewrite ssl

3. Restart the Apache server:
        sudo systemctl restart apache2

4. Verify the Apache server status and ensure it is still `active`. If the server is in a failed state, examine the server error logs and make any necessary changes to the `/etc/apache2/sites-enabled/nextcloud.conf` file.

        sudo systemctl status apache2

### Setting up Nextcloud Using the Web Interface

The remaining Nextcloud configuration tasks can be accomplished using the web interface. To configure and activate Nextcloud, follow these steps.

1. Visit the domain associated with the server. The Nextcloud configuration page appears in the browser window. In the following example, replace `example.com` with the name of the domain: `http://example.com/`.

    ![](/assets/nextcloud/nextcloud_web.png){width="457"}

2. On this page, perform the following tasks:

    - Create an administrative account. Provide a user name and password for the account.
    - Leave the address for the Data Folder at the current value.
    - In the Configure the database section, add information about the `nextcloud` database. Enter the user name and password for the account created in MariaDB earlier. The database name is `nextcloud`. Leave the final field set to `localhost`.
    - Click <b>Install</b> to complete the form.

3. Nextcloud proceeds to set up the application. This might take a minute or two. On the next page, Nextcloud asks whether to install a set of recommended applications. Click <b>Install recommended apps</b> to continue.

    ![](/assets/nextcloud/nextcloud_recommendation.png){width="920"}

4. Nextcloud displays a series of welcome slides. Click the right arrow symbol on the right-hand side of the page to walk through the slides. Read through each slide, recording any important information.

5. On the final welcome page, select <b>Start using Nextcloud</b> to proceed to the Nextcloud dashboard.

6. The browser now displays the Nextcloud Dashboard page.

### Misc. Tweaks and Adjustments

Correct the permissions of the config.php file

We definitely wouldn’t want the config.php file to fall into the wrong hands, as it contains valuable setup information regarding our Nextcloud setup. Let’s adjust the permissions to better protect it.

```
sudo chmod 660 /var/www/html/nextcloud/config/config.php
```

```
sudo chown root:www-data /var/www/html/nextcloud/config/config.php
```

Enable memory caching

Edit the Nextcloud config file:

```
sudo nano /var/www/html/nextcloud/config/config.php
```

Add the following line to the bottom:

```
'memcache.local' => '\\OC\\Memcache\\APCu',
```

Resolving warnings pertaining to the default phone region

Edit the Nextcloud config file:

```
sudo nano /var/www/nextcloud.learnlinux.cloud/config/config.php
```

Add the following line to the bottom of the file:

```
'default_phone_region' => 'US',
```

Enabling Strict Transport Security

Edit the SSL config file for our Nextcloud installation:

```
sudo nano /etc/apache2/sites-available/default-ssl.conf
```

Add the following line after the ServerName line:

```
<IfModule mod_headers.c>
    Header always set Strict-Transport-Security "max-age=15552000; includeSubDomains"
</IfModule>
```
