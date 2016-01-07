## Ceilometer Overview:
### Agents:
Collect information from each node, either run on central node or on the compute nodes.

### Backend Storage:
MongoDB, MySQL(or or any other SQLAlchemy-supported DB like PostgreSQL), HBase(via HBase Thrift interface)

### Plugins:
1. Instrument a measurement which has not yet been covered by existing plugins.

2. Polling agents could collect data.

3. The polling agent is implemented in ceilometer/agent/manager.py. As you will see in the manager, the agent loads all plugins defined in the namespace ceilometer.poll.agent, then periodically calls their get_samples() method.

4. Pollster: plugins are defined as subclasses of ceilometer.computer.BaseComputePollster

### New measurements:
Three type meters:
* Cumulative: Increasing over time (instance hours)
* Gauge: Discrete items (floating IPs, image uploads) and fluctuating values (disk I/O)
* Delta: Changing over time (bandwidth)

More information from official document: [Link](http://docs.openstack.org/developer/ceilometer/new_meters.html)
