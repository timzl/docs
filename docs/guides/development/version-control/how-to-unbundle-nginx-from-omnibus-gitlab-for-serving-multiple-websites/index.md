---
slug: how-to-unbundle-nginx-from-omnibus-gitlab-for-serving-multiple-websites
title: 'Unbundling NGINX from Omnibus Gitlab'
title_meta: 'How to Unbundle NGINX from Omnibus GitLab'
description: 'Unbundle the default NGINX server from Omnibus Gitlab and install and configure your own to enable virtual hosting.'
authors: ["Jordi Bassagañas"]
contributors: ["Jordi Bassagañas"]
published: 2016-02-29
modified: 2017-06-21
keywords: ["version control", "git", "gitlab", "install gitlab on ubuntu", "how to manage repositories with gitlab"]
tags: ["version control system","email","ruby","nginx","ubuntu"]
license: '[CC BY-ND 4.0](http://creativecommons.org/licenses/by-nd/4.0/)'
aliases: ['/applications/development/how-to-unbundle-nginx-from-omnibus-gitlab-for-serving-multiple-websites/','/development/version-control/how-to-unbundle-nginx-from-omnibus-gitlab-for-serving-multiple-websites/']
external_resources:
 - '[Updating GitLab via Omnibus GitLab](https://gitlab.com/gitlab-org/omnibus-gitlab/blob/master/doc/update.md)'
 - '[Installing Passenger + NGINX](https://www.phusionpassenger.com/library/install/nginx/install/oss/trusty/)'
audiences: ["intermediate"]
---

Omnibus GitLab is a software package (or software stack) that allows you to easily install and run GitLab on your Linode.
This guide walks you through the process of installing and setting up your own NGINX server on a typical Omnibus installation. Using the method outlined here, you are not forced to use Omnibus's default settings, and can create as many virtual hosts as you need for hosting multiple websites and apps on the same server as your GitLab.

Preconfigured software stacks sometimes bring a series of challenges to those who need to customize specific settings. If you require more control over your installation, consider [installing GitLab from source](/docs/guides/install-gitlab-on-ubuntu-14-04-trusty-tahr/). This application stack could benefit from large amounts of disk space, so also consider using our [Block Storage](/docs/products/storage/block-storage/) service with this setup.


## Before You Begin

1.  Familiarize yourself with Linode's [Getting Started guide](/docs/products/platform/get-started/) and complete the steps for setting your Linode's hostname and timezone.

2.  Complete the sections of our [Securing Your Server](/docs/products/compute/compute-instances/guides/set-up-and-secure/) guide to create a standard user account, harden SSH access and remove unnecessary network services.

3.  This guide has been tested with Ubuntu 14.04 LTS and 16.04 LTS. Some commands will be slightly different for each version, so be sure to read each step carefully for version-specific instructions.

4.  Update your system:

        sudo apt-get update && sudo apt-get upgrade
{{< note >}}
This guide is written for a non-root user. Commands that require elevated privileges are prefixed with `sudo`. If you’re not familiar with the `sudo` command, visit our [Users and Groups guide](/docs/guides/linux-users-and-groups/) for more information.
{{< /note >}}

## Install Omnibus GitLab

If you're already running an Omnibus GitLab environment [upgrade to the newest version](https://gitlab.com/gitlab-org/omnibus-gitlab/blob/master/doc/update.md) and proceed to the next section, [Unbundle NGINX from Omnibus](#unbundle-nginx-from-omnibus-gitlab). If you're installing GitLab for the first time, continue with the steps in this section.

Note that NGINX cannot be disabled in older versions of GitLab Community Edition (CE). If you currently have an older version of GitLab CE installed, we recommend that you upgrade incrementally to avoid issues.

1.  Install the dependencies:

        sudo apt-get install curl openssh-server ca-certificates postfix

2.  While installing Postfix, you'll be asked to configure a few basic settings. On the first [ncurses](https://en.wikipedia.org/wiki/Ncurses) screen, select **Internet Site** as the mail configuration. On the second screen, enter your fully qualified domain name (FQDN). This will be used to send email to users when configuring new accounts and resetting passwords. The rest of the mail options will be configured automatically.

    {{< content "email-warning-shortguide" >}}

