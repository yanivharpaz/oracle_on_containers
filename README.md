# oracle_on_containers
Oracle RDBMS running on docker / kubernetes


ssh into the linux server:
ssh -i ssh-key-XXXXX.key u000000@nnn.nnn.nnn.nnn

start / stop the DB instances:
docker start oradb07 oradb08
docker stop oradb07 oradb08

show container info (for example IP addresses):
docker inspect oradb07 oradb08 | grep -i addr

172.18.0.2
172.18.0.3

ssh into a db container:
docker exec -it oradb07 /bin/bash

see logs of the container (output of listener / alert stuff)
docker logs oradb07

example of connection between the containers:

open7# docker exec -it oradb07 /bin/bash


[oracle@56d03731d629 ~]$ ping 172.18.0.2

PING 172.18.0.2 (172.18.0.2) 56(84) bytes of data.

64 bytes from 172.18.0.2: icmp_seq=1 ttl=64 time=0.019 ms  
64 bytes from 172.18.0.2: icmp_seq=2 ttl=64 time=0.020 ms  
64 bytes from 172.18.0.2: icmp_seq=3 ttl=64 time=0.022 ms  
64 bytes from 172.18.0.2: icmp_seq=4 ttl=64 time=0.018 ms  

^C  
--- 172.18.0.2 ping statistics ---  
4 packets transmitted, 4 received, 0% packet loss, time 3075ms  
rtt min/avg/max/mdev = 0.018/0.019/0.022/0.005 ms  
[oracle@56d03731d629 ~]$ ping 172.18.0.3  
PING 172.18.0.3 (172.18.0.3) 56(84) bytes of data.  
64 bytes from 172.18.0.3: icmp_seq=1 ttl=64 time=0.055 ms  
64 bytes from 172.18.0.3: icmp_seq=2 ttl=64 time=0.051 ms  
64 bytes from 172.18.0.3: icmp_seq=3 ttl=64 time=0.042 ms  
64 bytes from 172.18.0.3: icmp_seq=4 ttl=64 time=0.050 ms  

^C  
--- 172.18.0.3 ping statistics ---  

4 packets transmitted, 4 received, 0% packet loss, time 3078ms
rtt min/avg/max/mdev = 0.042/0.049/0.055/0.008 ms  