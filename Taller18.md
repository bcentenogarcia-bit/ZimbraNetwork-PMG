Configuración de Split Domain y Policyd Server e Integración a Active Directory
Split Domain entre Zimbra Open Source y Zimbra Network
1.- Introducción a Split Domain
Una de las funcionalidades interesante de zimbra, es que puede trabajar en modo split domain para poder trabajar en modo cruzado con un dominio alojado en diferentes tipos de servidores MTA y cumpliendo rol de servidor primario o secundario; como por ejemplo

zimbra opensource – zimbra network
gmail – zimbra
office 365 – zimbra.
2.- Esquema de Split Domain Zimbra Network – Zimbra Open Source
Bajo nuestros servidores configurados vamos a desarrollar el esquema para zimbra network y open source

Esquema de Split domain con PMG –> Zimbra Network —> Zimbra Open Source

                  |MTA Externo|
                            |
                            |
                ---------------------- 
               | SophosXG             |  NAT o (DLP, encriptación canal, doble antivirus)
                ----------------------
                            |
                            |
                ----------------------
               | Proxmox Mail Gateway |  relay.aulautil.tk
                ----------------------
                      |
                      |
                      |
                  -----------                ----------
                 |Zimbra     |              |Zimbra    |
                 |OpenSource |---->         |Network   |
                  -----------                ----------
             smtp.aulautil.tk       mail.aulautil.tk
2.1.- Flujo de Recepción desde internet
Cuando un MTA externo envía al split domain, se realizará el siguiente flujo.

El correo llega por el MX a PMG y lo envía al mta de Zimbra Open Source.
Zimbra Open Source verifica que el correo es con destino a una de sus cuentas sin reenvío lo acepta.
Zimbra Open Source verifica que el correo es con destino a una de sus cuentas con reenvío, se lo pasa al Zimbra Network.
Si Zimbra OpenSource no encuentra que el correo este dirigida a una de sus cuentas, rebota el correo a PMG y este al MTA externo
Si Zimbra Network verifica que el correo es con destino a una de sus cuentas lo acepta.
2.2.- Flujo de Envío del propio dominio Zimbra Network – Zimbra Open Source
Zimbra Network envía a una de sus cuentas de su propio dominio y si esta en su lista de cuentas lo acepta.
Si Zimbra Network envía a una de sus cuentas de su propio dominio y no la encuentra; lo reenvía a Zimbra Open Source.
2.3.- Flujo de Envío del propio dominio Zimbra Open Source – Zimbra Network
Zimbra Open Source verifica que el correo es con destino a una de sus cuentas sin reenvío, lo acepta.
Sí Zimbra Open Source verifica que el correo es con destino a una de sus cuentas con reenvío, se lo pasa al Zimbra Network.
2.4.- Flujo de Envío hacia internet
Zimbra Network envía a internet, usa como relay a PMG
Zimbra Open Source envía a internet, usa como relay a PMG.
3.- Configuración de los Registros de DNS
Configurar en el panel del register del dominio

aulautil.tk.   IN  MX 10 relay.aulautil.tk
aulautil.tk.   IN TXT "v=spf1 a mx -all"
4.- Reconfigurar el reenvío en Proxmox Mail Gateway.
Verificar en el firewall que la IP pública del relay natee hacia la IP privada del relay en el puerto 25; luego dentro de Proxmox Mail Gateway debemos reenviar los correos ya no al Zimbra Network, si no al Zimbra Open Source, por ser el servidor primario para ello debemos declarar el host del Open Source en el /etc/hosts del relay

vim /etc/hosts
127.0.0.1   localhost localhost.localdomain
192.168.X.2 mail.aulautil.tk mail
192.168.X.4 smtp.aulautil.tk smtp
192.168.X.10 relay.aulautil.tk relay
Reiniciar el dnsmasq

systemctl restart dnsmasq
Luego cambiamos el reenvío del dominio

Configuración ---> Proxy de correo ----> Transportes
    Dominio de retransmisión: aulautil.tk
    Host: smtp.aulautil.tk
    Comentario: Zimbra Open Source
