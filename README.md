# Automated WIMS installation

Target Linux distribution is Debian.

Current supported version: Debian 9 (stretch)

Ansible is used to code Deployment. Please read pages like https://www.ansible.com/use-cases to know more about the benefits of using tools like Ansible, Chef, Puppet, Saltstack (etc.) instead of usual bash scripts to deploy and configure applications.

Scripts have been tested on WIMS v4.1x

## Prerequisites

### On the target server side

* Debian 9 (stretch)
* Public key of source user account, in `authorized_keys2` file of root
* Full domain name is already associated to the IP address of target server
* e-mail server configuration is out of scope of this script: either the target is already able to send e-mails (recommended) or configuration should be done after WIMS installation

### On the installation source

* Ansible v2.x
* hosts file with target server information

## How to deploy

If necessary, change the following variables in `./roles/wims_install_wims/vars/default.yml`
* wims_url: URL to get WIMS, but **without** package name (ex.: https://sourcesup.renater.fr/frs/download.php/file/5627/)
* wims_package : name of the package to be installed (ex.: wims-4.16.tgz)
* ip_webmaster : IP of webmaster machine allowed to connect in administrator mode
* email_webmaster : self explaining
* password_webmaster : self explaining

Ansible scripts expect target server defined as 'wims' in host file

Execute each Ansible script in order, using `ansible-playbook`
* `0_base_configuration.yml`
* `1_third_party_apps.yml`
* `2_wims_users.yml`
* `3_install_wims_as_wims.yml`
* `4_install_wims_as_root.yml`

Optional
* `5_web_configuration.yml`: to setup a default web site and prepare Let's Encrypt HTTPS configuration
    * As terms and conditions of Let's Encrypt must be agreed, the latter part is voluntarily left un automated

## Post install

In the server administration page, perform the following changes
* For GAP on v4.16, replace `gap.sh -T -m 64M` by `gap -T -m 64M`.
    * Note: this could be automated if needed by adding line `gap_command=gap -T -m 64M` in /home/wims/log/wim.conf
* Add contact for Data Protection Officer (DPO). Also scriptable by adding a line into wims.conf

## TODO

* Configure server to send-only e-mails
* Use Ansible vault to manage sensitive data:
    * Administrator IP and e-mail
    * Administrator password
* Fully automate use of a "Let's encrypt" certificate?
