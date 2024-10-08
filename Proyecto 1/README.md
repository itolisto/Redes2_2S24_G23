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
2. [Cambios en Switch Multicapa 3650-24PS](#cambios-en-switch-multicapa-3650-24ps)
3. [Configurar VLAN Trunking Protocol](#configurar-vlan-trunking-protocol)
4. [Configurar VLANs](#configurar-vlans)
5. [Configurar enlaces en modo truncal](#configurar-enlaces-en-modo-truncal)
6. [Configurar enlaces en modo acceso](#configurar-enlaces-en-modo-acceso)
7. [Configurar LACP](#configurar-lacp)
8. [Asignar direcciones IP en Switch MultiLayer](#asignar-direcciones-ip-en-switch-multilayer)
9. [Configurar EIGRP](#configurar-eigrp)
10. [Configurar SVI y DHCP Relay](#configurar-svi-y-dhcp-relay)
11. [Servidor Web](#servidor-web)
11. [Configuracion HSRP de la red](#configuracion-hsrp-de-la-red)

### Configurar servidores DHCP

#### Servidor 1 y 2

1. Seleccionar Servidor e ir a la pestaña de "Desktop"

![Desktop Servidores](./screenshots/1_1.jpg)

2. Seleccionar "IP Configuration" para ingresar valores

| IP Address     | Subnet Mask     | Default Gateway | DNS Server |
| -------------- | --------------- | --------------- | ---------- |
| 20.0.0.10      | 255.255.255.0   | 20.0.0.1        | 0.0.0.0    |
| 30.0.0.10      | 255.255.255.0   | 30.0.0.1        | 0.0.0.0    |

![IP Configuration Servidores](./screenshots/1_2.jpg)

3. Habilitar servicio DHCP moviendose a la pestaña de "Services" y seleccionando "DHCP" del menu lateral izquierdo. Los valores de cada servidor se indican en las siguientes tablas:

![DHCP Servidores](./screenshots/1_3.jpg)

| Pool Name  | Default gateway & DNS Server | Start IP Address | Subnet Mask     | Max User |
| ---------- | ---------------------------- | ---------------- | --------------- | -------- |
| serverPool | 20.0.0.1                     | 20.0.0.0         | 255.255.255.0   | 4        |
| Subnet1    | 192.168.23.1                 | 192.168.23.2     | 255.255.255.192 | 62       |
| Subnet2    | 192.168.23.65                | 192.168.23.66    | 255.255.255.192 | 62       |

| Pool Name  | Default gateway & DNS Server | Start IP Address | Subnet Mask     | Max User |
| ---------- | ---------------------------- | ---------------- | --------------- | -------- |
| serverPool | 30.0.0.1                     | 30.0.0.0         | 255.255.255.0   | 4        |
| Subnet3    | 192.168.23.129               | 192.168.23.130   | 255.255.255.192 | 62       |
| Subnet4    | 192.168.23.193               | 192.168.23.194   | 255.255.255.192 | 62       |

### Cambios en Switch Multicapa 3650-24PS

Para utilizar el Switch MultiCapa 3650-24PS, se debe agregar una fuente de alimentación y cuatro módulos GLC-LH-SMD para soportar la conexión con cable de fibra como se describe en el enunciado.

![Agregar módulos a Switch](./screenshots/2_1.jpg)

### Configurar VLAN Trunking Protocol

Para evitar crear las VLAN en todos los MultiLayer Switch y Switch Layer 2, se utiliza el protocolo VLAN Trunking Protocol (VTP), se escoge el MultiLayer Switch 0 (MLS0) como servidor y el resto se configuran en modo cliente.

:warning: Nota a futuro: Más adelante vamos a configurar el protocolo LACP y las interfaces se van a utilizar el comando `no switchport` para asignar una dirección ip, esto los convierte en interfaces de capa 3 mientras que VTP (VLAN Trunking Protocol) funciona en capa 2, lo que evitaria la propagación de las VLAN en todos los Switch de cada edificio, por tal razón utilizamos enlaces truncales primero y que las VLAN se puedan propagar en un inicio, luego cualquier nueva VLAN o modificación hay que hacerla manual o configurar nuevos Switch en modo servidor.

#### MLS0

```bash
Switch>en
Switch#conf t
Switch(config)#hostname MLS0

MLS0(config)#vtp mode server
MLS0(config)#vtp domain usac.g23
MLS0(config)#vtp password g23
```

#### MLS1-11 y S0-3

```bash
Switch>en
Switch#conf t
Switch(config)#hostname [MLS<1-11>] / [S<0-3>]

MLS1(config)#vtp mode client
MLS1(config)#vtp domain usac.g23
MLS1(config)#vtp password g23
```

### Configurar VLANs

Al tener cuatro subnets y dos servidores DHCP que van a servir dos subnets cada uno, además de una misma interfaz para conectar a cada servidor, es más fácil crear cuatro VLAN's para separar el tráfico de cada subnet y crear dos VLAN específicas para luego utilizar Switch Virtaul Interfaces (SVIs) y configurar DHCP Relay (Helper Addresses) para reenviar las peticiones de DHCP al servidor correcto. 

| VLAN | Nombre                 | Nota           |
| ---- | ---------------------- | -------------- |
| 10   | VLAN_Naranja1_Grupo_23 |                |
| 20   | VLAN_Verde2_Grupo_23   |                |
| 30   | VLAN_Verde3_Grupo_23   |                |
| 40   | VLAN_Naranja4_Grupo_23 |                |
| 98   | Link_To_DHCP1          | Solo para MLS0 |
| 99   | Link_To_DHCP2          | Solo para MLS0 |

#### MLS0

```bash
MLS0(config)#vlan <insertar numero>
MLS0(config-vlan)#name <insertar nombre>
```

Nota: Utilizar el mismo identificador de VLAN para diferentes subnets en el mismo Switch puede causar conflictos porque los VLAN IDs son identificadores únicos para transmitir dominios dentro de un Switch.

### Configurar enlaces en modo truncal

Para permitir la propagación de la información de las VLAN por todos los Switches en el dominio, se necesita configurar los puertos en modo truncal entre ellos.

| Switch | Rango interfaces              | VLAN        |
| ------ | ----------------------------- | ----------- |
| MLS0   | g1/1/1-2                      | 10,20,30,40 |
| MLS11  | g1/1/1-2                      | 10,20,30,40 |
| MLS1   | g1/1/1-3                      | 10,20,30,40 |
| MLS1   | g1/0/1-3                      | 10,20       |
| MLS2   | g1/1/1-3                      | 10,20,30,40 |
| MLS2   | g1/0/1-3                      | 30,40       |

```bash
MLS0(config)#int range <insertar rango>
MLS0(config-if-range)#switchport # Los pueertos gigabit ethernet del switch 3650 vienen por defecto como no switchport
MLS0(config-if-range)#switchport mode trunk
MLS0(config-if-range)#switchport trunk allowed vlan <insert vlans>
```

| Switch | Rango interfaces              | VLAN        |
| ------ | ----------------------------- | ----------- |
| MLS3   | f0/1-5                        | 10,20       |
| MLS4   | f0/1-2                        | 10,20       |
| MLS5   | f0/1-2                        | 10,20       |
| MLS6   | f0/1-4                        | 10,20       |
| MLS7   | f0/1-5                        | 30,40       |
| MLS8   | f0/1-2                        | 30,40       |
| MLS9   | f0/1-2                        | 30,40       |
| MLS10  | f0/1-4                        | 30,40       |
| S0     | f0/1                          | 10          |
| S1     | f0/1                          | 20          |
| S2     | f0/1                          | 30          |
| S3     | f0/1                          | 40          |


```bash
MLS3(config)#int range <insertar rango>
MLS3(config-if-range)#switchport mode trunk
MLS3(config-if-range)#switchport trunk allowed vlan <insert vlans>
```

:warning: Nota: Si las VLAN no se propagan luego de configurar los enlaces truncales, se puede probar recargar MLS0, MLS1 o MLS2 para corregir ese error utilizando `reload`.

### Configurar enlaces en modo acceso

Luego de crear las VLANs, podemos continuar a configurar los interfaces que conectan hacia los servidores DHCP y dispositivos finales.

| Switch | Rango de interfaz | VLAN asignada |
| ------ | ----------------- | ------------- |
| MLS0   | g1/0/1 (DHCP1)    | 98            |
| MLS0   | g1/0/2 (DHCP2)    | 99            |
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
MLS2(config-if)#ip address 13.0.0.1 255.255.255.240
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

#### MLS3-6 y MLS-7-10

##### Lado izquierdo

| MLS # | Interfaz   | Networks  |
| ----- | ---------- | --------- |
| 3     | f0/4       | 15.0.1.1  |
| 3     | f0/5       | 15.0.2.1  |
| 4     | f0/1       | 15.0.1.2  |
| 4     | f0/2       | 15.0.2.2  |
| 5     | f0/1       | 15.0.2.3  |
| 5     | f0/2       | 15.0.1.3  |
| 6     | f0/1       | 15.0.2.4  |
| 6     | f0/2       | 15.0.1.4  |

* Port-channel1 en MLS3 se configuro al momento de configurar LACP

##### Lado derecho

| MLS # | Interfaz   | Networks  |
| ----- | ---------- | --------- |
| 7     | f0/4       | 16.0.1.1  |
| 7     | f0/5       | 16.0.2.1  |
| 8     | f0/1       | 16.0.1.2  |
| 8     | f0/2       | 16.0.2.2  |
| 9     | f0/1       | 16.0.2.3  |
| 9     | f0/2       | 16.0.1.3  |
| 10    | f0/1       | 16.0.2.4  |
| 10    | f0/2       | 16.0.1.4  |

* Port-channel1 en MLS7 se configuro al momento de configurar LACP

```bash
MLS3(config)#int <reemplazar interfaz>
MLS3(config-if)#no switchport
MLS3(config-if)#ip address <reemplazar ip> 255.255.255.240
MLS3(config-if)#no shutdown
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
MLS0(config-router)#network 192.168.23.128 0.0.0.63
MLS0(config-router)#network 192.168.23.192 0.0.0.63
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
MLS11(config-router)#network 40.0.0.0 0.0.0.15
MLS11(config-router)#no auto-summary
```

#### MLS3-6 y MLS-7-10

##### Lado izquierdo

| MLS # | Networks                         |
| ----- | -------------------------------- |
| 3     | 15.0.0.0, 15.0.1.0, 15.0.2.0     |
| 4     | 15.0.1.0, 15.0.2.0, 192.168.23.0 |
| 5     | 15.0.1.0, 15.0.2.0, 192.168.23.0 |
| 6     | 15.0.1.0, 15.0.2.0, 192.168.23.0 |

##### Lado derecho

| MLS # | Networks                         |
| ----- | -------------------------------- |
| 7     | 16.0.0.0, 16.0.1.0, 16.0.2.0     |
| 8     | 16.0.1.0, 16.0.2.0, 192.168.23.0 |
| 9     | 16.0.1.0, 16.0.2.0, 192.168.23.0 |
| 10    | 16.0.1.0, 16.0.2.0, 192.168.23.0 |

```bash
MLS4(config)#ip routing
MLS4(config)#router eigrp 23
MLS4(config-router)#network 15.0.0.0 0.0.0.15
MLS4(config-router)#network 15.0.1.0 0.0.0.15
MLS4(config-router)#network 15.0.2.0 0.0.0.15
MLS4(config-router)#network 192.168.23.0 0.0.0.255
MLS4(config-router)#no auto-summary
```

### Configurar SVI y DHCP Relay

Al utilizar Switch Virtual Interfaces (SVIs) podemos implementar interfaces lógicas asociadas a las VLANs permitiendo al Switch usar routing de capa 3. Ademas con el uso de DHCP Relay (`ip helper-address`), le indicamos al Switch que reenvié el broadcast de DHCP a un servidor DHCP específico.

#### MSL0

```bash
#VLAN to DHCP 1
MLS0(config)#int vlan 98
MLS0(config-if)#ip address 20.0.0.1 255.255.255.0
MLS0(config-if)#no shutdown
#VLAN 10 Interface
MLS0(config-if)#int vlan 10
MLS0(config-if)#ip address 192.168.23.1 255.255.255.192
MLS0(config-if)#no shutdown
MLS0(config-if)#ip helper-address 20.0.0.10
#VLAN 20 Interface
MLS0(config-if)#int vlan 20
MLS0(config-if)#ip address 192.168.23.65 255.255.255.192
MLS0(config-if)#no shutdown
MLS0(config-if)#ip helper-address 20.0.0.10

#VLAN to DHCP 2
MLS0(config)#int vlan 99
MLS0(config-if)#ip address 30.0.0.1 255.255.255.0
MLS0(config-if)#no shutdown
#VLAN 30 Interface
MLS0(config-if)#int vlan 30
MLS0(config-if)#ip address 192.168.23.193 255.255.255.192
MLS0(config-if)#no shutdown
MLS0(config-if)#ip helper-address 30.0.0.10
#VLAN 40 Interface
MLS0(config-if)#int vlan 40
MLS0(config-if)#ip address 192.168.23.129 255.255.255.192
MLS0(config-if)#no shutdown
MLS0(config-if)#ip helper-address 30.0.0.10
MLS0(config-if)#exit
```

### Servidor Web

#### Actuliazar index.html
Eliminar todas los archivos en el servicio de `HTTP` a excepción de `index.html` y reemplazar el contenido con el siguiente HTML:

![Reemplazar contenido index.html](./screenshots/11_1.jpg)

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Redes 2 Sección N - 2024 2do Semestre</title>
    <style>
        table {
            width: 50%;
            border-collapse: collapse;
            margin: 20px auto;
        }
        table, th, td {
            border: 1px solid black;
        }
        th, td {
            padding: 8px;
            text-align: center;
        }
        th {
            background-color: #f2f2f2;
        }
    </style>
</head>
<body>
    <h2 style="text-align: center;">Grupo 23</h2>
    <table>
        <tr>
            <th>Carnet</th>
            <th>Nombre</th>
        </tr>
        <tr>
            <td>201114340</td>
            <td>Edgar Mauricio Gómez Flores</td>
        </tr>
        <tr>
            <td>201020232</td>
            <td>Daniel Eduardo Mellado Ayala</td>
        </tr>
        <tr>
            <td>201900822</td>
            <td>Osmar Noel Chacón Lemus</td>
        </tr>
    </table>
</body>
</html>
```

#### Configurar servidor

| IP Address     | Subnet Mask     | Default Gateway | DNS Server |
| -------------- | --------------- | --------------- | ---------- |
| 40.0.0.10      | 255.255.255.0   | 40.0.0.1        | 40.0.0.1   |

#### Actulizar MLS11 para utilizar como default gateway

```bash
MLS0(config)#int g1/0/1
MLS0(config-if)#no switchport
MLS0(config-if)#ip address 40.0.0.1 255.255.255.0
MLS0(config-if)#no shutdown
```

#### Cargar página de prueba

![Página web](./screenshots/11_2.jpg)




### Configuracion HSRP de la red

#### Lado izquierdo

Para esta parte de la red de la topologia se va escoger que Switch se va  escoger que sea de mayor prioridad o menor prioridad, ya que es como un balanceador, un switch que va estar activo y otro que va estar en espera o pasivo, se escoge el switch MLS4 como el switch activo y el MLS5 como el pasivo

```bash
MLS4(config)#interface vlan 10
MLS4(config-if)#ip address 192.168.23.2 255.255.255.192
MLS4(config-if)#standby 1 ip 192.168.23.1
MLS4(config-if)#standby 1 priority 110   
MLS4(config-if)#standby 1 preempt
MLS4(config-if)#ip helper-address 15.0.0.1
MLS4(config-if)#exit
MLS4(config)#
MLS4(config)#
MLS4(config)#interface vlan 20
MLS4(config-if)#ip address 192.168.23.66 255.255.255.192
MLS4(config-if)#standby 1 ip 192.168.23.65
MLS4(config-if)#standby 1 priority 110   
MLS4(config-if)#standby 1 preempt
MLS4(config-if)#ip helper-address 15.0.0.1
MLS4(config-if)#exit
```
Aqui se activa por medio de las VLAN 10 y 20, con las direcciones IP que se tienen configuradas y se da una prioridad de 110 para tener este switch como activo
![HRSP_MLS4](./screenshots/HRSP_MLS4.jpg)

```bash
MLS5(config)#interface vlan 10
MLS5(config-if)#ip address 192.168.23.3 255.255.255.192
MLS5(config-if)#standby 1 ip 192.168.23.1
MLS5(config-if)#standby 1 priority 90 
MLS5(config-if)#standby 1 preempt
MLS5(config-if)#ip helper-address 15.0.0.1
MLS5(config-if)#exit
MLS5(config)#
MLS5(config)#interface vlan 20
MLS5(config-if)#ip address 192.168.23.67 255.255.255.192
MLS5(config-if)#standby 1 ip 192.168.23.65
MLS5(config-if)#standby 1 priority 90 
MLS5(config-if)#standby 1 preempt
MLS5(config-if)#ip helper-address 15.0.0.1
MLS5(config-if)#exit
```
Aqui el switch ha sido configurado con las mismas ip y vlans, pero con la  prioridad mas baja para poner el switch como pasivo o en espera

![HRSP_MLS5](./screenshots/HRSP_MLS5.jpg)

Finalmente debemos crear las interfaces virtuales SVI en el Switch MSL6 y asignarles direcciones IP para que los dispositivos en esas VLAN puedan utilizarla como gateway.

```bash
MLS6(config)#int vlan10
MLS6(config-if)#ip address 192.168.23.4 255.255.255.192
MLS6(config-if)#int vlan20
MLS6(config-if)#ip address 192.168.23.68 255.255.255.192
```
