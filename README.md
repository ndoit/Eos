Eos
===

This is a dedicated vagrant script that creates a virtual development
environment. The purpose is 2 fold:

1. To allow the coolest devs to experiment [freely without fear](http://bit.ly/2q2SIjN).
If they make a mistake, they can blow the vagrant box away and start over.
This has the added benefit of making it easier for new devs to onboard quickly.
2. Upgrade infrastructure quickly and easily. When upgrading ruby version, it
is much easier to just blow away a box and start fresh with a new version of
ruby instead of upgrading over old versions.

It looks daunting now, but you only have to run this setup once. Then the ansible
script takes care of everything every `vagrant destroy && vagrant up` deploy
thereafter.

There are several steps needed before running `vagrant up`.

- Install Vagrant
- Install VirtualBox
- Have a plan for virtual box guest additions
- Install github *with credentials* on your mac
- Ability to SSH to Github from your Mac

If you have all these installed, then here is the next step:
```
git clone git@github.com:ndoit/Eos.git
cd Eos
vagrant up
```

---

Install Vagrant
---
Installing vagrant is really easy. Go to the
[downloads page](https://www.vagrantup.com/downloads.html) and download the
appropriate installer and follow the instructions.

Virtual Box Install
---
Go to the virtual box [downloads section](https://www.virtualbox.org/wiki/Downloads)
and select the download for your host machine. Follow the instructions.

Virtual Box Guest Additions
---

Next install is VirtualBox guest additions. These enable file sharing between
the guest and host machines. There are several ways to do this. The easiest
being the vagrant-vbguest plugin.

#### [vagrant-vbguest](https://github.com/dotless-de/vagrant-vbguest)

This is the easiest install, and involves only running one command

```
vagrant plugin install vagrant-vbguest
```

#### Manual Install

The other way is to do it manually. The versions in this doc is 5.1.22, it must
match your virtualbox version. Consequently, every time you upgrade virtualbox,
you must also upgrade the guest additions. These code blocks are for macs
```
# Remember the guest additions must be installed on your guest machine.
wget http://download.virtualbox.org/virtualbox/5.1.22/VBoxGuestAdditions_5.1.22.iso
sudo mkdir /media/VBoxGuestAdditions
sudo mount -o loop,ro VBoxGuestAdditions_5.1.22.iso /media/VBoxGuestAdditions
sudo sh /media/VBoxGuestAdditions/VBoxLinuxAdditions.run
rm VBoxGuestAdditions_5.1.22.iso
sudo umount /media/VBoxGuestAdditions
sudo rmdir /media/VBoxGuestAdditions
```

To check what version of guest additions you have installed-
```
# lsmod | grep -i vbox

Output:

vboxvideo 12575 2
drm 242038 3 vboxvideo
vboxsf 39436 0
vboxguest 228550 8 vboxsf

# modinfo vboxguest

Output:

filename: /lib/modules/3.2.0-29-generic/misc/vboxguest.ko
>>> version: 4.1.20 <<<
license: GPL
description: Oracle VM VirtualBox Guest Additions for Linux Module
author: Oracle Corporation
srcversion: F257C1B923A2AC55436E41D
alias: pci:v000080EEd0000CAFEsv00000000sd00000000bc*sc*i*
depends:
vermagic: 3.2.0-29-generic SMP mod_unload modversions

Or if you *really* want to be picky about it ;-)

# lsmod | grep -io vboxguest | xargs modinfo | grep -iw version

Output:

version: 4.1.20
```

GIT MUST BE INSTALLED ON YOUR HOST MACHINE
---
The Ansible script looks at your credentials on your host machine to streamline
the git install and prevent a common commit mistake.

Check if you have your credentials set properly. These will be your
github/bitbucket profile. Remember, the script uses this so these commands have
to return a value
```
# on host machine
git config --global user.name
#=> Ryan Snodgrass
```
```
# on host machine
git config --gobal user.email
#=> ryansnodgrass.dev@gmail.com
```

If nothing returns follow the directions on github for
[username](https://help.github.com/articles/setting-your-username-in-git/) and
[email](https://help.github.com/articles/setting-your-email-in-git/)

You should be able to ssh from github automatically once you tell it to. type:
```
# On host machine
ssh -T git@github.com
Hi RyanSnodgrass! You've successfully authenticated, but GitHub does not provide shell access.
```

If it does not and gives you `PermissionDenied Public Key` errors, checkout this
[walkthrough](https://github.com/ndoit/midgar/blob/master/PermissionDeniedPublicKey.md)
on fixing it. Note, this is for macs

Install Centos 7 Box and vagrant up
---

It should be this simple command and this starts up the box.
```
vagrant init centos/7; vagrant up --provider virtualbox
```

You're Done!
---

Once you have the environment setup, make sure you restart the box
`vagrant reload` and you're on your way to writing awesome code!
