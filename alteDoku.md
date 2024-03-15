# Abgabe der Aufgabe OSPF-Laborübung von Tisch 1 (Tim Linneberger, Markus Hartel und Dennis Wandschura)
*Aufgabenstellung:*

Es soll ein Netzwerk mit OSPF und den MikroTik-Routern eingerichtet werden.
Dazu sollen vier Router und vier Clients zu einem Netzwerk zusammengeschlossen werden.
Diese Teilaufgabe soll in vierergruppen erledigt werden.
Anschließend sollen die in Gruppenarbeit erstellten Netzwerke zu einem Klassennetz über eine Backbone-Area zusammengeschaltet werden.

*Der logische Netzplan:*

*Der physische Netzplan:*



Die Gelben Kabel (Interface lab1) der PCs gehen zu den Router-Config-Ports.
Die Blauen Kabel (Interface lab2) der PCs gehen immer an den Port 3 des Routers.
Die Router sind untereinander verbunden (siehe physischer Netzplan).


| Router | RID | IPv4 auf ether2, ether3 und ether4 | IPv4 auf ether5, ether6, ether7 und ether8 | IPv4 auf ether9 (Zugang zum Backbone) |
| ---- | ---- | ---- | ---- |
| 1 | 1.1.1.1 | 10.0.10.1/24 | 10.0.13.2/24 | ? |
| 2 | 2.2.2.2 | 10.0.11.1/24 | 10.0.10.2/24 | - |
| 3 | 3.3.3.3 | 10.0.12.1/24 | 10.0.11.2/24 | - |
| 4 | 4.4.4.4 | 10.0.13.1/24 | 10.0.12.2/24 | - |

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
# bridge erstellen
/interface bridge
add name=bridge1
add name=bridge2

#OSPF RouterID config
/routing ospf area
add area-id=1.1.1.1 name=Area1
/routing ospf instance
set [ find default=yes ] router-id=1.1.1.1

# bridges hinzufügen
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
#PC01
sudo ipadd flush lab2
sudo ifconfig lab2 10.0.10.100/24
sudo route add default gw 10.0.10.1 lab2
```

```BASH
#PC02
sudo ipadd flush lab2
sudo ifconfig lab2 10.0.11.100/24
sudo route add default gw 10.0.11.1 lab2
```

```BASH
#PC03
sudo ipadd flush lab2
sudo ifconfig lab2 10.0.12.100/24
sudo route add default gw 10.0.12.1 lab2
```

```BASH
#PC04
sudo ipadd flush lab2
sudo ifconfig lab2 10.0.13.10/24
sudo route add default gw 10.0.13.1 lab2
```
