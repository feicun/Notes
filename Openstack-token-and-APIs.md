## How to create token of OpenStack
1. Check [Keystone openrc document](http://docs.openstack.org/liberty/install-guide-ubuntu/keystone-openrc.html) firstly.
2. Create `admin-openrc.sh` or `demo-openrc.sh` according to above link
3. `source admin-openrc.sh` or `source demo-openrc.sh`
4. Test token: `$ openstack token issue`

Then you should get some output like this:
| Field      | Value                            |
|------------|----------------------------------|
| expires    | 2015-12-07T21:31:49.019395Z      |
| id         | 813c5df5d60d4f50a8cd4aafb41a92d4 |
| project_id | 93bbd9cafb1b43309782c04ff38f5d5f |
| user_id    | 5009a4fb8d054328abd5d2b592cd9d4e |


## Test OpenStack RESTful APIs:
[Document](http://developer.openstack.org/api-guide/quick-start/api-quick-start.html)

1. Use post method to fetch tokens:
```shell
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
$ curl -s -H "X-Auth-Token: $TOKEN" http://10.10.1.16:8777/v2/resources | python -m json.tool
```

OpenStack web API: [Link](http://docs.openstack.org/developer/ceilometer/webapi/v2.html)

curl -s -H "X-Auth-Token: $TOKEN" http://10.10.1.16:8777/v2/resources/ostack-compute-s2-16-vdimm_cd_(0x62) | python -m json.tool