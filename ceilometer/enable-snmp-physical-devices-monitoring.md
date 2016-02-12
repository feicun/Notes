##  How to enable SNMP physical devices monitoring of ceilometer
### Enable SNMP deamon on Ubuntu:
You should install SNMP service on all nodes that you would like to monitor.

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
        - snmp://localhost  # List IP of nodes that you willing to monitor here
    meters:
        - "hardware.cpu*"
        - "hardware.memory*"
        - "hardware.disk*"
        - "hardware.network*"
        - "hardware.system_stats*"
    sinks:
        - meter_sink
...
```
  * Update: above settings may cause duplicate SNMP samples collecting, if you find every SNMP samples are duplicating, please try following pipeline settings:
  ```
  ---
  sources:
      - name: meter_source
        interval: 30
        meters:
            - "*"
            - "!hardware.cpu*"
            - "!hardware.memory*"
            - "!hardware.disk*"
            - "!hardware.network*"
            - "!hardware.system_stats*"
        sinks:
            - meter_sink
      - name: meter_snmp
        interval: 30
        resources:
            - snmp://10.10.1.14
            - snmp://10.10.1.15
        meters:
            - "hardware.cpu*"
            - "hardware.memory*"
            - "hardware.disk*"
            - "hardware.network*"
            - "hardware.system_stats*"
        sinks:
            - snmp_sink
      - name: cpu_source
        interval: 30
        meters:
            - "cpu"
        sinks:
            - cpu_sink
            - cpu_delta_sink
      - name: disk_source
        interval: 30
        meters:
            - "disk.read.bytes"
            - "disk.read.requests"
            - "disk.write.bytes"
            - "disk.write.requests"
            - "disk.device.read.bytes"
            - "disk.device.read.requests"
            - "disk.device.write.bytes"
            - "disk.device.write.requests"
        sinks:
            - disk_sink
      - name: network_source
        interval: 30
        meters:
            - "network.incoming.bytes"
            - "network.incoming.packets"
            - "network.outgoing.bytes"
            - "network.outgoing.packets"
        sinks:
            - network_sink
  sinks:
      - name: meter_sink
        transformers:
        publishers:
            - notifier://
      - name: snmp_sink
        transformers:
        publishers:
            - notifier://
      - name: cpu_sink
        transformers:
            - name: "rate_of_change"
              parameters:
                  target:
                      name: "cpu_util"
                      unit: "%"
                      type: "gauge"
                      scale: "100.0 / (10**9 * (resource_metadata.cpu_number or 1))"
        publishers:
            - notifier://
      - name: cpu_delta_sink
        transformers:
            - name: "delta"
              parameters:
                  target:
                      name: "cpu.delta"
                  growth_only: True
        publishers:
            - notifier://
      - name: disk_sink
        transformers:
            - name: "rate_of_change"
              parameters:
                  source:
                      map_from:
                          name: "(disk\\.device|disk)\\.(read|write)\\.(bytes|requests)"
                          unit: "(B|request)"
                  target:
                      map_to:
                          name: "\\1.\\2.\\3.rate"
                          unit: "\\1/s"
                      type: "gauge"
        publishers:
            - notifier://
      - name: network_sink
        transformers:
            - name: "rate_of_change"
              parameters:
                  source:
                     map_from:
                         name: "network\\.(incoming|outgoing)\\.(bytes|packets)"
                         unit: "(B|packet)"
                  target:
                      map_to:
                          name: "network.\\1.\\2.rate"
                          unit: "\\1/s"
                      type: "gauge"
        publishers:
            - notifier://
  ```
  * Little explain of above yaml file: Ceilometer will send SNMP sample through meter_sink if you are not exclude the SNMP meters with '!', seems it is a bug.

2. Restart ceilometer services:
  * `$ restart ceilometer-agent-central`
  * `$ restart ceilometer-agent-compute`
