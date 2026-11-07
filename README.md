
Asignatura: Seguridad de Redes
Estudiante: Cóndor Bautista
Matrícula: 2025-0791

6 de noviembre de 2026

1. Propósito del Laboratorio
Diseñar, implementar y documentar una topología de red segura utilizando GNS3, integrando un Router Cisco, un Switch administrable, un servidor Zabbix (para monitorización mediante SNMP) y servicios de asignación dinámica DHCP, bajo un esquema estricto de direccionamiento IP adaptado a las direcciones específicas solicitadas (10.7.91.224 para el Router y 10.7.91.225 para el servidor Zabbix).

2. Plan de Direccionamiento IP y Segmentación
Subred Base: 10.7.91.128 / 25 (Rango útil: 10.7.91.129 a 10.7.91.254)

Router Cisco (Gateway): 10.7.91.224

Servidor Zabbix: 10.7.91.225

Switch Administrable (Gestión): 10.7.91.226

Pool DHCP LAN: 10.7.91.227 – 10.7.91.254

3. Topología de Red (Diseño en GNS3)
   
[ Cloud1 ] <---> [ Ethernet Switch ] <---> [ Router Cisco (g0/0: 10.7.91.224) ]
                                    <---> [ Servidor Zabbix (10.7.91.225) ]

<img width="409" height="419" alt="imagen_2026-11-06_212544541" src="https://github.com/user-attachments/assets/358f138a-5b4a-4659-8151-a866b07c1548" />


<img width="1263" height="790" alt="imagen_2026-11-06_213000979" src="https://github.com/user-attachments/assets/0ccb37f5-0c99-4648-b04f-144e108a55d2" />

<img width="642" height="121" alt="imagen_2026-11-06_213051622" src="https://github.com/user-attachments/assets/6441ce8e-49f5-4725-a68b-5e16b86c04f4" />

4. Guía de Configuración Paso a Paso
Paso 1: Configuración de Direccionamiento y DHCP en el Router Cisco
Accede a la consola del Router Cisco y aplica la siguiente configuración:

enable
configure terminal

hostname Router-HQ
ip domain-name red.local

interface gigabitethernet 0/0
 description Enlace LAN hacia Zabbix y Switch
 ip address 10.7.91.224 255.255.255.128
 no shutdown
exit

ip dhcp excluded-address 10.7.91.128 10.7.91.226
ip dhcp pool RED-LAN-POOL
 network 10.7.91.128 255.255.255.128
 default-router 10.7.91.224
 dns-server 8.8.8.8 1.1.1.1
exit

snmp-server community RedSecureRO RO
snmp-server contact Administrador - Matrícula 2025-0791
snmp-server location Laboratorio GNS3

end
write memory


Paso 2: Configuración del Switch Administrable
Configura la interfaz de gestión en el switch de la topología:

enable
configure terminal

hostname Switch-HQ

interface vlan 1
 ip address 10.7.91.226 255.255.255.128
 no shutdown
exit

ip default-gateway 10.7.91.224

snmp-server community RedSecureRO RO

end
write memory

Paso 3: Configuración de la IP Estática en el Servidor Zabbix
Accede a tu servidor Ubuntu y edita el archivo de Netplan:

sudo nano /etc/netplan/00-installer-config.yaml
Modifica la configuración respetando estrictamente la indentación por espacios:

network:
  version: 2
  ethernets:
    eth0:
      addresses:
        - 10.7.91.225/25
      routes:
        - to: default
          via: 10.7.91.224
      nameservers:
        addresses: [8.8.8.8, 1.1.1.1]
Aplica los cambios ejecutando:

Bash
sudo netplan apply
Verification: Ejecuta ip a para confirmar que el servidor Zabbix posee configurada la dirección IP 10.7.91.225.


VIDEO: 
