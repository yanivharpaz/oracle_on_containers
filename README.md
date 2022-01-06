# Oracle RDBMS on Containers // Demo by Oracle ACS-IL
---
- Oracle RDBMS running on docker / kubernetes **(demo purpose only!)**
- The data will go into the */data* directory
- so make sure you have at least 5GB per DB container  
  

## install docker on Centos / Oracle Linux

```
sudo yum update -y

sudo yum install -y https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
sudo yum install -y epel-release apt-transport-https conntrack 
sudo yum install -y git mc ncdu zsh htop vim gcc wget jq

sudo yum -y groupinstall "Development Tools"
sudo yum install -y openssl-devel bzip2-devel libffi-devel xz-devel

sudo yum install -y python36 python36-devel python36-pip python-devel python-virtualenv
sudo yum install -y bind-utils mlocated yum-utils createrepo bin-utils openssh-clients perl parted

sudo yum remove docker \
                  docker-client \
                  docker-client-latest \
                  docker-common \
                  docker-latest \
                  docker-latest-logrotate \
                  docker-logrotate \
                  docker-engine
 
 
sudo yum install -y yum-utils
sudo yum-config-manager \
    --add-repo \
    https://download.docker.com/linux/centos/docker-ce.repo


sudo yum install -y docker-ce docker-ce-cli containerd.io
sudo service docker start
sudo systemctl enable docker
```

## Allow the current user to run docker commands
```
sudo groupadd docker
sudo usermod -aG docker $USER
```
## Load the demo image for Oracle RDBMS  
*make sure you accept the license terms on the website
```
docker login container-registry.oracle.com
docker pull container-registry.oracle.com/database/enterprise:latest
```   
if you want a specific version, go with:
```
docker pull docker pull container-registry.oracle.com/database/enterprise:19.3.0.0
```


## Create the docker network for the RDBMS containers
```
docker network create oradbnet
docker network ls
```

## Docker run and create the first DB
```
export ORACLE_SID=oradb01
export ORANET=oradbnet

sudo mkdir -p /data/oradata/$ORACLE_SID
sudo chown 54321:54322 -R /data/oradata/$ORACLE_SID
docker run -d -p 1521:1521 -p 5500:5500 --name $ORACLE_SID --user oracle --network $ORANET -v /data/oradata/$ORACLE_SID:/opt/oracle/oradata -e ORACLE_PDB=$ORACLE_SID_PDB -e ORACLE_SID=$ORACLE_SID container-registry.oracle.com/database/enterprise:latest

docker ports $ORACLE_SID
docker logs -f $ORACLE_SID
```

Change the initial sys/system password:
```
docker exec <oracle-db> ./setPassword.sh <your_password>
```

## Docker run and create the second DB (on different ports)
```
export ORACLE_SID=oradb02
export ORANET=oradbnet

sudo mkdir -p /data/oradata/$ORACLE_SID
sudo chown 54321:54322 -R /data/oradata/$ORACLE_SID
docker run -d -p 1522:1521 -p 5501:5500 --name $ORACLE_SID --user oracle --network $ORANET -v /data/oradata/$ORACLE_SID:/opt/oracle/oradata -e ORACLE_PDB=$ORACLE_SID_PDB -e ORACLE_SID=$ORACLE_SID yanivharpaz/oradb

docker logs -f $ORACLE_SID
```

## SSH into the DB Container
```
docker exec -it oradb01 /bin/bash
```
## get the IP Addresses of the containers
```
docker inspect oradb01 oradb02 -f '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}'
```  
or use this:    
```
docker network inspect oradbnet
```

## You can stop the containers
```
docker start oradb01 oradb02
```
## and later start the containers again
```
docker stop oradb01 oradb02
```

## Show all current containers (including stopped (-a) )
```
docker container ls -a
```

## When you no longer need these DB containers you can remove them
```
docker rm oradb01 oradb02
```

---
example of connection between the containers:  
```
open7# docker exec -it oradb07 /bin/bash
```

```
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
```
### Bonus material: video on docker basics  
[Getting started w/Docker YouTube link](https://youtu.be/iqqDU2crIEQ)

### for any questions, you can find me on twitter
[Yaniv Harpaz on Twitter](http://www.twitter.com/w1025)
