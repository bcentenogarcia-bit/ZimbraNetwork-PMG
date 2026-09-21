Instalación de Zimbra Network 8.8.15 en CentOS 7, Upgrade a Zimbra 9.0, Instalación de Certificados Digitales Letsencrypt y Parches de Zimbra
En el capítulo anterior registramos un dominio gratuito tk en freenom y configuramos un servidor DNS. Ahora instalaremos un servidor DNS interno (dnsmasq) para Zimbra; luego instalaremos Zimbra Network 8.8.15 con certificado digital Letsencrypt y finalmente haremos el Upgrade de Zimbra Network a la versión 9.0; esto para entender el proceso de instalación y upgrade de Zimbra para cualquier versión.

Tabla de Contenidos
Tiempo de Vida de soporte de las versiones de Zimbra
Instalación de Zimbra Network 8.8.15 en CentOS 7
Registrar un Dominio y configurar un DNS Público con la Ip Pública de Zimbra
Cambiar el Hostname del Servidor
Editar el /etc/hosts para agregar la ip interna del zimbra con el hostname
Cambiar el Hostname del Servidor
Instalación de DNS Server Interno DNSMASQ
Instalación de Zimbra 8.8.15 en CentOS 7
Acceder al zimbra instalado con el navegador
Upgrade de Zimbra
Upgrade a Zimbra Network 9.0
Parchar zimbra
Certificados Digitales Letsencrypt para zimbra
Desplegar el certificado letsencrypt en Zimbra
1.- Tiempo de Vida de soporte de las versiones de Zimbra
Zimbra tiene ciclo de vida y soporte definido para cada versión Ciclo de vida de producto

2.- Instalación de Zimbra Network 8.8.15 en CentOS 7
Para instalar Zimbra debemos

Instalar un Servidor CentOS 7, Mínimo 8GB de RAM y pornerlo en Zona DMZ con Ip privada; ej: 192.168.X.2
Registrar un dominio y configurar el DNS público con la Ip pública de Zimbra Ver Manual
Cambiar el nombre del hostname del servidor
Editar el /etc/hosts para agregar la ip interna del zimbra con el hostname
Instalar y Configurar un dns interno que resuelva la ip privada de zimbra
Descargar e Instalar Zimbra
2.1.- Registrar un Dominio y configurar un DNS Público con la Ip Pública de Zimbra
Ver Manual

2.2.- Cambiar el hostname del servidor
hostnamectl set-hostname mail.aulautil.tk
exit
Volver a loguearse y verificar el hostname

hostnamectl
2.3.- Editar el /etc/hosts para agregar la ip interna del zimbra con el hostname
Verificar la Ip del Host

ifconfig
ó
ip addr show
vim /etc/hosts
127.0.0.1 localhost.localdomain localhost
192.168.X.2 mail.aulautil.tk mail
2.4.- Instalación de DNS Server Interno DNSMASQ
Instalar un DNS local

yum -y install dnsmasq
Editar el MX del dominio

vim /etc/dnsmasq.d/mail.aulautil.tk.conf
mx-host=aulautil.tk,mail.aulautil.tk,10
Editar el localhost para que dnsmasq solo escuche en 127.0.0.1 y reenvie las consultas a google

vim /etc/dnsmasq.d/localhost.conf
port=53
server=8.8.8.8
bind-interfaces
Reiniciar dnsmasq y habilitar arranque automático

systemctl restart dnsmasq
systemctl enable dnsmasq
Verifica el nombre de la tarjeta de red

ip l
Configurar como dns Cliente, 127.0.0.1 en la tarjeta, ej: ens18

vim /etc/sysconfig/network-scripts/ifcfg-ens18
DNS1=127.0.0.1
systemctl restart network
2.4.1.- Comprobacion de Registro Interno
yum -y install bind-utils

dig MX aulautil.tk
dig mail.aulautil.tk
2.5.- Instalación de Zimbra Network 8.8.15 en CentOS 7
2.5.1.- Registrar trial
Acceder a https://www.zimbra.com/try/zimbra-collaboration-network-edition/ y Registrar trial para obetener una licencia por 60 días en el correo debe llegar adjunto la licencia ZCSLicense.xml

El archivo ZCSLicense.xml se tiene que copiar al servidor vps

scp ~/Descargas/ZCSLicense.xml root@51.68.161.X:
En windows copiar el archivo ZCSLicense.xml con Winscp https://winscp.net/eng/download.php

