Instalación y Upgrade de Proxmox Mail Gateway
En el capítulo anterior instalamos el servidor de correo Zimbra Network Standalone Ahora para brindar seguridad a nuestro servidor de correo, vamos a instalar un  servidor gateway antispam.

1.- Flujo de filtrado
1.1.- Filtrado en la recepción
1.- Tipos de Recepción

(ZIMBRA) {-------------------(Servidor que Envía)

(ZIMBRA) {------------------  | Relay Antispam | {----------------(Servidor que Envía)
                             Proxmox Mail Gateway

2.- Filtrado con Proxmox Mail Gateway
 |    - SPF
 |    - Filtros de recepción por sender y receiver
 |    - WhiteList/Blacklist
 |    - RBL
 |    - Spamassassin
 |    - Graylist
 |    - Objetos de filtrado: cabeceras, dominios, correos, horario, regexp
 |    - Notificaciones
 |    - Filtros de MTA (postfix), protocolo, DNS
3.- Filtrado con Zimbra
(MTA) {-------------------(Servidor que Envía)
 |    - Filtros de Protocolo
 |    - Filtros de DNS
 |    - Filtros de Cabecera
 |    - Whitelist a nivel MTA
 |
(Servidor de Políticas) Opcional
Policyd, postfwd
 |    - Filtros por sender y receiver
 |    - Rate Limitis por usuario
 |    - Tamaño de Adjunto por usuario
 |    - Verificación SASL
 |    - SPF
 |    - Graylist, etc
(Filtro de Contenido)
 |    * Amavis: Listas Blancas y Negras, quarentena
 |     - Spamassasin: Filtros de cabeceras y de contenido
 |     - Clamav (Antivirus libre)
 |     - Otros antivirus: Sophos, Mcaffee, etc
 |    * MailScanner: Listas Blancas y Negras, quarentena
 |     - Spamassassin: Filtros de cabeceras y de contenido
 |     - Clamav (Antivirus libre) 
 |     - Otros antivirus: Sophos, Mcaffee, etc
 |     - MailWatch (Panel Web de Monitoreo y Visibilidad de correos internos y externos, Gestor de Quarentena)
 |    
(Reglas de Correo del Webmail)
 |      - Creados y definidos por el usuario
 |
(Buzón)
1.2.- Filtrado en el envío
1.- Tipos de Envío

(ZIMBRA) -------------------}(Servidor que recibe)

(ZIMBRA) ------------------}  | Relay Antispam | ----------------} (Servidor que Recibe)
                             Proxmox Mail Gateway

2.- Filtrado con Zimbra
(MTA) -------------------} (Servidor que Recibe)
 |    - Verificación SASL
 |    
(Servidor de Políticas) Opcional
Policyd, postfwd
 |    - Filtros por sender y receiver
 |    - Rate Limitis por usuario
 |    - Tamaño de Adjunto por usuario
 |    - Verificación SASL
(Filtro de Contenido)
 |    * Amavis: Listas Blancas y Negras, quarentena
 |     - Spamassasin: Filtros de cabeceras y de contenido
 |     - Clamav (Antivirus libre)
 |     - Otros antivirus: Sophos, Mcaffee, etc
 |    * MailScanner: Listas Blancas y Negras, quarentena
 |     - Spamassassin: Filtros de cabeceras y de contenido
 |     - Clamav (Antivirus libre) 
 |     - Otros antivirus: Sophos, Mcaffee, etc
 |     - MailWatch (Panel Web de Monitoreo y Visibilidad de correos internos y externos, Gestor de Quarentena)
 |
3.- Filtrado con Proxmox Mail Gateway
 |    - Filtros de envío por sender y receiver
 |    - WhiteList/Blacklist
 |    - Spamassassin
 |    - Objetos de filtrado: cabeceras, dominios, correos, horario, regexp
 |    - Notificaciones
 |    - Rate limits generales
4.- (Servidor que recibe)
       - Verifica comunicación de protocolo
       - Verifica registros DNS (MX, A, PTR, SPF)
       - Verifica firma de mensaje (DKIM, opcional)
       - Verifica RBL
2.- Instalación de Proxmox Mail Gateway
Documentación Oficial https://pmg.proxmox.com/pmg-docs/pmg-admin-guide.pdf

Podemos instalar el Gateway antispam con una IP pública distinta a la del correo, o usar la misma IP nateando el puerto 25 y 8006 hacia el Gateway Antispam

