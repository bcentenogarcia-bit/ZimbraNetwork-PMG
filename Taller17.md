Instalación de Zimbra en Entorno MultiServidor
1.- Esquema de despliegue
Una de las funcionalidades de zimbra es que puede instalarse en modo multiservidor con lo cual nos permite el balanceo de carga y despliegue de la solución para una gran cantidad de usuarios. En el presente capítulo configuraremos Zimbra en un entorno multiservidor bajo el siguiente esquema:

                ----------------------
               | MTA,LDAP,DNS, Proxy  |  smtp.aulautil.tk
                ----------------------
                      |        | 
                      |        |
                      |        |
                  --------   -------
                 |Mailbox | |Mailbox|  
                 |Store   | |Store  |
                 |Webmail | |Webmail| 
                 |Logger  | |       | 
                  --------   -------
  mailbox1.aulautil.tk  mailbox2.aulautil.tk

2.- Instalación de los sistemas operativos del Cluster
2.1.- Instalación de los servidores CentOS
Descargar el CentOS 7 ISO Minimal https://www.centos.org/download

2.1.1.- Instalación del servidor smtp
Iniciar la instalación de CentOS 7 con el iso del minimal
    Seleccionar Install CentOS
    Welcome to CentOS
        Spanish     ---> Español (País donde te encuentres)
    Continuar

    Resumen de la Instalación

    KDUMP
            Quitar el check "Habilitar Kdump"
        Listo

    DESTINO DE LA INSTALACIÓN
            Otras Opciones de Almacenamiento
                Check en "Voy a configurar las particiones"
            Discos estandares locales
                doble click en el disco (fondo azul y en check)
            Listo

            Crear Nuevos puntos de Montaje
                Combo: Partición estándar

            Click en el ícono + abajo a la izquierda
                Punto de Montaje:   /
                Capacidad deseada:  30G
            Añadir punto de montaje

            Click en el ícono + abajo a la izquierda
                Punto de Montaje:   swap
                Capacidad deseada:  4G
            Añadir punto de montaje

            Click en el ícono + abajo a la izquierda
                Punto de Montaje:   /opt
                Capacidad deseada:  Dejar vacio  (ocupa el espacio restante)
            Añadir punto de montaje

            Seleccionar /opt
                Tipo de Dispositivo:
                    LVM 
                Sistema de archivos:
                    xfs
        Listo

    RED NOMBRE DE EQUIPO
        Nombre de Host:  smtp.aulautil.tk
        Click boton de abajo a la derecha "Configurar"
            Ir a a la Pestaña **Ajustes IPv4**
                Método: Manual
                    Click en **Añadir**
                        Dirección:      192.168.X.4
                        Mascara de Red:     24
                        Puerta de Enlace:   192.168.X.1
                        Servidores DNS:     8.8.8.8 
                    Guardar

            Dar click en el botón Ethernet (que se ponga azul)
        Listo

    Empezar Instalación

    Contraseña de root
        Que aparezca como robusta
        Listo   

Una vez que terminó click en Reiniciar
2.1.2.- Instalación del servidor mailbox1
Iniciar la instalación de CentOS 7 con el iso del minimal
    Seleccionar Install CentOS
    Welcome to CentOS
        Spanish     ---> Español (País donde te encuentres)
    Continuar

    Resumen de la Instalación

    KDUMP
            Quitar el check "Habilitar Kdump"
        Listo

    DESTINO DE LA INSTALACIÓN
            Otras Opciones de Almacenamiento
                Check en "Voy a configurar las particiones"
            Discos estandares locales
                doble click en el disco (fondo azul y en check)
            Listo

            Crear Nuevos puntos de Montaje
                Combo: Partición estándar

            Click en el ícono + abajo a la izquierda
                Punto de Montaje:   /
                Capacidad deseada:  30G
            Añadir punto de montaje

            Click en el ícono + abajo a la izquierda
                Punto de Montaje:   swap
                Capacidad deseada:  4G
            Añadir punto de montaje

            Click en el ícono + abajo a la izquierda
                Punto de Montaje:   /opt
                Capacidad deseada:  Dejar vacio  (ocupa el espacio restante)
            Añadir punto de montaje

            Seleccionar /opt
                Tipo de Dispositivo:
                    LVM 
                Sistema de archivos:
                    xfs
        Listo

    RED NOMBRE DE EQUIPO
        Nombre de Host:  mailbox1.aulautil.tk
        Click boton de abajo a la derecha "Configurar"
            Ir a a la Pestaña **Ajustes IPv4**
                Método: Manual
                    Click en **Añadir**
                        Dirección:      192.168.X.5
                        Mascara de Red:     24
                        Puerta de Enlace:   192.168.X.1
                        Servidores DNS:     8.8.8.8 
                    Guardar

            Dar click en el botón Ethernet (que se ponga azul)
        Listo

    Empezar Instalación

    Contraseña de root
        Que aparezca como robusta
        Listo   

Una vez que terminó click en Reiniciar
2.1.3.- Instalación del servidor mailbox2
Iniciar la instalación de CentOS 7 con el iso del minimal
    Seleccionar Install CentOS
    Welcome to CentOS
        Spanish     ---> Español (País donde te encuentres)
    Continuar

    Resumen de la Instalación

    KDUMP
            Quitar el check "Habilitar Kdump"
        Listo

    DESTINO DE LA INSTALACIÓN
            Otras Opciones de Almacenamiento
                Check en "Voy a configurar las particiones"
            Discos estandares locales
                doble click en el disco (fondo azul y en check)
            Listo

            Crear Nuevos puntos de Montaje
                Combo: Partición estándar

            Click en el ícono + abajo a la izquierda
                Punto de Montaje:   /
                Capacidad deseada:  30G
            Añadir punto de montaje

            Click en el ícono + abajo a la izquierda
                Punto de Montaje:   swap
                Capacidad deseada:  4G
            Añadir punto de montaje

            Click en el ícono + abajo a la izquierda
                Punto de Montaje:   /opt
                Capacidad deseada:  Dejar vacio  (ocupa el espacio restante)
            Añadir punto de montaje

            Seleccionar /opt
                Tipo de Dispositivo:
                    LVM 
                Sistema de archivos:
                    xfs
        Listo

    RED NOMBRE DE EQUIPO
        Nombre de Host:  mailbox2.aulautil.tk
        Click boton de abajo a la derecha "Configurar"
            Ir a a la Pestaña **Ajustes IPv4**
                Método: Manual
                    Click en **Añadir**
                        Dirección:      192.168.X.6
                        Mascara de Red:     24
                        Puerta de Enlace:   192.168.X.1
                        Servidores DNS:     8.8.8.8 
                    Guardar

            Dar click en el botón Ethernet (que se ponga azul)
        Listo

    Empezar Instalación

    Contraseña de root
        Que aparezca como robusta
        Listo   