3.  Add the GitLab CE repository and install the `gitlab-ce` package:

        curl -sS https://packages.gitlab.com/install/repositories/gitlab/gitlab-ce/script.deb.sh | sudo bash
        sudo apt-get install gitlab-ce

    You can view the contents of the script in its entirety on the [GitLab website](https://packages.gitlab.com/gitlab/gitlab-ce/install) if you're hesitant to run it sight-unseen. The [GitLab downloads page](https://about.gitlab.com/downloads/) also contains alternative download methods if you're still not comfortable running their script.

## Unbundle NGINX from Omnibus GitLab

1.  To unbundle NGINX from GitLab, we'll need to disable the version included in the Omnibus package. Add the following lines to `/etc/gitlab/gitlab.rb`:

    {{< file "/etc/gitlab/gitlab.rb" >}}
# Unbundle NGINX from Omnibus GitLab
nginx['enable'] = false
# Set your NGINX's username
web_server['external_users'] = ['www-data']

{{< /file >}}


2.  Reconfigure GitLab to apply the changes:

        sudo gitlab-ctl reconfigure

For more information on how to customize Omnibus NGINX, visit the [official NGINX documentation](https://gitlab.com/gitlab-org/omnibus-gitlab/blob/master/doc/settings/nginx.md).

## Install Ruby, Passenger, and NGINX

Now that GitLab's bundled NGINX has been disabled, the next step is to install and configure the web server from scratch.

1.  Since GitLab is written in Ruby, install Ruby on your system:

        sudo apt-get install ruby
        sudo gem install rubygems-update
        sudo update_rubygems

2.  We'll also need to install [Phusion Passenger](https://en.wikipedia.org/wiki/Phusion_Passenger), a web application server for Ruby. Install Phusion Passenger's PGP key:

        sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 561F9B9CAC40B2F7

3.  Add Passenger's APT repository by adding the following lines to `/etc/apt/sources.list.d/passenger.list`:

    {{< file "/etc/apt/sources.list.d/passenger.list" >}}
deb https://oss-binaries.phusionpassenger.com/apt/passenger trusty main

{{< /file >}}


    {{< note respectIndent=false >}}
If you're using Ubuntu 16.04, replace `trusty` with `xenial` in the above command.
{{< /note >}}

4.  Update your package repositories:

        sudo apt-get update

5.  Install Passenger and NGINX:

        sudo apt-get install nginx-extras passenger

6.  Enable the new Passenger module by uncommenting the `include /etc/nginx/passenger.conf;` line from the `/etc/nginx/nginx.conf` file:

    {{< file "/etc/nginx/nginx.conf" aconf >}}
include /etc/nginx/passenger.conf;

{{< /file >}}


4.  Finally, restart NGINX. On Ubuntu 14.04:

        sudo service nginx restart

    On Ubuntu 16.04:

        sudo systemctl restart nginx

For further information, please refer to [Installing Passenger + NGINX on Ubuntu 14.04 LTS (with APT)](https://www.phusionpassenger.com/library/install/nginx/install/oss/trusty/ "Installing Passenger + NGINX").

## Create a New Virtual Host

In this section, we'll create a new virtual host to serve GitLab. Since we've unbundled NGINX, we'll also be able to configure other virtual hosts for other websites and apps.

1.  Copy the default virtual host file to a new virtual host file, replacing `example.com` with your virtual host:

        sudo cp /etc/nginx/sites-available/default /etc/nginx/sites-available/example.com

2.  Edit your new virtual host file to match the following, replacing `example.com` with your own hostname:

    {{< file "/etc/nginx/sites-available/example.com" >}}
upstream gitlab {
    server unix:/var/opt/gitlab/gitlab-rails/sockets/gitlab.socket;
}

      server {
    listen 80;
    server_name example.com;
    server_tokens off; # don't show the version number, a security best practice
    root /opt/gitlab/embedded/service/gitlab-rails/public;

    # Increase this if you want to upload large attachments
    # Or if you want to accept large git objects over http
    client_max_body_size 250m;

    # individual nginx logs for this gitlab vhost
    access_log  /var/log/nginx/gitlab_access.log;
    error_log   /var/log/nginx/gitlab_error.log;

    location / {
        proxy_redirect off;
        proxy_set_header Host $http_host;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_pass http://gitlab;
    }
}

{{< /file >}}


3.  Enable your new virtual host by symbolically linking it to `sites-enabled` (change `example.com`):

        sudo ln -s /etc/nginx/sites-available/example.com /etc/nginx/sites-enabled/example.com

4.  Restart NGINX to load your changes. On Ubuntu 14.04:

        sudo service nginx restart

    On Ubuntu 16.04:

        sudo systemctl restart nginx

5.  Since NGINX needs to access GitLab, add the `www-data` user to the `gitlab-www` group:

        sudo usermod -aG gitlab-www www-data

Congratulations! You have turned a default Omnibus GitLab server into a multi-purpose one. To serve additional websites and apps using your newly unbundled NGINX server, simply create additional virtual hosts above, and configure them to your needs. For more information, please refer to our guide on [how to configure NGINX](/docs/guides/how-to-configure-nginx/).
