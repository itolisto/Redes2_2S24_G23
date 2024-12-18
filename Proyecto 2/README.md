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


# COMANDOS DE RED CONEXIONES FUTURAS

```code
enable
conf t
vlan 50
name Clientes
exit
vlan 10
name Seguridad
exit


enable
conf t
vlan 60
name Clientes1
exit

interface f0/1
switchport mode trunk
switchport trunk allowed vlan all
exit

interface range f0/2-4
switchport mode access
switchport access vlan 50
exit



interface f0/1
switchport mode trunk
switchport trunk allowed vlan all
exit

interface f0/2
switchport mode trunk
switchport trunk allowed vlan all
exit

interface f0/3
switchport mode access
switchport access vlan 10
exit



interface f0/1
switchport mode trunk
switchport trunk allowed vlan all
exit

interface range f0/2-4
switchport mode access
switchport access vlan 50
exit

interface range f0/2-4
switchport mode access
switchport access vlan 60
exit





interface f1/0.50
encapsulation dot1Q 50
ip address 192.168.50.1 255.255.255.0
exit
interface f1/0
no shutdown
exit
interface f0/0
no shutdown
exit



interface f1/0.10
encapsulation dot1Q 10
ip address 192.168.10.1 255.255.255.0
exit
interface f1/0
no shutdown
exit
interface f0/0
no shutdown
exit


interface f1/0.60
encapsulation dot1Q 60
ip address 192.168.60.1 255.255.255.0
exit


router ospf 1
network 192.168.50.0 0.0.0.255 area 0
network 192.168.60.0 0.0.0.255 area 0
network 192.168.10.0 0.0.0.255 area 0
network 10.0.0.0 0.0.0.63 area 0
network 11.0.0.0 0.0.0.63 area 0
network 12.0.0.0 0.0.0.63 area 0
network 13.0.0.0 0.0.0.63 area 0
exit

enable
conf t
interface s2/0
ip address 10.0.0.2 255.255.255.192
no shutdown
exit


enable
conf t
interface s2/0
ip address 11.0.0.2 255.255.255.192
no shutdown
exit


enable
conf t
interface s2/0
ip address 12.0.0.2 255.255.255.192
no shutdown
exit


  	

enable
conf t
interface s2/0
ip address 13.0.0.2 255.255.255.192
no shutdown
exit



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

interface s4/0
ip address 12.0.0.1 255.255.255.192
no shutdown
exit

interface s5/0
ip address 13.0.0.1 255.255.255.192
no shutdown
exit

interface f0/1
channel-group 1 mode active
exit

interface f0/2
channel-group 2 mode pasive
exit
```

# COMANDOS INTERCONEXION DE ISPs

## MSW1
```bash
enable
conf t
hostname MSW1
ip routing
interface g1/1/1
no switchport
ip address 172.23.1.1 255.255.255.240
no shutdown
exit
interface g1/1/2
no switchport
ip address 172.23.2.1 255.255.255.240
no shutdown
exit
interface g1/1/3
no switchport
ip address 172.23.3.1 255.255.255.240
no shutdown
exit
router bgp 100
neighbor 172.23.1.2 remote-as 200
neighbor 172.23.2.2 remote-as 400
neighbor 172.23.3.2 remote-as 300
network 172.23.1.0 mask 255.255.255.240
exit
```

## MSW2
```bash
enable
conf t
hostname MSW2
ip routing
interface g1/1/1
no switchport
ip address 172.23.1.4 255.255.255.240
no shutdown
exit
interface g1/1/2
no switchport
ip address 172.23.2.5 255.255.255.240
no shutdown
exit
interface g1/1/3
no switchport
ip address 172.23.3.6 255.255.255.240
no shutdown
exit
router bgp 200
neighbor 172.23.1.1 remote-as 100
neighbor 172.23.3.8 remote-as 300
neighbor 172.23.4.12 remote-as 400
network 172.23.2.0 mask 255.255.255.240
exit
```

## MSW3
```bash
enable
conf t
hostname MSW3
ip routing
interface g1/1/1
no switchport
ip address 172.23.3.7 255.255.255.240
no shutdown
exit
interface g1/1/2
no switchport
ip address 172.23.3.8 255.255.255.240
no shutdown
exit
interface g1/1/3
no switchport
ip address 172.23.3.9 255.255.255.240
no shutdown
exit
router bgp 300
neighbor 172.23.1.3 remote-as 100
neighbor 172.23.2.5 remote-as 200
neighbor 172.23.4.10 remote-as 400
network 172.23.3.0 mask 255.255.255.240
exit
```

## MSW4
```bash
enable
conf t
hostname MSW4
ip routing
interface g1/1/1
no switchport
ip address 172.23.4.10 255.255.255.240
no shutdown
exit
interface g1/1/2
no switchport
ip address 172.23.4.11 255.255.255.240
no shutdown
exit
interface g1/1/3
no switchport
ip address 172.23.4.12 255.255.255.240
no shutdown
exit
router bgp 400
neighbor 172.23.1.2 remote-as 100
neighbor 172.23.2.6 remote-as 200
neighbor 172.23.3.9 remote-as 300
network 172.23.4.0 mask 255.255.255.240
exit
```