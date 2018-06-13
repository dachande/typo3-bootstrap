# TYPO3 Bootstrap

Fully automated provisioning of a virtual machine for TYPO3 website development using **VirtualBox**, **Vagrant** and **Ansible**.

## Contents

This package is using [bento/ubuntu-16.04](https://app.vagrantup.com/bento/boxes/ubuntu-16.04) as its Vagrant base box. The provisioned virtual machine will have the following features installed:

* TYPO3 8.7
* Apache 2.4
* MariaDB 10.0
* PHP 7.1 (customizable via provisioning configuration)
* GraphicsMagick 1.3
* MailHog 1.0.0
* PhpMyAdmin 4.5.4
* Node.js 8.x

## Requirements

* A Linux or MacOS machine (not tested on Windows)
* Oracle VirtualBox
* Vagrant 2.0+
* Ansible

## Optional dependencies

* `vagrant-hostmanager` plug-in to easily access the virtual machine using domain names.
* `vagrant-vbguest` plug-in to keep VirtualBox guest additions up to date.

## Setup

Only a few steps are necessary to get your virtual machine up and running:

1. Download and install **VirtualBox**, **Vagrant** and **Ansible**.
2. Install the **Vagrant plugins** (optional): In a terminal execute (as root or using sudo)
    - `vagrant plugin install vagrant-hostmanager`
    - `vagrant plugin install vagrant-vbguest`
3. Clone this repository.
4. Edit the provisioning configuration located at `configuration.yml` and modify it to your needs.
    - If you want to use automatic update of the VirtualBox guest additions make sure to set `vbguest_auto_update` to `true`.
5. Run `vagrant up` to boot and provision the virtual machine. If you have installed the `vagrant-hostmanager` plug-in vagrant will ask you for your password to escalate privileges to modify the `/etc/hosts` file.

## How do I get onto the box?

### TYPO3

After the box has been successfully booted up you can access TYPO3 by pointing your browser to https://192.168.101.100/typo3 (which is the IP address of your virtual machine set in `configuration.yml`).

If you have installed the `vagrant-hostmanager` plug-in you can alternatively access the TYPO3 instance through its hostname https://typo3-bootstrap.test/typo3.

If you can't access the virtual machine through its default IP address and/or hostname chances are you have modified these information in the provisioning configuration.

### MailHog

[MailHog](https://github.com/mailhog/MailHog) is a virtual SMTP server and email testing tool for developers and is automatically installed on the virtual machine. To access the Web-UI just visit http://192.168.101.100:8025 or http://typo3-bootstrap.test:8025 and enter the username and password that have been set in the provisioning configuration.

The default configuration will automatically send all emails to MailHog using the mhsendmail wrapper. If you don't want to use MailHog but your own SMTP server instead, just update the corresponding settings in the provisioning configuration.

More information about MailHog can be found at [github.com/mailhog](https://github.com/mailhog/MailHog)

### phpMyAdmin

[phpMyAdmin](https://www.phpmyadmin.net/) is a free software tool written in PHP, intended to handle the administration of MySQL over the Web. You can access phpMyAdmin by visiting http://192.168.101.100/phpmyadmin or http://typo3-bootstrap.test/phpmyadmin using the TYPO3 database login credentials that have been set in the provisioning configuration.

Under normal circumstances you can't login to phpMyAdmin using the **database root account** because of security restrictions.

### SSH

To connect to the virtual machine through SSH just enter `vagrant ssh` and you are good to go.

## Post configuration

This modern TYPO3 installation is shipped with a fanstatic tool to configure important settings of your project directly on the command line. Just SSH into the virtual machine and let the magic happen:

```
user@local$ vagrant ssh

vagrant@vm$ cd /var/www/typo3-website
vagrant@vm$ vendor/bin/typo3cms
```

If you run `typo3cms` without any parameters you'll get a list of available commands. To get additional help for a specific command just use `vendor/bin/typo3cms help <command>`.

If you want to clear the TYPO3 cache for instance just run `vendor/bin/typo3cms cache:flush`. This way clearing the TYPO3 cache is much quicker than clearing the cache through the TYPO3 backend.

## PHP Version

By default PHP 7.1 is installed/used on the virtual machine. You can however customize the installed PHP version by changing the PHP version in the provisioning configuration.

If you want to use PHP 7.2 for instance, just change `php_version` to `7.2` in `configuration.yml`.

## TLS encryption

TLS encryption is enabled by default. In order to avoid certificate warnings in your browser you'll need to import the custom CA certificate into your browser. The certificate is located in `/provision/ssl/certs/ca.crt.pem`

Additional information on how to customize your hostname without breaking validation of the TLS certificate can be found in the network settings in `configuration.yml`.

## Current limitations

* Re-Provisioning the machine will clear the database. This is currently necessary as the *setup* command run from the *typo3cms* CLI will fail otherwise as it needs an empty database to perform its setup routine.

## Planned features

* Re-Provision without clearing the database.
* Implementation of **TYPO3 Surf** for automated deployments.

## Credits

* Thanks to [Reizwerk GmbH](https://www.reizwerk.com) allowing me to use some of my work time for package development.