Una vez que terminó click en Reiniciar
2.2.- Deshabilitar temporalmente el firewall en en los nodos
systemctl stop firewalld
systemctl disable firewalld
3.- Configuración de las IP públicas y NAT en sophos XG
3.1.- Creación de Alias de IP en la tarjeta WAN
Acceder al panel de Sophos y agregar alias de IP públicas

Ir a Configurar --> RED
  Derecha botón "Añadir Interfaz" --> Añadir alias
  Intefaz física: Port2
    Versión IPv4
    IPv4/Netmask:  51.68.161.Y / 25

  Derecha botón "Añadir Interfaz" --> Añadir alias
  Intefaz física: Port2
    Versión IPv4
    IPv4/Netmask:  51.68.161.A / 25

  Derecha botón "Añadir Interfaz" --> Añadir alias
  Intefaz física: Port2
    Versión IPv4
    IPv4/Netmask:  51.68.161.B / 25
3.2.- Configuración de SNAT y DNAT de puertos/servicios
3.2.1- Regla de Salida para Zimbra Cluster Smtp
PROTEGER --> Reglas y Políticas
    Reglas de Firewall 
        Click en "Añadir regla de Firewall"
            Nueva Regla de Firewall
                Nombre de regla:                Salida Cluster Smtp
                Posición de la regla:           Arriba
                Grupo:                          
                        Crear Nuevo
                            Zimbra Cluster
                Acción:                         Aceptar
                Registrar tráfico de firewall:  CHECK

                ORIGEN
                    Zonas de origen:                DMZ
                    Dispositivos y Redes de Origen:
                        Añadir Nuevo Elemento
                                Crear Nuevo: IP
                                        Nombre: 192.168.X.4_Cluster_Smtp
                                        Direccion IP: 192.168.X.4
                                Guardar
                    Durante la hora programada:     Siempre

                DESTINO y Servicios
                    Zona de Destino:                WAN
                    Redes de Destino:               Cualquiera
                    Servicios:                      Cualquiera

                Crear Regla NAT Vinculada:          Dar Click
                    Nombre de la Regla:             Salida Cluster Smtp
                    Posición de la Regla:           Arriba

                    Configuración de traducción
                        Origen Traducido (SNAT):
                            Añadir Nuevo Elemento
                                Crear Nuevo: IP
                                        Nombre: 51.68.161.Y
                                        Direccion IP: 51.68.161.Y
                            Guardar

            Guardar
3.2.2.- Regla de NAT para Zimbra Cluster Smtp
Conectarse y loguearse al firewall Sophos XG

PROTEGER --> Reglas y Políticas
    Reglas de Firewall 
        Click en "Añadir regla de Firewall"
             Nueva Regla de Firewall
                Nombre de regla:                Nat Cluster Smtp
                Descripción:                    Nat Cluster Smtp, ldap, proxy
                Posición de la regla:           Arriba
                Grupo de Reglas:                Zimbra Cluster
                Acción:                         Aceptar
                Registrar tráfico de firewall:  CHECK

                ORIGEN
                    Zonas de origen:                WAN
                    Dipositivos y redes de origen:  Cualquiera
                    Durante la hora programada:     Siempre

                DESTINO y Servicios
                    Zona de Destino:                DMZ
                    Redes de Destino:               51.68.161.Y
                    Servicios:                      SMTP, SMTPS_465, SMTP_TLS, HTTP, HTTPS, ZIMBRA_ADMIN, SSH, IMAP, IMAPS, POP3, POP3S

            Guardar

    Reglas de NAT 
        Click en "Añadir regla de NAT"
            Nueva Regla NAT
                Nombre de regla:                Nat Cluster Smtp
                Descripción:                    Nat Cluster Smtp, ldap, proxy
                Posición de la regla:           Arriba

            Configuración de traducción
                Origen original:                Cualquiera
                Destino original:               51.68.161.Y
                Servicio original:              SMTP, SMTPS_465, SMTP_TLS, HTTP, HTTPS, ZIMBRA_ADMIN, SSH, IMAP, IMAPS, POP3, POP3S

            Destino Traducido (DNAT):           192.168.X.4_Cluster_Smtp

            Criterios de coincidencia de interfaz
                Interfaz de entrada:            Port2
                Interfaz de salida:             Cualquiera                
        Guardar
Loguearse por SSH y actualizar los paquetes

yum -y update
yum -y install vim wget
reboot
3.2.3- Regla de Salida para Zimbra Cluster Mailbox1
PROTEGER --> Reglas y Políticas
    Reglas de Firewall 
        Click en "Añadir regla de Firewall"
            Nueva Regla de Firewall
                Nombre de regla:                Salida Cluster Mailbox1
                Posición de la regla:           Arriba
                Grupo:                          Zimbra Cluster
                Acción:                         Aceptar
                Registrar tráfico de firewall:  CHECK

                ORIGEN
                    Zonas de origen:                DMZ
                    Dispositivos y Redes de Origen:
                        Añadir Nuevo Elemento
                                Crear Nuevo: IP
                                        Nombre: 192.168.X.5_Cluster_Mailbox1
                                        Direccion IP: 192.168.X.5
                                Guardar
                    Durante la hora programada:     Siempre

                DESTINO y Servicios
                    Zona de Destino:                WAN
                    Redes de Destino:               Cualquiera
                    Servicios:                      Cualquiera

                Crear Regla NAT Vinculada:          Dar Click
                    Nombre de la Regla:             Salida Cluster Mailbox1
                    Posición de la Regla:           Arriba

                    Configuración de traducción
                        Origen Traducido (SNAT):
                            Añadir Nuevo Elemento
                                Crear Nuevo: IP
                                        Nombre: 51.68.161.A
                                        Direccion IP: 51.68.161.A
                            Guardar

            Guardar
3.2.4.- Regla de NAT para Zimbra Cluster Mailbox1
Conectarse y loguearse al firewall Sophos XG

