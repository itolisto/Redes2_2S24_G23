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
1. [Crear VLANs en Switches](#crear-vlans-en-switches)

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
