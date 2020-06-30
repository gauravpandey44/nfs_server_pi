## NFS using your server

### Setup Server (192.168.196.10)

1. REFER: [click](https://hub.docker.com/r/itsthenetwork/nfs-server-alpine/)

2. mkdir -p ~/NFS

3. Start your server after creating this docker-compose.yml file `docker-compose up -d`

> NOTE: Here I have chosen the network mode as host because docker creates the specific nat filter rules in bridge  mode allowing all traffic to pass through rules.Event ufw firewall don't work here in the case    

```
version: "3.7"
services:
  nfs:
    image: itsthenetwork/nfs-server-alpine:latest
    volumes:
    - ~/NFS:/nfsshare
    network_mode: host
    environment:
    - SHARED_DIRECTORY=/nfsshare
    privileged: true

```

4. For security allow certain IP addresses :

```

{

sudo iptables -I INPUT  ! -s 192.168.196.0/24 -p tcp --dport 2049 -j REJECT        
sudo iptables -I INPUT  ! -s 192.168.196.0/24 -p udp --dport 2049 -j REJECT  

sudo iptables -I INPUT  -s 192.168.196.0/24 -p udp --dport 2049 -j ACCEPT  
sudo iptables -I INPUT  -s 192.168.196.0/24 -p tcp --dport 2049 -j ACCEPT

}

```

### Setup Client (192.168.196.120)

1. On client :


  -  `sudo apt install nfs-common`    
   
    
  - `sudo fusermount -u /mnt/NFS; sudo mkdir -p /mnt/NFS; sudo chmod 777 /mnt/NFS; sudo mount -v -o port=2049 192.168.196.10:/ /mnt/NFS`


2. Mount on restart:

```

echo '192.168.196.10:/ /mnt/NFS  nfs      defaults,proto=tcp,port=2049     0       0' | sudo tee -a  /etc/fstab


```


> Note : - first change the `~/NFS` with the  directory you want to share `- ~/NFS:/nfsshare`   
> Note : - Also change the repective IP with your IPs


## Launching 
- Run this on server machine : `docker-compose up -d` 