PROTEGER --> Reglas y Políticas
    Reglas de Firewall 
        Click en "Añadir regla de Firewall"
             Nueva Regla de Firewall
                Nombre de regla:                Nat Cluster Mailbox1
                Descripción:                    Nat Cluster Mailbox1, webmail y admin
                Posición de la regla:           Arriba
                Grupo de Reglas:                Zimbra Cluster
                Acción:                         Aceptar
                Registrar tráfico de firewall:  CHECK

                ORIGEN
                    Zonas de origen:                WAN
                    Dipositivos y redes de origen:  Cualquiera
                    Durante la hora programada:     Siempre

                DESTINO y Servicios
                    Zona de Destino:                DMZ
                    Redes de Destino:               51.68.161.A
                    Servicios:                      HTTP, HTTPS, ZIMBRA_ADMIN, ZIMBRA_WEBMAIL, SSH, IMAP, IMAPS, POP3, POP3S

            Guardar

    Reglas de NAT 
        Click en "Añadir regla de NAT"
            Nueva Regla NAT
                Nombre de regla:                Nat Cluster Mailbox1
                Descripción:                    Nat Cluster Mailbox1, webmail y admin
                Posición de la regla:           Arriba

            Configuración de traducción
                Origen original:                Cualquiera
                Destino original:               51.68.161.A
                Servicio original:              HTTP, HTTPS, ZIMBRA_ADMIN, ZIMBRA_WEBMAIL, SSH, IMAP, IMAPS, POP3, POP3S

            Destino Traducido (DNAT):           192.168.X.5_Cluster_Mailbox1

            Criterios de coincidencia de interfaz
                Interfaz de entrada:            Port2
                Interfaz de salida:             Cualquiera                
        Guardar
Conectarse por SSH y actualizar los paquetes

yum -y update
yum -y install vim wget
reboot
3.2.5- Regla de Salida para Zimbra Cluster Mailbox2
PROTEGER --> Reglas y Políticas
    Reglas de Firewall 
        Click en "Añadir regla de Firewall"
            Nueva Regla de Firewall
                Nombre de regla:                Salida Cluster Mailbox2
                Posición de la regla:           Arriba
                Grupo:                          Zimbra Cluster
                Acción:                         Aceptar
                Registrar tráfico de firewall:  CHECK

                ORIGEN
                    Zonas de origen:                DMZ
                    Dispositivos y Redes de Origen:
                        Añadir Nuevo Elemento
                                Crear Nuevo: IP
                                        Nombre: 192.168.X.6_Cluster_Mailbox2
                                        Direccion IP: 192.168.X.6
                                Guardar
                    Durante la hora programada:     Siempre

                DESTINO y Servicios
                    Zona de Destino:                WAN
                    Redes de Destino:               Cualquiera
                    Servicios:                      Cualquiera

                Crear Regla NAT Vinculada:          Dar Click
                    Nombre de la Regla:             Salida Cluster Mailbox2
                    Posición de la Regla:           Arriba

                    Configuración de traducción
                        Origen Traducido (SNAT):
                            Añadir Nuevo Elemento
                                Crear Nuevo: IP
                                        Nombre: 51.68.161.B
                                        Direccion IP: 51.68.161.B
                            Guardar

            Guardar
3.2.6.- Regla de NAT para Zimbra Cluster Mailbox2
Conectarse y loguearse al firewall Sophos XG

PROTEGER --> Reglas y Políticas
    Reglas de Firewall 
        Click en "Añadir regla de Firewall"
             Nueva Regla de Firewall
                Nombre de regla:                Nat Cluster Mailbox2
                Descripción:                    Nat Cluster Mailbox2, webmail y admin
                Posición de la regla:           Arriba
                Grupo de Reglas:                Zimbra Cluster
                Acción:                         Aceptar
                Registrar tráfico de firewall:  CHECK

                ORIGEN
                    Zonas de origen:                WAN
                    Dipositivos y redes de origen:  Cualquiera
                    Durante la hora programada:     Siempre

                DESTINO y Servicios
                    Zona de Destino:                DMZ
                    Redes de Destino:               51.68.161.B
                    Servicios:                      HTTP, HTTPS, ZIMBRA_ADMIN, ZIMBRA_WEBMAIL, SSH, IMAP, IMAPS, POP3, POP3S

            Guardar

    Reglas de NAT 
        Click en "Añadir regla de NAT"
            Nueva Regla NAT
                Nombre de regla:                Nat Cluster Mailbox2
                Descripción:                    Nat Cluster Mailbox2, webmail y admin
                Posición de la regla:           Arriba

            Configuración de traducción
                Origen original:                Cualquiera
                Destino original:               51.68.161.B
                Servicio original:              HTTP, HTTPS, ZIMBRA_ADMIN, ZIMBRA_WEBMAIL, SSH, IMAP, IMAPS, POP3, POP3S

            Destino Traducido (DNAT):           192.168.X.6_Cluster_Mailbox2

            Criterios de coincidencia de interfaz
                Interfaz de entrada:            Port2
                Interfaz de salida:             Cualquiera                
        Guardar
Conectarse por SSH y actualizar los paquetes

yum -y update
yum -y install vim wget
reboot
4.- Configuración de los registros DNS en Freenom/Godaddy
Como son tres servidores vamos a requerir 3 IP públicas y debemos crear los siguientes registros

4.1.- Registros A
smtp.aulautil.tk ----> 51.68.161.Y
mailbox1.aulautil.tk ----> 51.68.161.A
mailbox2.aulautil.tk ----> 51.68.161.B
4.2.- Registro MX
alalutil.tk  MX    10 smtp.aulautil.tk 
4.3.- Registro SPF
alalutil.tk  TXT    "v=spf1 a mx -all" 
5.- Instalación de DNS Server y configuración de los nombres de Host y resolv
5.1.- Instalación de Servidor DNS Dnsmasq en el host smtp
Nos conectamos al servidor smtp y ejecutamos

yum -y install dnsmasq vim bind-utils
Editamos el /etc/hosts para declarar los nombres de servidor

vim /etc/hosts
127.0.0.1 localhost.localdomain localhost
192.168.X.4 smtp.aulautil.tk smtp
192.168.X.5 mailbox1.aulautil.tk mailbox1
192.168.X.6 mailbox2.aulautil.tk mailbox2
vim /etc/dnsmasq.d/aulautil.tk.conf
mx-host=aulautil.tk,smtp.aulautil.tk,10
vim /etc/dnsmasq.d/server.conf
port=53
server=8.8.8.8
bind-interfaces
systemctl restart dnsmasq
systemctl enable dnsmasq
5.2.- Configurar el hostname y resolv en los tres equipos
5.2.1.- Configuración de hostname y resolv en el nodo smtp
Cambiar nombre del host