2.5.2.- Instalar dependencias de paquetes
yum install -y perl perl-core ntpl nmap sudo libidn gmp libaio libstdc++ unzip sysstat sqlite
2.5.3.- Deshabilitar Postfix para que no haga conflicto de puerto
Deshabilitar postfix

systemctl stop postfix
systemctl disable postfix
2.5.4.- Descarga de Zimbra Network Edition 8.8.15
yum -y install wget
Ir a la página https://www.zimbra.com/downloads/zimbra-collaboration/ Y descargar la versión 8.8.15 https://files.zimbra.com/downloads/8.8.15_GA/zcs-NETWORK-8.8.15_GA_3869.RHEL7_64.20190918004220.tgz

wget https://files.zimbra.com/downloads/8.8.15_GA/zcs-NETWORK-8.8.15_GA_3869.RHEL7_64.20190918004220.tgz
Descomprimir los instaladores

tar -zxvf zcs-NETWORK-8.8.15_GA_3869.RHEL7_64.20190918004220.tgz
cd zcs-NETWORK-8.8.15_GA_3869.RHEL7_64.20190918004220
2.5.5.- Instalar Zimbra Network 8.8.15
Antes de Instalar verificar que este configurado bien los requisitos previos

Nombre de Host

hostnamectl
Archivo /etc/hosts

cat /etc/hosts
Resolución DNS local del nombre de host, debe apuntar con la IP privada de zimbra

dig MX aulautil.tk
dig mail.aulautil.tk
Si todo esta bien, instalamos zimbra

./install.sh
Do you agree with the terms of the software license agreement? [N] Y
Do you agree with the terms of the software license agreement? [N] Y

Use Zimbra Packages repositories [Y] Y  

Install zimbra-ldap [Y] Y

Install zimbra-logger [Y] Y

Install zimbra-mta [Y] Y

Install zimbra-dnscache [Y] N

Install zimbra-snmp [Y] Y

Install zimbra-store [Y] Y

Install zimbra-apache [Y] Y

Install zimbra-spell [Y] Y

Install zimbra-convertd [N] Y

Install zimbra-memcached [N] Y

Install zimbra-proxy [N] Y

Install zimbra-archiving [N] Y

Install zimbra-drive [Y] N      ----->  ¡ Reemplazado por zimbra-drive-ng !

Install zimbra-imapd (BETA - for evaluation only) [N] N

Install zimbra-network-modules-ng [Y]

Install zimbra-talk [N] Y

The system will be modified.  Continue? [N] Y

Change domain name? [Yes] Yes
Create domain: [mail.aulautil.tk] aulautil.tk

--------------------------- Opciones del Menu 1 -------------------------
Address unconfigured (**) items  (? - help) 1

Select, or 'r' for previous menu [r] 7
Enter the number for the local timezone: [23] 18          (America/Bogota)
Select, or 'r' for previous menu [r] 4 (Opcional)
Password for ldap admin user (min 6 characters): [TVwckVm4Y] Tuxito1.23

Select, or 'r' for previous menu [r] r

--------------------------- Opciones del menu 6 ------------------------

Address unconfigured (**) items  (? - help) 6

Select, or 'r' for previous menu [r] 4

Password for admin@aulautil.tk (min 6 characters): [gpLZfFUr] Tuxito1.23

Select, or 'r' for previous menu [r] 25

Enter the name of the file that contains the license: /root/ZCSLicense.xml

Select, or 'r' for previous menu [r]

-------------------------- Opciones del Menu 2 (OPCIONAL) --------------------------
Address unconfigured (**) items  (? - help) 2 (OPCIONAL) Claves de Servicio

elect, or 'r' for previous menu [r] 4

Password for ldap root user (min 6 characters): [TVwckVm4Y] Tuxito1.23

Select, or 'r' for previous menu [r] 5

Password for ldap replication user (min 6 characters): [TVwckVm4Y] Tuxito1.23

Select, or 'r' for previous menu [r] 6

Password for ldap Postfix user (min 6 characters): [TVwckVm4Y] Tuxito1.23

Select, or 'r' for previous menu [r] 7

Password for ldap Amavis user (min 6 characters): [TVwckVm4Y] Tuxito1.23

Select, or 'r' for previous menu [r] 8

Password for ldap Nginx user (min 6 characters): [TVwckVm4Y] Tuxito1.23

Select, or 'r' for previous menu [r] 9

Password for ldap BES user (min 6 characters): [TVwckVm4Y] Tuxito1.23

Select, or 'r' for previous menu [r]

