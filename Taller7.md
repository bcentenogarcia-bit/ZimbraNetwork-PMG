taller 7: Manual de Administración y Configuración de Zimbra Network Edition 9.0
En este capítulo pasamos a Configurar y Administrar Zimbra Network Edition a través del panel de administración.


1.- Panel de Administración de Zimbra Network 9.0

1.1.- Acceso al Panel
Acceder a https://mail.aulautil.tk:7071

user: admin
clave: Tuxito1.23
Si nos olvidamos de la clave del admin, se puede cambiar con el comando

/opt/zimbra/bin/zmprov sp admin@aulautil.tk "NuevaClaveSuperFuerte"
Si el idioma esta en inglés cambiar en la clase de servicio a español

Configuration
    Clase Of Services 
        doble click en "default"
             Preferences ---> Language ----> spanish
             Logout
Volver a loguearse como admin


1.2.- Ver estado de servicios
Al acceder al panel nos nuestra primero el estado de servicios, el cual a veces no funciona bien, por ello

Ejecutar, mejor en consola para ver el estado real de los servicios

su - zimbra -c "zmcontrol status"

1.3.- Menús Principales
Al lado izquierdo tenemos los siguientes Menús
Supervisar
Administrar
Configurar
Herramientas y Migración
Buscar
Zimbra Network
Sin embargo; cuando queremos desplegar por primera vez Zimbra, veremos el siguiente orden.
Configurar, Administrar, Supervisar, Buscar, Zimbra Network, Herramientas y Migración


2.- Menú Configurar
2.1.- Configuración General (Le afecta todos los servidores)
2.1.1 Información General
Tamaño máximo que podemos subir en upload
Tamaño máximo de un archivo cargado desde el escritorio (KB): 30720

2.1.2.- Archivos Adjuntos
Enviar notificación de extensión bloqueada al destinatario Desmarcar
Añadir todas las Extensiones Comunes (dar click en Añadir Todos)
Dar click en boton Guardar

2.1.3.- MTA
Tamaño máximo del adjunto en los correos
Tamaño máximo del mensaje (KB): 30720

Comprobaciones de Protocolo
Quitar check «El nombre del servidor …»
Quitar check «La dirección del remitente …»

Comprobaciones de DNS Quitar check en todos.

Lista RBL (Comprobación de Listas Negras)
Ninguno; el filtrado RBL lo hace el gateway

webs para verificar listas negras, de nuestra IP o del Cliente
http://whatismyipaddress.com/blacklist-check http://multirbl.valli.org

2.1.4.- IMAP
Si tengo muchos usuarios conectandose por Imap, ej: 1000
Número de subprocesos: 2000

2.1.5.- POP3
Si tengo muchos usuarios conectandose por Pop3, ej: 2000
Número de subprocesos: 1000

2.1.6.- AS/AV (AntiSpam, Antivirus)
Zimbra viene configurado por defecto con Amavis; si lo mantenemos como filtro de contenido, ajustamos los valores de AntiSPam; en donde mientras mayor sea el Score es más probable que sea Spam.

Valores por defecto
Porcentaje eliminado: 75 (75 x 0.2 = 15.0)
Porcentaje etiquetado: 33 (33 x 0.2 = 6.6)

Valores recomendados
Porcentaje eliminado: 100 (100 x 0.2 = 20.0) —> Los correos con score >= 7.0 no llegarán al usuario porque son muy Spam
Porcentaje etiquetado: 18 (18 x 0.2 = 3.6) —> Los correos con score >= 3.6 llegarán a la carpeta Spam del usuario

Nota
Si instalamos MailScanner y MailWatch estos valores ya no tendrán efecto.

Configuración antivirus
Desmarcar Bloquear archivos cifrados

Click en Guardar arriba a la derecha

2.2.- Servidores
Click en Configurar para ir al menú anterior, luego Click en Servidores, seleccionar
mail.aulautil.tk (dar doble click)

2.2.1.- Servicios
Si uso un relay de entrada y salida, desmarcar AntiSpam