hostnamectl set-hostname smtp.aulautil.tk
su -
Ver el nombre de la tarjeta ej: eth0

ip l
Cambiar el dns en la tarjeta con la IP del servidor SMTP

sed -i  's/8.8.8.8/192.168.X.4/g' /etc/sysconfig/network-scripts/ifcfg-eth0
Reiniciar la tarjeta de red

systemctl restart network
Comprobar los nombres de DNS Nos conectamos al servidor smtp y ejecutamos

dig MX aulautil.tk
dig smtp.aulautil.tk
dig mailbox1.aulautil.tk
dig mailbox2.aulautil.tk
5.2.2.- Configuración de hostname y resolv en el nodo mailbox1
Instalar vim y bind-utils

yum -y install vim bind-utils
Cambiar nombre del host

hostnamectl set-hostname mailbox1.aulautil.tk
su -
Editamos el /etc/hosts para declarar los nombres de servidor

vim /etc/hosts
127.0.0.1 localhost.localdomain localhost
192.168.X.4 smtp.aulautil.tk smtp
192.168.X.5 mailbox1.aulautil.tk mailbox1
192.168.X.6 mailbox2.aulautil.tk mailbox2
Ver el nombre de la tarjeta ej: eth0

ip l
Cambiar el dns en la tarjeta con la IP del servidor SMTP

sed -i  's/8.8.8.8/192.168.X.4/g' /etc/sysconfig/network-scripts/ifcfg-eth0
Reiniciar la tarjeta de red

systemctl restart network
Comprobar los nombres de DNS Nos conectamos al servidor smtp y ejecutamos

dig MX aulautil.tk
dig smtp.aulautil.tk
dig mailbox1.aulautil.tk
dig mailbox2.aulautil.tk
5.2.3.- Configuración de hostname y resolv en el nodo mailbox2
Instalar vim y bind-utils

yum -y install vim bind-utils
Cambiar nombre del host

hostnamectl set-hostname mailbox2.aulautil.tk
su -
Editamos el /etc/hosts para declarar los nombres de servidor

vim /etc/hosts
127.0.0.1 localhost.localdomain localhost
192.168.X.4 smtp.aulautil.tk smtp
192.168.X.5 mailbox1.aulautil.tk mailbox1
192.168.X.6 mailbox2.aulautil.tk mailbox2
Ver el nombre de la tarjeta ej: eth0

ip l
Cambiar el dns en la tarjeta con la IP del servidor SMTP

sed -i  's/8.8.8.8/192.168.X.4/g' /etc/sysconfig/network-scripts/ifcfg-eth0
Reiniciar la tarjeta de red

systemctl restart network
Comprobar los nombres de DNS Nos conectamos al servidor smtp y ejecutamos

dig MX aulautil.tk
dig smtp.aulautil.tk
dig mailbox1.aulautil.tk
dig mailbox2.aulautil.tk
6.- Instalación del primer servidor Zimbra smtp.alautil.tk (smtp, ldap, proxy, memcached)
instalar dependencias

yum install -y perl perl-core ntpl nmap sudo libidn gmp libaio libstdc++ unzip sysstat sqlite
Deshabilitar postfix

systemctl stop postfix 
systemctl disable postfix
Instalar utilidades

yum -y install vim wget elinks telnet net-tools
Descargar Zimbra Open Source Edition

wget https://files.zimbra.com/downloads/8.8.15_GA/zcs-8.8.15_GA_3869.RHEL7_64.20190918004220.tgz
tar -zxvf zcs-8.8.15_GA_3869.RHEL7_64.20190918004220.tgz
cd zcs-8.8.15_GA_3869.RHEL7_64.20190918004220
Instalación de Zimbra en smtp.aulautil.tk

./install.sh
Do you agree with the terms of the software license agreement? [N] Y

Use Zimbra's package repository [Y]

Select the packages to install

Install zimbra-ldap [Y] Y

Install zimbra-logger [N] N 

Install zimbra-mta [Y] Y

Install zimbra-dnscache [Y] N

Install zimbra-snmp [Y] Y

Install zimbra-store [Y] N

Install zimbra-apache [Y] N

Install zimbra-spell [Y] N

Install zimbra-memcached [N] Y

Install zimbra-proxy [N] Y

The system will be modified.  Continue? [N] Y

Change domain name? [Yes] Yes
Create domain: [smtp.aulautil.tk] aulautil.tk

************ Menu 1 *************************
Select from menu, or press 'a' to apply config (? - help) 1
Select, or 'r' for previous menu [r] 7
Enter the number for the local timezone: [23] 17
Select, or 'r' for previous menu [r] 4
Password for ldap admin user (min 6 characters): [MxRacDZv5_] Tuxito1.23
Select, or 'r' for previous menu [r] r

************ Menu 2 *************************
Address unconfigured (**) items  (? - help) 2
Select, or 'r' for previous menu [r] 4
Password for ldap root user (min 6 characters): [MxRacDZv5_] Tuxito1.23
Select, or 'r' for previous menu [r] 5
Password for ldap replication user (min 6 characters):[MxRacDZv5_] Tuxito1.23
Select, or 'r' for previous menu [r] 6
Password for ldap Postfix user (min 6 characters): [MxRacDZv5_] Tuxito1.23
Select, or 'r' for previous menu [r] 7
Password for ldap Amavis user (min 6 characters): [MxRacDZv5_] Tuxito1.23
Select, or 'r' for previous menu [r] 8
Password for ldap Nginx user (min 6 characters): [MxRacDZv5_] Tuxito1.23
Select, or 'r' for previous menu [r] 9
Password for ldap BES user (min 6 characters): [MxRacDZv5_] Tuxito1.23
Select, or 'r' for previous menu [r] r

*** CONFIGURATION COMPLETE - press 'a' to apply
Select from menu, or press 'a' to apply config (? - help)a

Save configuration data to a file? [Yes] Yes
Save config in file: [/opt/zimbra/config.25891] ENTER
The system will be modified - continue? [No] Yes

Nos sale estos mensajes, por el proxy que no encuentra mailstore,
lo solucionaremos después

