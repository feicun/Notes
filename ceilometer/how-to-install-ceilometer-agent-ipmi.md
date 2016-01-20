### Steps to install ceilometer-agent-ipmi:

`$ sudo apt-get install ceilometer-agent-ipmi`

`$ service ceilometer-agent-ipmi restart`

#### Troubleshooting:
- Possible error while install ipmitool:

    `Could not open device at /dev/ipmi0 or /dev/ipmi/0 or /dev/ipmidev/0: No such file or directory`

- Solution:
    You probably need to load the IPMI kernel modules:

    `$ modprobe ipmi_devintf`

    `$ modprobe ipmi_si`

    You can add these to /etc/modules to have them loaded automatically (just list the module names):
     ```
    ipmi_devintf
    ipmi_si
    ```

- Check if the driver is loaded

    `$ lsmod | grep -i ipmi`

    Possible output: `ipmi_devintf 7729 2`

- [Optional]: If ipmitool or ipmiutil still doesn't work after loading the drivers, try restarting ipmi server using:

    `$ service ipmi restart`



### Create ceilometer sudoers:
Ceilometer IPMI agent may not work correctly during to permission issue, if you find error in log, you may follow the below steps:

[Document](https://wiki.openstack.org/wiki/Rootwrap)
1. Enter the directory:
    * `cd /etc/sudoers.d/`

2. The ceilometer sudoers is similar with other sudoers, you can make a copy of nova_sudoers, then change the copy's name to ceilometer_sudoers. You also need to modify your new sudoers file: replace all 'nova' to 'ceilometer' in this file.



3. Create a filter file: [example](https://github.com/openstack/ceilometer/blob/master/etc/ceilometer/rootwrap.d/ipmi.filters)
    * `$ vim /etc/ceilometer/rootwrap.d/ipmi.filters`

4. Add following lines to fillter file:
    ```
    # ceilometer-rootwrap command filters for IPMI capable nodes
    # This file should be owned by (and only-writeable by) the root user

    [Filters]
    # ceilometer/ipmi/nodemanager/node_manager.py: 'ipmitool'
    ipmitool: CommandFilter, ipmitool, root
    ```