Agregamos en las redes de confianza la IP del servidor MTA de OpenSource

Configuración ---> Proxy de correo ----> Redes ---> Crear
    Red de Confianza: 192.168.X.4/32
    Comentario: smtp.aulautil.tk
5.- Cambiar los /etc/hosts de los servidores zimbra (Open Source y Network)
vim /etc/hosts
127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4
::1         localhost localhost.localdomain localhost6 localhost6.localdomain6
192.168.X.2 mail.aulautil.tk mail
192.168.X.3 docs.aulautil.tk docs
192.168.X.10 relay.aulautil.tk relay
192.168.X.4 smtp.aulautil.tk smtp
192.168.X.5 mailbox1.aulautil.tk mailbox1
192.168.X.6 mailbox2.aulautil.tk mailbox2
Reiniciar el dnsmasq

systemctl restart dnsmasq
6.- Configuración de Zimbra Open Source como servidor primario del dominio
Se deben crear todas las cuentas alojadas en zimbra network dentro de Zimbra Open Source, para luego cambiar su transporte de destino hacia el Zimbra Network. No lo hacemos alreves para no consumir licencias del Zimbra Network.

Ej: Crear tuxito en zimbra Open Source, que ya esta alojado en Zimbra Network

zmprov ca tuxito@aulautil.tk Lolcito.123 displayName "Tuxito Linuxero"
Luego definir su ruta de entrega reenviando hacia el zimbra network

zmprov ma tuxito@aulautil.tk zimbraMailTransport smtp:mail.aulautil.tk:25
Ej: Crear gerentazo en zimbra Open Source, que ya esta alojado en Zimbra Network

zmprov ca gerentazo@aulautil.tk Lolcito.123 displayName "Gerentazo Maloso"
Luego definir su ruta de entrega reenviando hacia el zimbra network

zmprov ma gerentazo@aulautil.tk zimbraMailTransport smtp:mail.aulautil.tk:25
7.- Configuración de Zimbra Network como servidor secundario del dominio
El sistema secundario debe aceptar el correo de las cuentas que están alojadas en el secundario (Zimbra Network), pero debe reenviar todos los demás correos de las cuentas de este dominio al sistema primario (Zimbra Open Source). En un servidor zimbra, puede realizar este enrutamiento de correo con estos comandos

zmprov md aulautil.tk zimbraMailCatchAllAddress @aulautil.tk
zmprov md aulautil.tk zimbraMailCatchAllForwardingAddress @aulautil.tk
zmprov md aulautil.tk zimbraMailTransport smtp:smtp.aulautil.tk
8.- Configuración de Zimbra Open Source para que use como relay a PMG
Dentro de Zimbra Open Source cambiamos el relay

zmprov ms $HOSTNAME zimbraMtaRelayHost relay.aulautil.tk:26
zmmtactl restart
9.- Configuración de Zimbra Network para que use como relay a PMG
Dentro de Zimbra Network cambiamos el relay, para que todos sus correos que no son de sus dominios, los reenvíe a través de PMG

zmprov ms $HOSTNAME zimbraMtaRelayHost relay.aulautil.tk:26
zmmtactl restart
10.- Configuración del GAL en Zimbra Network
El Zimbra Network necesita mapear todos los usuarios del dominio, pero por defecto solo tiene esa información de su GAL interno; es por ello que en el Zimbra Network modificaremos la configuración de GAL del Dominio para que lo lea del LDAP del Zimbra Open Source.

Ej: Crear usuario linuxito en zimbra Open Source para probar la integración del GAL

