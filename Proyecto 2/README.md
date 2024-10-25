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

enable
conf t
vlan 30
name administracion1
exit
vlan 40
name soporte1
exit


Router central
enable
conf t
interface s2/0
ip address 10.0.0.1 255.255.255.192
no shutdown
exit
interface s3/0
ip address 11.0.0.1 255.255.255.192
no shutdown
exit



#router derecho

enable
conf t
interface s2/0
ip address 11.0.0.2 255.255.255.192
no shutdown
exit

interface f0/0.30
encapsulation dot1Q 30
ip address 192.168.30.1 255.255.255.0
exit
interface f0/0.40
encapsulation dot1Q 40
ip address 192.168.40.1 255.255.255.0
exit



#Router izquierdo
enable
conf t
interface s2/0
ip address 10.0.0.2 255.255.255.192
no shutdown
exit

interface s3/0
ip address 12.0.0.1 255.255.255.192
no shutdown
exit

interface s4/0
ip address 13.0.0.1 255.255.255.192
no shutdown
exit


#Router izquiero izquiero
enable
conf t
interface s2/0
ip address 12.0.0.2 255.255.255.192
no shutdown
exit


interface f0/0.10
encapsulation dot1Q 10
ip address 192.168.10.1 255.255.255.0
exit


#Router izquierdo derecho
enable
conf t
interface s2/0
ip address 13.0.0.2 255.255.255.192
no shutdown
exit

interface f0/0.20
encapsulation dot1Q 20
ip address 192.168.20.1 255.255.255.0
exit

interface f0/0
no shutdown
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
switchport trunk allowed vlan all
exit
interface f0/3
switchport mode trunk
switchport trunk allowed vlan all
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


LACP
interface range fastEthernet 0/2
channel-group 1 mode active
exit

interface range fastEthernet 0/3
channel-group 1 mode active
exit

```


# COMANDOS REDES NACIONALES

```code 
enable
conf t
vlan 10
name Ventas
exit
vlan 20
name Facturacion
exit


enable
conf t
vlan 30
name Ventas01
exit
vlan 40
name Facturacion01
exit



interface range f0/2-3
switchport mode access
switchport access vlan 10
exit

interface range f0/2-3
switchport mode access
switchport access vlan 20
exit


interface range f0/1-3
switchport mode trunk
switchport trunk allowed vlan all
exit





interface f0/0.10
encapsulation dot1Q 10
ip address 192.168.10.1 255.255.255.0
exit
interface f0/0.20
encapsulation dot1Q 20
ip address 192.168.20.1 255.255.255.0
exit




router rip
version 2
network 192.168.10.0
network 192.168.20.0
network 192.168.30.0
network 192.168.40.0
network 10.0.0.0
network 11.0.0.0
network 12.0.0.0
network 13.0.0.0
no auto-summary
exit




interface s2/0
ip address 10.0.0.1 255.255.255.192
no shutdown
exit
interface s3/0
ip address 11.0.0.2 255.255.255.192
no shutdown
exit

interface s2/0
ip address 10.0.0.2 255.255.255.192
no shutdown
exit




interface s2/0
ip address 11.0.0.1 255.255.255.192
no shutdown
exit
interface s3/0
ip address 12.0.0.1 255.255.255.192
no shutdown
exit



interface s2/0
ip address 12.0.0.2 255.255.255.192
no shutdown
exit

interface s3/0
ip address 13.0.0.1 255.255.255.192
no shutdown
exit


interface s2/0
ip address 13.0.0.2 255.255.255.192
no shutdown
exit



interface f0/0
no shutdown
exit

interface f1/0
no shutdown
exit


interface f0/0.10
encapsulation dot1Q 10
ip address 192.168.10.1 255.255.255.0
exit

interface f0/0.20
encapsulation dot1Q 20
ip address 192.168.20.1 255.255.255.0
exit

interface f0/0
no shutdown
exit





interface f0/0.30
encapsulation dot1Q 30
ip address 192.168.30.1 255.255.255.0
exit

interface f1/0.40
encapsulation dot1Q 40
ip address 192.168.40.1 255.255.255.0
exit

interface f1/0
no shutdown
exit

interface range f0/1-3
switchport mode trunk
switchport trunk allowed vlan all
exit


interface f0/2
switchport mode access
switchport access vlan 10
exit


interface f0/1
switchport mode trunk
switchport trunk allowed vlan all
exit
interface f0/2
switchport mode access
switchport access vlan 40


LACP

interface range fastEthernet 0/1
channel-group 1 mode active
exit

interface range fastEthernet 0/2
channel-group 1 mode active
exit



```