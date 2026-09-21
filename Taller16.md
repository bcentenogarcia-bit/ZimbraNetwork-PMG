Zimbra drive NG, zimbra docs, zimbra connect, 2FA
1.- Instalar Zimlet Drive NG y deshabilitar OpenDrive
En zimbra Network se agregó una nueva versión de Drive nativo para zimbra Si tenemos el zilmet de drive open lo desinstalamos y luego instalamos el zimbra-drive-ng

Desinstalar el zimbra drive open

su - zimbra -c "zmzimletctl disable com_zextras_drive_open"
su - zimbra -c "zmzimletctl undeploy com_zextras_drive_open"
yum -y erase zimbra-drive
su - zimbra -c "zmmailboxdctl restart"
1.1.- Instalar zimbra-drive-ng
Primero instalamos como dependencia libreoffice

yum -y install libreoffice-headless libreoffice
Luegos instalamos zimbra drive, que instalará tambien el zimbra-docs cliente

yum -y install zimbra-drive-ng
su - zimbra
zmmailboxdctl restart
sleep 20
zxsuite config global set attribute isDriveEnabledOnStartup value true
zxsuite drive doStartService module
Acceder al panel de Administración y Modificar las Clases de Servicio y Habilitar zimlets drive y docs
Luego refrescar el panel de administración y el Webmail, ya debemos tener acceso al Drive

Nota
Si luego de habilitar los zimlets en una Clase de Servicio sigue sin aparecer el drive en la cuenta; entonces debemos editar directamente la cuenta y la opción Zimlets habilitar los zimlets drive y docs

2.- Instalación de Zimbra Docs
2.1.- Abrir el puerto 389 de Zimbra Network
En el servidor zimbra abrir el puerto 389

firewall-cmd --add-service=ldap --permanent
firewall-cmd --reload
2.2.- Instalar CentOS para servidor docs
Instalar un servidor Centos 7 con una IP privada 192.168.X.3

2.3.- Configurar salida a internet del servidor Zimbra Docs
Conectarse y loguearse al firewall Sophos XG

2.3.1.- Regla de Salida para Zimbra Docs
PROTEGER --> Reglas y Políticas
    Reglas de Firewall 
        Click en "Añadir regla de Firewall"
            Nueva Regla de Firewall
                Nombre de regla:                Salida Zimbra Docs
                Posición de la regla:           Arriba
                Grupo:                          Zimbra Network
                Acción:                         Aceptar
                Registrar tráfico de firewall:  CHECK

                ORIGEN
                    Zonas de origen:                DMZ
                    Dispositivos y Redes de Origen:
                        Añadir Nuevo Elemento
                                Crear Nuevo: IP
                                        Nombre: 192.168.X.3_Zimbra_Docs
                                        Direccion IP: 192.168.X.3
                                Guardar
                    Durante la hora programada:     Siempre

                DESTINO y Servicios
                    Zona de Destino:                WAN
                    Redes de Destino:               Cualquiera
                    Servicios:                      Cualquiera

                Crear Regla NAT Vinculada:          Dar Click
                    Nombre de la Regla:                 Salida Zimbra Docs
                    Posición de la Regla:                Arriba

                    Configuración de traducción
                        Origen Traducido (SNAT):    MASQ 
                    Guardar
            Guardar
Actualizar los paquetes

yum -y update
yum -y install vim wget net-tools bash-completion
reboot
2.4.- Configurar los archivos /etc/hosts de ambos servidores
vim /etc/hosts
127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4
::1         localhost localhost.localdomain localhost6 localhost6.localdomain6
192.168.X.2 mail.aulautil.tk mail
192.168.X.3 docs.aulautil.tk docs
192.168.X.10 relay.aulautil.tk relay
Reiniciar el dnsmasq del servidor Zimbra

systemctl restart dnsmasq
2.5.- Cambiar el nombre del host de Zimbra Docs
hostnamectl set-hostname docs.aulautil.tk
cerrar la sesión y volver a loguearse, verificar el nombre de host

hostname
2.6.- Instalar Zimbra Docs
Una vez instalado el servidor CentOS accedemos como root en el servidor docs

Descargar y descomprimir el zimbra docs

wget https://files.zimbra.com/downloads/zimbra-docs-server/6.4.8/zimbra-docs-centos7.tgz
tar -zxvf zimbra-docs-centos7.tgz
Instalar Zimbra docs

cd zimbra-docs-installer/
Instalar zimbra-docs

./install.sh
This script will install Zimbra DOCS, and all its runtime
dependencies onto the running system.

To complete the process a working network connection is required.

System will be modified, would you like to proceed? [Y]
python-ldap not found. Would you like to install it? [Y]

Would you like to install Zimbra DOCS? [Y]

LDAP url (None): ldap://mail.aulautil.tk:389

LDAP dn (uid=zimbra,cn=admins,cn=zimbra): uid=zimbra,cn=admins,cn=zimbra

