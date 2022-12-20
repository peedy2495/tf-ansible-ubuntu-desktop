# Terraforming a Cloud-Init template for filling Nexus3 proxied repos in a Proxmox hypervisor

This repo is for deploying a precreated proxmox cloud-init template (ubuntu 20.04 @ vmid:8000).
I'm using this one e.g. for caching nexus repositories needing in air-gap environments. 

The file *`vars.tf`* is for defining basic variables.

## Prerequisites:
1st, a cloud-init image on your hypervisor (how to bake, see below) 

Next, a Proxmox api-token and saved it's values in *`token.export`*

Create in your main project-dir a file named: token.export  
Content:  
```
export PM_API_TOKEN_ID='your_token_id'
export PM_API_TOKEN_SECRET='your_token_secret'
```

In your current shell context you have to set API relevant variables.  
`source token.export`  
! This have to be done in every new shell-context 

On first use your have to init this repo via `terraform init`, if `.terraform.lock.hcl`* doesn't exist.

Now, create an ed25519 keypair for accessing the new instance with  
`ssh-keygen -o -a 256 -t ed25519 -C "$(hostname)-$(date +'%d-%m-%Y')" -f [Projectpath]/assets/id_ed25519`

## Terraforming:

Steps to deploy instances ...  

create an execution plan
`terraform plan`  

apply the execution plan  
`terraform apply`  
you have to type *`yes`* to confirm the execution

if u're absolutely shure what u're doing, u're able to apply all steps in one  
`terraform apply -auto-approve`

You want to reject this whole deployment? U're able to do this with  
`terraform destroy`

## How to create your own Ubuntu Desktop cloudimage

This description is made for Proxmox instances. Feel free to adopt to another virtualization ;-)  
First, create a new instance and make an standard Installation from an official ISO.
You have to define a temporary user for accessing the system during your setup session.

We don't need the cd/dvd device. So, remove it now.
By using nexus repositories, modify *`/etc/apt/sources.list`* with your flavour, first 

Install required services ...  
`apt install -y openssh-server cloud-init qemu-guest-agent`

Enable "Guest Agent" in your instance options as virtio device

Now, reboot the instance and log in via ssh.
Change into the root-context via `sudo su`

Run guest agent as persistent service  
`systemctl enable --now quemu-guest-agent.service`

In *`/etc/ssh/sshd_config`* modify following entries
```
PasswordAuthentication no
PermitRootLogin no
```

Remove the homedir of your temporary user. Why? Userdel doesn't work because of existing user-PIDs of your temporary user.  
Remove user relevant entries in `/etc/passwd` `/etc/shadow` and ALL associations in `/etc/group`.

Now, cutoff/stop (not shutdown) the instance.  

Last, add a "CloudInit Drive" in the same storage where your systemdrive resides.

Convert the instance as template and ... don't forget to create a backup :-)  
Finished!

Feel free to use this cloud-init template to fire up cloned instances as much as u like :-)

Hint: Never define a smaler OS-disk than the original. Proxmox is only be able to increase the Size!