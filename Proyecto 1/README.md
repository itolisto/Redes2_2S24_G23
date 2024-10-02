# Proyecto 1

Topología para el primer proyecto de Redes de Computadoras 2 del 2do Semestre de 2024

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

A continuación se describen los pasos realizados para configurar el proyecto:

## Índice
1. [Configurar servidores DHCP](#configurar-servidores-dhcp)
2. [Configurar VLAN Trunking Protocol](#configurar-vlan-trunking-protocol)
3. [Configurar enlaces truncales](#configurar-enlaces-truncales)
4. [Configurar VLANs](#configurar-vlans)
5. [Configurar enlaces en modo acceso](#configurar-enlaces-en-modo-acceso)
6. [Configurar LACP](#configurar-lacp)
7. [Asignar direcciones IP en Switch MultiLayer](#asignar-direcciones-ip-en-switch-multilayer)
8. [Configurar EIGRP](#configurar-eigrp)
9. [Configurar SVI](#configurar-svi)

### Configurar servidores DHCP

#### Servidor 1 y 2

1. Seleccionar Servidor e ir a la pestaña de "Desktop"

![Desktop Servidores](./screenshots/1_1.jpg)

2. Seleccionar "IP Configuration" para ingresar valores

| IP Address     | Subnet Mask     | DNS Server | Default Gateway |
| -------------- | --------------- | ---------- | --------------- |
| 20.0.0.1       | 255.255.255.252 | 0.0.0.0    | 20.0.0.1        |
| 20.0.0.2       | 255.255.255.252 | 0.0.0.0    | 20.0.0.2        |

![IP Configuration Servidores](./screenshots/1_2.jpg)

3. Habilitar servicio DHCP moviendose a la pestaña de "Services" y seleccionando "DHCP" del menu lateral izquierdo. Los valores de cada servidor se indican en las siguientes tablas:

![DHCP Servidores](./screenshots/1_3.jpg)

| Pool Name  | Default gateway & DNS Server | Start IP Address | Subnet Mask     | Max User |
| ---------- | ---------------------------- | ---------------- | --------------- | -------- |
| serverPool | 20.0.0.1                     | 192.168.23.0     | 255.255.255.252 | 4        |
| Subnet1    | 20.0.0.1                     | 192.168.23.2     | 255.255.255.192 | 62       |
| Subnet2    | 20.0.0.1                     | 192.168.23.66    | 255.255.255.192 | 62       |

| Pool Name  | Default gateway & DNS Server | Start IP Address | Subnet Mask     | Max User |
| ---------- | ---------------------------- | ---------------- | --------------- | -------- |
| serverPool | 20.0.0.2                     | 192.168.23.128   | 255.255.255.252 | 4        |
| Subnet3    | 20.0.0.2                     | 192.168.23.130   | 255.255.255.192 | 62       |
| Subnet4    | 20.0.0.2                     | 192.168.23.194   | 255.255.255.192 | 62       |

### Configurar VLAN Trunking Protocol

Para evitar crear las VLAN en todos los MultiLayer Switch y Switch Layer 2, se utiliza el protocolo VLAN Trunking Protocol (VTP), se escoge el MultiLayer Switch 0 (MLS0) como servidor y el resto se configuran en modo cliente.

#### MLS0

```bash
Switch>en
Switch#conf t
Switch(config)#hostname MLS0

MLS0(config)#vtp mode server
MLS0(config)#vtp domain usac.g23
MLS0(config)#vtp password g23
```

#### MLS1 al MLS11 y S0 al S3

```bash
Switch>en
Switch#conf t
Switch(config)#hostname [MLS<1-11>] / [S<0-3>]

MLS1(config)#vtp mode client
MLS1(config)#vtp domain usac.g23
MLS1(config)#vtp password g23
```

### Configurar enlaces truncales

Para permitir la propagación de la información de las VLAN por todos los Switches en el dominio, se necesita configurar los puertos en modo truncal entre ellos.

| Switch | Rango interfaces              | VLAN        |
| ------ | ----------------------------- | ----------- |
| MLS0   | g1/0/1 (DHCP1)                | 10,20       |
| MLS0   | g1/0/2 (DHCP2)                | 30,40       |
| MLS0   | g1/1/1                        | 10,20       |
| MLS0   | g1/1/2                        | 30,40       |
| MLS3   | f0/4-5                        | 10,20       |
| MLS4   | f0/4-5                        | 10,20       |
| MLS5   | f0/5-6                        | 10,20       |
| MLS6   | f0/5-6                        | 10,20       |
| MLS6   | f0/10                         | 10          |
| MLS6   | f0/11                         | 20          |
| MLS7   | f0/4-5                        | 30,40       |
| MLS8   | f0/4-5                        | 30,40       |
| MLS9   | f0/5-6                        | 30,40       |
| MLS10  | f0/5-6                        | 30,40       |
| MLS10  | f0/10                         | 40          |
| MLS10  | f0/11                         | 30          |
| MLS11  | g1/0/1                        | 10,20,30,40 |
| S0     | f0/10                         | 10          |
| S1     | f0/10                         | 20          |
| S2     | f0/10                         | 40          |
| S3     | f0/10                         | 30          |

```bash
MLS0(config)#int range <insertar rango>
MLS0(config)#switchport trunk encapsulation dot1q // Solo para interfaces FastEthernet conectando a Switch Layer 2 no MultiLayer Switch
MLS0(config-if-range)#switchport mode trunk
MLS0(config-if-range)#switchport trunk allowed vlan <insert vlans>
```

Nota: Las interfaces del rango `g1/0/1-2` se configuran en modo truncal ya que no pueden manejar nativamente múltiples VLAN sin modo truncal.

### Configurar VLANs

Una vez configurados los enlaces truncales, procedemos a crear las VLAN en el Switch Servidor

| VLAN | Nombre                 |
| ---- | ---------------------- |
| 10   | VLAN_Naranja1_Grupo_23 |
| 20   | VLAN_Verde2_Grupo_23   |
| 30   | VLAN_Naranja3_Grupo_23 |
| 40   | VLAN_Verde4_Grupo_23   |

#### MLS0

```bash
MLS0(config)#vlan <insertar numero>
MLS0(config-vlan)#name <insertar nombre>
```

Nota: Utilizar el mismo identificador de VLAN para diferentes subnets en el mismo Switch puede causar conflictos porque los VLAN IDs son identificadores únicos para transmitir dominios dentro de un Switch.

### Configurar enlaces en modo acceso

Luego de crear las VLANs, podemos continuar a configurar los interfaces que conectan con los dispositivos finales en modo acceso con su correspondiente VLAN.

| Switch | Rango de interfaz | VLAN asignada |
| ------ | ----------------- | ------------- |
| S0     | f0/11-12          | 10 (Naranja)  |
| S1     | f0/11-12          | 20 (Verde)    |
| S2     | f0/11-12          | 40 (Verde)    |
| S3     | f0/11-12          | 30 (Naranja)  |

```bash
S0(config)#int range <insertar rango>
S0(config-if)#switchport mode access
S0(config-if)#switchport access vlan <insert vlan>
```

### Configurar LACP

| Switch | Rango de interfaz | IP Address     | Subnet Mask     |
| ------ | ----------------- | -------------- | --------------- |
| MLS1   | g1/0/1-3          | 15.0.0.1       | 255.255.255.240 |
| MLS3   | f0/1-3            | 15.0.0.2       | 255.255.255.240 |
| MLS2   | g1/0/1-3          | 16.0.0.1       | 255.255.255.240 |
| MLS7   | f0/1-3            | 16.0.0.2       | 255.255.255.240 |

```bash
MLS1(config)#int range <insertar rango>
MLS1(config-if-range)#no switchport
MLS1(config-if-range)#channel-group 1 mode active
MLS1(config-if-range)#channel-protocol lacp
MLS1(config-if-range)#interface Port-channel1
MLS1(config-if)#ip address <insertar ip> 255.255.255.240
```

### Asignar direcciones IP en Switch MultiLayer

#### MLS0

```bash
# MLS0 → MLS1
MLS0(config)#int g1/1/1
MLS0(config-if)#no switchport
MLS0(config-if)#ip address 10.0.0.1 255.255.255.240
MLS0(config-if)#no shutdown
# MLS0 → MLS2
MLS0(config-if)#int g1/1/2
MLS0(config-if)#no switchport
MLS0(config-if)#ip address 11.0.0.1 255.255.255.240
MLS0(config-if)#no shutdown
```

#### MLS1

```bash
# Port channel se configuro al momento de configurar LACP

# MLS1 → MLS0
MLS1(config)#int g1/1/1
MLS1(config-if)#no switchport
MLS1(config-if)#ip address 10.0.0.2 255.255.255.240
MLS1(config-if)#no shutdown
# MLS1 → MLS2
MLS1(config-if)#int g1/1/3
MLS1(config-if)#no switchport
MLS1(config-if)#ip address 14.0.0.1 255.255.255.240
MLS1(config-if)#no shutdown
# MLS1 → MLS11
MLS1(config-if)#int g1/1/2
MLS1(config-if)#no switchport
MLS1(config-if)#ip address 12.0.0.1 255.255.255.240
MLS1(config-if)#no shutdown
```

#### MLS2

```bash
# Port channel se configuro al momento de configurar LACP

# MLS2 → MLS0
MLS2(config)#int g1/1/1
MLS2(config-if)#no switchport
MLS2(config-if)#ip address 11.0.0.2 255.255.255.240
MLS2(config-if)#no shutdown
# MLS2 → MLS1
MLS2(config-if)#int g1/1/3
MLS2(config-if)#no switchport
MLS2(config-if)#ip address 14.0.0.2 255.255.255.240
MLS2(config-if)#no shutdown
# MLS2 → MLS11
MLS2(config-if)#int g1/1/2
MLS2(config-if)#no switchport
MLS2(config-if)#ip address 12.0.0.1 255.255.255.240
MLS2(config-if)#no shutdown
```

#### MLS11

```bash
# MLS11 → MLS1
MLS11(config)#int g1/1/1
MLS11(config-if)#no switchport
MLS11(config-if)#ip address 12.0.0.2 255.255.255.240
MLS11(config-if)#no shutdown
# MLS11 → MLS2
MLS11(config-if)#int g1/1/2
MLS11(config-if)#no switchport
MLS11(config-if)#ip address 13.0.0.2 255.255.255.240
MLS11(config-if)#no shutdown
```

### Configurar EIGRP

#### MLS0

```bash
MLS0(config)#ip routing
MLS0(config)#router eigrp 23
MLS0(config-router)#network 10.0.0.0 0.0.0.15
MLS0(config-router)#network 11.0.0.0 0.0.0.15
MLS0(config-router)#network 192.168.23.0 0.0.0.63
MLS0(config-router)#network 192.168.23.64 0.0.0.63
MLS0(config-router)#no auto-summary
```

#### MLS1

```bash
MLS1(config)#ip routing
MLS1(config)#router eigrp 23
MLS1(config-router)#10.0.0.0 0.0.0.15
MLS1(config-router)#12.0.0.0 0.0.0.15
MLS1(config-router)#14.0.0.0 0.0.0.15
MLS1(config-router)#15.0.0.0 0.0.0.15
MLS1(config-router)#no auto-summary
```

#### MLS2

```bash
MLS2(config)#ip routing
MLS2(config)#router eigrp 23
MLS2(config-router)#network 11.0.0.0 0.0.0.15
MLS2(config-router)#network 13.0.0.0 0.0.0.15
MLS2(config-router)#network 14.0.0.0 0.0.0.15
MLS2(config-router)#network 16.0.0.0 0.0.0.15
MLS2(config-router)#no auto-summary
```

#### MLS11

```bash
MLS11(config)#ip routing
MLS11(config)#router eigrp 23
MLS11(config-router)#network 12.0.0.0 0.0.0.15
MLS11(config-router)#network 13.0.0.0 0.0.0.15
MLS11(config-router)#no auto-summary
```

### Configurar SVI

### MSL0

```bash
#VLAN 10 Interface
MLS0(config)# int vlan 10
MLS0(config-if)# ip address 192.168.23.1 255.255.255.192
MLS0(config-if)# ip helper-address 20.0.0.1
MLS0(config-if)# no shutdown
MLS0(config-if)# exit

#VLAN 20 Interface
MLS0(config)# int vlan 20
MLS0(config-if)# ip address 192.168.23.65 255.255.255.192
MLS0(config-if)# ip helper-address 20.0.0.1
MLS0(config-if)# no shutdown
MLS0(config-if)# exit

#VLAN 30 Interface
MLS0(config)# int vlan 30
MLS0(config-if)# ip address 192.168.23.129 255.255.255.192
MLS0(config-if)# ip helper-address 20.0.0.2
MLS0(config-if)# no shutdown
MLS0(config-if)# exit

#VLAN 40 Interface
MLS0(config)# int vlan 40
MLS0(config-if)# ip address 192.168.23.193 255.255.255.192
MLS0(config-if)# ip helper-address 20.0.0.2
MLS0(config-if)# no shutdown
MLS0(config-if)# exit
```
