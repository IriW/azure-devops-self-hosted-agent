Install and configure Avahi daemon on a Ubuntu
=========

Avahi daemon allows for easy access to network services. Here it is meant to discover servers on a local network and resolve to correct IP which is dynamic in my case, therefore included.

The playbook performs the following tasks:

    - Installs the Avahi daemon.
    - Configures the Avahi daemon using the template module and a Jinja2 template file (avahi.conf.j2) loated in ansible playbook location.
    - Restart the Avahi daemon using the service module.
    - Set the ansible_hostname variable using the set_fact module. This variable can be used to set the hostname of the system.

The playbook is designed to be idempotent, meaning it can be run multiple times without causing any unintended changes.

It uses several variables. AZDEVOPS_AGENT_NAME should be defined in a separate vars file or passed in as extra variables when running the playbook.