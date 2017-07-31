# TYPO3 Bootstrap

Fully automated provisioning of a virtual machine for TYPO3 website development using **VirtualBox**, **Vagrant** and **Ansible**.

## Contents

The provisioned virtual machine will have the following packages installed:

* TYPO3 8.7
* Apache 2.4
* MariaDB 10.0
* PHP 7.1
* GraphicsMagick 1.3
* MailHog 1.0.0
* PhpMyAdmin 4.5.4
* NodeJS 6.x

## Requirements

* A Linux or MacOS machine (not tested on Windows)
* Oracle VirtualBox
* Vagrant 1.8.6+
* Ansible

## Optional dependencies

* `vagrant-hostmanager` plug-in to easily access the virtual machine using domain names.
* `vagrant-vbguest` plug-in to keep VirtualBox guest additions up to date.

## Setup

Only a few steps are necessary to get your virtual machine up and running:

1. Download and install **VirtualBox**, **Vagrant** and **Ansible**.
2. Install the **Vagrant plugins** (optional): In a terminal execute
    - `vagrant plugin install vagrant-hostmanager`
    - `vagrant plugin install vagrant-vbguest`
3. Clone this repository.
4. Edit the provisioning configuration located at `provision/default.yml` and modify it to your needs.
    - If you want to use automatic update of the VirtualBox guest additions make sure to set `vbguest_auto_update` to `true`.
5. Run `vagrant up` to boot and provision the virtual machine. If you have installed the `vagrant-hostmanager` plug-in vagrant will ask you for your password to escalate privileges to modify the `/etc/hosts` file.

## How do I get onto the box?

### TYPO3

After the box has been successfully booted up you can access TYPO3 by pointing your browser to http://192.168.101.100/typo3 (default IP address of your virtual machine).

If you have installed the `vagrant-hostmanager` plug-in you can alternatively access the TYPO3 instance through its hostname http://typo3-bootstrap.dev.

If you can't access the virtual machine through its default IP address and/or hostname chances are you have modified these information in the provisioning configuration.

### MailHog

[MailHog](https://github.com/mailhog/MailHog) is a virtual SMTP server and email testing tool for developers and is automatically installed on the virtual machine. To access the Web-UI just visit http://192.168.101.100:8025 or http://typo3-bootstrap.dev:8025 and enter the username and password that have been set in the provisioning configuration.

The default configuration will automatically send all emails to MailHog using the mhsendmail wrapper. If you don't want to use MailHog but your own SMTP server instead, just update the corresponding settings in the provisioning configuration.

More information about MailHog can be found at [github.com/mailhog](https://github.com/mailhog/MailHog)

## Current limitations

* Re-Provisioning the machine will clear the database. This is currently necessary as the *setup* command run from the *typo3cms* CLI will fail otherwise as it needs an empty database to perform its setup routine.

## Planned features

* Switch webserver user from *www-data* to *vagrant* for easier work inside the virtual machine.
* Re-Provision without clearing the database.
* Implement **MailHog** as a systemd service instead of using SysVinit.
* **TYPO3 Surf** for automated deployments.

## What about TYPO3 7.6?

Although this packages is meant to provision TYPO3 8.7 it should also be possible provisioning an earlier TYPO3 version like 7.6. Please bear in mind that this feature has not yet been fully tested so there is no 100% guarantee that this will work.

Please check the comments in the provisioning configuration for additional information on how to set up this package to provision TYPO3 7.6.

## Credits

The provisioning workflow is loosely based on the provisioning workflow used in the [VCCW](https://github.com/vccw-team/vccw) project.