2.2.2.- MTA
Si uso un relay de salida configurar
MTA de retransmisión para entrega externa: Ej: relay.aulautil.tk

2.2.2.1- Redes de Confianza MTA
Son las redes o Ips que pueden enviar desde zimbra, sin autenticarse.
Solo considerar como excepción ip de servidores que no tienen un mecanismo de autenticación; ej: La Ip de zimbra es 192.168.X.2 y tengo un servidor de monitoreo 192.168.100.200

Entonces modificamos las redes de confianza por consola Web
‘127.0.0.0/8 [::1]/128 192.168.X.2/32 192.168.100.200/32’

O por consola

su - zimbra
zmprov modifyServer mail.aulautil.tk zimbraMtaMyNetworks '127.0.0.0/8 [::1]/128 192.168.X.2/32 192.168.100.200/32'
zmmtactl restart
Al final verificamos el cambio de valor en el panel, como muestra el gráfico.

2.2.3.- IMAP y POP3
Vamos a deshabilitar el protoloco plano en IMAP y POP3

su - zimbra
zmprov ms `zmhostname` zimbraImapClearTextLoginEnabled FALSE
zmprov ms `zmhostname` zimbraPop3ClearTextLoginEnabled FALSE 
zmmailboxdctl restart
2.3.- Dominios
2.3.1.- Crear un nuevo dominio
Click en la reuda dentada superior y —> Nuevo
Nombre del dominio: aulautil.pe
Siguiente
Servidor de correo: mail.aulautil.tk
Finalizar

Luego configurar la resolución de DNS a nivel publico y local

2.3.2.- GAL de un dominio
Click derecho en el dominio y luego Configurar GAL
Interno –> LDAP de Zimbra
Externo –> LDAP externo o ADS
Hibrido –> Ambos casos

2.3.3.- Autenticacion
Click derecho en el dominio y luego Autenticación
Interno –> LDAP de Zimbra
Externo –> LDAP externo o ADS

2.3.4.- Alias de Dominio
Click en rueda dentada
Añadir Alias de dominio
Alias de dominio: aulautil.tk
Dominio de destino: aulautil.tk

Para hacer efectivo los cambios

su - zimbra -c "zmcontrol restart"
2.4.- Clase de Servicio (COS)
2.4.1.- COS default
Informacion General
Nombre mostrado:*
Descripcion: Usuario Estandar con 1GB de Quota y sin conector para Outlook

2.4.2.- Funciones
 Dar click en Maletin (disco duro virtual)
 Conector MAPI (Microsoft Outlook)     Zimbra colaborativo para Outllok en PC  (Zimbra Network Professional Edition)
 EWS (Exchange) client access, solo para clientes MAC con Outlook  (Zimbra Network Professional Edition + Licencia adicional para MAC) (No hacer check)
 Touch Client (Aplicación web para móviles)  --- Opción Legada, solo funciona hasta 8.8.15  (No hacer check)
 Carpeta Contenedora        
 Enviar Correo mas tarde
 Recordatorios por SMS                       Network Edition 
 Smime                                       Network Edition
Habilitar la función de VOz     Network Edition 
2.4.3.- Preferencias de COS
Iniciar Sesión como:  Modern
 Idioma: Español 
 Reproducir un sonido cuando llegue un mensaje (requiere los complementos QuickTime o Windows Media).
 Resaltar la pestaña Correo cuando llegue un mensaje
 Título del navegador intermitente cuando llega un mensaje
 Redactar siempre correo utilizando: HTML
 Habilitar el corrector ortográfico obligatorio (a criterio)
2.4.4.- Avanzado
Número máximo de direcciones de reenvío: 10 (Para evitar spam)  
Espacio de almacenamiento disponible para la cuenta (MB): 1024 (0 es ilimitado)
Contraseña
 Tamaño mínimo de la contraseña: 8
 Mínimo de caracteres en mayúsculas: 1
 Mínimo de signos de puntuación: 1
 Mínimo de caracteres numéricos: 1
