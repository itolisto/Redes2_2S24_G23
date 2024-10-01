# Proyecto 1

Topología para la primera práctica de Redes de Computadoras 2 del 2do Semestre de 2024

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
1. [Configurar servidores DHCP](#configurar-servidores-dhcp)
2. [Configurar MultiLayer Switches](#configurar-multilayer-switches)
3. [Configurar LACP](#configurar-servidores-dhcp)
4. [Configurar EIGRP](#configurar-eigrp)

### Configurar servidores DHCP

#### Servidor 1 y 2

1. Seleccionar Servidor e ir a la pestaña de "Desktop"

![Desktop Servidores](./screenshots/1_1.jpg)

2. Seleccionar "IP Configuration" para ingresar valores

| IP Address     | Subnet Mask     | DNS Server | Default Gateway |
| 192.168.23.1   | 255.255.255.192 | 0.0.0.0    | 192.168.23.1    |
| 192.168.23.129 | 255.255.255.192 | 0.0.0.0    | 192.168.23.129  |

![IP Configuration Servidores](./screenshots/1_2.jpg)

3. Habilitar servicio DHCP moviendose a la pestaña de "Services" y seleccionando "DHCP" del menu lateral izquierdo. Los valores de cada servidor se indican en las siguientes tablas:

![DHCP Servidores](./screenshots/1_3.jpg)

| Pool Name  | Default gateway & DNS Server | Start IP Address | Subnet Mask     | Max User |
| serverPool | 192.168.23.1                 | 192.168.23.0     | 255.255.255.192 | 64       |
| Subnet1    | 192.168.23.1                 | 192.168.23.2     | 255.255.255.192 | 62       |
| Subnet2    | 192.168.23.1                 | 192.168.23.66    | 255.255.255.192 | 62       |

| Pool Name  | Default gateway & DNS Server | Start IP Address | Subnet Mask     | Max User |
| serverPool | 192.168.23.129               | 192.168.23.128   | 255.255.255.192 | 64       |
| Subnet3    | 192.168.23.129               | 192.168.23.130   | 255.255.255.192 | 62       |
| Subnet4    | 192.168.23.129               | 192.168.23.194   | 255.255.255.192 | 62       |

### Configurar MultiLayer Switches

Para evitar crear las VLAN en todos los MultiLayer Switch y Switch Layer 2 se escoge el MultiLayer Switch 0 (MLS0) como servidor y el resto se configuran en modo cliente

#### MLS0

```bash
Switch>en
Switch#conf t
Switch(config)#hostname MLS0

MLSM2(config)#vtp mode server
MLSM2(config)#vtp domain usac.g23
MLSM2(config)#vtp password g23

MLSM2(config)#do write
```

#### MLS1 al MLS11 y S0 al S3

```bash
Switch>en
Switch#conf t
Switch(config)#hostname [MLS<1-11>] / [S<0-3>]

MLSM2(config)#vtp mode client
MLSM2(config)#vtp domain usac.g23
MLSM2(config)#vtp password g23
```

### Configurar LACP

#### MLS0

Ya que se configuro una única interfaz hasta este punto, vamos a desconfigurarla para configurar la dirección IP en el port-channel.

M2: 11.0.0.2
T9: 12.0.0.2 
Biblioteca central: 13.0.0.2

```bash
Switch>en
Switch#conf t
Switch(config)#hostname MLS0


MLSM2(config)#int g1/0/1

MLSM2(config)#int g1/0/1
MLSM2(config-if)#no switchport

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











### Configurar EIGRP

#### MLS0

```bash
MLSM2(config)#router eigrp 23
MLSM2(config-router)#network 11.0.0.2 0.0.0.255
MLSM2(config-router)#passive-interface f0/1
MLSM2(config-router)#no auto-summary
MLSM2(config-router)#end
```

#### Multilayer Switch 2

```bash
MLST3(config)#router eigrp 23
MLST3(config-router)#network 11.0.0.3 0.0.0.255
MLST3(config-router)#network 12.0.0.3 0.0.0.255
MLST3(config-router)#network 13.0.0.3 0.0.0.255
MLST3(config-router)#passive-interface f0/1
MLST3(config-router)#no auto-summary
MLST3(config-router)#end
```

#### Multilayer Switch 3

```bash
MLST9(config)#router eigrp 23
MLST9(config-router)#network 12.0.0.3 0.0.0.255
MLST9(config-router)#passive-interface f0/1
MLST9(config-router)#no auto-summary
MLST9(config-router)#end
```

#### Multilayer Switch 0

```bash
MLSBCENTRAL(config)#router eigrp 23
MLSBCENTRAL(config-router)#network 13.0.0.3 0.0.0.255
MLSBCENTRAL(config-router)#passive-interface f0/1
MLSBCENTRAL(config-router)#no auto-summary
MLSBCENTRAL(config-router)#end
```

#### Verificar el Routing EIGRP

```bash
MLSBCENTRAL#show ip eigrp neighbors
MLSBCENTRAL#show ip protocols
```
