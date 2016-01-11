# `awstats`

This ansible role will install and assist in managing `awstats` on your
system. It will take advantage of the fact that Debian installs a crontab
to handle processing logs for each site according to files in
`/etc/awstats/`, including static pages, so you do not need to configure
running it as a CGI.

The `awstats` package installs in `/etc/awstats/` a `awstats.conf` file
with all of the default options, and `awstats.conf.local` that
`awstats.conf` includes for your custom configuration. Their idea is that
you either:

1.  For a single virtual host, put your custom config into
    `awstats.conf.local`, or:

2.  For multiple virtual hosts, create full configs for each one in
    individual files named `awstats.${VHOSTNAME}.conf`, where
    `${VHOSTNAME}` is the hostname or some other identifier for the
    specific virtual host.

You *could* just go with option 2 without cleaning up the
package-installed files, but then the scripts that `cron` calls to run
`awstats` to generate reports will throw errors about the `awstats.conf`
config being incomplete.

Thus, the approach this role will take will be to move `awstats.conf` to
`awstats.conf.dist`, so that it's out of the way, and use option
2 straight off, even for just one virtual host. By default, each virtual
host's `awstats` config will include `awstats.conf.dist` first. Config
options will then be composed as follows:

1.  `awstats.conf.dist` will provide the baseline defaults as determined
    by the developers and/or Debian.

2.  `awstats.conf.dist` includes `awstats.conf.local`, which this role
    will allow you to configure for global options across all of your
    virtual hosts.

3.  Then this role will configure the options specific to this virtual
    host in the individual config file.

As obliquely mentoned above, the package handles providing a `cron`
configuration that runs scripts that manage running `awstats`. This role
lets those mechanisms work without interference. This will include
generating static pages once a day, so you don't need to set up the CGI.
(You can if you want to; I don't intend to.)

You will, however, be responsible for configuring the web server of your
choice to serve up the generated pages. A super-simple configuration for
`nginx` (my server of choice) that uses the static pages under
a `/awstats` directory is as follows:

    server {
        listen 80;
        server_name example.com;

        # Some sort of authentication should be set up here,
        # but I leave that for you to do yourself.

        location /awstats {
            alias /var/cache/awstats;
            index index.html index.en.html;
            autoindex on;
        }

        location /awstats-icon {
            alias /usr/share/awstats/icon;
        }
    }

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
