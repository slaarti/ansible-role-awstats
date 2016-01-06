# `awstats`

This ansible role will install and assist in managing awstats on your system.
It will take advantage of the fact that Debian installs a crontab to handle
processing logs for each site according to files in `/etc/awstats/`, including
static pages, so you do not need to configure running it as a CGI.

You will, however, be responsible for configuring the web server of your choice
to serve up the generated pages.

Role Variables
--------------

    awstats_packages:
        - "awstats"

The list of packages to install. You can override this if you want to make sure
additional packages have been installed for plugins like GeoIP or the like.

    awstats_default_release: ""

Default release for installing packages. Allows you to specify using the
backports repository, if you've configured it previously.

Example Playbook
----------------

    - hosts: servers
      roles:
         - { role: slaarti.awstats }

License
-------

Unlicense; see the LICENSE file for details.

Author Information
------------------

Chris Pinard <slaarti@gmail.com>