zmprov ca linuxito@aulautil.tk Lolcito.123 displayName "Linuxito Zimbra"
Procedimiento 1
Entrar al panel del Zimbra Network

  Ir a Configurar --> Dominios
    Seleccionar "aulautil.tk" --> Click Derecho ---> "Configurar GAL"
        Modo de GAL "externo"
            Tipo de Servidor: LDAP
            Nombre del servidor: smtp.aulautil.tk
            Puerto: 389
            Filtro LDAP:  (&(|(cn=%s*)(sn=%s*)(gn=%s*)(mail=%s*)(displayName=%s*)))
            Filtro de Autoconpletado:  (&(|(cn=%s*)(sn=%s*)(gn=%s*)(mail=%s*)(displayName=%s*)))
            Base de Búsqueda LDAP: dc=aulautil,dc=tk
            Siguiente
        Configuración de DN del GAL
            Utiliza el DN/contraseña para asociar al Servidor: CHECK 
            DN de Enlace: uid=zimbra,cn=admins,cn=zimbra
            Contraseña:  XXXXXXXX  -----> Ejecutar como usuario zimbra en el smtp.aulautil.k
                                          # zmlocalconfig -s |grep zimbra_ldap_password
        Configuración de modo de sincronización GAL
            Utiliza los parámetros de búsqueda de GAL para sincr. de GAL: CHECK
        Resumen de la configuración
            Introduce una palabra de búsqueda: linuxito
            click en "Prueba"
    Finalizar        
Probar entrando al Webmail (refresca) de Zimbra Network y ver si autocompleta linuxito

Procedimiento 2
Si lo anterior no sincroniza el GAL crearemos la cuenta GAL por consola y la sincronizaremos con el LDAP del Open Source
Ejecutar en Zimbra Network

Loguearse como zimbra y obtener la cuenta GAL y la borramos

su - zimbra
zmprov -l gaa|grep -i gal
zmgsautil deleteAccount -a galsync.XXXXX@aulautil.tk
Crear cuenta GAL external

zmgsautil createAccount -a galsync@aulautil.tk -n ExternalGAL --domain aulautil.tk -s mail.aulautilzimbra.tk -t ldap -f _ExternalGAL -p 1d
Sincronizar el GAL externo con los contacto del Zimbra Open

zmprov mds galsync@aulautil.tk ExternalGAL zimbraGalSyncLdapBindDn uid=zimbra,cn=admins,cn=zimbra zimbraGalSyncLdapBindPassword Tuxito1.23 zimbraGalSyncLdapFilter '(|(cn = %s*)(sn=%s*)(uid=%s)(mail=%s*)(zimbraMailDeliveryAddress = %s*)(zimbraMailAlias=%s*)(zimbraMailAddress = %s*))' zimbraGalSyncLdapSearchBase dc=aulautil,dc=tk zimbraGalSyncLdapURL ldap://smtp.aulautil.tk:389
Habilitar la cuenta GAL externa

zmprov mds galsync@aulautil.tk ExternalGAL zimbraDataSourceEnabled TRUE
Forzar la sincronización de contactos externos del GAL

zmgsautil forceSync -a galsync@aulautil.tk -n ExternalGAL
Probar entrando al Webmail (refrescando) de Zimbra Network y ver si autocompleta linuxito

11.- Pruebas de envío y recepción del Split Domain
Realizar las siguiente pruebas

Enviar correo desde zimbra OpenSource a una cuenta de Zimbra Network
Enviar correo desde zimbra Network a una cuenta de Zimbra Open Source
Enviar correo desde internet a una cuenta de Zimbra Open Source
Enviar correo desde internet a una cuenta de Zimbra Network
Enviar correo desde Zimbra Network a una cuenta de Internet
Enviar correo desde Zimbra Open Source a una cuenta de Internet
Revisar los logs de los servidores ZImbra; así como el tracking center de PMG

12.- Policyd Server
12.1.- Introducción a Policyd Server
Cluebringer Policyd (Policyd V2) es un servicio que se usa para implementar políticas de envío y recepción a nivel de MTA. Estas son algonas de sus funciones:

Access Control: Simple access control on all the incoming/outgoing
Accounting: Message count and cumulative size policies
Amavis: Amavisd-new integration
CheckHelo: Helo/Ehlo checks and blacklisting
CheckSPF: Sender Policy Framework support
Greylist: Anti-spam Greylisting support
Quotas: Message count and cumulative size policies
En el sitio Web de policyd podemos ver información detallada de sus módulos https://wiki.policyd.org/documentation

