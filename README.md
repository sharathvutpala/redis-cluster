# redis-cluster
Redis cluster with 1 master node, 3 slaves and 3 Sentinel Nodes

Highly-available redis cluster is configured with 1 Master node, 3 slave nodes and 3 Sentinel nodes. Redis Sentinel nodes monitor the master machine for high-availability and we can achieve automatic-failover by using sentinel configuration.

Cloudformation stack redis.json is used to provide the infrastructure needed for building the high-available redis cluster mentioned in this guide. 

## Configuration of infrastructure with Ansible

Present setup is automated with the help of Ansible. A role called ansible-redis is used. 

From the host machine we can configure the infrastructure mentioned in this guide, by running ansible-playbook "redis-ansible.yml" with some arguments. 

``` bash
$ ansible-playbook -i inventory.txt redis-ansible.yml -u ubuntu -b 
```
**Note:** We make use of argument -u, to tell the playbook to login as user ubuntu, and -b is for privilege escalation using become method
Here is the inventory file.
``` ini
[redis-master]
52.90.113.117

[redis-slave]
54.152.5.35
54.165.232.211
52.90.195.178

[redis-sentinel]
54.164.80.86 redis_sentinel=True
54.174.149.211 redis_sentinel=True
54.164.98.169 redis_sentinel=True
```
And here is the playbook.

``` yml 
---
- name: configure the master redis server
  hosts: redis-master
  roles:
    - ansible-redis

- name: configure redis slaves
  hosts: redis-slave
  vars:
    - redis_slaveof: 52.90.113.117 6379
  roles:
    - ansible-redis

- name: configure redis sentinel nodes
  hosts: redis-sentinel
  vars:
    - redis_sentinel_monitors:
      - name: master01
        host: 52.90.113.117
        port: 6379
  roles:
    - ansible-redis
```

We can test the status of master node, number of slave nodes, their status by logging in to any of sentinel nodes. 

To login into any of the sentinel node, master node, or slave node 
``` bash
$ ssh -i ansible.pem ubuntu@IPADDRESS
```
After logged in to any of the sentinel node, we can check the status of master node, slave nodes. 

We can use redis-cli command.

``` bash
$ redis-cli -p 26379
127.0.0.1:26379> sentinel master master01
```
This gives all the information about our master, in this case, our master's name is master01, so we get all the information about master01.

Similarly we can get all the information about our slave nodes and sentinel nodes using 
``` ini
sentinel slaves master01
sentinel sentinels master01
```
## Testing the Failover
