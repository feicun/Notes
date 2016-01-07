##  How to enable SNMP physical devices monitoring of ceilometer
### Enable SNMP monitoring on Ubuntu:
1. Install SNMPD
  * `$ sudo apt-get install snmpd`

2. Create a backup file of snmpd.conf original file that we will edit later
  * `$ sudo mv /etc/snmp/snmpd.conf /etc/snmp/snmpd.conf.origin`

3. Create the new snmpd.conf file
  * `$ sudo vim /etc/snmp/snmpd.conf`

4. Add the new config file with these lines
  ```
  rocommunity public
  syslocation "Your Location"
  syscontact admin@domain.com
  ```
  * Note: above lines only keep the minimal running requirement for SNMP, for full details, please read original conf file.

5. Restart SNMPD service
  * `$ service snmpd restart`


### Add meters:
1. Add to /etc/ceilometer/pipeline.yaml:
```
...
  - name: meter_snmp
    interval: 600
    resources:
        - snmp://localhost
    meters:
        - "hardware.cpu*"
        - "hardware.memory*"
        - "hardware.disk*"
        - "hardware.network*"
    sinks:
        - meter_sink
...
```

2. Restart ceilometer services:
  * `$ restart ceilometer-agent-central`
  * `$ restart ceilometer-agent-compute`
