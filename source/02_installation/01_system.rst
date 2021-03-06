.. _Installation/system:

=================================
Installation with system packages
=================================


Alignak system requirements are documented :ref:`in this chapter <Installation/requirements>`. Make sure that your system is complying before trying to install;)


Alignak packaging and download repositories for Linux/Unix is done thanks to the `Bintray software distribution <https://bintray.com/alignak/>`_. Check the `Alignak Bintray home page <https://bintray.com/alignak>`_ for all the available repositories and their packages.

.. _Installation/deb:

On Debian-like Linux
====================

Installing Alignak for a Debian based Linux distribution (eg. Debian, Ubuntu, etc.) is using ``deb`` packages and it is the recommended way. You can find packages in the Alignak dedicated repositories.

To proceed with installation, you must register the alignak repository and store its public key on your system. This script is an example (for Ubuntu 16) to be adapted to your system.

Create the file */etc/apt/sources.list.d/alignak.list* with the following content::

   # Alignak DEB stable packages
   sudo echo deb https://dl.bintray.com/alignak/alignak-deb-stable xenial main | sudo tee -a /etc/apt/sources.list.d/alignak.list

If your system complains about missing GPG key, you can add the publib BinTray GPG key::

   sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv D401AB61

If you wish to use the non-stable versions (eg. current develop or any other specific branch), you can also add the repository source for the test versions::

   # Alignak DEB testing packages
   sudo echo deb https://dl.bintray.com/alignak/alignak-deb-testing xenial main | sudo tee -a /etc/apt/sources.list.d/alignak.list

.. note:: According to your OS, replace {xenial} in the former script example:

    - Debian 9: ``stretch``
    - Debian 8: ``jessie``
    - Ubuntu 16.04: ``xenial``
    - Ubuntu 14.04: ``trusty``
    - Ubuntu 12.04: ``precise``

And then update the repositories list::

   sudo apt-get update


The Alignak packages repositories contain several version of the application. Some information about the versioning scheme are :ref:`available on this page <contributing/release_cycle>`.

Once the download sources are set, you can simply use the standard package tool to have more information about Alignak packages and available versions::

   apt-cache search --full alignak
      Package: alignak
      Version: 1.1.0rc5-test
      License: AGPL
      Vendor: Alignak Team (contact@alignak.net)
      Architecture: all
      Maintainer: Alignak Team (contact@alignak.net)
      Installed-Size: 3247
      Section: default
      Priority: extra
      Homepage: http://alignak.net
      Description: Alignak, modern Nagios compatible monitoring framework
      Description-md5: 74f94d855b20459eaf2949fb24bf78bb
      Filename: alignak_1.1.0rc5-test_all.deb
      SHA1: 8a5d18a048ca6a146f9010a6efd3aebab40f161a
      SHA256: 9c400ea3a7293a6331badcd1d9ed26d16c2fd0c95db79910eda7703e93886ad5
      Size: 837996


Or you can simply use the standard package tool to install Alignak::

    # For Python 2
    sudo apt install python-alignak

    # For Python 3
    sudo apt install python3-alignak

    # Check Alignak installation
    # It copied the default shipped files and sample configuration.
    ll /usr/local/share/alignak/
      total 24
      drwxrwxr-x 4 root root 4096 juin  19 19:53 ./
      drwxr-xr-x 9 root root 4096 juin  19 19:53 ../
      drwxrwxr-x 5 root root 4096 juin  19 19:53 bin/
      drwxrwxr-x 6 root root 4096 juin  19 19:53 etc/
      -rwxrwxr-x 1 root root  531 juin  19 09:49 post-install.sh*
      -rw-rw-r-- 1 root root 1889 juin  19 09:49 requirements.txt

    # Install the man pages
    sudo cp /usr/local/share/alignak/bin/manpages/manpages/* /usr/share/man/man8

    # It installed the Alignak systemctl services
    ll /lib/systemd/system/alignak*
      -rw-r--r-- 1 root root  777 juin  19 09:50 /lib/systemd/system/alignak-arbiter@.service
      -rw-r--r-- 1 root root  770 juin  19 09:50 /lib/systemd/system/alignak-broker@.service
      -rw-r--r-- 1 root root  770 juin  19 09:50 /lib/systemd/system/alignak-poller@.service
      -rw-r--r-- 1 root root  805 juin  19 09:50 /lib/systemd/system/alignak-reactionner@.service
      -rw-r--r-- 1 root root  784 juin  19 09:50 /lib/systemd/system/alignak-receiver@.service
      -rw-r--r-- 1 root root  791 juin  19 09:50 /lib/systemd/system/alignak-scheduler@.service
      -rw-r--r-- 1 root root 1286 juin  19 09:50 /lib/systemd/system/alignak.service

    # Alignak service status
    sudo systemctl status alignak
    ● alignak.service - Alignak daemons instance
      Loaded: loaded (/lib/systemd/system/alignak.service; enabled; vendor preset: enabled)
      Active: inactive (dead) since mar. 2018-06-19 19:53:33 CEST; 7min ago
     Process: 13321 ExecStart=/bin/echo Starting Alignak daemons... (code=exited, status=0/SUCCESS)
     Process: 13318 ExecStartPre=/bin/chown -R alignak:alignak /usr/local/var/run/alignak (code=exited, status=0/SUCCESS)
     Process: 13310 ExecStartPre=/bin/mkdir -p /usr/local/var/run/alignak (code=exited, status=0/SUCCESS)
     Process: 13293 ExecStartPre=/bin/chown -R alignak:alignak /usr/local/var/log/alignak (code=exited, status=0/SUCCESS)
     Process: 13275 ExecStartPre=/bin/mkdir -p /usr/local/var/log/alignak/monitoring-log (code=exited, status=0/SUCCESS)
    Main PID: 13321 (code=exited, status=0/SUCCESS)

.. note:: that immediately after the installation the *alignak* service is enabled and started! This is a side effect of the packaging tool that is used (*fpm*).

.. note:: more information about the default shipped configuration is available :ref:`on this page <configuration/default_configuration>`.


A post-installation script (repository *bin/post-install.sh*) is started at the end of the installation procedure to install the required Python packages. This script is copied during the installation in the default installation directory: */usr/local/share/alignak*. It is using the Python pip tool to get the Python packages listed in the default installation directory *requirements.txt* file.

