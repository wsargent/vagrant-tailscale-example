
# Vagrant Box with Tailscale and 1Password

## Prequisites

You must have Ansible, Vagrant, Virtualbox, and 1Password CLI installed.

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
api_key: "<INSERT API KEY>"    # a Tailscale API Key - https://tailscale.com/kb/1101/api/
tailnet: <INSERT TAILNET NAME>     # The name of your tailnet - What you see at the top left of https://login.tailscale.com/admin/machines
```

## Running

To create `vagrant/derp` directory containing an appropriately configured `Vagrantfile`:

```bash
ansible-playbook playbooks/vagrant-template.yml \
  -e box_name=derp \
  -e password_authkey=vagrant-tailscale \
  -e password_field=credential \
  -e password_vault=will-connect-vault 
```

Then change to the newly created directory and start up Vagrant:

```
vagrant up
```

And then from the project directory, you can install zsh to the server:

```bash
ansible-playbook playbooks/zsh.yml -l vagrant-derp
```

And you should now have zsh running in the vagrant box:

```
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
