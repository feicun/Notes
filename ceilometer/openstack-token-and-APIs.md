## How to create token of OpenStack from CLI
[Keystone openrc document](http://docs.openstack.org/liberty/install-guide-ubuntu/keystone-openrc.html)

1. Create `admin-openrc.sh` or `demo-openrc.sh` according to above link.
2. `source admin-openrc.sh` or `source demo-openrc.sh`
3. Test token: `$ openstack token issue`

Then you should get some output like this:

| Field      | Value                            |
|------------|----------------------------------|
| expires    | 2015-12-07T21:31:49.019395Z      |
| id         | 813c5df5d60d4f50a8cd4aafb41a92d4 |
| project_id | 93bbd9cafb1b43309782c04ff38f5d5f |
| user_id    | 5009a4fb8d054328abd5d2b592cd9d4e |


## Test OpenStack RESTful APIs with Curl:

[Document](http://developer.openstack.org/api-guide/quick-start/api-quick-start.html)

1. Use post method to fetch tokens:
```
$ curl -s -X POST http://yourhost:5000/v2.0/tokens \
            -H "Content-Type: application/json" \
            -d '{"auth": {"tenantName": "'"admin"'", "passwordCredentials":
            {"username": "'"admin"'", "password": "'"123456"'"}}}' \
            | python -m json.tool
```
* Parameters:
  * `-s : Slient mode`
  * `-X : Use giving method(POST/GET etc.)`
  * `-H : header`
  * `-d : data that send through POST method`


2.
Use the token id that we got from above cmd to create a token on local machine:
`export TOEKN=$token_id`

3.
Try the following API, which authoried by local token, and will return resources list:
```shell
$ curl -s -H "X-Auth-Token: $TOKEN" http://yourhost:8777/v2/resources | python -m json.tool
```

Ceilometer web API: [Link](http://docs.openstack.org/developer/ceilometer/webapi/v2.html)


## Test OpenStack RESTful APIs with Python:

[Ceilometer web API doc](http://docs.openstack.org/developer/ceilometer/webapi/v2.html)

1.
Use above commands to request a token.

2.
Post this token with header by X-Auth-Token.
A working code as below, which demostrate how to get the statistics from 01/19/2016 to 01/20/2016.
```python
import requests
import json

start = '2016-01-19T18:58:29.002000'
end = '2016-01-20T18:58:29.002000'

response = requests.get(
    'http://10.10.1.14:8777/v2/meters/cpu_util/statistics?' +
    'q.field=timestamp&q.op=ge&q.value=' + start +
    '&q.field=timestamp&q.op=lt&q.value=' + end +
    '&q.field=resource_id&q.op=eq&q.value=4dd6b611-24a7-4f48-8787-71719c06c750',
    headers={'X-Auth-Token': 'b0f76b6d63a94c44880220f316e08e2f'}
    )

print response
parsed = json.loads(response.text)
print json.dumps(parsed, indent=4, sort_keys=True)
```
You should get the response similar with this:
```
<Response [200]>
[
    {
        "avg": 0.13931440122086403,
        "count": 2141,
        "duration": 85386.1,
        "duration_end": "2016-01-20T18:58:26.974000",
        "duration_start": "2016-01-19T19:15:20.874000",
        "groupby": null,
        "max": 24.70870358407425,
        "min": 0.05550174112834843,
        "period": 0,
        "period_end": "2016-01-20T18:58:26.974000",
        "period_start": "2016-01-19T18:58:29.002000",
        "sum": 298.2721330138699,
        "unit": "%"
    }
]
```