2.4.5.- Crear una clase de servicio
 Click derecho en default y duplicar
 Nombre Mostrado: gerente
 Descripción: clase de servicio para Gerentes 10 GB de Quota y con Conector para Outllok en PC

 Funciones->  Conector MAPI (Microsoft Outlook)    Zimbra colaborativo para Outllok en PC  (Zimbra Network Professional Edition) (hacer check)
 Avanzado-> Número máximo de direcciones de reenvío: 30 (Para evitar spam)
 Avanzado-> Espacio de almacenamiento disponible para la cuenta (MB): 10240 
 Aceptar
Nota Se puede crear una clase de servicio de cero dando click en Ruedita->Nuevo


2.5.- Zimlets
Lista de zimlets disponibles https://gallery.zetalliance.org/extend/
Los zimlets que dicen «Modern UI» son para la versión 9 de Zimbra Network, no funcionan con Open Source

Se pueden implementar y desmantelar los zimlets; pero hay que probarlos bien antes de pasarlo a producción

2.5.1.- Instalar Zimlet Drive 2 y deshabilitar OpenDrive
En zimbra Network se agregó una nueva versión de Drive nativo para zimbra Si tenemos el zilmet de drive open lo desinstalamos y luego instalamos el zimbra-drive-ng

Desinstalar el zimbra drive open

su - zimbra -c "zmzimletctl disable com_zextras_drive_open"
su - zimbra -c "zmzimletctl undeploy com_zextras_drive_open"
yum -y erase zimbra-drive
su - zimbra -c "zmmailboxdctl restart"
Instalar zimbra-drive-modern, el cual también instala zimbra-docs-modern

yum -y install zimbra-drive-modern
su - zimbra
zmmailboxdctl restart
sleep 10
zxsuite config global set attribute isDriveEnabledOnStartup value true
zxsuite drive doStartService module
Luego refrescar el panel de administración y el Webmail, ya debemos tener acceso al Drive

Nota Cuanto tenemos el Drive para Moder UI, podemos usar la APP de Zextras Drive como aplicación de drive que pide los siguientes datos

Usuario:   tuxito@aulautil.tk
Clave:       Lolcito.123
Servidor: mail.aulautil.tk
2.5.2.- Instalación de Otros Zimlets para Zimbra Network 9
Explorar en https://gallery.zetalliance.org/extend/ los zimlets que tengan «Modern UI»
por ejemplo instalar Jitsi y One Drive

2.5.2.1.- Jitsi Zimlet
Ir al enlace https://gallery.zetalliance.org/extend/items/view/jitsi
Guia:https://zimbra.github.io/zimbra-9/adminguide.html#_setting_up_jitsi_videoconferencing_solution

Instalar zimlet

yum install zimbra-zimlet-jitsi
Editar el URL del servidor Jitsi

vim /opt/zimbra/zimlets-deployed/zimbra-zimlet-jitsi/config_template.xml
   
        https://meets.aulautil.tk
   

Activar Zimlet

su - zimbra
cd /opt/zimbra/zimlets-deployed/zimbra-zimlet-jitsi/
zmzimletctl configure config_template.xml
zmprov fc all
Probar el zimlet creando una cita de calendario (debe aparecer ícono de Jitsi)

2.5.2.2.- One Drive Zimlet
Ir al enlace https://gallery.zetalliance.org/extend/items/view/onedrive

Instalar zimlet

yum install zimbra-zimlet-onedrive
Seguir la guía indicada en https://zimbra.github.io/zimbra-9/adminguide.html#_setting_up_onedrive

2.5.2.3.- Documentación de otros Zimlets
Revisar https://zimbra.github.io/zimbra-9/adminguide.html#_configuring_zimlets_for_modern_web_app

2.5.3.- Extensiones de Administración
Similar a los zimlets pero sólo para el módulo administración

2.6.- Certificados
Zimbra por defecto viene con un certificado autofirmado; No valido para los navegadores y cliente de correo En el capítulo anterior hemos instalado el Certificado comercial gratuito LetsEncrypt que se actualiza cada 3 meses.

