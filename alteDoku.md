Die Gelben Kabel gehen zu den PCs, die Router sind untereinander verbunden (verdeutlicht mit den pinken Linien).

| Router | RID | IPv4 auf ether2 und ether4 | IPv4 auf ether3 |
| ---- | ---- | ---- | ---- |
| 0 | 10.0.0.1 | 10.0.0.1/24 | 10.0.3.2/24 |
| 1 | 10.0.1.1 | 10.0.1.1/24 | 10.0.2.2/24 |
| 2 | 10.0.2.1 | 10.0.2.1/24 | 10.0.1.2/24 |
| 3 | 10.0.3.1 | 10.0.3.1/24 | 10.0.0.2/24 |

| Clients (angebunden an ether4) | Router | IP auf lab2 | Defaultgateway |
| ---- | ---- | ---- | ---- |
| PC01 | 0 | 10.0.0.10 | 10.0.0.1/24 |
| PC02 | 1 | 10.0.1.10 | 10.0.1.1/24 |
| PC03 | 2 | 10.0.2.10 | 10.0.2.1/24 |
| PC04 | 3 | 10.0.3.10 | 10.0.3.1/24 |

| Name | Netzadresse | Subnetzmaske |
| ---- | ---- | ---- |
| Netz0 | 10.0.0.0 | /24 |
| Netz1 | 10.0.1.0 | /24 |
| Netz2 | 10.0.2.0 | /24 |
| Netz3 | 10.0.3.0 | /24 |


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


# config für Router0
/ip address
add address=10.0.0.1/24 interface=ether2 network=10.0.0.0
add address=10.0.3.2/24 interface=ether3 network=10.0.3.0

# bridge erstellen
/interface bridge
add name=br1
add name=br2
# bridges hinzufügen
/interface bridge port
add bridge=br1 interface=ether2
add bridge=br1 interface=ether3
add bridge=br2 interface=ether4
add bridge=br2 interface=ether5

# OSPF RouterID Config
/routing ospf instance
set [ find default=yes ] router-id=10.0.0.1

# OSPF auf angeschlossenen Netzwerken aktivieren
/routing ospf network
add area=backbone network=10.0.0.0/24
add area=backbone network=10.0.3.0/24

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
