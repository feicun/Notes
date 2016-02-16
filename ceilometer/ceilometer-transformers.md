Ceilometer transformers:

[Official Documents](http://docs.openstack.org/admin-guide-cloud/telemetry-data-collection.html#transformers)

Currently Ceilometer supports four kind of transformers

To make a transformed sample is accessable, you have to define a meter, so you can fetch the sample out via web APIs(meter name will be the parameter of API request).

To define a meter, you can add a new meter to configration by follow [this link](http://docs.openstack.org/admin-guide-cloud/telemetry-data-collection.html#transformers)

Or, you can simply add a "name" parameter to "target" field. Like following:
```
    - name: test_aggregate_sink
      transformers:
          - name: "aggregator"
            parameters:
                target:
                    name: "cpu_four_polling"
                retention_time: 120
                user_id: first
      publishers:
          - notifier://
          - file:///var/log/ceilometer/ceilometer-file-publisher
```
The new meter will be named to "cpu_four_polling", and you can query it by `$ceilometer sample-list -m cpu_four_polling`
