---
title: Discovering Ansible
tags: ansible server
datetime: 2020-06-07T19:40:41Z
---
#### 2020-06-06

<time>7:41pm</time> DigitalOcean hat some issue with their servers and had schedule a migration to move the droplet to another server, But my server a day after the migration was suppose to be done, still unreachable, no email from them saying if they had issue or not. I sent them a email about this couple of hours ago, and no reply. I shutdown my droplet and now I'm unable to boot it up.

<time>7:50pm</time> One thing I know is that I have some services that I have to run manually and I knew this time will come, and I do not know which one are those. Great server management skills. It's just some small servers, and every year or so I say I don't need fancy configuration because I don't do this often, but now I would take this as an excuse to use a server management tool.

<time>8:09pm</time> I would Ansible, less complex solution without the need to have a master server, and I can use my local environment to be the master server where all the changes will be made to a the server.
I'm using Mac OSX and I [installed ansible using pip](https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html#from-pip)

    pip install --user ansible

<time>9:01pm</time> Ansible when installed with `pip --user` is installed in your user directory, `~/Library/Python/<version>/bin` instead of system directory. The user path should be added to your user `$PATH`.

    export PATH="$PATH:~/Library/Python/2.7/bin"

<time>9:27pm</time> By default ansible loads its configuration from `/etc/ansible`, I created a directory in my user directory and create a symlink to `/etc/ansible`. I'm following both ansible documentation and digital ocean guide. The other option is to use `-i` and specify the inventory path.

<time>9:33pm</time> I found you don't need to create the symlink or anything there's a order in which ansible look for the the configuration, and you can set one in your home directory and change the inventory path there.

> Changes can be made and used in a configuration file which will be searched for in the following order:
>
> - ANSIBLE_CONFIG (environment variable if set)
> - ansible.cfg (in the current directory)
> - ~/.ansible.cfg (in the home directory)
> - /etc/ansible/ansible.cfg
>
> Source: [Ansible docs](https://docs.ansible.com/ansible/latest/reference_appendices/config.html#the-configuration-file)


    # ~/.ansible.cfg
    inventory = ~/.ansible/hosts

<time>9:43pm</time> I got an error while loading the configuration `Error reading config file (/Users/<username>/.ansible.cfg): File contains no section headers.`, taking a look at an example configuration, I need to set the section header as `[defaults]`. Here's a [config example](https://github.com/ansible/ansible/blob/devel/examples/ansible.cfg)

    # ~/.ansible.cfg
    [defaults]
    inventory = ~/.ansible/hosts

<time>9:52pm</time> Time to add the  host into `~/.ansible/hosts`

    [servers]
    wellingguzman ansible_host=<the-ip>

Now the server has an alias of wellingguzman that point to the host defined in `ansible_host`.

    $ ansible-inventory --list

Now I can list the ansible inventory.

<time>9:58pm</time> The host are defined, it's time to test them:

    $ ansible all -a "df -h" -u root
    Filesystem      Size  Used Avail Use% Mounted on
    udev            3.9G     0  3.9G   0% /dev
    tmpfs           798M  624K  798M   1% /run
    /dev/vda1       155G  2.3G  153G   2% /
    tmpfs           3.9G     0  3.9G   0% /dev/shm
    tmpfs           5.0M     0  5.0M   0% /run/lock
    tmpfs           3.9G     0  3.9G   0% /sys/fs/cgroup
    /dev/vda15      105M  3.6M  101M   4% /boot/efi
    tmpfs           798M     0  798M   0% /run/user/0


#### 2020-06-07

<time>12:16pm</time> Copied the Digital Ocean init playbook, everything was working until I changed the default ssh port and wasn't able to connect my server anymore. I was locked out of my own server.

<time>12:18pm</time> Luckily I'm able to log in through the web console in Digital Ocean, and solve the issue from there.

<time>12:20pm</time> After checking the firewall, I learned that the `OpenSSH` is actually a profile name that translate to 22/tcp, which means the new port was blocked. I was mistaken, and OpenSSH is not a dynamic name for fetching the actual port from the sshd configuration.

<time>12:41pm</time> I changed the port to the default one (22), on the server and updated the playbook to use the actual number and protocol instead of the profile name. I'm running the playbook one more time and see what happens.

    # From this
    - name: UFW - Allow SSH connections
      ufw:
        rule: allow
        name: OpenSSH

    # To this
    - name: UFW - Allow SSH connections
      ufw:
        rule: allow
        port: '{{ ssh_port | int }}'
        proto: tcp

<time>01:03pm</time> I'm now able to run my playbook successfully now the server are working, and more important I know what are the commands that I used, so I can destroy this all the time I want and I will have my script to recreate them.


    ansible-playbook playbook.yml -l wellingguzman -u root

<time>01:08pm</time> My playbook, is almost identical as the playbook from digital ocean [initial server playbook](https://github.com/do-community/ansible-playbooks/blob/master/setup_ubuntu1804/playbook.yml), the only difference here is that I changed the group name, and install nginx and node.

    - name: Install Web Server Packages
      apt: name={{ item }} update_cache=yes state=latest
      loop: [ 'nginx' ]
    
    # Install Node.js
    - name: Add the NodeSource package signing key
      apt_key:
        url: 'https://deb.nodesource.com/gpgkey/nodesource.gpg.key'
        state: present
    - name: Add the desired NodeSource repository
      apt_repository:
        repo: "deb https://deb.nodesource.com/node_{{ node_version }}.x {{ distro }} main"
        state: present
        update_cache: yes
    - name: Install Node.js
      apt:
        name: nodejs
        state: present

<time>9:43pm</time> There's still a lot to learn here, but at least I'm now able to make changes and apply them from a single command

<time>9:46pm</time> Ansible can also be used for deployment, probably this going to be my next thing to learn, my starting point will be [site deployment](https://docs.ansible.com/ansible/latest/user_guide/guide_rolling_upgrade.html#site-deployment) guide on the ansible documentation.

<time>9:48pm</time> I'm closing this expecting the next time I will jump into learn more things about ansible I will try to create a proper task to create and add ssl certificate to the domains.

Another question I still have is how do I actually make the best workflow? Do I have one playbook for initial setup, another for specific upgrade?

<time>9:51</time> By the way, still not response from Digital Ocean support. I had already moved my site to another droplet.

<time>9:55pm</time> Below are some of the references I used. I mostly use everything on Digital Ocean ansible playbooks, and the Ansible documentation.

#### Reference:

- [How to Install and Configure Ansible on Ubuntu 18.04](https://www.digitalocean.com/community/tutorials/how-to-install-and-configure-ansible-on-ubuntu-18-04)
- [Ansible - Get Started](https://www.ansible.com/resources/get-started)
- [How to build your inventory](https://docs.ansible.com/ansible/latest/user_guide/intro_inventory.html)
- [How to Use Ansible to Automate Initial Server Setup on Ubuntu 18.04](https://www.digitalocean.com/community/tutorials/how-to-use-ansible-to-automate-initial-server-setup-on-ubuntu-18-04)
- [Digital Ocean Ansible Cheat Sheet Guide](https://www.digitalocean.com/community/cheatsheets/how-to-use-ansible-cheat-sheet-guide)
- [ufw module](https://docs.ansible.com/ansible/latest/modules/ufw_module.html)
- [DO Community Ansible Playbooks](https://github.com/do-community/ansible-playbooks)

