# Self-hosted Azure DevOps agent (running on Ubuntu20.04LTS) 

## Azure DevOps PAT

Before you start, create a new Azure DevOps Personal Access Token:

- Scope: `Agent Pools (read, manage)`. Make sure all the other boxes are cleared.
- If it's deployment group agent, define scope: `Deployment group (read, manage)`. Make sure all the other boxes are cleared.
- If you want to add your agent to some other Agent Pool then default - make sure to provide correct name.

## Vagrantfile
- Creates Ubuntu20.04LTS VM locally.
- Uses specified SSH key pair for ansible.
- Creates VM with dynamically assigned IP (my network doesn't have static IP assigned, so this is a workaround). Due to that fact, Vagrant needs to publish the VM hostname on the network, which by next VM runs resolves the hostname `azdevops-ubuntu20-agent.local` to the correct IP by Avahi daemon, whatever the IP is.
- Uses ansible to provision & configure Azure DevOps self-hosted agent.

## Ansible playbook
- Supposed to configue Azure DevOps self-hosted agent, however, https://packages.microsoft.com/repos/ doesn't include packages for Azure DevOps as per 06.01.2023. Workaround: Manual agent installation after host provisioning, installing dependencies and software required for future pipelines.
- Uses variables declared in `azdevops-vars.yaml`.
- Installs software such as: Git, Azure CLI, Python 3, Docker, .NET Core, Node.js, and Java, Kubernetes.
- The agent is assigned to a single project and listens to jobs request in that project only.
- Installs and configures Avahi daemon on self-hosted agent VM (uses avahi.conf.j2 provided in the same directory, where ansible playbook is).


### Kubernetes - to install or not to install
- For building Kubernetes infrastructure using Azure DevOps pipelines, and BE ABLE TO TEST it by deploying apps to the built infrastructure, installation of Kubernetes on self-hosted agent is MANDATORY.

- For building the infrastructure and NOT TESTING it, Kubernetes installation is NOT needed. Instead, use Azure DevOps to create the necessary resources in cloud (such as AKS clusters or AKS node pools) and use other tools/processes to deploy apps to those resources. If this is your case, just remove/comment related tasks: _Install Kubernetes dependencies, Add the Kubernetes GPG key, Add the Kubernetes package repository, Install the Kubernetes packages_.

# Alternative self-hosted agent configuration (multiple projects)

## Configure agent to listen to DIFFERENT projects:

Omit `--project "${AZDEVOPS_PROJECT_NAME}"` in `azdevops-agent-playbook.yaml`. 

It will then listen if any job requests are posted across all projects in organization.

## Configure agent to listen to SPECIFIED projects:

Adjust `azdevops-agent-playbook.yaml`:
```yaml
    - name: Configure the Azure DevOps agent
      shell: |
        azdevopsagent configure \
          --unattended \
          --agent "${AZDEVOPS_AGENT_NAME}" \
          --url "https://dev.azure.com/{{ AZDEVOPS_ACCOUNT_NAME }}" \
          --auth pat \
          --token "${AZDEVOPS_ACCESS_TOKEN}" \
          --pool "${AZDEVOPS_AGENT_POOL}" \
          --project "${AZDEVOPS_PROJECT_NAME1},${AZDEVOPS_PROJECT_NAME2,${AZDEVOPS_PROJECT_NAME2}" \
          --replace \
          --acceptTeeEula
```


Before running the playbook, make sure you have declared all the required variables and changed the file name `azdevops-vars-sample.yaml` to `azdevops-vars.yaml`.


> #### Tested with the following software versions installed on host instance:
> - Vagrant 2.2.19
> - Ansible 2.10.8
> - Python 3.10.6