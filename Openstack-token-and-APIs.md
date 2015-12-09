
OpenStack create user OS users parameters:
http://docs.openstack.org/liberty/install-guide-ubuntu/keystone-openrc.html
1. Create admin-openrc.sh / demo-openrc.sh
2. source admin-openrc.sh / demo-openrc.sh
3. Test token:
root@ostack-compute-s2-16:/opt/devstack# openstack token issue
+------------+----------------------------------+
| Field      | Value                            |
+------------+----------------------------------+
| expires    | 2015-12-07T21:31:49.019395Z      |
| id         | 813c5df5d60d4f50a8cd4aafb41a92d4 |
| project_id | 93bbd9cafb1b43309782c04ff38f5d5f |
| user_id    | 5009a4fb8d054328abd5d2b592cd9d4e |
+------------+----------------------------------+


Test OpenStack RESTful APIs:
http://developer.openstack.org/api-guide/quick-start/api-quick-start.html
1.
Use post method fetch tokens:
curl -s -X POST http://10.10.1.16:5000/v2.0/tokens \
            -H "Content-Type: application/json" \
            -d '{"auth": {"tenantName": "'"admin"'", "passwordCredentials":
            {"username": "'"admin"'", "password": "'"123456"'"}}}' \
            | python -m json.tool

-s : Slient mode
-X : Use giving method(POST/GET etc.)
-H : header
-d : data that send through POST method


2.
Use the token id that we got from above cmd to create a token on local machine:
export TOEKN=$token_id

3.
Try the following API, which authoried by local token, and will return resources list:
curl -s -H "X-Auth-Token: $TOKEN" http://10.10.1.16:8777/v2/resources | python -m json.tool




OpenStack web API:
http://docs.openstack.org/developer/ceilometer/webapi/v2.html

curl -s -H "X-Auth-Token: $TOKEN" http://10.10.1.16:8777/v2/resources/ostack-compute-s2-16-vdimm_cd_(0x62) | python -m json.tool