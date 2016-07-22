# redis-cluster
Redis cluster with 1 master node, 3 slaves and 3 Sentinel Nodes

Highly-available redis cluster is configured with 1 Master node, 3 slave nodes and 3 Sentinel nodes. Redis Sentinel nodes monitor the master machine for high-availability and we can achieve automatic-failover by using sentinel configuration.

Cloudformation stack redis.json is used to provide the infrastructure needed for building the high-available redis cluster mentioned in this guide. 