2.6.1.- Instalar o Renovar el Certificado Autofirmado (en deshuso)
 Rueda -> Instalar Certificado
 Nombre del Servidor: mail.aulautil.tk
 Siguiente
 Instalar el Certificado autofirmado
 Ckeck en Reemplazar la CSR existente
 Digest: sha1
 Longitud de la Clave: 2048 
 Nombre Común: mail.aulautil.tk
 No hacer check en Utilizar el comodin del nombre comun
 Nombre del Pais: PE
 Estado/Provincia: Lima
 Ciudad: Los Olivos
 Nombre de la Organizacion: Nombre de tu empresa
 Unidad de Organizacion: Sistemas
 Nombre alternativo de asunto:  mail.aulautil.tk    
 Siguiente
    Días de validación del certificado:  3650
 Instalar
    Una vez culminado reiniciar
su - zimbra
zmcontrol restart
zmcontrol status 
2.6.2.- Instalar el Certificado Comercial
WorkFlow de un certificado comercial

1.- Certificado CSR (podemos generarlo con Zimbra, OpenSSL o un sitio Web Online)
   . tipo de certificado: 1 host, múltiple host o wilcard
   . clave privada (lo generan ustedes)

2.- Comprar certificado comercial (Se paga por un certificado de tipo host, multihost o wilcard en Namecheap)
   . Subir CSR
   . Validar con correo el certificado solicitado
   . Fima y generación del certificado
   - crt (certificado)
   - ca-bundle

3.- En zimbra por panel se necesita
   - crt
   - csr
   - ca-bundle
   - clave privada (generado interno al crear el csr)

4.- En apache, ngnix se necesita
   - crt
   - clave privada
   - ca-bundle

Generación de certificado desde el panel

 Rueda -> Instalar Certificado
 Nombre del Servidor: mail.aulautil.tk
 Generar la función CSR para la autoridad de certificación comercial 
 Nombre del Servidor: mail.aulautil.tk
 Siguiente
 Instalar el Certificado autofirmado
 Ckeck en Reemplazar la CSR existente
 Digest: sha1
 Longitud de la Clave: 2048 
 Nombre Común: mail.aulautil.tk
 No hacer check en Utilizar el comodin del nombre comun
 Nombre del Pais: PE
 Estado/Provincia: Lima
 Ciudad: Lince
 Nombre de la Organizacion: Nombre de tu empresa
 Unidad de Organizacion: Sistemas
 Nombre alternativo de asunto:  mail.aulautil.tk    

 Siguiente

 **Sale Mensaje** 
 Servidor objetivo: 
 mail.aulautil.tk
 Para poder obtener un certificado comercial firmado, debes descargar la CSR generada 
 y enviarla a la autoridad certificadora. Una vez obtenido el certificado, 
 reinicia el “Asistente de instalación de certificado” y selecciona la opción 
 “Instalar el certificado comercial firmado” para completar el certificado de instalación.

 Dar click en Descargar la CSR
 Finalizar

 Enviar la CSR a la entidad autorizada; luego de que lo firmen, descargar el certificado comercial
2.6.2.1.- Instalar el Certificado Comercial por el Panel
 Rueda -> Instalar Certificado
 Nombre del Servidor: mail.aulautil.tk
 Instalar el certificado firmado comercialmente 
 Subir archivos
    Certificado:
    CA Raiz
    CA intermedia   
    Siguiente
    Finalizar

  Una vez culminado reiniciar
su - zimbra
zmcontrol restart
zmcontrol status 
2.6.2.2.- Instalar el Certificado Comercial por consola
Se asume que contamos con los siguientes archivos 1.- Clave Privada Generado con zimbra

ls /opt/zimbra/ssl/zimbra/commercial/commercial.key
Generado manualmente

certificado.key
2.- El archivo intermedio CSR

certificado.csr
3.- El certificado firmado por Ej. namecheap

certificado.crt
certificado.ca-bundle
4.- Verificar el despliegue del certificado Copiar el key al entorno de Zimbra