.. note:: as stated :ref:`formerly in this document <Installation/requirements>`, this hack is necessary to be sure that we use the expected versions of the needed Python libraries...

Once you achieved this tricky part, running Alignak daemons is easy. All you need is to inform the Alignak daemons where they will find the configuration to use and start the `alignak` system service. All this is explained :ref:`in this chapter <run_alignak/services_systemd>`.

.. _Installation/rpm:

On RHEL-like Linux
==================

Installing Alignak for an RPM based Linux distribution (eg. RHEL, CentOS, etc.) is using ``rpm`` packages and it is the recommended way. You can find packages in the Alignak dedicated repositories.

To proceed with installation, you must register the alignak repositories on your system.

Create the file */etc/yum.repos.d/alignak-stable.repo* with the following content::

   [Alignak-rpm-stable]
   name=Alignak RPM stable packages
   baseurl=https://dl.bintray.com/alignak/alignak-rpm-stable
   gpgcheck=0
   repo_gpgcheck=0
   enabled=1

And then update the repositories list::

   sudo yum repolist


If you wish to use the non-stable versions (eg. current develop or any other specific branch), you can also create a repository source for the test versions. Then create a file */etc/yum.repos.d/alignak-testing.repo* with the following content::

   [Alignak-rpm-testing]
   name=Alignak RPM testing packages
   baseurl=https://dl.bintray.com/alignak/alignak-rpm-testing
   gpgcheck=0
   repo_gpgcheck=0
   enabled=1

The Alignak packages repositories contain several version of the application. Some information about the versioning scheme are :ref:`available on this page <contributing/release_cycle>`_.


Once the download sources are set, you can simply use the standard package tool to have more information about Alignak packages and available versions.

 ::

   yum search alignak
   # Note that it exists some Alignak packages in the EPEL repository but it is an old version. Contact us for more information...
      Loaded plugins: fastestmirror
      Loading mirror speeds from cached hostfile
       * base: mirrors.atosworldline.com
       * epel: mirror.speedpartner.de
       * extras: mirrors.atosworldline.com
       * updates: mirrors.standaloneinstaller.com
      =========================================================================== N/S matched: alignak ===========================================================================
      alignak.noarch : Alignak, modern Nagios compatible monitoring framework
      alignak-all.noarch : Meta-package to pull in all alignak
      alignak-arbiter.noarch : Alignak Arbiter
      alignak-broker.noarch : Alignak Broker
      alignak-common.noarch : Alignak Common
      alignak-poller.noarch : Alignak Poller
      alignak-reactionner.noarch : Alignak Reactionner
      alignak-receiver.noarch : Alignak Poller
      alignak-scheduler.noarch : Alignak Scheduler

        Name and summary matches only, use "search all" for everything.

   yum info alignak
      Loaded plugins: fastestmirror
      Loading mirror speeds from cached hostfile
       * base: mirrors.atosworldline.com
       * epel: mirror.speedpartner.de
       * extras: mirrors.atosworldline.com
       * updates: mirrors.standaloneinstaller.com
      Available Packages
      Name        : alignak
      Arch        : noarch
      Version     : 1.1.0rc5_test
      Release     : 1
      Size        : 816 k
      Repo        : alignak-testing
      Summary     : Alignak, modern Nagios compatible monitoring framework
      URL         : http://alignak.net
      License     : AGPL
      Description : Alignak, modern Nagios compatible monitoring framework


Or you can simply use the standard package tool to install Alignak and its dependencies.

 ::

    # For Python 2
    sudo yum install python-alignak

    # For Python 3
    sudo yum install python3-alignak

    # Check Alignak installation
    # It copied the default shipped files and sample configuration.
    ll /usr/local/share/alignak/
        total 8
        drwxr-xr-x. 5 root root   49 May 24 17:52 bin
        drwxr-xr-x. 6 root root  144 May 24 17:52 etc
        -rwxrwxr-x. 1 root root 2179 Jun 22  2018 post-install.sh
        -rw-rw-r--. 1 root root 1889 Jun 22  2018 requirements.txt

.. warning:: on some CentOS versions, the installation of the `setproctitle` Python library is raising an error and requiring *gcc*! To cope with this problem, you must ` sudo yum install python-devel` and then ` sudo yum reinstall python-alignak` !

.. note:: any help for a correct RPM packaging will be much appreciated ;)

Contrary to the debian installer, no system services are installed. You must then follow :ref:`this procedure <Installation/services>`.

.. _Installation/freebsd:

On BSD-like Unix
================

There is not yet any package available for BSD based systems. You can install Alignak from the source code or with `pip`, ... :ref:`see this procedure <Installation/pip>`.

The alignak repository contains an rc.d script that allows running Alignak daemons as system services. See the *bin/rc.d/alignak-daemon* file in the project repository.

To install the system service startup script you must::

    sudo cp /usr/local/share/alignak/bin/rc.d/alignak /usr/local/etc/rc.d/

You can also run the post-installation script that is shipped with the application. Run::

    sudo /usr/local/share/alignak/post-install.sh

Once you achieved the installation part, you need to configure the Alignak daemon startup script before starting the daemons. This configuration is explained :ref:`in this chapter <run_alignak/services_freebsd>`.