Notify Zimbra of your installation? [Yes]No
Configuration complete - press return to exit
Si falla al instalar, se puede reinstalar con
yum erase zimbra-openjdk
cd zcs-8.8.15_GA_3869.RHEL7_64.20190918004220
./install.sh -u
rm -fR /opt/zimbra
./install.sh
7.- Instalación del segundo servidor: mailbox1.aulautil.tk (logger, mailbox, java, spell)
instalar dependencias

yum install -y perl perl-core ntpl nmap sudo libidn gmp libaio libstdc++ unzip sysstat sqlite
Deshabilitar postfix

systemctl stop postfix 
systemctl disable postfix
Instalar utilidades

yum -y install vim wget elinks telnet net-tools
Descargar Zimbra Open Source Edition

wget https://files.zimbra.com/downloads/8.8.15_GA/zcs-8.8.15_GA_3869.RHEL7_64.20190918004220.tgz
tar -zxvf zcs-8.8.15_GA_3869.RHEL7_64.20190918004220.tgz
cd zcs-8.8.15_GA_3869.RHEL7_64.20190918004220
Instalación de Zimbra en mailbox1.aulautil.tk

./install.sh
Do you agree with the terms of the software license agreement? [N] Y

Use Zimbra's package repository [Y] Y

Select the packages to install

Install zimbra-ldap [Y] N

Install zimbra-logger [Y] Y 

Install zimbra-mta [Y] N

Install zimbra-dnscache [N] N

Install zimbra-snmp [Y] Y

Install zimbra-store [Y] Y

Install zimbra-apache [Y] Y

Install zimbra-spell [Y] Y

Install zimbra-memcached [N] N 

Install zimbra-proxy [N] N

Install zimbra-drive [Y] N

Install zimbra-imapd (BETA - for evaluation only) [N] N

Install zimbra-chat [Y] Y

The system will be modified.  Continue? [N] Y

************ Menu 1 *************************
Address unconfigured (**) items  (? - help) 1
Select, or 'r' for previous menu [r] 2
Please enter the ldap server hostname: smtp.aulautil.tk
Select, or 'r' for previous menu [r] 4
Password for ldap admin user (min 6 characters): Tuxito1.23
Select, or 'r' for previous menu [r] r

************ Menu 4 *************************
Address unconfigured (**) items  (? - help) 4
Select, or 'r' for previous menu [r] 4
Password for admin@aulautil.tk (min 6 characters): [W0oVBhou] Tuxito1.23
Select, or 'r' for previous menu [r] 9
Please enter the SMTP server hostname: smtp.aulautil.tk
Select, or 'r' for previous menu [r] r

CONFIGURATION COMPLETE - press 'a' to apply
Select from menu, or press 'a' to apply config (? - help) a
Save configuration data to a file? [Yes] Yes
Save config in file: [/opt/zimbra/config.32536]  ENTER
Saving config in /opt/zimbra/config.32536...done.
The system will be modified - continue? [No] Yes 
Actualizamos los keys del ssh en el mailbox1 para obtener los logs del smtp

Ejecutar esto en el servidor mailbox1

su - zimbra
zmupdateauthkeys
ahora como root editar en el servidor mailbox1

vim /etc/sysconfig/rsyslog
SYSLOGD_options="-r -m 0"
systemctl restart rsyslog 
Hacemos que el SMTP loguee al mailbox1, Ejecutar esto en el servidor smtp

su - zimbra
zmupdateauthkeys
ahora como root ejecutar en el servidor smtp

/opt/zimbra/libexec/zmsyslogsetup
systemctl restart rsyslog
8.- Instalación del tercer servidor: mailbox2.aulautil.tk (mailbox, java, spell)
instalar dependencias

yum install -y perl perl-core ntpl nmap sudo libidn gmp libaio libstdc++ unzip sysstat sqlite
Deshabilitar postfix

systemctl stop postfix 
systemctl disable postfix
Instalar utilidades

yum -y install vim wget elinks telnet net-tools
Descargar Zimbra Open Source Edition

wget https://files.zimbra.com/downloads/8.8.15_GA/zcs-8.8.15_GA_3869.RHEL7_64.20190918004220.tgz
tar -zxvf zcs-8.8.15_GA_3869.RHEL7_64.20190918004220.tgz
cd zcs-8.8.15_GA_3869.RHEL7_64.20190918004220
Instalación de Zimbra en mailbox2.aulautil.tk

./install.sh
Do you agree with the terms of the software license agreement? [N] Y

Use Zimbra's package repository [Y] Y

Select the packages to install

Install zimbra-ldap [Y] N

Install zimbra-logger [Y] N

Install zimbra-mta [Y] N

Install zimbra-dnscache [N] N

Install zimbra-snmp [Y] Y

Install zimbra-store [Y] Y

Install zimbra-apache [Y] Y

Install zimbra-spell [Y] Y

Install zimbra-memcached [N] N 

Install zimbra-proxy [N] N

Install zimbra-drive [Y] N

Install zimbra-imapd (BETA - for evaluation only) [N] N

Install zimbra-chat [Y] Y

The system will be modified.  Continue? [N] Y

*************** Menu 1 *************************
Address unconfigured (**) items  (? - help) 1
Select, or 'r' for previous menu [r] 2
Please enter the ldap server hostname: smtp.aulautil.tk
Select, or 'r' for previous menu [r] 4
Password for ldap admin user (min 6 characters): Tuxito1.23
Select, or 'r' for previous menu [r] r

*************** Menu 3 *************************
Address unconfigured (**) items  (? - help) 3
Select, or 'r' for previous menu [r] 4
Password for admin@aulautil.tk (min 6 characters): [W0oVBhou] Tuxito1.23
Select, or 'r' for previous menu [r] 6 
Please enter the SMTP server hostname: smtp.aulautil.tk
Select, or 'r' for previous menu [r] r

*** CONFIGURATION COMPLETE - press 'a' to apply
Select from menu, or press 'a' to apply config (? - help) a
Save configuration data to a file? [Yes] Yes
Save config in file: [/opt/zimbra/config.32536] 
Saving config in /opt/zimbra/config.32536...done.
The system will be modified - continue? [No] Yes 
Actualizamos los keys, ejecutar en el servidor mailbox2

su - zimbra
zmupdateauthkeys
como root ejecutar en el servidor mailbox2

/opt/zimbra/libexec/zmsyslogsetup
systemctl restart rsyslog
Acceder al webmail y a los paneles de administración por mailbox1 y mailbox2 para verificar que se ven los 3 servidores
https://smtp.aulautil.tk
https://mailbox1.aulautil.tk:7071
https://mailbox2.aulautil.tk:7071

