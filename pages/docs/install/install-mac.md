---
title: Running Singularity with Vagrant (Mac)
sidebar: main_sidebar
permalink: install-mac
folder: docs
---

This recipe demonstrates how to run Singularity on your Mac via Vagrant and Ubuntu. The recipe requires access to `brew` which is a package installation subsystem for OS X. This recipe may take anywhere from 5-20 minutes to complete.

```bash
# Install Brew if you do not have it installed already
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"

# The next commands will install Vagrant and the necessary bits
brew cask install virtualbox
brew cask install vagrant
brew cask install vagrant-manager

# Create a working directory for the Vagrant configuration and
# generate a template Vagrantfile for "ubuntu/trusty64"
mkdir singularity-vm
cd singularity-vm
vagrant init ubuntu/trusty64

# Build and start the Vagrant hosted VM
vagrant up --provider virtualbox

# Run the necessary commands within the VM to install Singularity
vagrant ssh -c /bin/sh <<EOF
    sudo apt-get update
    sudo apt-get -y install build-essential curl git sudo man vim autoconf libtool
    git clone {{ site.repo }}.git
    cd singularity
    ./autogen.sh
    ./configure --prefix=/usr/local
    make
    sudo make install
EOF

# Singularity is installed in your Vagrant Ubuntu VM! Now you can
# use Singularity as you would normally by logging into the VM
# directly
vagrant ssh
```

Remember that the VM is running in the background because we started it via the command `vagrant up`. You can shut the VM down using the command 'vagrant halt' when you no longer need it.