cp certificado.key /opt/zimbra/ssl/zimbra/commercial/commercial.key
chown zimbra.zimbra /opt/zimbra/ssl/zimbra/commercial/commercial.key
Verificar la validez de los certificados (Ej: /opt/certificado)

su - zimbra
zmcertmgr verifycrt comm /opt/zimbra/ssl/zimbra/commercial/commercial.key /opt/certificado/certificado.crt
Desplegar el certificado (Ej: /opt/certificado)

su - zimbra
cd /opt/zimbra/ssl/zimbra/commercial/
zmcertmgr deploycrt comm/opt/certificado/certificado.crt /opt/certificado/certificado.ca-bundle
Reiniciar Zimbra y verificar status

su - zimbra
zmcontrol restart
zmcontrol status 
Certificados de Dominios adicionales
Entrar a Configurar -> Dominios -> clik derecho en un dominio y Editar Certificado

Para un despliegue multitenant de dominios alojados y sus certificados revisar https://wiki.zimbra.com/wiki/SSL_certificates_per_domain

3.- Menú Administrar
Cuentas y Recursos son buzones de correo (1 licencia por buzón en el Network Edition) Alias y Listas de correo son alias

3.1.- Cuentas
3.1.1.- Crear cuentas
Click en la rueda y Nuevo

Nombre de Cuenta: tuxito@aulautil.tk 
Nombre: Tuxito
Apellido: Linuxero
Nombre Común: dejar en auto

Estado: Activo
Clase de Servicio:   por defecto es default

Contraseña: Tuxito1. (por seguridad habilitar fortaleza de contraseña en el COS) 
                     Longitud 8, 1 número, una mayúscula, un signo de puntuación  
Finalizar
Click en la rueda y Nuevo

 Nombre de Cuenta: gerentazo@aulautil.tk 
 Nombre: Gerentazo
 Apellido: Maloso
 Nombre Común: dejar en auto

 Estado: Activo
 Clase de Servicio: Desmarcar auto; y escribir gerente

 Contraseña: Gerente1. (por seguridad habilitar fortaleza de contraseña en el COS) 
                       Longitud 8, 1 número, una mayúscula, un signo de puntuación  
 Finalizar
3.2.- Alias
3.2.1.- Crear alias
 Rueda -> nuevo

 Alias:  linuxero@aulautil.tk
 Cuenta Objetivo : tuxito@aulautil.tk
3.3.- Listas de Correo
3.3.1.- Crear lista
 Rueda -> nuevo

 Nombre de Lista: todos@aulautil.tk
 Todas las cuentas en aulautil.tk

 Dar click al boton de buscar en la derecha
 Seleccionar cuentas y dar click en 
 Añadir seleccionados
 Finalizar
3.4.- Recursos
3.4.1.- Crear Nuevo recurso
 Rueda --> nuevo
 Nombre de recurso:  proyector1@aulautil.tk
 Tipo:  Equipo
 Clase de Servicio:  default
 Contraseña:  Proyector1.
 Rueda --> nuevo
 Nombre de recurso:  sala1@aulautil.tk
 Tipo:  Ubicacion
 Clase de Servicio:  default
 Contraseña:  Sala123.
4.- Menú Supervisar
4.1.- Estado del Servidor
Muestra estados de los servicios (no fiarse)

Mejor ver con

su - zimbra -c "zmcontrol status"
4.2.- Estadísticas Avanzadas
Muestra valores estadísticos SNMP del disco y logs
Ej ver actividad del disco de los últimos 30 días
Seleccionar Grupo: io.csv
Empezar: now-30d
Final: now
Contadores: Seleccionar 1 o más

Click en "Actualizar Cuadro"
4.3.- Información y estadísticas de correo entrante y saliente a internet
Podemos revisar las estadísticas de: Número de Mensajes, Volumen de Mensajes, Actividad Antispam Sin embargo, la consola centralizada de MailWatch, nos muestra mejores estadísticas.

4.4.- Estadísticas del servidor
Seleccionar Servidor «mail.aulautil.tk» y doble click