9.- Generación de certificados digitales Letsencript para los servidores del cluster
9.1.- Certificados Letsencrypt del servidor smtp
Nos conectamos al servidor smtp

Instalar epel

yum -y install epel-release
Instalar Letsencrypt y ca-certificates en CentOS 7

yum -y install certbot python-certbot-apache ca-certificates
Nota, si fuera Ubuntu instalar con

apt -y update
apt -y install certbot ca-certificates python3-certbot-apache
update-ca-certificates
Actualizar los CA de CentOS

update-ca-trust force-enable
Detener apache y zimbra proxy

systemctl stop httpd
su - zimbra -c "zmproxyctl stop"
Generar certificado para el dominio

certbot -d smtp.aulautil.tk -m usuario@tucorreoactivo.com --force-renewal --preferred-chain "ISRG Root X1" certonly
- Seleccionar opción 2 "Spin up a temporary webserver (standalone)": 2
Please read the Terms ...
(A)Agree/(C)Cancel: A
(Y)es/(N)o: Y
Modificar el certificado generado para agregar el CA de Letsencrypt

echo "-----BEGIN CERTIFICATE-----
MIIFazCCA1OgAwIBAgIRAIIQz7DSQONZRGPgu2OCiwAwDQYJKoZIhvcNAQELBQAw
TzELMAkGA1UEBhMCVVMxKTAnBgNVBAoTIEludGVybmV0IFNlY3VyaXR5IFJlc2Vh
cmNoIEdyb3VwMRUwEwYDVQQDEwxJU1JHIFJvb3QgWDEwHhcNMTUwNjA0MTEwNDM4
WhcNMzUwNjA0MTEwNDM4WjBPMQswCQYDVQQGEwJVUzEpMCcGA1UEChMgSW50ZXJu
ZXQgU2VjdXJpdHkgUmVzZWFyY2ggR3JvdXAxFTATBgNVBAMTDElTUkcgUm9vdCBY
MTCCAiIwDQYJKoZIhvcNAQEBBQADggIPADCCAgoCggIBAK3oJHP0FDfzm54rVygc
h77ct984kIxuPOZXoHj3dcKi/vVqbvYATyjb3miGbESTtrFj/RQSa78f0uoxmyF+
0TM8ukj13Xnfs7j/EvEhmkvBioZxaUpmZmyPfjxwv60pIgbz5MDmgK7iS4+3mX6U
A5/TR5d8mUgjU+g4rk8Kb4Mu0UlXjIB0ttov0DiNewNwIRt18jA8+o+u3dpjq+sW
T8KOEUt+zwvo/7V3LvSye0rgTBIlDHCNAymg4VMk7BPZ7hm/ELNKjD+Jo2FR3qyH
B5T0Y3HsLuJvW5iB4YlcNHlsdu87kGJ55tukmi8mxdAQ4Q7e2RCOFvu396j3x+UC
B5iPNgiV5+I3lg02dZ77DnKxHZu8A/lJBdiB3QW0KtZB6awBdpUKD9jf1b0SHzUv
KBds0pjBqAlkd25HN7rOrFleaJ1/ctaJxQZBKT5ZPt0m9STJEadao0xAH0ahmbWn
OlFuhjuefXKnEgV4We0+UXgVCwOPjdAvBbI+e0ocS3MFEvzG6uBQE3xDk3SzynTn
jh8BCNAw1FtxNrQHusEwMFxIt4I7mKZ9YIqioymCzLq9gwQbooMDQaHWBfEbwrbw
qHyGO0aoSCqI3Haadr8faqU9GY/rOPNk3sgrDQoo//fb4hVC1CLQJ13hef4Y53CI
rU7m2Ys6xt0nUW7/vGT1M0NPAgMBAAGjQjBAMA4GA1UdDwEB/wQEAwIBBjAPBgNV
HRMBAf8EBTADAQH/MB0GA1UdDgQWBBR5tFnme7bl5AFzgAiIyBpY9umbbjANBgkq
hkiG9w0BAQsFAAOCAgEAVR9YqbyyqFDQDLHYGmkgJykIrGF1XIpu+ILlaS/V9lZL
ubhzEFnTIZd+50xx+7LSYK05qAvqFyFWhfFQDlnrzuBZ6brJFe+GnY+EgPbk6ZGQ
3BebYhtF8GaV0nxvwuo77x/Py9auJ/GpsMiu/X1+mvoiBOv/2X/qkSsisRcOj/KK
NFtY2PwByVS5uCbMiogziUwthDyC3+6WVwW6LLv3xLfHTjuCvjHIInNzktHCgKQ5
ORAzI4JMPJ+GslWYHb4phowim57iaztXOoJwTdwJx4nLCgdNbOhdjsnvzqvHu7Ur
TkXWStAmzOVyyghqpZXjFaH3pO3JLF+l+/+sKAIuvtd7u+Nxe5AW0wdeRlN8NwdC
jNPElpzVmbUq4JUagEiuTDkHzsxHpFKVK7q4+63SM1N95R1NbdWhscdCb+ZAJzVc
oyi3B43njTOQ5yOf+1CceWxG1bQVs5ZufpsMljq4Ui0/1lvh+wjChP4kqKOJ2qxq
4RgqsahDYVvTH9w7jXbyLeiNdd8XM2w9U/t7y0Ff/9yi0GE44Za4rF2LN9d11TPA
mRGunUHBcnWEvgJBQl9nJEiU0Zsnvgc/ubhPgXRR4Xq37Z0j4r7g1SgEEzwxA57d
emyPxgcYxn/eR44/KJ4EBs+lVDR3veyJm+kXQ99b21/+jh5Xos1AnX5iItreGCc=
-----END CERTIFICATE-----">> /etc/letsencrypt/live/$HOSTNAME/chain.pem
Copiar el key comercial

chown zimbra -R /etc/letsencrypt
cp /etc/letsencrypt/live/$HOSTNAME/privkey.pem /opt/zimbra/ssl/zimbra/commercial/commercial.key
Cambiamos de dueño al directorio letsencrypt

chown  zimbra /opt/zimbra/ssl/zimbra/commercial/commercial.key
Desplegamos el certificado

su - zimbra
cd /etc/letsencrypt/live/$HOSTNAME
/opt/zimbra/bin/zmcertmgr deploycrt comm cert.pem chain.pem
zmcontrol restart
9.2.- Certificados Letsencrypt del servidor mailbox1
Nos conectamos al servidor mailbox1