Policyd se incluyó en zimbra a partir de la versión 7.1 de Zimbra, se añadió este servicio; sin embargo viene deshabilitado por defecto y hay que habilitarlo mediante comandos. Es importante recalcar que policyd actúa a nivel del MTA, quiere decir que no realizar filtrado de contenido; para ello esta Amavis o MailScanner. Una de sus ventajas es que cuando por ejemplo policyd rechaza un correo ya no hay necesidad de analizar el mensaje, mejorando la performance del servidor de correo.

12.2.- Configuración de Policyd Server en Zimbra Network
Vamos a configurar Policyd en el zimbra Network; el policyd puede usarse en cualquier versión de zimbra (Open Source o Network)

Ejecutar en el Zimbra Network, nos logueamos como zimbra

su - zimbra
comprobamos si esta instalado Policyd y si existe el servicio

zmprov gs `zmhostname` | grep zimbraServiceInstalled
Instalamos el servicio de Policyd, en caso de no estar activo

zmprov ms `zmhostname` +zimbraServiceInstalled cbpolicyd
Si entramos a la consola de administración de Zimbra veremos que es servicio esta listado con el resto pero esta deshabilitado.

habilitamos el servicio de Policyd

zmprov ms `zmhostname` +zimbraServiceEnabled cbpolicyd
comprobamos que fue habilitado correctamente

zmprov gs `zmhostname` | grep zimbraServiceEnabled | grep cbpolicyd
Después de un par de minutos, zmconfigd detecta que tenemos el servicio habilitado y automáticamente instala la base de datos SQLite; para guardar las configuraciones, y nos inicia el servicio de Policyd. Esperamos un par de segundos y verificamos que el servicio está funcionando correctamente. Para verificar que se han creado las bases de datos, observado que en el directorio: /opt/zimbra/data/cbpolicyd/db/ nos ha creado los ficheros de las bases de datos, con el cual gestionaremos el sistema de Policyd. Por defecto las quotas y el rate limiting están habilitados, pero no hay ninguna política definida.

zmcbpolicydctl status
Una vez el servicio de Policyd esté funcionando, hemos de indicarle al servidor de postfix (MTA de Zimbra) que cuando llegue un correo, lo reenvié al puerto 10031, que es donde escucha por defecto Policyd:

zmprov mcf +zimbraMtaRestriction "check_policy_service inet:127.0.0.1:10031"
zmprov ms `zmhostname` zimbraMtaEnableSmtpdPolicyd TRUE
12.3.- Habilitación de módulos de Policyd
Habilitamos el módulo a emplear, ej: AccessControl: Para el control de acceso a los correos de entrada y salida.

zmprov ms `zmhostname` zimbraCBPolicydAccessControlEnabled TRUE
zmprov ms `zmhostname` zimbraCBPolicydCheckHeloEnabled TRUE
zmprov ms `zmhostname` zimbraCBPolicydCheckSPFEnabled TRUE
Reiniciamos los servicios de Policyd y de postfix, para que se apliquen los cambios que hemos realizado

zmcbpolicydctl restart && zmmtactl restart
12.4.- Configuración de Rate Limits
Una de la utilidades principales de Policyd es la configuración de rate limits (quotas de envío y recepción) por usuario, algo que no puede hacerse por ejemplo en postfix o en PMG

Para definir políticas de rate limit, creamos un fichero con las restricciones personalizadas. En nuestro caso crearemos las siguientes restricciones:

Rate limit: cualquier usuario de nuestro servidor no pueda enviar más de 3 mensajes cada 60 segundos.
Rate limit: que cualquier dominio cuando reciba más de 5 mails en un periodo de 60 segundos, el mensaje sea rechazado y el servidor que lo envía, lo guarde en su cola.
El código a insertar en la base de datos del Policyd, quedaría de la siguiente manera:

