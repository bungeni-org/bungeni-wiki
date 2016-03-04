


## Shrink VDI disk

```
cd <folder containing virtual disk>
VboxManage modifyvdi `pwd`/disk.vdi compact
```


## Enumerate Customizations on VirtualBox guest

```
~$ VBoxManage getextradata <guest-name> enumerate
```


## HTTP port forwarding

If you have a VirtualBox guest running on  a NAT connection -- and you want to provide access to some http services on the VBox guest via the host computer, do the following :

e.g. Your virtual box guest has a http server running on port 5000, and you want to make this accessible on port 5000 of the host.

```
VBoxManage modifyvm <guest name> --natpf1 "any-name, tcp,,5000,,5000"
```

To make this available on a specific IP interfaces of the host :

```
VBoxManage modifyvm <guest name> --natpf1 "any-name, tcp,<this.is.an.ip>,5000,,5000"
```



This will forward all requests on localhost of the host on port 5000  to port 5000 of the guest.



## SSH access to VirtualBox guest

If the VirtualBox guest is running on a bridged network connection - ssh access is via the IP address assigned to the guest.

If the VirtualBox guest is running on a NAT network connection - ssh access is via tcp tunneling, to set this up do the following :
All of the commands have to be run on the host computer.

First set the forwarding port (e.g. 2345) on the host computer :
```
$ VBoxManage setextradata <guest-name> "VBoxInternal/Devices/pcnet/0/LUN#0/Config/ssh/HostPort" 2345
```

Next the port to forward to on the guest computer :
```
$ VBoxManage setextradata <guest-name> "VBoxInternal/Devices/pcnet/0/LUN#0/Config/ssh/GuestPort" 22
```

Finally specify the protocol :
```
$ VBoxManage setextradata <guest-name> "VBoxInternal/Devices/pcnet/0/LUN#0/Config/ssh/Protocol" TCP
```

Restart the guest, and ssh from the host into the guest :
```
ssh -l <user-on-guest> -p 2345 localhost

```

To unset a customization , run it without any parameters :
```
$ VBoxManage setextradata <guest-name> "VBoxInternal/Devices/pcnet/0/LUN#0/Config/ssh/GuestPort"
```

## Using Snapshots

[Using Snapshots](http://blogs.sitepoint.com/virtualbox-branched-snapshots-tutorial/)

## Moving Snapshotted VMs to Another computer

[Cloning and copying VMs](http://srackham.wordpress.com/cloning-and-copying-virtualbox-virtual-machines/)

You can also use the "Export Appliance" action in virtualbox to create an installation appliance for transporting to another computer (remember to merge your snapshots before doing so )