Instalar epel

yum -y install epel-release
Instalar Letsencrypt y ca-certificates en CentOS 7

yum -y install certbot python-certbot-apache ca-certificates
Nota, si fuera Ubuntu instalar con

apt -y update
apt -y install certbot ca-certificates python3-certbot-apache
update-ca-certificates
Actualizar los CA de CentOS

update-ca-trust force-enable
Detener apache y zimbra proxy

systemctl stop httpd
su - zimbra -c "zmproxyctl stop"
Generar certificado para el dominio

certbot -d mailbox1.aulautil.tk -m usuario@tucorreoactivo.com --force-renewal --preferred-chain "ISRG Root X1" certonly
- Seleccionar opción 2 "Spin up a temporary webserver (standalone)": 2
Please read the Terms ...
(A)Agree/(C)Cancel: A
(Y)es/(N)o: Y
Modificar el certificado generado para agregar el CA de Letsencrypt

echo "-----BEGIN CERTIFICATE-----
MIIFazCCA1OgAwIBAgIRAIIQz7DSQONZRGPgu2OCiwAwDQYJKoZIhvcNAQELBQAw
TzELMAkGA1UEBhMCVVMxKTAnBgNVBAoTIEludGVybmV0IFNlY3VyaXR5IFJlc2Vh
cmNoIEdyb3VwMRUwEwYDVQQDEwxJU1JHIFJvb3QgWDEwHhcNMTUwNjA0MTEwNDM4
WhcNMzUwNjA0MTEwNDM4WjBPMQswCQYDVQQGEwJVUzEpMCcGA1UEChMgSW50ZXJu
ZXQgU2VjdXJpdHkgUmVzZWFyY2ggR3JvdXAxFTATBgNVBAMTDElTUkcgUm9vdCBY
MTCCAiIwDQYJKoZIhvcNAQEBBQADggIPADCCAgoCggIBAK3oJHP0FDfzm54rVygc
h77ct984kIxuPOZXoHj3dcKi/vVqbvYATyjb3miGbESTtrFj/RQSa78f0uoxmyF+
0TM8ukj13Xnfs7j/EvEhmkvBioZxaUpmZmyPfjxwv60pIgbz5MDmgK7iS4+3mX6U
A5/TR5d8mUgjU+g4rk8Kb4Mu0UlXjIB0ttov0DiNewNwIRt18jA8+o+u3dpjq+sW
T8KOEUt+zwvo/7V3LvSye0rgTBIlDHCNAymg4VMk7BPZ7hm/ELNKjD+Jo2FR3qyH
B5T0Y3HsLuJvW5iB4YlcNHlsdu87kGJ55tukmi8mxdAQ4Q7e2RCOFvu396j3x+UC
B5iPNgiV5+I3lg02dZ77DnKxHZu8A/lJBdiB3QW0KtZB6awBdpUKD9jf1b0SHzUv
KBds0pjBqAlkd25HN7rOrFleaJ1/ctaJxQZBKT5ZPt0m9STJEadao0xAH0ahmbWn
OlFuhjuefXKnEgV4We0+UXgVCwOPjdAvBbI+e0ocS3MFEvzG6uBQE3xDk3SzynTn
jh8BCNAw1FtxNrQHusEwMFxIt4I7mKZ9YIqioymCzLq9gwQbooMDQaHWBfEbwrbw
qHyGO0aoSCqI3Haadr8faqU9GY/rOPNk3sgrDQoo//fb4hVC1CLQJ13hef4Y53CI
rU7m2Ys6xt0nUW7/vGT1M0NPAgMBAAGjQjBAMA4GA1UdDwEB/wQEAwIBBjAPBgNV
HRMBAf8EBTADAQH/MB0GA1UdDgQWBBR5tFnme7bl5AFzgAiIyBpY9umbbjANBgkq
hkiG9w0BAQsFAAOCAgEAVR9YqbyyqFDQDLHYGmkgJykIrGF1XIpu+ILlaS/V9lZL
ubhzEFnTIZd+50xx+7LSYK05qAvqFyFWhfFQDlnrzuBZ6brJFe+GnY+EgPbk6ZGQ
3BebYhtF8GaV0nxvwuo77x/Py9auJ/GpsMiu/X1+mvoiBOv/2X/qkSsisRcOj/KK
NFtY2PwByVS5uCbMiogziUwthDyC3+6WVwW6LLv3xLfHTjuCvjHIInNzktHCgKQ5
ORAzI4JMPJ+GslWYHb4phowim57iaztXOoJwTdwJx4nLCgdNbOhdjsnvzqvHu7Ur
TkXWStAmzOVyyghqpZXjFaH3pO3JLF+l+/+sKAIuvtd7u+Nxe5AW0wdeRlN8NwdC
jNPElpzVmbUq4JUagEiuTDkHzsxHpFKVK7q4+63SM1N95R1NbdWhscdCb+ZAJzVc
oyi3B43njTOQ5yOf+1CceWxG1bQVs5ZufpsMljq4Ui0/1lvh+wjChP4kqKOJ2qxq
4RgqsahDYVvTH9w7jXbyLeiNdd8XM2w9U/t7y0Ff/9yi0GE44Za4rF2LN9d11TPA
mRGunUHBcnWEvgJBQl9nJEiU0Zsnvgc/ubhPgXRR4Xq37Z0j4r7g1SgEEzwxA57d
emyPxgcYxn/eR44/KJ4EBs+lVDR3veyJm+kXQ99b21/+jh5Xos1AnX5iItreGCc=
-----END CERTIFICATE-----">> /etc/letsencrypt/live/$HOSTNAME/chain.pem
Copiar el key comercial

chown zimbra -R /etc/letsencrypt
cp /etc/letsencrypt/live/$HOSTNAME/privkey.pem /opt/zimbra/ssl/zimbra/commercial/commercial.key
Cambiamos de dueño al directorio letsencrypt

chown  zimbra /opt/zimbra/ssl/zimbra/commercial/commercial.key
Desplegamos el certificado

su - zimbra
cd /etc/letsencrypt/live/$HOSTNAME
/opt/zimbra/bin/zmcertmgr deploycrt comm cert.pem chain.pem
zmcontrol restart
9.3.- Certificados Letsencrypt del servidor mailbox2
Nos conectamos al servidor mailbox2

Instalar epel