3.4.1.- Disco
Muestra estadísticas de uso de los discos Sin embargo por consola podemos saber el espacio usado y libre de los discos con el comando

df -h
4.4.2.- Sesión
Muestra las últimas sesiones por webmail, del administrador y por imap. Para proteger al servidor de ataques de fuerza bruta, usar Fail2ban

4.4.3 Espacio de Almacenamiento del buzón de correo
Muestra el uso general de las cuotas de disco de todas las cuentas de correo.

Las otras estadísticas son similares al punto 3.3


4.5.- Colas de correo
Funcionalidad poco útil, mejor gestionar por consola.

4.5.1.- Habilitar el registro del Subject en logs de correo
 Ir al panel de Administración de Zimbra, 
 Configuración -> Configuración General → Archivos adjuntos y deshabilitar la opción
 Enviar notificación de extensión bloqueada al destinatario 
Agregar en la última línea del archivo header_checks de postfix

vim /opt/zimbra/conf/postfix_header_checks.in
/^Subject:/ WARN
Reiniciar el MTA

su - zimbra -c "postfix reload"
Verificar los logs en tiempo real y verificar que se guarde el subject de los mensajes

tail -f /var/log/zimbra.log
Verificar los logs histórico

less /var/log/zimbra.log
4.5.2.- Ver cola de Correo actual
Como root ejecutar (zimbra 8.7)

/opt/zimbra/common/sbin/mailq
Como root ejecutar (zimbra 8.6)

/opt/zimbra/postfix/sbin/mailq
4.5.3.- Reenviar la cola de correo
Como root ejecutar (zimbra 8.7)

/opt/zimbra/common/sbin/postsuper -r ALL
/opt/zimbra/common/sbin/postqueue -f
Como root ejecutar (zimbra 8.6)

/opt/zimbra/postfix/sbin/postsuper -r ALL
/opt/zimbra/postfix/sbin/postqueue -f
4.5.4.- Borrar toda la cola
Como root ejecutar (zimbra 8.7)

/opt/zimbra/common/sbin/postsuper -d ALL
Como root ejecutar (zimbra 8.6)

/opt/zimbra/postfix/sbin/postsuper -d ALL
4.5.5.- Borrar correos específicos de la cola de correos
mkdir /opt/zimbra/scripts
vim /opt/zimbra/scripts/borracola
#!/usr/bin/perl

$REGEXP = shift || die "no email-adress given (regexp-style, e.g. bl.*\@yahoo.com)!";

@data = qx</opt/zimbra/common/sbin/postqueue -p>;
for (@data) {
  if (/^(\w+)(\*|\!)?\s/) {
     $queue_id = $1;
  }
  if($queue_id) {
       if (/$REGEXP/i) {
      $Q{$queue_id} = 1;
      $queue_id = "";
    }
  }
}

#open(POSTSUPER,"|cat") || die "couldn't open postsuper" ;
open(POSTSUPER,"|/opt/zimbra/common/sbin/postsuper -d -") || die "couldn't open postsuper" ;

foreach (keys %Q) {
  print POSTSUPER "$_\n";
};
close(POSTSUPER);
chmod 755 /opt/zimbra/scripts/borracola
Borrar correos encolados de hotmail

/opt/zimbra/scripts/borracola hotmail
Borrar correos sin conexion con el host de destino

/opt/zimbra/scripts/borracola "Connection time"
Borrar todos los correos de pepito encolados

/opt/zimbra/scripts/borracola pepito
Poder ejecutar borracola desde cualquier lado

ln -s /opt/zimbra/scripts/borracola /usr/bin/borracola
5.- Menú Buscar
Permite mostrar el listado de todas las cuentas, alias, dominios y listas de correo

Para exportar el resultado de la búsqueda, dar click en la ruedita superior a la derecha y 
luego "Descargar", nos generará un archivo CSV
En opciones de búsqueda se puede especificar atributos de la cuenta.

Por consola se pueden hacer búsquedas especiales, se verá un capítulo de ello.

6.- Menú Herramientas y Migración
Se verá en detalle en el taller de migración
