# Proyecto 2

Topología para el segundo proyecto de Redes de Computadoras 2 del 2do Semestre de 2024

## Requisitos

Para correr el proyecto se debe contar con los siguientes requisitos:

- Cisco Packet Tracer 8.2.2.0400 [Aquí](https://www.netacad.com/portal/resources/packet-tracer)

## Integrantes

| Carnet      | Nombre                                 | 
|-------------|----------------------------------------|
|  201114340  |  Edgar Mauricio Gómez Flores           |
|  201020232  |  Daniel Eduardo Mellado Ayala          |
|  201900822  |  Osmar Noel Chacón Lemus               |

## Configuración



# COMANDOS TELECOM UNO

```code
vlan 10
name administracion
exit
vlan 20
name soporte
exit


vlan 30
name administracion1
exit
vlan 40
name soporte1
exit


Router central
interface f0/0
ip address 10.0.0.1 255.255.255.192
no shutdown
exit
interface f1/0
ip address 11.0.0.1 255.255.255.192
no shutdown
exit



#router derecho
interface f0/0
ip address 11.0.0.2 255.255.255.192
no shutdown
exit

interface f1/0.30
encapsulation dot1Q 30
ip address 192.168.30.1 255.255.255.0
exit


interface f1/0.40
encapsulation dot1Q 40
ip address 192.168.40.1 255.255.255.0
exit



#Router izquierdo
interface f2/0
ip address 10.0.0.2 255.255.255.192
no shutdown
exit



interface f1/0
ip address 12.0.0.1 255.255.255.192
no shutdown
exit

interface f0/0
ip address 13.0.0.1 255.255.255.192
no shutdown
exit


#Router izquiero izquiero
interface f1/0
ip address 12.0.0.2 255.255.255.192
no shutdown
exit


interface f0/0.10
encapsulation dot1Q 10
ip address 192.168.10.1 255.255.255.0
exit


#Router izquierdo derecho
interface f1/0
ip address 13.0.0.2 255.255.255.192
no shutdown
exit

interface f0/0.20
encapsulation dot1Q 20
ip address 192.168.20.1 255.255.255.0
exit



router eigrp 1
network 10.0.0.0 0.0.0.63
network 11.0.0.0 0.0.0.63
network 12.0.0.0 0.0.0.63
network 13.0.0.0 0.0.0.63
network 192.168.10.0 0.0.0.255
network 192.168.20.0 0.0.0.255
no auto-summary
exit

router eigrp 1
network 10.0.0.0 0.0.0.63
network 11.0.0.0 0.0.0.63
network 12.0.0.0 0.0.0.63
network 13.0.0.0 0.0.0.63
network 192.168.10.0 0.0.0.255
network 192.168.20.0 0.0.0.255
network 192.168.30.0 0.0.0.255
network 192.168.40.0 0.0.0.255
no auto-summary
exit


interface f0/1
switchport mode trunk
switchport trunk allowed vlan all
exit
interface f0/1
switchport mode trunk
switchport trunk allowed vlan 40
exit

interface f0/2
switchport mode trunk
switchport trunk allowed vlan 30
exit
interface f0/3
switchport mode trunk
switchport trunk allowed vlan 40
exit


interface range f0/2-3
switchport mode access
switchport access vlan 40
exit
interface range f0/2-3
switchport mode access
switchport access vlan 30
exit



interface range f0/2-3
switchport mode access
switchport access vlan 10
exit
interface range f0/2-3
switchport mode access
switchport access vlan 20
exit




interface range f0/2
channel-group 1 mode active
switchport mode trunk
switchport trunk allowed vlan all
no shutdown

interface range f0/3
channel-group 1 mode active
switchport mode trunk
switchport trunk allowed vlan all
no shutdown
```


# COMANDOS REDES NACIONALES

```code 
vtp mode server
vtp domain g23


vtp mode client
vtp domain g23


vlan 10
name VLAN10
exit
vlan 20
name VLAN20
exit



interface range g1/0/1-2
switchport mode trunk
switchport trunk allowed vlan all


interface range g1/0/1
switchport mode trunk
switchport trunk allowed vlan all
exit

interface range g1/0/2
switchport mode trunk
switchport trunk allowed vlan all
exit


interface range g1/0/3-5
switchport mode trunk
switchport trunk allowed vlan all
exit



interface g1/0/1
no switchport 
ip address 20.0.0.1 255.255.255.192
exit
interface g1/0/2
no switchport 
ip address 21.0.0.1 255.255.255.192
exit


interface g1/0/1
no switchport 
ip address 20.0.0.2 255.255.255.192
exit



interface g1/0/2
no switchport 
ip address 21.0.0.2 255.255.255.192
exit


LACP

interface range g1/0/3-5
no switchport
channel-group 1 mode active
channel-protocol lacp
exit
interface Port-channel 1
ip address 22.0.0.1 255.255.255.192
exit


interface range g1/0/3-5
no switchport
channel-group 1 mode active
channel-protocol lacp
exit
interface Port-channel 1
ip address 22.0.0.2 255.255.255.192
exit

interface range g1/0/3-5
no switchport
channel-group 1 mode active
channel-protocol lacp
exit
interface Port-channel 1
ip address 23.0.0.1 255.255.255.192
exit

interface range g1/0/3-5
no switchport
channel-group 1 mode active
channel-protocol lacp
exit
interface Port-channel 1
ip address 23.0.0.2 255.255.255.192
exit



interface range g1/0/1-2
switchport mode trunk
switchport trunk allowed vlan all
exit



interface f0/1
switchport mode trunk
switchport trunk allowed vlan all
exit




interface range f0/2-3
switchport mode access
switchport access vlan 10
switchport access vlan 20
exit




router rip
network 20.0.0.0 
network 21.0.0.0 
network 22.0.0.0 
network 23.0.0.0
exit
```