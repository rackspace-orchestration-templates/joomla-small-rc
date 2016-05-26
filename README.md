Description
===========

#### Production


Instructions
===========

#### Getting Started
If you're new to Joomla, the [Beginner Tutorials](https://docs.joomla.org/Beginner_Tutorials)
documentation will step you through the process of logging into the admin
panel, customizing your blog, and changing your theme.

After the stack has been created, you can find the admin username and
password listed in the "Credentials" section of Stack Details.

#### Accessing Your Deployment
If you provided a domain name that is associated with your Rackspace Cloud
account and chose to create DNS records, you should be able to navigate to
the provided domain name in your browser. If DNS has not been configured yet,
please refer to this
[documentation](http://www.rackspace.com/knowledge_center/article/how-do-i-modify-my-hosts-file)
on how to setup your hosts file to allow your browser to access your
deployment via domain name. Please note: some applications like WordPress,
Drupal, Joomla, and Magento may not work properly unless accessed via domain name.
DNS should point to the IP address of the Load Balancer.

#### Migrating an Existing Site
If you'd like to move your existing site to this deployment, there is a
tutorial available [here](https://docs.joomla.org/Copying_a_Joomla_website).

#### Extensions
There are over 23,000 plugins that have been created by an engaged developer
community. The [Extensions directory](http://extensions.joomla.org/)
provides an easy way to discover popular extensions that other users have found
to be helpful. Please note that adding plugins can negatively impact the
performance of your site.

#### Scaling out
This deployment is configured to be able to scale out easily.  However,
if you are expecting higher levels of traffic, please look into one of our
larger-scale stacks.

#### Details of Your Setup
This deployment was stood up using [Ansible](http://www.ansible.com/).
Once the stack has been deployed, Ansible will not run again unless you update the
stack. **Any changes made to the configuration may be overwritten when the stack
is updated.**

Joomla was downloaded directly from [joomla.org](http://joomla.org/). Joomla
is installed in /var/www/vhosts/YOUR DOMAIN/httpdocs and served by [nginx](https://www.nginx.com/).
The nginx configuration file will be named using the domain name used as a
part of this deployment (for example, domain.com.conf).

Because this stack is intended for lower-traffic deployments, there is no
caching configured.

[Lsyncd](https://github.com/axkibe/lsyncd) has been installed in order to
sync static content from the Master server to all secondary servers.
When uploading content, it only needs to be uploaded to the Master node,
and will be automatically synchronized to all secondary nodes.

MySQL is being hosted on a Cloud Database instance, running MySQL 5.1.
Backups for MySQL are provided by [Holland](http://wiki.hollandbackup.org/),
which is running on the Master server.

Backups are configured using Cloud Backups.  The Master server is configured
to back up /var/spool/holland and /var/www once per week, and to retain
these backups for 30 days.

In order to restore the Database from backup, you will need to first restore
/var/spool/holland from the appropriate Cloud Backup.  After you have done so,
you will need to log into the Master server and restore the Holland backup
to the Cloud Database via the MySQL client.  For more assistance, please
contact your Support team.

Monitoring is configured to verify that nginx is running on both the Master
and all secondary servers, as well as that the Cloud Load Balancer is
functioning.  Additionally, the default CPU, RAM, and Filesystem checks
are in place on all servers.

#### Logging in via SSH
The private key provided with this deployment can be used to log in as
root via SSH. We have an article on how to use these keys with [Mac OS X and
Linux](http://www.rackspace.com/knowledge_center/article/logging-in-with-a-ssh-private-key-on-linuxmac)
as well as [Windows using
PuTTY](http://www.rackspace.com/knowledge_center/article/logging-in-with-a-ssh-private-key-on-windows).
This key can be used to log into all servers on this deployment.
Additionally, passwordless authentication is configured from the Master
server to all secondary servers.

#### Additional Notes
You can add additional servers to this deployment by updating the
"server_count" parameter for this stack. This deployment is
intended for low to medium traffic production websites and can be
scaled as needed to accommodate future growth.

When scaling this deployment by adjusting the "server_count" parameter,
make sure that you DO NOT change the "database_flavor" and "database_disk"
parameters, as this will result in the loss of all data within the
database.

This stack will not ensure that Joomla or the servers themselves are
up-to-date.  You are responsible for ensuring that all software is
updated.


Requirements
============
* A Heat provider that supports the following:
  * OS::Heat::RandomString
  * OS::Heat::ResourceGroup
  * OS::Heat::SoftwareConfig
  * OS::Heat::SoftwareDeployment
  * OS::Nova::KeyPair
  * OS::Nova::Server
  * OS::Trove::Instance
  * Rackspace::Cloud::BackupConfig
  * Rackspace::Cloud::LoadBalancer
  * Rackspace::CloudMonitoring::Check
* An OpenStack username, password, and tenant id.
* [python-heatclient](https://github.com/openstack/python-heatclient)
`>= v0.2.8`:

```bash
pip install python-heatclient
```

We recommend installing the client within a [Python virtual
environment](http://www.virtualenv.org/).

Parameters
==========
Parameters can be replaced with your own values when standing up a stack. Use
the `-P` flag to specify a custom parameter.

* `joomla_url`: Domain to use with Joomla Site (Default: example.com)
* `joomla_sitename`: Title to use for Joomla Site (Default: Example Site)
* `joomla_user`: Username for Joomla login (Default: admin)
* `joomla_user_name`: Full name of Joomla Admin (Default: Joe User)
* `joomla_email`: E-mail Address for Joomla Admin User (Default: admin@example.com)
* `flavor`: Flavor of Cloud Server to use for Joomla (Default: 4 GB General Purpose v1)
* `database_disk`: Size of the Cloud Database volume in GB (Default: 5)
* `database_flavor`: Flavor for the Cloud Database (Default: 1GB Instance)
* `server_count`: Number of secondary web nodes (Default: 0)

Outputs
=======
Once a stack comes online, use `heat output-list` to see all available outputs.
Use `heat output-show <OUTPUT NAME>` to get the value of a specific output.

* `joomla_login_user`: Joomla Admin User 
* `joomla_login_password`: Joomla Admin Password 
* `joomla_public_ip`: Load Balancer IP 
* `joomla_admin_url`: Joomla Admin URL 
* `joomla_public_url`: Joomla Public URL 
* `phpmyadmin_url`: PHPMyAdmin URL 
* `mysql_user`: Database User 
* `mysql_password`: Database Password 
* `ssh_private_key`: SSH Private Key 
* `server_ip`: Server Public IP 
* `secondary_ips`: Secondary Node IPs 

For multi-line values, the response will come in an escaped form. To get rid of
the escapes, use `echo -e '<STRING>' > file.txt`. For vim users, a substitution
can be done within a file using `%s/\\n/\r/g`.
