# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/focal64"
  # write hostname to network
  config.vm.hostname = "azdevops-ubuntu20-agent.local"
  config.vm.provision "ansible" do |ansible|
    ansible.playbook = "ansible/azdevops-agent-playbook.yaml"
#    config.ssh.private_key_path = "~/.ssh/azdevops_agent_ssh_key"
  end
end