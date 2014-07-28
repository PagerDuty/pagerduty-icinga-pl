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
    
    
#License and Copyright
Copyright (c) 2014, PagerDuty
All rights reserved.

Redistribution and use in source and binary forms, with or without modification, are permitted provided that the following conditions are met:

* Redistributions of source code must retain the above copyright notice, this list of conditions and the following disclaimer.

* Redistributions in binary form must reproduce the above copyright notice, this list of conditions and the following disclaimer in the documentation and/or other materials provided with the distribution.

* Neither the name of [project] nor the names of its contributors may be used to endorse or promote products derived from this software without specific prior written permission.

THIS SOFTWARE IS PROVIDED BY THE COPYRIGHT HOLDERS AND CONTRIBUTORS "AS IS" AND ANY EXPRESS OR IMPLIED WARRANTIES, INCLUDING, BUT NOT LIMITED TO, THE IMPLIED WARRANTIES OF MERCHANTABILITY AND FITNESS FOR A PARTICULAR PURPOSE ARE DISCLAIMED. IN NO EVENT SHALL THE COPYRIGHT HOLDER OR CONTRIBUTORS BE LIABLE FOR ANY DIRECT, INDIRECT, INCIDENTAL, SPECIAL, EXEMPLARY, OR CONSEQUENTIAL DAMAGES (INCLUDING, BUT NOT LIMITED TO, PROCUREMENT OF SUBSTITUTE GOODS OR SERVICES; LOSS OF USE, DATA, OR PROFITS; OR BUSINESS INTERRUPTION) HOWEVER CAUSED AND ON ANY THEORY OF LIABILITY, WHETHER IN CONTRACT, STRICT LIABILITY, OR TORT (INCLUDING NEGLIGENCE OR OTHERWISE) ARISING IN ANY WAY OUT OF THE USE OF THIS SOFTWARE, EVEN IF ADVISED OF THE POSSIBILITY OF SUCH DAMAGE.
