Redis Sentinel High Availability Setup
===================


Follow this procedure to setup a High Available Redis Caching Layer.

### Environment

 1. haproxy 
 2. redis 
 3. redis-sentinel

Haproxy 1.5+ do have a TCP health check feature for redis. Haproxy will be configured in such a way that proxy connections will be forwarded to master instance only. Sentinel will constantly monitor the redis master instance and will promote slave node with lowest priority as next redis master in case of failure. Redis will be configured in such a way that one node will be master and other two nodes will be configured as slaveof the master instance.

Sentinel will be running as a separate service. we'll need minimum 3 sentinel instances to monitor redis master instance. Our cluster will have quorum of 2 ie; two sentinel instances should agree / vote for a slave to be promoted as master incase of master failure.

### Architecture Diagram

[![redis-sentinel.jpg](https://s21.postimg.org/ccw0qpnh3/redis_sentinel.jpg)](https://postimg.org/image/4wwr4wzrn/)


### Server setup

**Redis, Redis-sentinel instances - redis-server-001,002,003**

Install system updates
>yum update -y

Install remi repository

>rpm -Uvh http://download.fedoraproject.org/pub/epel/6/x86_64/epel-release-6-8.noarch.rpm
>
>rpm -Uvh http://rpms.famillecollet.com/enterprise/remi-release-6.rpm

Install redis

>yum --enablerepo=remi,remi-test install redis

Configure redis and redis-sentinel

>Create redis-sentinel working directory
>
>mkdir /var/lib/redis-sentinel

>Download and install redis and redis-sentinel configuration from redis-server-001,002 and 003 directories

Bootstrap Redis HA enviorment - Start Services and add services into system startup

>chkconfig redis on ; chkconfig redis-sentinel on
>
>service redis start ; service redis-sentinel start


---

**Haproxy Instance - haproxy-server-001**

Install system updates

>yum update -y

Install epel repository

>yum -y install epel-release

Install haproxy

>yum -y install haproxy

Configure haproxy

>cp /etc/haproxy/haproxy.cfg /etc/haproxy/haproxy.cfg_bak
>
>Download and install haproxy configuration from haproxy-server-001 directory

Start Haproxy service and service into system startup

>service haproxy start
>
>chkconfig haproxy on

---
At this point, you'll have 

 1. Redis master running on redis-server-001 instance
 
 2. Redis slaves running on redis-server-002 and redis-server-003 instances
 
 3. Redis Sentinel services running on all 3 redis nodes
 
 4. Haproxy Server listerning on port 6379 and forwarding connections to master redis instance.
 
 5. Upon master failure, redis-sentinel service will promote a slave into the master and haproxy's tcp health-check will detect next master and forward connections to the new master instance.
 

We setup multiple HAProxy instances and manage them using cluster management services. That's how we ensure redundancy and HA for haproxy instances.
