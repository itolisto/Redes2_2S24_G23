# Práctica 2

Topología para la segunda práctica de Redes de Computadoras 2 del 2do Semestre de 2024

## Requisitos

Para correr la práctica se de contar con los siguientes requisitos:

- Cisco Packet Tracer 8.2.2.0400 [Aquí](https://www.netacad.com/portal/resources/packet-tracer)

## Integrantes

| Carnet      | Nombre                                 | 
|-------------|----------------------------------------|
|  201114340  |  Edgar Mauricio Gómez Flores           |
|  201020232  |  Daniel Eduardo Mellado Ayala          |
|  201900822  |  Osmar Noel Chacón Lemus               |

## Configuración

A continuación se describen los pasos realizados para configurar la práctica:

## Índice
1. [Crear VLANs en Switches](#crear-vlans-en-switches)
2. [Asignar VLAN a Interfaces](#asignar-vlan-a-interfaces)
3. [Configurar Enlaces Troncales](#configurar-enlaces-troncales)
4. [Crear VLANs en Switches MultiLayer](#crear-vlans-en-switches-multilayer)
5. [Configurar Enlaces Troncales MultiLayer](#configurar-enlaces-troncales-multilayer)
6. [Configurar Interfaces VLAN en Switch MultiLayer](#configurar-interfaces-vlan-en-switch-multilayer)
7. [Habilitar Inter-VLAN en Switch MultiLayer](#habilitar-inter-vlan-en-switch-multilayer)
8. [Configurar Static Routing en Switch MultiLayer](#configurar-static-routing-en-switch-multilayer)
9. [Configurar Listas de Control de Acceso (ACL)](#configurar-listas-de-control-de-acceso-acl)
10. [Configurar LACP](#configurar-lacp)
11. [Configurar OSPF](#configurar-ospf)
12. [Configurar EIGRP](#configurar-eigrp)
13. [Notas](#notas)

### Crear VLANs en Switches

#### M2, T3, T9 y Biblioteca Central
```bash
Switch>en
Switch#conf t
Switch(config)#hostname <M2, T3, T9, BCENTRAL> 
M2(config)#vlan 15
M2(config-vlan)#name SOPORTE
M2(config-vlan)#exit
M2(config)#vlan 25
M2(config-vlan)#name VISITANTES
M2(config-vlan)#exit
M2(config)#vlan 35
M2(config-vlan)#name RECURSOS
M2(config-vlan)#exit
M2(config)#do write
```

### Asignar VLAN a interfaces

Asignar los puertos donde están conectados los dispositivos finales a las VLANs correspondientes

#### M2
```bash
M2(config)#int f0/10
M2(config-if)#switchport mode access
M2(config-if)#switchport access vlan 35
M2(config-if)#exit
M2(config)#int f0/11
M2(config-if)#switchport mode access
M2(config-if)#switchport access vlan 25
M2(config-if)#exit
M2(config)#do write
```

#### T3
```bash
T3(config)#int f0/10
T3(config-if)#switchport mode access
T3(config-if)#switchport access vlan 35
T3(config-if)#exit
T3(config)#int f0/11
T3(config-if)#switchport mode access
T3(config-if)#switchport access vlan 15
T3(config-if)#exit
T3(config)#do write
```

#### T9
```bash
T9(config)#int f0/10
T9(config-if)#switchport mode access
T9(config-if)#switchport access vlan 25
T9(config-if)#exit
T9(config)#int f0/11
T9(config-if)#switchport mode access
T9(config-if)#switchport access vlan 35
T9(config-if)#exit
T9(config)#do write
```

#### Biblioteca central
```bash
BCENTRAL(config)#int f0/10
BCENTRAL(config-if)#switchport mode access
BCENTRAL(config-if)#switchport access vlan 35
BCENTRAL(config-if)#exit
BCENTRAL(config)#int f0/11
BCENTRAL(config-if)#switchport mode access
BCENTRAL(config-if)#switchport access vlan 25
BCENTRAL(config-if)#exit
BCENTRAL(config)#do write
```

### Configurar Enlaces Troncales

Configurar los puertos de enlace que conectan este Switch normal a un Switch MultiLayer como troncales, para que permitan pasar el tráfico de todas las VLANs.

#### M2, T3, T9 y Biblioteca Central
```bash
M2(config)#int f0/1
M2(config-if)#switchport mode trunk
M2(config-if)#switchport trunk allowed vlan 15,25,35
M2(config-if)#exit
M2(config)#do write
```

### Crear VLANs en Switches MultiLayer

#### M2, T3, T9 y Biblioteca Central
```bash
Switch>en
Switch#conf t
Switch(config)#hostname <MLSM2, MLST3, MLST9, MLSBCENTRAL> 
MLSM2(config)#vlan 15
MLSM2(config-vlan)#name SOPORTE
MLSM2(config-vlan)#exit
MLSM2(config)#vlan 25
MLSM2(config-vlan)#name VISITANTES
MLSM2(config-vlan)#exit
MLSM2(config)#vlan 35
MLSM2(config-vlan)#name RECURSOS
MLSM2(config-vlan)#exit
MLSM2(config)#do write
```

### Configurar Enlaces Troncales MultiLayer

Configurar los puertos de enlace que conectan este Switch normal a un Switch MultiLayer como troncales, para que permitan pasar el tráfico de todas las VLANs.

#### M2, T9 y Biblioteca Central
```bash
MLSM2(config)#int range f0/1-5 // Este rango nos servira luego para configurar LACP
MLSM2(config-if-range)#switchport trunk encapsulation dot1q
MLSM2(config-if-range)#switchport mode trunk
MLSM2(config-if-range)#switchport trunk allowed vlan 15,25,35
MLSM2(config-if-range)#exit
MLSM2(config)#do write
```

#### T3
```bash
MLST3(config)#int range f0/1-13 // Este rango nos servira luego para configurar LACP
MLST3(config-if-range)#switchport trunk encapsulation dot1q
MLST3(config-if-range)#switchport mode trunk
MLST3(config-if-range)#switchport trunk allowed vlan 15,25,35
MLST3(config-if-range)#exit
MLST3(config)#do write
```

### Configurar Interfaces VLAN en Switch MultiLayer

#### M2
```bash
MLSM2(config)#int vlan 35
MLSM2(config-if)#ip address 192.168.15.1 255.255.255.0
MLSM2(config-if)#exit
MLSM2(config)#int vlan 25
MLSM2(config-if)#ip address 192.168.25.1 255.255.255.0
MLSM2(config-if)#exit
MLSM2(config)#do write
```

#### T3
```bash
MLST3(config)#int vlan 35
MLST3(config-if)#ip address 192.168.35.1 255.255.255.0
MLST3(config-if)#exit
MLST3(config)#int vlan 15
MLST3(config-if)#ip address 192.168.45.1 255.255.255.0
MLST3(config-if)#exit
MLST3(config)#do write
```

#### T9
```bash
MLST9(config)#int vlan 35
MLST9(config-if)#ip address 192.168.55.1 255.255.255.0
MLST9(config-if)#exit
MLST9(config)#int vlan 25
MLST9(config-if)#ip address 192.168.65.1 255.255.255.0
MLST9(config-if)#exit
MLST9(config)#do write
```

#### Biblioteca Central
```bash
MLSBCENTRAL(config)#int vlan 35
MLSBCENTRAL(config-if)#ip address 192.168.75.1 255.255.255.0
MLSBCENTRAL(config-if)#exit
MLSBCENTRAL(config)#int vlan 25
MLSBCENTRAL(config-if)#ip address 192.168.85.1 255.255.255.0
MLSBCENTRAL(config-if)#exit
MLSBCENTRAL(config)#do write
```

### Habilitar Inter-VLAN en Switch MultiLayer

#### M2, T3, T9 y Biblioteca Central
```bash
MLSM2(config)#ip routing
MLSM2(config)#do write
```

### Configurar Static Routing en Switch MultiLayer

#### M2
```bash
MLSM2(config)#int f0/2
MLSM2(config-if)#no switchport
MLSM2(config-if)#ip address 11.0.0.2 255.255.255.0
MLSM2(config-if)#exit
MLSM2(config)#ip route 192.168.35.0 255.255.255.0 11.0.0.3
MLSM2(config)#ip route 192.168.45.0 255.255.255.0 11.0.0.3
MLSM2(config)#ip route 192.168.55.0 255.255.255.0 11.0.0.3
MLSM2(config)#ip route 192.168.75.0 255.255.255.0 11.0.0.3
MLSM2(config)#do write
```

#### T3
```bash
MLST3(config)#int f0/2
MLST3(config-if)#no switchport
MLST3(config-if)#ip address 11.0.0.3 255.255.255.0
MLST3(config-if)#exit
MLST3(config)#int f0/6
MLST3(config-if)#no switchport
MLST3(config-if)#ip address 13.0.0.3 255.255.255.0
MLST3(config-if)#exit
MLST3(config)#int f0/10
MLST3(config-if)#no switchport
MLST3(config-if)#ip address 12.0.0.3 255.255.255.0
MLST3(config-if)#exit
MLST3(config)#ip route 192.168.15.0 255.255.255.0 11.0.0.2
MLST3(config)#ip route 192.168.25.0 255.255.255.0 11.0.0.2
MLST3(config)#ip route 192.168.55.0 255.255.255.0 12.0.0.2
MLST3(config)#ip route 192.168.65.0 255.255.255.0 12.0.0.2
MLST3(config)#ip route 192.168.75.0 255.255.255.0 13.0.0.2
MLST3(config)#ip route 192.168.85.0 255.255.255.0 13.0.0.2
MLST3(config)#do write
```

#### T9
```bash
MLST9(config)#int f0/2
MLST9(config-if)#no switchport
MLST9(config-if)#ip address 12.0.0.2 255.255.255.0
MLST9(config-if)#exit
MLST9(config)#ip route 192.168.35.0 255.255.255.0 12.0.0.3
MLST9(config)#ip route 192.168.45.0 255.255.255.0 12.0.0.3
MLST9(config)#ip route 192.168.15.0 255.255.255.0 12.0.0.3
MLST9(config)#ip route 192.168.75.0 255.255.255.0 12.0.0.3
MLST9(config)#do write
```

#### Biblioteca Central
```bash
MLSBCENTRAL(config)#int f0/2
MLSBCENTRAL(config-if)#no switchport
MLSBCENTRAL(config-if)#ip address 13.0.0.2 255.255.255.0
MLSBCENTRAL(config-if)#exit
MLSBCENTRAL(config)#ip route 192.168.35.0 255.255.255.0 13.0.0.3
MLSBCENTRAL(config)#ip route 192.168.45.0 255.255.255.0 13.0.0.3
MLSBCENTRAL(config)#ip route 192.168.15.0 255.255.255.0 13.0.0.3
MLSBCENTRAL(config)#ip route 192.168.55.0 255.255.255.0 13.0.0.3
MLSBCENTRAL(config)#do write
```

### Configurar Listas de Control de Acceso (ACL)

#### M2, T9 y Biblioteca Central
```bash
MLSM2(config)#ip access-list extended ACL_VISITANTES
MLSM2(config-ext-nacl)#permit icmp any 192.168.45.0 0.0.0.255 echo-reply # Permitir respuestas ICMP a SOPORTE unicamente
MLSM2(config-ext-nacl)#exit

MLSM2(config)#ip access-list extended ACL_RECURSOS
MLSM2(config-ext-nacl)#permit icmp any 192.168.45.0 0.0.0.255 echo-reply # Permitir respuestas ICMP a SOPORTE
MLSM2(config-ext-nacl)#deny icmp any 192.168.45.0 0.0.0.255 echo # Denegar peticion ICMP a SOPORTE
MLSM2(config-ext-nacl)#permit icmp any any echo # Permitir tráfico ICMP (ping)
MLSM2(config-ext-nacl)#permit icmp any any echo-reply # Permitir respuestas de ICMP (ping)
MLSM2(config-ext-nacl)#exit

MLSM2(config)#int vlan 25
MLSM2(config-if)#ip access-group ACL_VISITANTES in
MLSM2(config-if)#int vlan 35
MLSM2(config-if)#ip access-group ACL_RECURSOS in
MLSM2(config-if)#exit
MLSM2(config)#do write
```

#### T3
```bash
MLST3(config)#ip access-list extended ACL_SOPORTE
MLST3(config-ext-nacl)#permit icmp any any echo
MLST3(config-ext-nacl)#permit icmp any any echo-reply
MLST3(config-ext-nacl)#exit

MLST3(config)#ip access-list extended ACL_RECURSOS
MLST3(config-ext-nacl)#permit icmp any 192.168.45.0 0.0.0.255 echo-reply
MLST3(config-ext-nacl)#deny icmp any 192.168.45.0 0.0.0.255 echo
MLST3(config-ext-nacl)#permit icmp any any echo
MLST3(config-ext-nacl)#permit icmp any any echo-reply
MLST3(config-ext-nacl)#exit

MLST3(config)#int vlan 15
MLST3(config-if)#ip access-group ACL_SOPORTE out
MLST3(config-if)#int vlan 35
MLST3(config-if)#ip access-group ACL_RECURSOS in
MLST3(config-if)#exit
MLST3(config)#do write
```



### Configurar LACP

#### M2, T9 y Biblioteca Central

Ya que se configuro una única interfaz hasta este punto, vamos a desconfigurarla para configurar la dirección IP en el port-channel.

M2: 11.0.0.2
T9: 12.0.0.2 
Biblioteca central: 13.0.0.2

```bash
MLSM2(config)#int f0/2
MLSM2(config-if)#no ip address

MLSM2(config)#int range f0/2-5
MLSM2(config-if-range)#no switchport
MLSM2(config-if-range)#channel-group 1 mode active
MLSM2(config-if-range)#channel-protocol lacp
MLSM2(config-if-range)#interface Port-channel1
MLSM2(config-if)#ip address <ip salida> 255.255.255.0
MLSM2(config-if)# exit
MLSM2(config)# do write
```

#### T3

```bash
MLST3(config)#int f0/2
MLST3(config-if)#no ip address
MLST3(config-if)#int f0/6
MLST3(config-if)#no ip address
MLST3(config-if)#int f0/10
MLST3(config-if)#no ip address
MLST3(config)#exit

MLST3(config)#int range f0/2-5
MLSM2(config-if-range)#no switchport
MLSM2(config-if-range)#channel-group 1 mode active
MLSM2(config-if-range)#channel-protocol lacp
MLSM2(config-if-range)#interface Port-channel1
MLSM2(config-if)#ip address 11.0.0.3 255.255.255.0
MLSM2(config-if)#exit

MLST3(config)#int range f0/6-9
MLSM2(config-if-range)#no switchport
MLSM2(config-if-range)#channel-group 2 mode active
MLSM2(config-if-range)#channel-protocol lacp
MLSM2(config-if-range)#interface Port-channel2
MLSM2(config-if)#ip address 13.0.0.3 255.255.255.0
MLSM2(config-if)#exit

MLST3(config)#int range f0/10-13
MLSM2(config-if-range)#no switchport
MLSM2(config-if-range)#channel-group 3 mode active
MLSM2(config-if-range)#channel-protocol lacp
MLSM2(config-if-range)#interface Port-channel3
MLSM2(config-if)#ip address 12.0.0.3 255.255.255.0
MLSM2(config-if)#exit

MLST3(config)#do write
```

### Configurar OSPF

TODO

### Configurar EIGRP

TODO

### Notas

#### Mostrar listas de acceso
```bash
do show access-lists
```

#### Mostrar listas de acceso asignadas en interfaces
```bash
do sh run | I interface| access-group
```

#### Eliminar lista de acceso
```bash
no ip access-list extended ACL_RECURSOS
```

#### Eliminar lista de acceso asignada en interfaz
```bash
int vlan 35
no ip access-group ACL_RECURSOS <in/out (depende de como se creo la lista de acceso)>
```

#### Listas canales EtherChannel (LACP)
```bash
do show etherchannel summary
```