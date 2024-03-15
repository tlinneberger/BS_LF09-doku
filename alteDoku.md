Die Gelben Kabel gehen zu den PCs, die Router sind untereinander verbunden (verdeutlicht mit den pinken Linien).

| Router | RID | IPv4 auf ether2, ether3 und ether4 | IPv4 auf ether5, ether6, ether7 und ether8 |
| ---- | ---- | ---- | ---- |
| 1 | 1.1.1.1 | 10.0.10.1/24 | 10.0.13.2/24 |
| 2 | 2.2.2.2 | 10.0.11.1/24 | 10.0.10.2/24 |
| 3 | 3.3.3.3 | 10.0.12.1/24 | 10.0.11.2/24 |
| 4 | 4.4.4.4 | 10.0.13.1/24 | 10.0.12.2/24 |

| Clients (angebunden an ether3) | Router | IP auf lab2 | Defaultgateway |
| ---- | ---- | ---- | ---- |
| PC01 | 1 | 10.0.10.100 | 10.0.10.1/24 |
| PC02 | 2 | 10.0.11.100 | 10.0.11.1/24 |
| PC03 | 3 | 10.0.12.100 | 10.0.12.1/24 |
| PC04 | 4 | 10.0.13.100 | 10.0.13.1/24 |

| Name | Netzadresse | Subnetzmaske |
| ---- | ---- | ---- |
| Netz0 | 10.0.10.0 | /24 |
| Netz1 | 10.0.11.0 | /24 |
| Netz2 | 10.0.12.0 | /24 |
| Netz3 | 10.0.13.0 | /24 |


```BASH
# Interfaces zurücksetzen:
sudo ip addr flush lab1 && sudo ip addr flush lab2

# IP auf eine adr. der range des MT Routers setzten 
sudo ifconfig lab1 192.168.65.100

telnet 192.168.65.38
# mit "schueler" und keinem PWD anmelden

#Router Reset:
/system reset-configuration

#nach ein paar sekunden erneut mit telnet verbinden
telnet 192.168.65.38
# wieder mit "schueler" und keinem PWD anmelden


# config für Router1

/interface bridge
add name=bridge1
add name=bridge2
/routing ospf area
add area-id=1.1.1.1 name=Area1
/routing ospf instance
set [ find default=yes ] router-id=1.1.1.1
/interface bridge port
add bridge=bridge1 interface=ether2
add bridge=bridge1 interface=ether3
add bridge=bridge1 interface=ether4
add bridge=bridge2 interface=ether5
add bridge=bridge2 interface=ether6
add bridge=bridge2 interface=ether7
add bridge=bridge2 interface=ether8
/ip address
add address=10.0.10.1/24 interface=bridge1 network=10.0.10.0
add address=10.0.13.2/24 interface=bridge2 network=10.0.13.0
/routing ospf network
add area=Area1 network=10.0.10.0/24
add area=Area1 network=10.0.13.0/24

# Jetzt sollte alles passen
# um das zu überprüfen einfach folgenden Command laufen lassen
/routing ospf neighbor print

```

Konfiguration eines Client Copmuters (PC01 bis PC04):

```BASH
sudo ipadd flush lab2
sudo ifconfig lab2 10.0.0.10/24
sudo route add default gw 10.0.0.1 lab2
```

```BASH
sudo ipadd flush lab2
sudo ifconfig lab2 10.0.1.10/24
sudo route add default gw 10.0.1.1 lab2
```

```BASH
sudo ipadd flush lab2
sudo ifconfig lab2 10.0.2.10/24
sudo route add default gw 10.0.2.1 lab2
```

```BASH
sudo ipadd flush lab2
sudo ifconfig lab2 10.0.3.10/24
sudo route add default gw 10.0.3.1 lab2
```
