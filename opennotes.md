##  Network Security Open Notes Exam 01 - 31st March 2020
#### When in doubt, `tab` it out!!
#### Note `> command` is to disguise the previous command not necessarily the cisco enable etc.
#### Note `*` means optional to the command.
#### Note also not all commands are in order, some double up.

### Configure IP ACLs to Mitigate Attacks:

- SSH session on router/pc:
```
> ssh -l [username] [ip address]
```
- Configure number standard ACL on router:
```
# access-list [10/20/30 etc] [permit | deny ] host [ip address]
```
- Apply number ACL to ingress traffic on VTY lines:
```
# line vty 0 15 (used to be 4)
# access-class [10/20/30 etc] [in or out]
```
- Applying ACLs:
```
# access-list [number] [permit | deny] [ip address] [subnet mask] [any | eq | domain etc]
```
- ACL to interface [number]
```
# interface [s0/0/0 | g0/0/0]
# ip access-group [number] [in or out]
```

### Configuring a Zone-Based Policy Firewall:

- Enable Security Technology package:
```
> conf t
# license boot module c1900 technology-package securityk9
# copy running-config startup-config | copy start run
```
- Create internal zone: 
```
> conf t
# zone security IN-ZONE
# exit
```
- Create external zone:
```
> conf t
# zone security OUT-ZONE
# exit
```
- Create a cass map referencing the internal traffic ACL:
```
> conf t
# class-map type inspect match-all IN-NET-CLASS-MAP
# match access-group [number]
# exit
```
- Create a policy map:
```
> conf t
# policy-map type inspect IN-2-OUT-PMAP
# class type inspect IN-NET-CLASS-MAP
# inspect
# exit 
# exit 
```
- Create a pair of zones: 
```
> conf t
# zone-pair security IN-2-OUT-ZPAIR source IN-ZONE destination OUT-ZONE
# service-policy type inspect IN-2-OUT-PMAP
# exit
```
- Assigning appropriate security zones to interfaces:
```
# int [g0/0/1 etc]
# zone-member security [IN|OUT-ZONE]
# exit
```
### Configure Cisco Routers for Syslog, NTP, and SSH Operations:

- Configure NTP clients:
```
> conf t
# ntp server [ip address]
```
- Configure router to update hardware clock:
```
> conf t
# ntp update-calendar
# show clock (this verifies)
```
- Configure router to timestamp log messages
``` 
> conf t
# service timestamps log datetime msec
```
- Configure the routers to identify the remote host (syslog server)
```
> conf t
# logging host [ip address]
```
- Configure a domain name
```
> conf t
# ip domain-name [url]
```
- Configure users for login to the SSH server on router
```
> conf t
# username SSHadmin privilege 15 secret [password]
```
- Configure incoming VTY lines on router
```
> conf t
# line vty 0 4 (should be 15 but for cisco)
# login local 
# transport input ssh
```
- Erasing exisiting key pairs on router
```
> conf t
# crypto key zeroize rsa (how many bits: 1024)
```
- Configure SSH timeouts and authentication parameters
```
> conf t
# ip ssh time-out [number of secs]
# ip ssh authentication-retries [number of retries]
# ip ssh version [number of version]
```
- Connect via telnet on PC
```
> telent [ip address]
```
- Connect to router using SSH with verison
```
# ssh -v [version number] -l [username] [ip address]
```
## Configure AAA Authentication on Cisco Routers Topology:

- Configure a local username on router	
```
> conf t
# username [username] secret [password]
```
- Configure local AAA auth for console access on router
```
> conf t
# aaa new-model
# aaa authentication login default (local)*
```
- Configure the line console to use AAA authentication method
```
> conf t
# line console 0
# login authentication default (verify the AAA by doing end)
```
- Create an RSA crypto key using 1024 bits
```
> conf t
# crypto key generate rsa (how many bits: 1024)
```
- Configure a named list AAA authentication method for the VTY lines on router
```
> conf t
# aaa authentication login SSH-LOGIN local
```
- Configure the VTY lines to use defined AAA auth	
```
> conf t
# line vty 0 4 (normally 15)
# login authentication SSH-LOGIN
# transport input ssh
# end
```
- Configure the TACACS+ server conf
```
> conf t
# tacacs-server host [ip address]
# tacacs-server key [password]
```
- Configure AAA login auth for console using TACACS+ server
```
> conf t
# aaa new-model
# aaa authentication login default group tacacs+ local
```
- Configure an AAA RADIUS server 
```
> conf t
# radius-server host [ip address]
# radius-server key [password]
```
- Configure an AAA login auth with RADIUS server
```
> conf t
# aaa new-model
# aaa authentication login default group radius
```
### Layer 2 Security

- Configure Root Bridge
```
> conf t
# spanning-tree vlan 1 root [primary | secondary ]
```
- Verifying the spanning-tree configuration 
```
> conf t
# show spanning-tree
```
- Enable PortFast on all access ports
```
> conf t
# int range f0/1 - 4
# spanning-tree portfast
```
- Enable BPDU guard on all access ports
```
> conf t
# int range f0/1 - 4
# spanning-tree bpduguard enable
```
- Enable root guard
```
> conf t
# int range f0/23 - 24 
# spanning-tree guard root
```
- Configure basic port security on all ports connected to host 
```
> conf t
# interface range f0/1 - 22
# switchport mode access
# switchport port-security
# switchport port-security maximum [max number]
# switchport port-security violation shutdown
# switchport port-security mac-address sticky
```
- Verify port security 
```
# show port-security int f0/1
```
- Disable unused ports
```
> conf t
# int range f0/5 - 22
# shutdown
```
### Layer 2 VLAN Security 

- Enabling trunking, including all trunk security mechanisms on the links (same commands on other switch
```
> conf t
# int f0/[number]
# switchport mode trunk 
# switchport trunk native vlan 15
# switchport nonegotiate
# no shutdown
```
- Enable VLAN 10 / 20 / 30 etc
```
> conf t
# vlan [10 | 20 | 30 etc]
```
- Create an interface VLAN + assign IP addresses
``` 
> conf t
# int vlan [10 | 20 | 30 etc]
# ip address [ip address] [subnet mask]
```
- Ensure PC is apart of the VLAN 
```
> conf t
# int f0/[number]
# switchport access vlan [10 | 20 | 30 etc]
# no shutdown
```
- Enable subinterface and set encapsulations to dot1q
```
> conf t
# interface g0/0 
# encapsultation dot1q [number]
```
### Configure IOS Intrusion Prevention System

- Create a directory in flash
```
# mkdir [name of directory] <enter>
```
- Configure the IPS signature 
```
> conf t
# ip ips config locaiton flash:[name of directory]
```
- Create an IPS rule
```
> conf t
# ip ips name [name of rule]
```
- Enable logging 
```
> conf t
# ip ips notify log 
# exit 
# clock set [digital time] [Date/Month/year]
# conf t
# service timestamps log datetime msec
# logging host [ip address]
```
- Configure IOS IPS to use signature 
```
> conf t
# ip ips signature-category 
# category all
# retired true
# exit 
# category ios_ips basic
# retired false
# exit 
# exit <enter>
```
- Apply the IPS rule to an interface
```
> conf t
# int g0/0
# ip ips iosips [in | out]
```
- Change the event-action of a siganture
```
> conf t
# ip ips signature-definition 
# signature [year] [subsig ID number]
# staus 
# retired false
# enabled true
# exit
# engine
# event-action produce-alert 
# event-action deny-packet-inline
# exit <enter>
```
- To verify IPS all
```
# show ip ips all
```


















