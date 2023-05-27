
# Vagrant Box with Tailscale and 1Password

This is the github project for [Bootstrapping Boxes Into Tailscale With 1Password](https://tersesystems.com/blog/2023/05/25/bootstrapping-boxes-into-tailscale-with-1password/).

This project uses Ansible to create am appropriately configured `Vagrantfile`, `requirements.yml` and `playbook.yml` to bootstrap the Vagrant box into Tailscale.

## Prequisites

You must have [Ansible](https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html), [Vagrant](https://developer.hashicorp.com/vagrant/docs/installation), [Virtualbox](https://www.virtualbox.org/wiki/Downloads), [Tailscale](https://tailscale.com/kb/installation/) and [1Password CLI](https://developer.1password.com/docs/cli/get-started#install) installed.

You must be signed into 1Password CLI:

```bash
eval $(op signin)
```

You will want to install the following ansible galaxy collections:

```bash
ansible-galaxy install artis3n.tailscale && \
ansible-galaxy collection install community.general
```

You will need to get a [Tailscale API key](https://tailscale.com/kb/1101/api/) and your [tailnet name](https://login.tailscale.com/admin/machines
```), and add them to `tailscale.yaml`:

```
plugin: freeformz.ansible.tailscale # must be freeformz.ansible.tailscale
ansible_host: ipv4                  # ipv4, ipv6, dns, or host_name - Depends on how you referred to the hosts before this
api_key: "<INSERT API KEY>"         # a Tailscale API Key - https://tailscale.com/kb/1101/api/
tailnet: <INSERT TAILNET NAME>      # The name of your tailnet - What you see at the top left of https://login.tailscale.com/admin/machines
```

## Running

To create `vagrant/derp` directory containing an appropriately configured `Vagrantfile`:

```bash
ansible-playbook playbooks/vagrant-template.yml \
  -e box_name=derp \
  -e password_authkey=tailscale_authkey \
  -e password_field=credential \
  -e password_vault=your-vault 
```

You can change the variables in `playbooks/roles/vagrant-template/vars/main.yml` so that you don't have to type all that out repeatedly.

Then change to the newly created directory and start up Vagrant:

```bash
cd vagrant/derp
vagrant up
```

And then from the project directory, you can install zsh to the server:

```bash
ansible-playbook playbooks/zsh.yml -l vagrant-derp
```

And you should now have zsh running in the vagrant box:

```bash
tailscale ssh vagrant@vagrant-derp
```

## Deleting

For a clean exit, you'll want to destroy the vagrant box before you delete the directory:

```bash
cd vagrant/derp
vagrant destroy
cd ..
rm -rf derp
```

If you mess up and delete the directory first, you can kill the vagrant by running `vagrant global-status` and deleting it via id.
