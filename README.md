# Ubuntu Commands - Cheat Sheet #

## User Administration ##

### change root password ###

`sudo -i passwd`

### switch user ###

`su {USERNAME}`
`sudo -iu {USERNAME}`

### give user sudo privileges ###

`usermod -aG sudo {USERNAME}`

### add user ###

`adduser {USERNAME}`

### delete user ###

`sudo deluser --remove-home {USERNAME}`

## SystemCtl (Services) ##

### Restart ###

`sudo systemctl restart {SERVICE_NAME}`

### Status ###

`sudo systemctl status {SERVICE_NAME}`

### Stop ###

`sudo systemctl stop {SERVICE_NAME}`

### Disable ###

`sudo systemctl disable {SERVICE_NAME}`

### Enable ###

`sudo systemctl enable {SERVICE_NAME}`

### Start ###

`sudo systemctl stop {SERVICE_NAME}`

## Services ##

### Start ###

`sudo service {SERVICE_NAME} start`

### Stop ###

`sudo service {SERVICE_NAME} stop`

### Restart ###

`sudo service {SERVICE_NAME} restart`

### Status ###

`sudo service {SERVICE_NAME} status`


### Status of All Services ###

See [here](https://askubuntu.com/a/422178) for more information.

`sudo service --status-all`

## Apache 2 ##

### Enable Site ###

`sudo a2ensite {CONF_FILE}`

### Disable Site ###

`sudo a2dissite {CONF_FILE}`

### Enable Mod ###

`sudo a2enmod {MOD_NAME}`

### Disable Mod ###

`sudo a2dismod {MOD_NAME}`

### Check Configuriation Syntax ###

`sudo apache2ctl configtest`

### Read Error Logs ###

`tail -f /var/log/apache2/error.log`

## Permissions and Ownerships ##

[Source](https://www.unixtutorial.org/difference-between-chmod-and-chown)

### Ownership ###

The chown command stands for "change owner", and allows changing the owner of a given file or folder, which can be a user and a group.
`sudo chown -R {USER}:{USER} {PATH}`

### Permissions ###

The chmod command stands for "change mode" and allows changing permissions of files and folders, also known as modes in UNIX.

The chmod command can be used in a couple of different ways, with permissions (or modes) set by numbers or by letters. Permissions can be given to a user who owns the file (u = user), group of said user (g = group), everyone else (o = others) or all users (a). And the basic permissions that can be given include read (r), write (w), and execute (x).

When using numbers you can use a numeric value such as 644 to set permissions. The position of the value represents to whom is the permission given, and the actual value represents which (or how much) permissions are given as a sum total of each permission's unique value.

First position (in the above example 6) refers to the user. Second refers to the group of the user, and the third refers to all others.

Numeric values for permissions are:

4 = read 2 = write 1 = execute

So a value of 4 will only give read rights, but a value of 6 will give read and write rights because it is a sum of 4 and 2.
5 will give only read ane execute rights, and 7 will give all rights.

`sudo chmod 644 {FILE_OR_FOLDER}`

Now, we can also use letters to accomplish the same thing, and we've already mentioned the relevant letters above. This is probably easier to remember than using numbers. For example, to accomplish the 644 permissions above we would run this:

`sudo chmod u+rw,go+r {FILE_OR_FOLDER}`

Finally, if we're setting permissions to a folder we need to specify the -R option (standing for "recursive").

## Packages (apt) ##

### Install ###

`sudo apt install {PACKAGE}`

### Uninstall ###

`sudo apt remove {PACKAGE}`
if you also want to remove the configs you'll need to purge it
`sudo apt purge {PACKAGE}` or `sudo apt remove {PACKAGE} --purge`

### Update Repos ###

`sudo apt update`

### List ###

`apt list --installed`

## Archives ##

### Create Archive ###

Creates an archive in the current directory of the stuff in the target directory.
`tar czvf {FILENAME}.tar.gz {PATH_TO_DIRECTORY}`

## Initial Server ToDos ##

Also have a look at [this](https://www.digitalocean.com/community/tutorials/initial-server-setup-with-ubuntu-18-04)

### Stuff to install ###

#### Nano (Editor) ####

`sudo apt install nano`

### Curl ####

`sudo apt install curl`

#### Ufw (Firewall) ####

`sudo apt install ufw`

#### Apache2 ####

`sudo apt update`
`sudo apt install apache2`

To allow http/https traffic to apache2
`sudo ufw app info "Apache Full"`
`sudo ufw allow in "Apache Full"`

#### MySQL Server ####

`sudo apt install mysql-server`
`sudo mysql_secure_installation`

#### PHP ####

`sudo apt install php`

If working with Apache2
`sudo apt install libapache2-mod-php`
Afterwards use `sudo nano /etc/apache2/mods-enabled/dir.conf` and move the `index.php` to the fron of the `Directory Index`
After that restart Apache2

If working with MySQL
`sudo apt install php-mysql`

#### Certbot for Apache (HTTPS with LetsEncrypt) ####

`sudo apt install python-certbot-apache`

Dry-run renewing certificates

`sudo certbot renew --dry-run`

After renewing reload apache.

#### SFTP ####

#### phpMyAdmin ####

`sudo apt install phpmyadmin php-mbstring php-gettext`

### Stuff to configure ###

#### Users ####

Create a sudo non root user by adding a user and giving it sudo privileges.
`adduser {USERNAME}`
`usermod -aG sudo {USERNAME}`

#### Firewall ####

Let Firewall (ufw) allow only SSH connections to the server
`ufw allow OpenSSH`
`ufw enable`

#### Add SSH key #####

Create an ssh public/private key pair e.g. by using PUTTYgen.
Copy the contents of the ssh key to `~/.ssh/authorized_keys` logged in as the user, that the keys are supposed to belong to.
Update the permission scheme of the newly created directory and file.
`chmod -R go= ~/.ssh`
`chown -R {USERNAME}:{USERNAME} ~/.ssh`

Try logging in using your new private/public key combination. If it works, you can disable password authentication.

#### Disable password authentication ####

Update the ssh configuration file.
`sudo nano /etc/ssh/sshd_config`
Set `PasswordAuthentication no` and `UsePAM no`
`sudo systemctl restart ssh`

#### Disable root login ####

Comment out `PermitRootLogin prohibit-password` and add `PermitRootLogin no` in `/etc/ssh/sshd_config`
Make sure, that you've got at least one user with sudo privileges, which is able to login (preferably via ssh).

#### Certbot ####

`sudo ufw delete allow "Apache"`
`sudo certbot --apache -d {SITE_DOMAIN} -d www.{SITE_DOMAIN}`
To renew certificate
`sudo certbot renew`

To make a renewal dryrun
`sudo certbot renew --dry-run`

#### MySQL Server ####

Log in with root
`mysql -u root -p`

Create userAccount and give it access to the database
`GRANT ALL ON {DATABASE_NAME}.* TO '{USERNAME}'@'localhost' IDENTIFIED BY '{PASSWORD}';`

Flush Privileges
`FLUSH PRIVILEGES;`

Exit
`EXIT;`

#### Apache ####

Give ownership of domain specific folder to apache2 user
`sudo chown -R www-data:www-data /var/www/{SOME_DOMAIN_NAME}`
`sudo find /var/www/{SOME_DOMAIN_NAME} -type d -exec chmod g+rx {} +`
`sudo find /var/www/{SOME_DOMAIN_NAME} -type f -exec chmod g+r {} +`
`sudo find /var/www/{SOME_DOMAIN_NAME} -type d -exec chmod g+s {} +`

#### phpMyAdmin ####

If you get the following error when logging in `The phpMyAdmin configuration storage is not completely configured` follow these steps:

```shell
cd /usr/share/doc/phpmyadmin/examples
sudo gunzip create_tables.sql.gz
mysql -u root -p < create_tables.sql
mysql -u root -p -e 'GRANT SELECT, INSERT, DELETE, UPDATE ON phpmyadmin.* TO 'pma'@'localhost' IDENTIFIED BY "pmapassword"'
```

Then edit `/etc/phpmyadmin/config.inc.php` and set the following

```php
$cfg['Servers'][$i]['controluser'] = 'pma';
$cfg['Servers'][$i]['controlpass'] = 'pmapassword';
```

See [here](https://askubuntu.com/questions/261858/the-phpmyadmin-configuration-storage-is-not-completely-configured) for further details.

##### Errors in certain phpmyadmin versions #####

If you get the following error on the import page `count(): Parameter must be an array or an object that implements Countable`
do the following
`sudo nano +613 /usr/share/phpmyadmin/libraries/sql.lib.php`

and replace

```php
((empty($analyzed_sql_results['select_expr']))
    || (count($analyzed_sql_results['select_expr'] == 1)
        && ($analyzed_sql_results['select_expr'][0] == '*')))
```

with

```php
((empty($analyzed_sql_results['select_expr']))
    || (count($analyzed_sql_results['select_expr']) == 1)
        && ($analyzed_sql_results['select_expr'][0] == '*'))
```

If you get a similar error on the import/export page referencing `plugin_interface.lib.php` on line 551 do the following

replace
`if ($options != null && count($options) > 0) {`
with
`if ($options != null && count((array)$options) > 0) {`

See [here](https://stackoverflow.com/a/49483740/5721515) and the comment of Bernd Schuchmacher for more information

#### Wordpress #####

## Problem Solving ##

### Logs ###

#### OpenSSH Auth Log ####

`grep 'sshd' /var/log/auth.log`

#### Apache2 Error Logs ####

`tail -f /var/log/apache2/error.log`

### Memory ###

`df -h`

### RAM ###

`top`
 `cat /proc/meminfo`

### Processes ###

See processes:
`ps -ef`

Group processes by user:
`pgrep -l -u {USERNAME}`

Kill process:
`pkill {PROCESS}`
OR `killall {PROCESS}`

### Reboot ###

Straightforward as it gets:
`sudo reboot`