2.1.- Requisitos de Hardware
Mínimo 2GB de RAM Disco: 100 GB Cores: Mínimo 2

2.2.- Métodos de Instalación
2.2.1.- Con ISO Instalador
Instalador ISO de Proxmox Gateway Descargar desde https://www.proxmox.com/en/downloads/category/iso-images-pmg

Grabar el iso a un USD o DVD e instalar.

1.- Aceptar Licencia
2.- Target Disc: disco donde se va instalar, no es necesario definir particionamiento 
3.- Country: 
    Time Zone:
    Keyboar Layout: Spanish 
4.- Contraseña y correo del Administrador 
    password:----> PONER CONTRASEÑA ROBUSTA
    correo: admin@aulautil.tk           ----> En esa cuenta llegan las notificaciones    
5.- Interfaz de Red
    Hostname:       relay.aulautil.tk
    IP Address:     192.168.X.10
    Netmask:        255.255.255.0
    Gateway:        192.168.X.1
    DNS Server:     8.8.8.8
2.2.2.- Instalar PMG 6.4 Bajo debian buster
Configurar los respositorios

echo "deb http://download.proxmox.com/debian/pmg buster pmg-no-subscription" > /etc/apt/sources.list.d/pmg-install-repo.list
Descargar la llave pública de los paquetes

wget http://download.proxmox.com/debian/proxmox-ve-release-6.x.gpg -O /etc/apt/trusted.gpg.d/proxmox-ve-release-6.x.gpg
Borrar repositorio enterprise

rm /etc/apt/sources.list.d/pmg-enterprise*
Actualizar lista de paquetes

apt update
Instalar dentro de debian

apt-get install proxmox-mailgateway
Instalar como un contenedor LXC

apt-get install proxmox-mailgateway-container
Hacer un upgrade de PMG, ej: de 6.2 a 6.4

apt update
apt upgrade
3.- Reglas de NAT de entrada y salida en el Firewall Sophos XG
3.1.- Modificar regla de nateo del zimbra
Conectarse y loguearse al firewall Sophos XG

Ir a PROTEGER --> Reglas y Políticas
    Firewall
        Ampliar el grupo Zimbra Network
            Editar "Nat Zimbra Network"
                Destino y Servicios 
                    Servicios
                        Quitar "SMTP"
            Guardar
    NAT
        Editar "Nat Zimbra Network"
                Configuración de traducción
                    Servicio Original
                        Quitar "SMTP"
            Guardar
3.2.- Borrar reglas por defecto creadas por Sophos
Conectarse y loguearse al firewall Sophos XG

Ir a PROTEGER --> Reglas y Políticas
    Reglas de Firewall
        Seleccionar Regla "Auto added Firewall"    ...
            Eiminar
        Esta seguro de eliminar los registros seleccionados?
            Aceptar
3.3.- Crear reglas de Firewall para el Relay
3.3.1.- Regla de NAT para PMG (SMTP y 8006)
PROTEGER --> Reglas y Políticas
    Reglas de Firewall 
        Click en "Añadir regla de Firewall"
             Nueva Regla de Firewall
                Nombre de regla:                Nat Proxmox Mail Gateway
                Descripción:                    Gateway Antispam
                Posición de la regla:           Arriba
                Grupo de Reglas:                
                    Add to group
                        New Group
                            Nombre de Grupo:    Proxmox Mail Gateway
                Acción:                         Aceptar
                Registrar tráfico de firewall:  CHECK

                ORIGEN
                    Zonas de origen:                WAN
                    Dipositivos y redes de origen:  Cualquiera
                    Durante la hora programada:     Siempre

                DESTINO y Servicios
                    Zona de Destino:                DMZ
                    Redes de Destino:               51.68.161.X
                    Servicios:                      SMTP     
                                                    Crear Nuevo 
                                                        Servicio
                                                            Nombre:           PMG
                                                            Puerto origen:    1:65535
                                                            Puerto Destino:   8006                       

            Guardar

    Reglas de NAT 
        Click en "Añadir regla de NAT"
            Nueva Regla NAT
                Nombre de regla:                Nat Proxmox Mail Gateway
                Descripción:                    Gateway Antispam
                Posición de la regla:           Arriba

            Configuración de traducción
                Origen original:                Cualquiera
                Destino original:               51.68.161.X
                Servicio original:              SMTP, PMG

            Destino Traducido (DNAT):          
                            Direccion IP: Crear Nuevo --> Direccion IP
                                Nombre:       192.168.X.10_Relay
                                Direccion IP: 192.168.X.10
                            Guardar

            Servicio Traducido (PAT):           Se usa solo cuando el puerto de reenvío es distinto al de Servicio Original.

            Criterios de coincidencia de interfaz
                Interfaz de entrada:            Port2
                Interfaz de salida:             Cualquiera

        Guardar
