Role Name
=========

Install and configure Azure-DevOps self-hosted agent

Requirements
------------

Provisioned environment with updated packages. The Azure DevOps dependencies are installed by the actual role.
In order to fully configure agent to run jobs for build/test/deploy - basic packages are installed by role: `install-basic-agent-software`. Depending on needs, the list of packages may vary.

Role Variables
--------------

- AZDEVOPS_AGENT_NAME
- AZDEVOPS_ACCOUNT_NAME
- AZDEVOPS_ACCESS_TOKEN
- AZDEVOPS_AGENT_POOL
- AZDEVOPS_PROJECT_NAME

Devine in /vars/main.yml or pass in command line.

Azure DevOps agent dependencies
------------

- Update Cache
- Update Packages
- Import the Microsoft GPG key `https://packages.microsoft.com/keys/microsoft.asc`
- Import the Docker GPG key `https://download.docker.com/linux/ubuntu/gpg`
- Install curl
- Install apt-transport-https
- Install lsb-release
- Install ca-certificates
- Install gnupg

Example Playbook
----------------

    - hosts: servers
      roles:
         - { role: username.rolename, x: 42 }

Check if agent is up and ready to be used
----------------

> 1. **From VM** (elevated provileges mandatory):
>
>       - Run `systemctl list-unit-files`. 
>
>       - Your agent service should state should be `Enabled`
>
> 2. **From Azure DevOps** -> Agent Pools -> _your_pool_name_ -> Agents:
>
>       - Your Agent status should be `Online` to be able to listen to requests.