yum -y install epel-release
Instalar Letsencrypt y ca-certificates en CentOS 7

yum -y install certbot python-certbot-apache ca-certificates
Nota, si fuera Ubuntu instalar con

apt -y update
apt -y install certbot ca-certificates python3-certbot-apache
update-ca-certificates
Actualizar los CA de CentOS

update-ca-trust force-enable
Detener apache y zimbra proxy

systemctl stop httpd
su - zimbra -c "zmproxyctl stop"
Generar certificado para el dominio

certbot -d mailbox2.aulautil.tk -m usuario@tucorreoactivo.com --force-renewal --preferred-chain "ISRG Root X1" certonly
- Seleccionar opción 2 "Spin up a temporary webserver (standalone)": 2
Please read the Terms ...
(A)Agree/(C)Cancel: A
(Y)es/(N)o: Y
Modificar el certificado generado para agregar el CA de Letsencrypt

echo "-----BEGIN CERTIFICATE-----
MIIFazCCA1OgAwIBAgIRAIIQz7DSQONZRGPgu2OCiwAwDQYJKoZIhvcNAQELBQAw
TzELMAkGA1UEBhMCVVMxKTAnBgNVBAoTIEludGVybmV0IFNlY3VyaXR5IFJlc2Vh
cmNoIEdyb3VwMRUwEwYDVQQDEwxJU1JHIFJvb3QgWDEwHhcNMTUwNjA0MTEwNDM4
WhcNMzUwNjA0MTEwNDM4WjBPMQswCQYDVQQGEwJVUzEpMCcGA1UEChMgSW50ZXJu
ZXQgU2VjdXJpdHkgUmVzZWFyY2ggR3JvdXAxFTATBgNVBAMTDElTUkcgUm9vdCBY
MTCCAiIwDQYJKoZIhvcNAQEBBQADggIPADCCAgoCggIBAK3oJHP0FDfzm54rVygc
h77ct984kIxuPOZXoHj3dcKi/vVqbvYATyjb3miGbESTtrFj/RQSa78f0uoxmyF+
0TM8ukj13Xnfs7j/EvEhmkvBioZxaUpmZmyPfjxwv60pIgbz5MDmgK7iS4+3mX6U
A5/TR5d8mUgjU+g4rk8Kb4Mu0UlXjIB0ttov0DiNewNwIRt18jA8+o+u3dpjq+sW
T8KOEUt+zwvo/7V3LvSye0rgTBIlDHCNAymg4VMk7BPZ7hm/ELNKjD+Jo2FR3qyH
B5T0Y3HsLuJvW5iB4YlcNHlsdu87kGJ55tukmi8mxdAQ4Q7e2RCOFvu396j3x+UC
B5iPNgiV5+I3lg02dZ77DnKxHZu8A/lJBdiB3QW0KtZB6awBdpUKD9jf1b0SHzUv
KBds0pjBqAlkd25HN7rOrFleaJ1/ctaJxQZBKT5ZPt0m9STJEadao0xAH0ahmbWn
OlFuhjuefXKnEgV4We0+UXgVCwOPjdAvBbI+e0ocS3MFEvzG6uBQE3xDk3SzynTn
jh8BCNAw1FtxNrQHusEwMFxIt4I7mKZ9YIqioymCzLq9gwQbooMDQaHWBfEbwrbw
qHyGO0aoSCqI3Haadr8faqU9GY/rOPNk3sgrDQoo//fb4hVC1CLQJ13hef4Y53CI
rU7m2Ys6xt0nUW7/vGT1M0NPAgMBAAGjQjBAMA4GA1UdDwEB/wQEAwIBBjAPBgNV
HRMBAf8EBTADAQH/MB0GA1UdDgQWBBR5tFnme7bl5AFzgAiIyBpY9umbbjANBgkq
hkiG9w0BAQsFAAOCAgEAVR9YqbyyqFDQDLHYGmkgJykIrGF1XIpu+ILlaS/V9lZL
ubhzEFnTIZd+50xx+7LSYK05qAvqFyFWhfFQDlnrzuBZ6brJFe+GnY+EgPbk6ZGQ
3BebYhtF8GaV0nxvwuo77x/Py9auJ/GpsMiu/X1+mvoiBOv/2X/qkSsisRcOj/KK
NFtY2PwByVS5uCbMiogziUwthDyC3+6WVwW6LLv3xLfHTjuCvjHIInNzktHCgKQ5
ORAzI4JMPJ+GslWYHb4phowim57iaztXOoJwTdwJx4nLCgdNbOhdjsnvzqvHu7Ur
TkXWStAmzOVyyghqpZXjFaH3pO3JLF+l+/+sKAIuvtd7u+Nxe5AW0wdeRlN8NwdC
jNPElpzVmbUq4JUagEiuTDkHzsxHpFKVK7q4+63SM1N95R1NbdWhscdCb+ZAJzVc
oyi3B43njTOQ5yOf+1CceWxG1bQVs5ZufpsMljq4Ui0/1lvh+wjChP4kqKOJ2qxq
4RgqsahDYVvTH9w7jXbyLeiNdd8XM2w9U/t7y0Ff/9yi0GE44Za4rF2LN9d11TPA
mRGunUHBcnWEvgJBQl9nJEiU0Zsnvgc/ubhPgXRR4Xq37Z0j4r7g1SgEEzwxA57d
emyPxgcYxn/eR44/KJ4EBs+lVDR3veyJm+kXQ99b21/+jh5Xos1AnX5iItreGCc=
-----END CERTIFICATE-----">> /etc/letsencrypt/live/$HOSTNAME/chain.pem
Copiar el key comercial

chown zimbra -R /etc/letsencrypt
cp /etc/letsencrypt/live/$HOSTNAME/privkey.pem /opt/zimbra/ssl/zimbra/commercial/commercial.key
Cambiamos de dueño al directorio letsencrypt

chown  zimbra /opt/zimbra/ssl/zimbra/commercial/commercial.key
Desplegamos el certificado

su - zimbra
cd /etc/letsencrypt/live/$HOSTNAME
/opt/zimbra/bin/zmcertmgr deploycrt comm cert.pem chain.pem
zmcontrol restart
Acceder al Webmail de Zimbra
https://smtp.aulautil.tk

Acceder al panel de Zimbra
https://mailbox1.aulautil.tk:7071
https://mailbox2.aulautil.tk:7071

Revisar la lista de servidores en Configuración –> Servidores