---------------------------- Finalizamos la instalacion -----------------------
Select from menu, or press 'a' to apply config (? - help) a
Save configuration data to a file? [Yes] Yes
Save config in file: [/opt/zimbra/config.14717]   Enter
Saving config in /opt/zimbra/config.14717...done.

The system will be modified - continue? [No] Yes  [-----  CUIDADO ES TRAIDOR

Configuration complete - press return to exit     Enter
2.5.5.1. Reinstalación de Zimbra, si la instalación falló; (sólo si falló)
Desinstalar Zimbra

cd
cd zcs-NETWORK-8.8.15_GA_3869.RHEL7_64.20190918004220
./install.sh -u
rm -fR /opt/zimbra
yum erase zimbra*
Volver a instalar

./install.sh
2.5.6.- Permitir el acceso local a los puertos del zimbra
yum -y install firewalld
systemctl start firewalld
systemctl enable firewalld
Habilitar los puertos TCP 22 443 25 110 143 993 995 465 8443 7071

firewall-cmd --add-service http --permanent
firewall-cmd --add-service https --permanent
firewall-cmd --add-service smtp --permanent
firewall-cmd --add-service imaps --permanent
firewall-cmd --add-service pop3s --permanent
firewall-cmd --add-port 110/tcp --permanent
firewall-cmd --add-port 143/tcp --permanent
firewall-cmd --add-port 465/tcp --permanent
firewall-cmd --add-port 8443/tcp --permanent
firewall-cmd --add-port 7071/tcp --permanent

firewall-cmd --reload
Verificar los puertos abiertos

iptables -S
Acceder al webmail de Zimbra
https://mail.aulautil.tk

2.5.6.1.- Puertos de servicio de Zimbra
25  --> smtp tls
110 --> pop3 tls
143 --> imap tls
80  --> http
443 --> https
465 --> smtp ssl
587 --> smtp tls
993 --> imap ssl
995 --> pop3 ssl
7071 -> panel zimbra
8443 -> webmail zimbra
2.5.6.2.- Natear los puertos de servicio adicionales desde el SophosXG hacia el Zimbra
Acceder al firewal Sophos XG por la IP Pública https://51.68.161.X

2.6.- Modificar las reglas de NAT hacia el servidor CentOS en DMZ
IrPROTEGER --> Reglas y Políticas
    Reglas de Firewall 
        Click en regla "Nat Zimbra Network" ... Editar
            Descripción: "Nat de servicio de Zimbra Network"
            DESTINO y Servicios
                Servicios
                    SMTP
                    HTTP
                    HTTPS
                    IMAP
                    POP3
                    IMAPS
                    POP3S
                    SMTP_465
                    SSH --> opcional

                    Agregar nuevos servicios en el firewall
                        Nombre:           SMTP_TSL
                        Puerto origen:    1:65535
                        Puerto Destino:   587

                        Nombre:           ZIMBRA_ADMIN  --> opcional
                        Puerto origen:    1:65535
                        Puerto Destino:   7071

                        Nombre:           ZIMBRA_WEBMAIL
                        Puerto origen:    1:65535
                        Puerto Destino:   8443
        Guardar

    Reglas de NAT
        Click en regla "Nat Zimbra Network"  ... Editar
            Descripción: "Nat de servicio de Zimbra Network"

            Configuración de traducción

                Servicio original:              SSH, SMTP, HTTP, HTTPS, IMAP, POP3, SMTP_465, SMTP_TLS, POP3S, IMAPS, ZIMBRA_ADMIN, ZIMBRA_WEBMAIL

        Guardar

2.7.- Acceder al zimbra Network instalado con el navegador
PANEL DE ADMINISTRACION
https://mail.aulautil.tk:7071

usuario: admin
clave: Tuxito1.23

WEBMAIL
https://mail.aulautil.tk

usuario: admin
clave: Tuxito1.23

3.- Instalación de Parches de Zimbra Network versión 8.8.15 y superiores
Debemos realizar un backup completo de zimbra, ya sea por snapshot de VM, o backup de Suite Plus

Luego ejecutar actualización del CentOS (esto actualiza y parcha zimbra)

yum -y update
Nota En caso que se actualice el kernel será necesario reiniciar el servidor.

Reiniciar Zimbra

su - zimbra -c "zmcontrol restart"
Verificar el estado de servicios

su - zimbra -c "zmcontrol status"
Verificar la versión instalada de zimbra

su - zimbra -c "zmcontrol -v"
3.1.- Parche de Zimbra en un entorno multiservidor
Si nuestro entorno de despliegue de Zimbra es multiservidor hay que identificar las funciones que realiza cada servidor

Parche del MTA

yum -y install zimbra-mta-patch
Parche del Proxy

yum -y install zimbra-proxy-patch
Parche del Mailbox

yum -y install zimbra-patch
zmcontrol restart
Para saber que parches últimos se han publicado mirar en https://blog.zimbra.com/


4.- Certificados Digitales Letsencrypt para Zimbra Network
Los certificados Letsencrypt son certificados comerciales gratuitos que se renuevan cada 3 meses y ya son reconocidos por los navegadores y clientes de correo actuales. Para instalar un certificado letsencrypt se requiere que el servidor zimbra esté publicado a internet con una IP pública y resolución de DNS; ya sea directa o a través de NAT

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

certbot -d mail.aulautil.tk -m usuario@tucorreoactivo.com --force-renewal --preferred-chain "ISRG Root X1" certonly
- Seleccionar opción 2 "Spin up a temporary webserver (standalone)": 2
Please read the Terms ...
(A)Agree/(C)Cancel: A
(Y)es/(N)o: Y
Si fuera mas de un dominio

certbot -d mail.aulautil.tk,mail.dominioY.com -m usuario@tucorreoreal.com --force-renewal --preferred-chain "ISRG Root X1" certonly
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

4.1.- Desplegar el certificado letsencrypt en Zimbra Network
Cambiamos de dueño al directorio letsencrypt

chown  zimbra /opt/zimbra/ssl/zimbra/commercial/commercial.key
Desplegamos el certificado

su - zimbra
cd /etc/letsencrypt/live/$HOSTNAME
/opt/zimbra/bin/zmcertmgr deploycrt comm cert.pem chain.pem
zmcontrol restart
Acceder al webmail

https://mail.aulautil.tk

Acceder al panel de administración

https://mail.aulautil.tk:7071

4.2.- Validación de la seguridad del certificado
Entramos a la página https://www.digicert.com/help/ para validar la fortaleza de nuestro certificado generado.
Ponemos el hostname: mail.aulautil.tk y click en «Check Server»
Si todo sale en verde, tenemos el certificado instalado correctamente.

4.3.- Script de Instalación y Renovación de Certificado LetsEncrypt para Zimbra Network en CentOS y Ubuntu
Crearemos ahora un script de renovación automática del certificado de todos los dominios activos; con resolución DNS pública, que esten alojados en zimbra.

vim /usr/bin/renuevacertificado
#!/bin/bash
# Script de renovación de certificados Letsencrypt para Zimbra en CentOS y Ubuntu 
# creado para el curso de zimbra de Aula Útil https://aulautil.com/curso/zimbra
# Copyrigth 2021 Clever Flores,  CC Reconocimiento - No Comercial - Compartir Igual (by-nc-sa)
# clever@aulautil.com

if [ -d "/etc/apt" ]; then
    ISCERTBOT=`dpkg -l|grep certbot`
    if [ -z "$ISCERTBOT" ]; then
       apt -y update
       apt -y install certbot ca-certificates python3-certbot-apache
    fi
else
    ISCERTBOT=`rpm -qa|grep certbot`
    if [ -z "$ISCERTBOT" ]; then
        yum -y install certbot python-certbot-apache ca-certificates
    fi
fi

if [ -z "$1" ]; then
    ACTUALIZAR=`certbot certificates|grep "VALID: 2 days"`
    if [ -z "$ACTUALIZAR" ]; then
        echo "Aún no es tiempo de actualizar el certificado, esperar a 2 días antes que venza"
        exit 0
    fi
else
    if [ "$1" = "forzar" ]; then
        rm -fR /etc/letsencrypt
    fi
fi

IPPUBLICA=`curl ifconfig.me`
echo "Obteniendo Lista de dominios activos alojados en Zimbra"
DOMS=`su - zimbra -c "zmprov -l gad"`
DOMAINS=""

for d in $DOMS
do
        if [[ "mail.$d" != "$HOSTNAME" ]]
        then
            digdomain=`dig "mail.$d" @8.8.8.8|grep $IPPUBLICA`
            if  [[ -z "${digdomain// }" ]]
            then
                 echo  "mail.$d" no esta alojado aquí
            else
                 DOMAINS=$DOMAINS"-d mail.$d "
            fi
        fi
done

if [ -n "$DOMAINS" ]; then
  DOMAINS=${DOMAINS::-1}
  DOMAINS="$HOSTNAME $DOMAINS"
else
  DOMAINS="$HOSTNAME"
fi

echo "deteniendo apache"
if [ -d "/etc/apt" ]; then
    systemctl stop apache2
else
    pkill -9 httpd
fi

echo "deteniendo el proxy de Zimbra"
su - zimbra -c "zmproxyctl stop"

echo "actualizando los CA"
if [ -d "/etc/apt" ]; then
    update-ca-certificates
else
    update-ca-trust force-enable
fi

echo "generando el certificado letsencrypt con soporte multidominio"
echo "certbot -t --non-interactive --agree-tos --standalone  -d $DOMAINS -m admin@$HOSTNAME --force-renewal --preferred-chain 'ISRG Root X1' certonly"
certbot -t --non-interactive --agree-tos --standalone  -d $DOMAINS -m admin@$HOSTNAME --force-renewal --preferred-chain "ISRG Root X1" certonly

echo "instalando y desplegando el certificado letsencryot"
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
-----END CERTIFICATE-----">> /etc/letsencrypt/live/$HOSTNAME/fullchain.pem

chown zimbra -R /etc/letsencrypt
cp /etc/letsencrypt/live/$HOSTNAME/privkey.pem /opt/zimbra/ssl/zimbra/commercial/commercial.key
chown  zimbra /opt/zimbra/ssl/zimbra/commercial/commercial.key
su - zimbra -c "/opt/zimbra/bin/zmcertmgr deploycrt comm /etc/letsencrypt/live/$HOSTNAME/cert.pem /etc/letsencrypt/live/$HOSTNAME/chain.pem"

echo "reiniciando servicios de zimbra"
su - zimbra -c "zmcontrol restart"

echo "reiniciando apache"
if [ -d "/etc/apt" ]; then
    systemctl restart apache2
else
    systemctl restart httpd
fi
chmod 755 /usr/bin/renuevacertificado
Probar el script

renuevacertificado forzar
Probar nuevamente

renuevacertificado
Programar la renovación automática en el cron a la 1:00 a.m.

crontab -e
0 1 * * * renuevacertificado

5.- Upgrade de Zimbra Network a su ultima version 9.0
El upgrade se tiene que hacer con cuidado con un backup previo, porque puede fallar Revisar procedimiento de backup de Imagen de la guía del curso

Verificar la version de zimbra actual

su - zimbra -c "zmcontrol -v"
Verificar estado de servicios

su - zimbra -c "zmcontrol status"

5.1.- Upgrade a Zimbra Network 9.0
Descargar de https://www.zimbra.com/downloads/zimbra-collaboration/

wget https://files.zimbra.com/downloads/9.0.0_GA/zcs-NETWORK-9.0.0_GA_3924.RHEL7_64.20200331010312.tgz
tar -zxvf zcs-NETWORK-9.0.0_GA_3924.RHEL7_64.20200331010312.tgz
cd zcs-NETWORK-9.0.0_GA_3924.RHEL7_64.20200331010312
./install.sh
Do you agree with the terms of the software license agreement? [N] Y
Do you agree with the terms of the software license agreement? [N] Y
Do you wish to continue without a backup? [N] Y
Do you want to verify message store database integrity? [Y] Y

Use Zimbra Packages repositories [Y] Y   ------> Mucho cuidado con esto si falla, se muere tu zimbra

Do you wish to upgrade? [Y] Y

Install zimbra-dnscache [N] N

Install zimbra-drive [N] N ---> Drive OpenSource (NextCloud) con la interfaz vieja

Install zimbra-imapd (BETA -for evaluation only) [N] N ---> prueba fallida

Install zimbra-connect [Y] Y ----> Suite de Chat y Videoconferencia

The system will be modified.  Continue? [N] Y  ------> Mucho cuidado con esto si falla, se muere tu zimbr

Configuration complete - press return to exit
Verificar versión de zimbra

su - zimbra -c "zmcontrol -v"
Verificar estado de servicios

su - zimbra -c "zmcontrol status"
NOTA
Si en el proceso de actualización no logra desplegar los zimlets o no logra culminar exitosamente se puede intentar recuperar el upgrade volviendo a ejecutar el install.sh

cd zcs-NETWORK-9.0.0_GA_3924.RHEL7_64.20200331010312
./install.sh
Una vez terminada el upgrade, parchar zimbra con sus últimas actualizaciones, tal como se ha descrito en el paso 3