LDAP password (None): Tuxito1.23  {--- Si no se sabe la clave obtenerla con zmlocalconfig -s |grep zimbra_ldap_password
Reiniciar los units de systemd

systemctl daemon-reload
Actualizar las fuentes

fc-cache /opt/zimbra/docs/zextras-docs-core/share/fonts
Abrir localmente el puerto 9980

firewall-cmd --add-port=9980/tcp --permanent
firewall-cmd --reload
2.7.- Regenerar configuración del Zimbra Proxy
Una vez instalado regenerar configuración del proxy en el servidor Zimbra Ejecutar en el servidor Zimbra Network

su - zimbra
/opt/zimbra/libexec/zmproxyconfgen
zmproxyctl restart
Probar ahora el funcionamiento del zimbra docs creando documentos odt desde el ícono drive del Webmail.

3.- Zimbra Connect
Se tiene que tener una licencia activa para zimbra connect, se puede comprobar esto con el comando

su - zimbra
zxsuite core getLicenseInfo
Verificar ahí las licencias disponibles en zxChat y las licencias activas en teamchatActiveCount

3.1.- Instalación y Configuración de Zimbra Connect
yum install zimbra-connect
3.2.- Migrar de Zimbra Talk (versión anterior) a Zimbra Connect
su - zimbra
zxsuite connect doImportChannels
3.3.- Habilitar el módulo Connect por clase de servicio
Podemos habilitar el módulo connect por usuario o COS

Acceder al panel de administración

Configuración
    Clase de Servicio
        default
            Editar
                Connect
                    Habilitar funcionalidad Connect: Poner en check
                Guardar
Luego crear un usuario; ej: tuxito@aulautil.tk

Si accedemos al webmail veremos que tiene opción para realizar videollamadas en el módulo Connect

3.4.- Configuración de un servidor TURN para las videollamadas
Por necesidad del protocolo WebRTC se requiere un servidor TURN para mantener las videollamadas entre diferentes redes

Usaremos Coturn y lo configuraremos para su uso en Zimbra Connect

wget http://coturn.net/turnserver/v4.5.0.7/turnserver-4.5.0.7-CentOS7.4-x86_64.tar.gz
tar -zxvf turnserver-4.5.0.7-CentOS7.4-x86_64.tar.gz
cd turnserver-4.5.0.7
./install.sh
Definir variables para el dominio y clave

DOMAIN=aulautil.tk
clave=`cat /dev/urandom | tr -cd 'a-f0-9' | head -c 64`
echo $clave
Crear archivo de configuración

echo "listening-port=7443
tls-listening-port=5349
fingerprint
lt-cred-mech
use-auth-secret
static-auth-secret=$clave
realm=mail.$DOMAIN
total-quota=0
bps-capacity=0
stale-nonce
cert=/etc/letsencrypt/live/mail.$DOMAIN/fullchain.pem
pkey=/etc/letsencrypt/live/mail.$DOMAIN/privkey.pem
cipher-list='ECDH+AESGCM:DH+AESGCM:ECDH+AES256:DH+AES256:ECDH+AES128:DH+AES:ECDH+3DES:DH+3DES:RSA+AES:RSA+3DES:!ADH:!AECDH:!MD5'
log-file=/var/log/turnserver/turn.log
no-loopback-peers
no-multicast-peers" >> /etc/turnserver/turnserver.conf
Crear carpetas, habilitar servicio y abrir puerto 7443

mkdir -p /var/log/turnserver
chmod 777 /var/log/turnserver
systemctl start turnserver
systemctl enable turnserver
firewall-cmd --add-port=7443/tcp --permanent
firewall-cmd --add-port=7443/udp --permanent
firewall-cmd --reload
3.5.- Configurar servidor Turn para zimbra connect
Natear el puerto 7443 tcp/udp hacia el zimbra

Loguearse a SophosXG

Ir PROTEGER --> Reglas y Políticas --> Reglas de Firewall
        Abrir Grupo Zimbra Nerwork
                Editar regla "NAT Zimbra Network"
                        Ir a Destino y Servicios
                                Servicios
                                        Añadir Nuevo Elemento
                                                Crear Nuevo --> Servicios
                                                        Nombre:         COTURN
                                                        Protocolo:      TCP/UDP
                                                        Puerto Destino: 7443
                Guardar

Ir PROTEGER --> Reglas y Políticas --> Reglas de Firewall
        Editar regla "NAT Zimbra Network"
            Configuración de traducción
                Servicio original
                    Añadir: COTURN
        Guardar
Ejecutar

su - zimbra -c "zxsuite connect iceServer add turn:mail.aulautil.tk:7443?transport=udp credential $clave"
Obtener servidores turn configurados

su - zimbra -c "zxsuite connect iceServer get"
Probar las videoconferencias

4.- Doble Factor de Autenticación (2FA)
Se tiene que tener una licencia activa para 2FA, se puede comprobar esto con el comando

su - zimbra
zmlicense -p | egrep -i 'Twofactor'
Habilitar el Doble Factor por Clase de Servicio

Seleccionar una clase de Servicio; ej: gerente
    Ir a Avanzado
        Autenticación de Dos Factores
            Activar Autenticación de Dos Factores:                                              (Check)
                Requiere Autenticación de dos pasos:                                           (NO Check)
                Número de códigos para un solo acceso para generar:            10
                Activar códigos de acceso de aplicación:                                       (Check)
    Guardar
4.1.- Habilitar la autenticación de dos factores desde el Webmail
Acceder como gerentazo al webmail

Click en la rueda
    Configuración
        Cuentas
            click en  🮦
                Scroll hasta "Autenticación de dos factores"
                    click en botón "Configurar la autenticación de dos factores"
                        Contraseña actual de gerentazo: Lolcito.123
Nos pide descargar una aplicación en nuestro celular, la lista de aplicaciones compatibles estan en
https://wiki.zimbra.com/wiki/TOTPApps

Probaremos con Authy

Instalamos Authy en el smartphone
    Agregar Cuenta

En el Webmail le damos siguiente y generará un código QR
    Escanear el código QR con Authy
        En authy se agrega cuenta Zimbra y genera códigos
        Poner código del Authy en el Web y se terminará la configuración del 2FA
Cerramos la sesión del Webmail y volver a Loguearse con gerentazo
Nos pide ahora que ingresemos el doble factor con Authy