vim /opt/zimbra/scripts/rate-limits.sql
BEGIN TRANSACTION;
INSERT INTO "policies" (Name,Priority,Description) VALUES('Zimbra CBPolicyd Policies', 0, 'Zimbra CBPolicyd Policies');
INSERT INTO "policy_members" (PolicyID,Source,Destination) VALUES(6, 'any', 'any');
INSERT INTO "quotas" (PolicyID,Name,Track,Period,Verdict,Data) VALUES (6, 'Sender:tuxito@aulautil.tk','tuxito@aulautil.tk', 60, 'DEFER', 'Estas enviando demasiados mensajes en 60 segundos,');
INSERT INTO "quotas" (PolicyID,Name,Track,Period,Verdict) VALUES (6, 'Recipient:@aulautil.tk', 'Recipient:@aulautil.tk', 60, 'REJECT');
INSERT INTO "quotas_limits" (QuotasID,Type,CounterLimit) VALUES(3, 'MessageCount', 3);
INSERT INTO "quotas_limits" (QuotasID,Type,CounterLimit) VALUES(4, 'MessageCount', 5);
Si necesitásemos que algún equipo se salte las restricciones, simplemente con añadir la siguiente línea haríamos que el servidor con la IP: 192.168.X.65 (un outlook de cliente), no sufriese estas restricciones:

INSERT INTO policy_group_members (PolicyGroupId,Member,Disabled,Comment) VALUES(3,'192.168.X.65',0,'Saltarse restricciones');
Amtes de realizar modificaciones en la base de datos de policyd, tenemos que hacer un backup:

cp -p /opt/zimbra/data/cbpolicyd/db/cbpolicyd.sqlitedb /opt/zimbra/data/cbpolicyd/db/cbpolicyd.sqlitedb-bk
Ahora ejecutamos el script de rate limits dentro de la base de datos; como usuario zimbra

sqlite3 /opt/zimbra/data/cbpolicyd/db/cbpolicyd.sqlitedb < /opt/zimbra/scripts/rate-limits.sql
Reiniciamos los servicios de Policyd y de postfix, para que se apliquen los cambios que hemos realizado

zmcbpolicydctl restart && zmmtactl restart
Realizar las pruebas de envío y recepción para verificar los rate limits

13.- Integración con Active Directory
Zimbra se puede integrar con Active Directory en dos formas

Usar el Active Directory como servidor de autenticación; es necesario crear las cuentas tanto en Active Directory como en Zimbra
Usar el Active Directory como Libreta de Direcciones Global externo o híbrido.
13.1.- Configuración de Zimbra y Active Directory para autenticación de usuarios
Crear cuenta pepito@aulautil.tk en Zimbra
En Active Directory crear una Unidad Organizativa llamada Zimbra. Asumiendo que el dominio Windows es AULAUTIL.TK la ruta LDAP de usuarios será: ou=Zimbra,dc=aulautil,dc=tk
Dentro de esa unidad organizativa crear el usuario pepito@aulautil.tk

13.2.- Autenticar zimbra con AD
Entrar al panel de Zimbra ir a

 Configurar --> Dominios
 seleccionar el dominio y click derecho "Configurar autenticación"
    Modo de autenticación para este dominio: Active Directory ---> Siguiente
    Configuración de Active Directory
       Nombre del dominio AD: AULAUTIL.TK
       Nombre del Servidor LDAP
          ldap: IP_O_NOMBRE_DEL_SERVIDOR  Puerto: 389   Utilizar SSL: Desmarcar ---> Siguiente
    Enlace de LDAP ---> Siguiente (se deja vacío, solo si no funciona aca poner ruta LDAP de usuario administrador y clave)
    Resumen de configuración de la autenticación
      Probar con usuario y clave del dominio para ver si autentica.
    Configuración de Grupo Externo ---> Siguiente
    Finalizar
Probar crear otro usuario en Active Directory y en Zimbra con claves distintas; al loguearse a zimbra ese usuario, se tiene que ingresar la clave del Active Directory.
