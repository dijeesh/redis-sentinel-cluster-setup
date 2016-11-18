# redis-sentinel-cluster-setup


Redis Sentinel High Availability Setup
===================


Hey ! Follow this procedure to setup a High Available Redis Caching Layer. 

### Environment 

> haproxy
> redis 
> redis-sentinel


Haproxy 1.5+ do have a TCP health check feature for redis. Haproxy will be configured in such a way that proxy connections will be forwarded to master instance only. 

Sentinel will constantly monitor the redis master instance and will promote slave node with lowest priority as next redis master.

Redis will be configured in such a way that one node will be master and other two nodes will be configured as slaveof the master instance.
