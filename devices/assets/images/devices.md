# Devices

## udev

udev stands for User Device management. It dynamically discovers built-in hardware as well as peripheral devices:

during system boot
when hotplugged (at any time)
udev handles loading and unloading device drivers with proper configurations, as need, and its tasks include:

Device naming
Device file and symlink creating
Setting file attributes
Taking needed actions
When devices are added or removed from the system, udev receives a message from the kernel. It then parses the rules files in the **/etc/udev/rules.d** directory to see if any rules are there for the device added or removed.

These rules are totally customizable and can specify device file names, device file creation, specify symlinks to be created, specify file attributes to be set for the device file (including user and group ownership), and even specify actions to be taken (programs to be executed).

# Device Nodes

Character and block devices have device nodes; network devices do not. These device nodes can be used by programs to communicate with devices through nodes using normal I/O methods.

A device driver may use multiple device nodes. Device nodes are located in the **/dev** directory.

```sh
ls -l /dev
```

Device nodes can be created with the following command:

```sh
# sudo mknod [-m mode] /dev/name <type> </major> <minor>
  sudo mknod -m 666 /dev/mycdrv c 254 1
```


![Device Nodes](/assets/images/devices.png)