3.3.2.- Regla de Salida y NAT Fuente para PMG
Conectarse y loguearse al firewall Sophos XG

PROTEGER --> Reglas y Políticas
    Reglas de Firewall 
        Click en "Añadir regla de Firewall"
            Nueva Regla de Firewall
                Nombre de regla:                    Salida Proxmox Mail Gateway
                Posición de la regla:               Arriba
                Grupo:                              Proxmox Mail Gateway
                Acción:                             Aceptar
                Registrar tráfico de firewall:      CHECK

                ORIGEN
                    Zonas de origen:                DMZ
                    Dispositivos y Redes de Origen: 192.168.X.10_PMG
                    Durante la hora programada:     Siempre

                DESTINO y Servicios
                    Zona de Destino:                WAN
                    Redes de Destino:               Cualquiera
                    Servicios:                      Cualquiera

                Crear Regla NAT Vinculada:          Dar Click
                    Nombre de la Regla:             Salida Proxmox Mail Gateway
                    Posición de la Regla:           Arriba

                    Configuración de traducción (NAT Fuente)
                        Origen Traducido (SNAT):    51.68.161.X
                    Guardar
            Guardar
Comprobar en el relay si esta con su ip pública Ejecutar en el relay

curl ifconfig.ma
Actualizar los paquetes; si pide confirmar actualización de configuración a todo le respondemos «Y»

apt update
apt upgrade
reboot
Instalar Utilidades

apt-get install vim elinks wget ftp telnet mlocate net-tools
Instalar Paquetes de lenguaje

apt-get install locales-all
vim ~/.vimrc
i
syntax on
set paste
set mouse-=a
ESC:wq
vim /etc/vim/vimrc
34:au BufReadPost * if line("'\"") > 1 && line("'\"") <= line("$") | exe "normal! g'\"" | endif
ESC:wq
3.3.2.1.- Verificar el NAT SMTP
Desde la laptop ejecutar

telnet 51.68.161.X 25
4.- UPGRADE de Promox Mail Gateway
4.1.- Upgrade de la misma serie
Para realizar upgrade de Proxmox Mail Gateway de la serie debemos saber con que versión de PMG estamos

pmgversion
Agregar repositorio no-subscription

PMG 5.x

echo "deb http://download.proxmox.com/debian/pmg stretch pmg-no-subscription" > /etc/apt/sources.list.d/pmg-nosubscription.list
PMG 6.x

echo "deb http://download.proxmox.com/debian/pmg buster pmg-no-subscription" > /etc/apt/sources.list.d/pmg-nosubscription.list
PMG 7.x

echo "deb http://download.proxmox.com/debian/pmg bullseye pmg-no-subscription" > /etc/apt/sources.list.d/pmg-nosubscription.list
Borrar repositorio enterprise por defecto

rm /etc/apt/sources.list.d/pmg-enterprise* -f
Actualizar paquetes y hacer Upgrade; si pide confirmar actualización de configuración a todo le respondemos «Y»

apt update
apt dist-upgrade
Reiniciar el equipo

reboot
Comprobar versión de PMG Loguearse por ssh y ejecutar

pmgversion
4.2.- Upgrade de PMG 5.x a PMG 6.x
Para realizar un upgrade de una serie a otra normalmente implica un upgrade completo de la distro base.
Por ejemplo pasar de PMG 5.x a 6.x implica cambiar de debian stretch a debian buster.

0.- Hacer upgrade la serie descrito en el paso 4.1
1.- Hacer un Backup de la configuración de PMG

pmgbackup backup
2.- Cambiar los repositorios de stretch a buster

sed -i 's/buster\/updates/bullseye-security/g;s/stretch/buster/g' /etc/apt/sources.list
3.- Agregar repositorio no-subscription

