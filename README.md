# Introduction

This guide describes how to integrate your [Icinga](http://www.icinga.org) installation with PagerDuty using a simple Perl-based plugin. As Icinga is a fork of Nagios, you'll use PagerDuty's built in support for Nagios for this integration.

# Getting Started

If you don't already have a PagerDuty "Nagios" service, you should create one:

- In your account, under the Services tab, click "Add New Service".
- Enter a name for the service and select an escalation policy. Then, select "Nagios" for the Service Type.
- Click the "Add Service" button.
- Once the service is created, you'll be taken to the service page. On this page, you'll see the "Service key", which will be needed when you configure your Icinga server to send events to PagerDuty.

# Setup

Install the necessary Perl dependencies:

For Debian, Ubuntu, and other Debian-derived systems:

    aptitude install libwww-perl libcrypt-ssleay-perl

RHEL, Fedora, CentOS, and other Redhat-derived systems:

    yum install perl-libwww-perl perl-Crypt-SSLeay

Download `pagerduty_icinga.cfg`:

    wget https://raw.github.com/PagerDuty/pagerduty-icinga-pl/master/pagerduty_icinga.cfg

Open the file in your favorite editor. Enter the service key corresponding to your Nagios/Icinga service into the pager field.  The service key is a 32 character string that can be found on the service's detail page.

Copy the Icinga configuration file into place and change owner:

    cp pagerduty_icinga.cfg /usr/local/icinga/etc/objects
    chown icinga:icinga /usr/local/icinga/etc/objects/pagerduty_icinga.cfg

Add the contact "pagerduty" to your Icinga configuration's main contact group. If you're using the default configuration, open `/usr/local/icinga/etc/objects/contacts.cfg` and look for the "admins" contact group. Then, simply add the "pagerduty" contact.

    define contactgroup{ 
      contactgroup_name       admins
      alias                   Icinga Administrators
      members                 icingaadmin,pagerduty   ; <-- Add 'pagerduty' here.
    }

Download `pagerduty_icinga.pl` and copy it to `/usr/local/bin`:

    wget https://raw.github.com/PagerDuty/pagerduty-icinga-pl/master/pagerduty_icinga.pl
    cp pagerduty_icinga.pl /usr/local/bin

Make sure the file is executable by Icinga
  <pre><code>chmod 755 /usr/local/bin/pagerduty_icinga.pl</code></pre>


Enable environment variable macros in `/usr/local/icinga/icinga.cfg` (if not enabled already):
  
    enable_environment_macros=1

Edit the icinga user's crontab:

    crontab -u icinga -e

Add the following line to the crontab:

    * * * * * /usr/local/bin/pagerduty_icinga.pl flush

Restart Icinga:
    
    /etc/init.d/icinga restart
