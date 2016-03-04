

## Introduction

Fabric ([http://www.fabfile.org ](http://www.fabfile.org)) is a python deployment tool to automate builds.

## Setup

You need to have at least python 2.5 installed in your system

First update your operating system --

```
sudo apt-get update 
sudo apt-get upgrade
```

Then install subversion,python-dev headers and setuptools --

```
sudo apt-get install build-essential ssh subversion python-dev python-setuptools
```

Now easy\_install fabric

```
sudo easy_install fabric
```

If you are on python 2.6 you will need to update the Paramiko package used by Fabric :

```
sudo easy_install paramiko
```

You can check your python version by running :

```
python -V
```


## Quick Guide

All of the commands listed above are presented in a sequence (for easy copy and paste into a terminal window ) :

First switch to the root user
```
sudo -s
```

Then paste these commands and enter --
```
apt-get update 
apt-get upgrade -y
apt-get install build-essential ssh subversion python-dev python-setuptools -y
easy_install fabric paramiko

```

For Redhat/Fedora or rpm-based distributions.

```
yum groupinstall "Development Tools" "Legacy Software Development"
```

should suffice to setup build-essentials on the machine.