echo "deb http://download.proxmox.com/debian/pmg buster pmg-no-subscription" > /etc/apt/sources.list.d/pmg-nosubscription.list
4.- Detener servicios

systemctl stop postfix pmg-smtp-filter pmgpolicy pmgdaemon pmgproxy pmgmirror pmgtunnel
5.- Deshabilitar arranque automático

systemctl mask postfix pmg-smtp-filter pmgpolicy pmgdaemon pmgproxy pmgmirror pmgtunnel
6.- Realizar el upgrade; si pide confirmar actualización de configuración a todo le respondemos «Y» y si nos pregunta que configuración instalar; «Install the mantainer package …»

apt update
apt dist-upgrade
7.- Purgar clamav-base y clamav-daemon

apt -y purge clamav-base clamav-daemon
8.- Instalar PMG

apt -y install proxmox-mailgateway
9.- Desbloqueamos el inicio de servicios

systemctl unmask postfix pmg-smtp-filter pmgpolicy pmgdaemon pmgproxy pmgmirror pmgtunnel
10.- Reiniciamos el equipo

reboot
11.- Purgamos la versión antigua de postgreSQL

apt purge postgresql-9.6 postgresql-client-9.6
12.- Corregir el puerto y reiniciar la base de datos postgreSQL

sed -i "s/5433/5432/g" /etc/postgresql/11/main/postgresql.conf
systemctl restart postgresql
systemctl enable postgresql
13.- Inicializar la configuración y base de datos de pmgbackup

cd /tmp
pmgconfig init
pmgdb init
14.- Restauramos el último backup guardado del listado (Ej: pmg-backup_2022_01_15_XXXX.tgz

cd /tmp
pmgbackup list
pmgbackup restore --filename pmg-backup_2022_01_15_XXXX.tgz
15.- Verificar el funcionamiento de la DB haciendo un dump

cd /tmp
pmgdb dump
16.- Verificar la versión de PMG

pmgversion
17.- Borramos repositorio enterprise

rm /etc/apt/sources.list.d/pmg-enterprise*
18.- Finalizamos el upgrade con un reboot

reboot
4.3.- Upgrade de PMG 6.x a PMG 7.x
Para realizar un upgrade de una serie a otra normalmente implica un upgrade completo de la distro base.
Por ejemplo pasar de PMG 6.x a 7.x implica cambiar de debian buster a debian bullseye.

0.- Hacer upgrade la serie descrito en el paso 4.1
1.- Hacer un Backup de la configuración de PMG

pmgbackup backup
2.- Cambiar los repositorios de buster a bullseye

sed -i 's/buster\/updates/bullseye-security/g;s/buster/bullseye/g' /etc/apt/sources.list
3.- Agregar repositorio no-subscription

echo "deb http://download.proxmox.com/debian/pmg bullseye pmg-no-subscription" > /etc/apt/sources.list.d/pmg-nosubscription.list
4.- Detener servicios

systemctl stop postfix pmg-smtp-filter pmgpolicy pmgdaemon pmgproxy pmgmirror pmgtunnel
5.- Deshabilitar arranque automático

systemctl mask postfix pmg-smtp-filter pmgpolicy pmgdaemon pmgproxy pmgmirror pmgtunnel
6.- Realizar el upgrade; si pide confirmar actualización de configuración a todo le respondemos «Y» y si nos pregunta que configuración instalar; «Install the mantainer package …»

apt update
apt dist-upgrade
7.- Desbloqueamos el inicio de servicios

systemctl unmask postfix pmg-smtp-filter pmgpolicy pmgdaemon pmgproxy pmgmirror pmgtunnel
8.- Reiniciamos el equipo

reboot
9.- Purgamos la versión antigua de postgreSQL

apt purge postgresql-11 postgresql-client-11
10.- Corregir el puerto y reiniciar la base de datos postgreSQL

sed -i "s/5433/5432/g" /etc/postgresql/13/main/postgresql.conf
systemctl restart postgresql
systemctl enable postgresql
11.- Inicializar la configuración y base de datos de pmgbackup

cd /tmp
pmgconfig init
pmgdb init
12.- Restauramos el último backup guardado del listado (Ej: pmg-backup_2022_01_15_XXXX.tgz

cd /tmp
pmgbackup list
pmgbackup restore --filename pmg-backup_2022_01_15_XXXX.tgz
13.- Verificar el funcionamiento de la DB haciendo un dump

cd /tmp
pmgdb dump
14.- Rotar los logs

/usr/lib/rsyslog/rsyslog-rotate
15.- Verificar la versión de PMG

pmgversion
16.- Borramos repositorio enterprise

rm /etc/apt/sources.list.d/pmg-enterprise*
17.- Finalizamos el upgrade con un reboot

reboot
Nota Si tenemos problemas para levantar un servicio debes desinstalar apparmor

apt remove apparmor
reboot
5.- Crear y modificar los registros DNS para el Relay
Acceder al register del dominio:

Crear registro A para relay.aulautil.tk apuntado a la IP pública 51.68.161.X
Modificar el MX que apunte a relay.aulautil.tk
Freenom

    Ir a Services --> My Domains
    Elegir Dominio ---> Manage Domain
        Manage Freenom DNS
            Add records
                Name:   relay
                Type:   A
                Target: 51.68.161.X
            Save Changes

            Modify Records
                Name:
                Type:   MX
                Target: relay.aulautil.tk
            Save Changes
Namecheap

 Ej: Registro A para relay.aulautil.club
        Ir a Domain List
        Elegir aulautil.club ----> Click en 'Manage'
            Ir a "Advanced DNS"
            Hosts Records
                Clik "Add New Record"
                    Type:               "A Record"
                    Host:               relay
                    IP Address:    51.68.161.X
                    TTL:                Automatic
                    Click en ícono "CHECK"

Ej: Registro MX para aulautil.club
        Ir a Domain List
        Elegir aulautil.club ----> Click en 'Manage'
            Ir a "Advanced DNS"
                Mail Setting "Custom MX"
                    "MX Record"
                    Host:               @
                    Value:              relay.aulautil.club
                    Priority:           10
                    TTL:                 Automatic
                    Click en "Save ALL Changes"
Verificar los cambios del DNS público

dig relay.aulautil.tk @8.8.8.8
dig MX aulautil.tk @8.8.8.8
dig TXT aulautil.tk @8.8.8.8
6.- Declarar los nombres de zimbra y el relay en los /etc/hosts
Conectarse al zimbra por SSH

Agregar en el /etc/hosts la resolución del relay por IP privada o pública según corresponda

vim /etc/hosts
127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4
::1         localhost localhost.localdomain localhost6 localhost6.localdomain6
192.168.X.2 mail.aulautil.tk mail
192.168.X.10 relay.aulautil.tk relay
Reiniciar servicio de DNSMASQ

systemctl restart dnsmasq
Conectarse al relay por SSH

Agregar en el /etc/hosts la resolución del relay por IP privada o pública según corresponda

vim /etc/hosts
127.0.0.1 localhost.localdomain localhost
192.168.X.10 relay.aulautil.tk relay
192.168.X.2  mail.aulautil.tk mail
7.- Configurar en Zimbra para que envíe a través del relay
Loguearse a Zimbra por SSH como root

Luego loguearse como usuario zimbra

su - zimbra
Definir un relay de salida de correos para Zimbra

zmprov ms `zmhostname` zimbraMtaRelayHost relay.aulautil.tk:26
Reiniciar el servicio de mta

zmmtactl restart
8.- Configurar el Dominio en el relay para la recepción y envío
Loguearse a PMG https://relay.aulautil.tk:8006

user: root pass: ClaveConqueSeInstalo

Ir a

Configuración --> Proxy de Correo
    Dominios de retransmisión
        Click en crear
            Dominio de retransmisión: aulautil.tk
    Transportes (para el reenvío de la recepción)
        Click en crear
            Dominio de retransmisión: aulautil.tk
            Host: 192.168.X.2     (tambien es válido poner nombre de host si hay dns que apunte a la ip privada)
            Puerto: 25
            Usar MX () quitar check
            Comentario: Zimbra Network
    Redes (Para autorizar el envío a través del relay)
        Click en crear
            CIDR: 192.168.X.2/32
            Comentario: mail.aulautil.tk
    TLS
        Configuración
            Activar TLS: Sí
            Activar TLS Loggin: Sí
            Activar encabezado recibido de TLS: Sí
9.- Realizar pruebas de envío y recepción de correo
Opcionalmente si tenemos problemas en la recepción de Google, Microsoft; deshabilitar graylist

Configuración --> Proxy de Correo
    Opciones
    Use Graylist for IPv4: Quitar Check
Volver a probar la recepción
