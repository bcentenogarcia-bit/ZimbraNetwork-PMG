Configuración y Administración de Proxmox Mail Gateway
Siguiendo con el despliegue de Proxmox Mail Gateway, vamos ahora a configurar y Administrarlo.

Documentación Oficial de Proxmox Mail Gateway https://pmg.proxmox.com/pmg-docs/

1. Introduction
1.1. Qué es Proxmox Mail Gateway?
Proxmox Mail Gateway (PMG) es una solución de gateway antispam de entrada y salida que permite filtrar el tráfico de correo electrónico malicioso y no deseado, enfocando la detección y eliminación de spam y virus. Asimismo PMG permite establecer políticas de entrada y salida de correo con uso de múltiples condiciones y filtros.

<img width="1129" height="668" alt="image" src="https://github.com/user-attachments/assets/003f7641-b31e-49f2-a100-31362d4fffb7" />

1.2. Caracteristicas
1.2.1 Detección de spam
Proxmox Mail Gateway utiliza una amplia variedad de filtros locales y de red para identificar el correo no deseado. Aquí hay una breve lista de los métodos de filtrado utilizados:

Verificación del receptor

Muchos de los mensajes basura que llegan a su red son correos electrónicos a usuarios inexistentes. Proxmox Mail Gateway detecta estos correos electrónicos en el nivel SMTP, lo que significa antes de que se transfieran a sus redes. Esto reduce el tráfico a analizar en busca de spam y virus hasta en un 90% y reduce la carga de trabajo en sus servidores de correo y escáneres.

Marco de políticas del remitente (SPF)

Sender Policy Framework (SPF) es un estándar abierto para validar correos electrónicos y evitar la falsificación de la dirección IP del remitente. SPF permite al administrador de un dominio de Internet especificar qué computadoras están autorizadas para enviar correos electrónicos con un dominio determinado mediante la creación de un registro SPF específico en el Sistema de nombres de dominio (DNS).

Lista de agujeros negros basada en DNS

Una Lista de agujeros negros basada en DNS (DNSBL) es un medio por el cual un sitio de Internet puede publicar una lista de direcciones IP, en un formato que los programas de computadora pueden consultar fácilmente en Internet. La tecnología está construida sobre el Sistema de nombres de dominio. Los DNSBL se utilizan para publicar listas de direcciones vinculadas a spam.

Lista blanca SMTP

Es una excepción de bloqueo de SMTP para evitar todos los bloqueos a nivel de MTA (listas grises, verificación del receptor, SPF y RBL) y aceptar todos los correos electrónicos para el análisis en el sistema de reglas, se puede agregar lo siguiente a esta lista: Dominios (remitente / receptor), dirección de correo (remitente / Receptor), Expresión regular (remitente / receptor), dirección IP (remitente), red IP (remitente)

Filtro Bayesiano

Filtros estadísticos entrenados automáticamente. Algunas palabras particulares tienen una mayor probabilidad de aparecer en correos electrónicos no deseados en lugar de correos electrónicos legítimos. Al ser entrenado para reconocer esas palabras, el Bayesiano revisa cada correo electrónico y ajusta las probabilidades de que sea una palabra de spam o no en su base de datos. Esto se hace automáticamente.

Listas negras y blancas

Las listas negras y blancas son un mecanismo de control de acceso para aceptar, bloquear o poner en cuarentena los correos electrónicos a los destinatarios. Esto le permite ajustar el sistema de reglas mediante la aplicación de diferentes objetos como dominios, dirección de correo electrónico, expresión regular, red IP, grupo LDAP y otros.

Algoritmo de autoaprendizaje

Proxmox Mail Gateway recopila información estadística sobre correos electrónicos no deseados. Esta información es utilizada por un algoritmo de autoaprendizaje, por lo que el sistema se vuelve más inteligente con el tiempo.

Spam Uri Realtime BlockList (SURBL)

Los SURBL se utilizan para detectar el correo no deseado en función de los URI del cuerpo del mensaje (generalmente sitios web). Esto los hace diferentes de la mayoría de las otras listas de bloqueo en tiempo real, porque los SURBL no se usan para bloquear a los remitentes de spam. Los SURBL le permiten bloquear mensajes que tienen hosts de spam que se mencionan en los cuerpos de mensajes.

Lista gris

Poner en gris un correo electrónico de un remitente que su sistema no reconoce, significa que será rechazado temporalmente. Dado que las fallas temporales están integradas en las especificaciones RFC para la entrega de correo, un servidor legítimo intentará reenviar el correo electrónico más adelante. Este es un método efectivo porque los spammers no hacen cola y reintentan la entrega de correo como es normal para un agente de transporte de correo normal.

Las listas grises pueden reducir el tráfico de correo electrónico hasta en un 50%. Un correo electrónico en la lista gris nunca llega a su servidor de correo y, por lo tanto, su servidor de correo no enviará «Informes de no entrega» inútiles a los spammers.

Pruebas de protocolo SMTP

Postfix es capaz de hacer algunas pruebas sofisticadas de protocolo SMTP (ver pantalla posterior del hombre). Los zombis envían la mayor parte del correo no deseado (malware en las computadoras comprometidas del usuario final), y esos zombis a menudo intentan maximizar la cantidad de correos entregados. Para hacerlo, muchos de ellos violan la especificación del protocolo SMTP y, por lo tanto, pueden ser detectados por estas pruebas.

1.2.2. Detección de virus
Proxmox Mail Gateway integra ClamAV®, que es un motor antivirus de código abierto (GPL) diseñado para detectar troyanos, virus, malware y otras amenazas maliciosas.

Proporciona un demonio de escaneo multiproceso de alto rendimiento, utilidades de línea de comando para escaneo de archivos a pedido y una herramienta inteligente para actualizaciones automáticas de firmas.

1.2.3 Sistema de reglas orientado a objetos
El sistema de reglas orientado a objetos habilita reglas personalizadas para sus dominios. Es una manera fácil pero muy poderosa de definir reglas de filtro por usuario, dominios, marco de tiempo, tipo de contenido y acción resultante. Proxmox Mail Gateway ofrece muchos objetos poderosos para configurar su propio sistema personalizado.

WHO- objects
¿Quién es el remitente o receptor del correo electrónico?

WHAT – objects
¿Qué hay en el correo electrónico?

WHEN – objetos
¿Cuándo recibe el correo electrónico Proxmox Mail Gateway?

ACTIONS – objects
Define las acciones finales.

Cada regla tiene cinco categorías FROM, TO, WHEN, WHAT and ACTION. Cada una de estas categorías puede contener varios objetos y una dirección (dentro, fuera o ambos).

Las opciones van desde configuraciones simples de filtro de correo no deseado y virus hasta configuraciones sofisticadas y altamente personalizadas que bloquean ciertos tipos de correos electrónicos y generan notificaciones.

1.2.4. Cuarentena de spam
Los correos spam identificados se pueden almacenar en la cuarentena de spam accesible para el usuario. Por lo tanto, los usuarios pueden ver y administrar sus correos spam por sí mismos.

1.2.5 Seguimiento y registro
El innovador Centro de seguimiento de mensajes de Proxmox rastrea y resume todos los registros disponibles. Con la interfaz de administración basada en la web y fácil de usar, los administradores de TI pueden fácilmente ver y controlar todas las funciones desde una sola pantalla.

El Centro de seguimiento de mensajes es muy rápido y potente, probado en los sitios de Proxmox Mail Gateway que procesan más de un millón de correos electrónicos por día. Se pueden consultar todos los archivos de registro diferentes de los últimos 7 días y los resultados se resumen mediante un algoritmo inteligente.

Llegada del correo electrónico.

Procesamiento de filtrado Proxmox con resultados

Cola interna a su servidor de correo electrónico

Estado de entrega final

1.2.6. Alta disponibilidad con clúster HA Proxmox
Para proporcionar un sistema de correo electrónico 100% seguro para su negocio, desarrollamos el clúster de alta disponibilidad (HA) de Proxmox. Proxmox HA Cluster utiliza un esquema de agrupación de nivel de aplicación único, que proporciona un rendimiento extremadamente bueno. La configuración rápida en minutos y una administración simple e intuitiva mantienen bajas las necesidades de recursos. Después de fallas temporales, los nodos se reintegran automáticamente sin ninguna interacción del operador.

1.2.7. Integración LDAP
Es posible consultar datos de usuarios y grupos desde servidores LDAP. Esto se puede usar para crear reglas de filtro especiales o simplemente para proporcionar servicios de autenticación para la GUI de cuarentena de correo no deseado.

1.2.8. Integración de Fetchmail
Proxmox Mail Gateway le permite obtener correo de otros servidores IMAP o POP3.

1.2.9. Gestión flexible de usuarios
La interfaz de administración utiliza un esquema de control de acceso basado en roles, que utiliza los siguientes roles:

Superusuario Este rol puede hacer todo (reservado para el usuario root ).

Administrador Acceso total a la configuración del filtro de correo, pero no está permitido cambiar la configuración de la red.

Gerente de cuarentena Puede ver y administrar la Cuarentena de correo no deseado.

Auditor Tiene acceso de solo lectura a toda la configuración, puede acceder a registros y ver estadísticas.

1.3. Beneficios de Proxmox Mail Gateway
Software de código abierto
Sin restricciones de uso
Kernel de Linux
Instalación rápida y fácil de usar.
Interfaz de gestión basada en web
API REST
Enorme comunidad activa
Bajos costos de administración y despliegue simple
2. Planificación del despliegue
2.1- Esquema de Despliegue de PMG

<img width="1138" height="590" alt="image" src="https://github.com/user-attachments/assets/00f8589b-e262-4bc0-8a8b-0f96b1fb5665" />

2.2. Filtrado de correos electrónicos salientes
Muchas soluciones de filtro de correo electrónico no analizan los correos salientes, en cambio Proxmox Mail Gateway está diseñado para escanear correos electrónicos entrantes y salientes. Esto tiene dos ventajas principales:

Proxmox Mail Gateway es capaz de detectar virus enviados desde un host interno. En muchos países, usted es responsable de enviar virus a otras personas. La función de escaneo de correo electrónico saliente de Proxmox Mail Gateway es una protección adicional para evitar eso.

Proxmox Mail Gateway también puede recopilar estadísticas sobre correos electrónicos salientes. Las estadísticas sobre los correos electrónicos entrantes se ven bien, pero son bastante inútiles. Considere dos usuarios, el usuario 1 recibe 10 correos electrónicos de portales de noticias y escribió 1 correo electrónico a una persona de la que nunca escuchó. Mientras que el usuario-2 recibe 5 correos electrónicos de un cliente y devuelve 5 correos electrónicos. ¿Qué usuario consideras más activo? Estoy seguro de que es el usuario 2, porque se comunica con sus clientes. Las estadísticas de direcciones avanzadas de Proxmox Mail Gateway pueden mostrarle esta información importante. Una solución que no escanea el correo electrónico saliente no puede hacerlo.

Para habilitar el filtrado de correo electrónico saliente, solo necesita enviar todos los «smarthost» salientes en su servidor de correo electrónico.

2.3. Configuraciones de cortafuegos
Para pasar el tráfico de correo electrónico a Proxmox Mail Gateway, debe permitir el tráfico en el puerto SMTP. PMG usa además el Protocolo de tiempo de red (NTP) para la sincronización horaria, RAZOR, DNS, SSH, HTTP y el puerto 8006 para la interfaz de administración basada en la web.

<img width="991" height="368" alt="image" src="https://github.com/user-attachments/assets/2f5d016f-7204-49c3-89f2-869c66f191af" />

3.- Instalación
Con el ISO oficial descargable desde https://www.proxmox.com/en/downloads/category/iso-images-pmg
Revisar el taller 5 para detalles de instalación y Upgrade

4.- Configuración
Proxmox Mail Gateway generalmente se configura utilizando la interfaz gráfica de usuario (GUI) basada en la web, pero también es posible editar directamente los archivos de configuración, usar la API REST a través de https o la herramienta de línea de comandos pmgsh .

La herramienta de línea de comandos pmgconfig se usa para simplificar algunas tareas de configuración comunes, es decir, para generar certificados y para reescribir archivos de configuración del servicio.

4.1.- Archivos de configuración relevantes
/etc/network/interfaces

Archivo de configuración de la red|

/etc/resolv.conf

resolución DNS

/etc/hostname

Nombre del Host

/etc/hosts

Tabla de nombres e IP

/etc/pmg/pmg.conf

Almacena opciones de administración comunes.

/etc/pmg/cluster.conf

Configuración del cluster

/etc/pmg/domains

Lista de dominios alojados en el relay

/etc/pmg/fetchmailrc

Configuración de fetchmail (POP3 and IMAP setup).

/etc/pmg/ldap.conf

Configuración de LDAP

/etc/pmg/mynetworks

Lista de redes de confianza.

/etc/pmg/subscription

Keys de suscripción de PMG y su estado

/etc/pmg/tls_policy

Política TLS para conexiones salientes

/etc/pmg/transport

Configuración de transporte y entrega de mensajes

/etc/pmg/user.conf

Configuración de usuarios de la GUI

/etc/mail/spamassassin/custom.cf

Configuración de reglas personalizadas de spamassassin Se verá luego ejemplos ninja de aula útil.

4.2. Claves y Certificados
/etc/pmg/pmg-api.pem La clave y el certificado (combinados) utilizados son el servidor HTTP (API).

/etc/pmg/pmg-authkey.key Uso de clave privada para generar tickets de autenticación.

/etc/pmg/pmg-authkey.pub Uso de clave pública para verificar tickets de autenticación.

/etc/pmg/pmg-csrf.key Utilizado internamente para generar tokens CSRF.

/etc/pmg/pmg-tls.pem Clave y certificado (combinado) para cifrar el tráfico de correo (TLS).

4.3. Plantillas de configuración de servicio
Proxmox Mail Gateway utiliza varios servicios para implementar el filtrado de correo, por ejemplo, el Agente de transporte de correo Postfix (MTA), el motor antivirus ClamAV® y el proyecto Apache SpamAssassin ™ . Esos servicios usan archivos de configuración separados, por lo que necesitamos reescribir esos archivos cuando se cambia la configuración.

Utilizamos un enfoque basado en plantillas para generar esos archivos. El Template Toolkit es un conocido sistema de procesamiento de plantilla, rápido y flexible. Puede encontrar las plantillas predeterminadas en /var/lib/pmg/templates/ . No los modifique directamente, porque su modificación se perdería en la próxima actualización. En su lugar, copie la plantilla que desea cambiar a /etc/pmg/templates/ , luego aplique sus cambios allí.

Las plantillas pueden acceder a cualquier configuración, y puede usar el comando de volcado pmgconfig para obtener una lista de todos los nombres de variables:

pmgconfig dump
… dns.domain = yourdomain.tld dns.hostname = pmg ipconfig.int_ip = 192.168.2.127 pmg.admin.advfilter = 1 … La misma herramienta se utiliza para forzar la regeneración de todos los archivos de configuración basados ​​en plantillas. Debe ejecutar eso después de modificar una plantilla o cuando edita directamente los archivos de configuración

pmgconfig sync
El comando anterior también reinicia los servicios si se cambian los archivos de configuración subyacentes. Tenga en cuenta que esto se hace automáticamente cuando cambia la configuración utilizando la GUI o la API.

Nota Las plantillas modificadas de /etc/pmg/templates / se sincronizan automáticamente desde el nodo maestro a todos los miembros del clúster.

4.3.1.- Habilitar la visualización de Subject en los logs
Habilitar chequeo de cabeceras en postfix

mkdir /etc/pmg/templates
cp /var/lib/pmg/templates/main.cf.in /etc/pmg/templates
Agregar al final

vim /etc/pmg/templates/main.cf.in
header_checks = regexp:/etc/postfix/header_checks
Crear archivo de filtrado de cabeceras

vim /etc/postfix/header_checks
/^Subject:/  WARN
Regenerar archivos de configuración y reiniciar servicio

cd /etc
pmgconfig sync
systemctl restart pmg-smtp-filter.service 
systemctl restart postfix
4.4. Configuración del sistema

<img width="1132" height="846" alt="image" src="https://github.com/user-attachments/assets/accaaf1d-ba90-47fc-b772-d781502ea7a9" />

4.4.1. Red y tiempo
Normalmente, la red y la hora ya están configuradas cuando visita la GUI. El instalador solicita esas configuraciones y configura los valores correctos.

La configuración predeterminada utiliza un único adaptador Ethernet y asignación de IP estática. La configuración se almacena en /etc/network/interfaces , y la configuración de red real se realiza de la manera estándar de Debian usando el paquete ifupdown .

Ejemplo de configuración de red /etc/network/interfaces

source /etc/network/interfaces.d/*

auto lo
iface lo inet loopback

auto ens18
iface ens18 inet static
        address  192.168.X.10
        netmask  255.255.255.0
        gateway  192.168.X.1
Recomendaciones de DNS Muchas pruebas para detectar correos SPAM utilizan consultas DNS, por lo que es importante tener un servidor DNS rápido y confiable. También consultamos algunas listas negras de DNS públicas disponibles. La mayoría de ellos aplican límites de velocidad para los clientes, por lo que simplemente no funcionarán si usa un servidor DNS público (porque generalmente están bloqueados). Recomendamos utilizar su propio servidor DNS, que debe configurarse en modo recursivo.

4.4.1.1.- Instalación de Dnsmasq

Para mejorar la velocidad de consultas DNS instalaremos dnsmasq. Dnsmasq lee el archivo /etc/hosts y crea automáticamente registros A y PTR

Editamos el /etc/hosts para agregar la resolución de mail

vim /etc/hosts
127.0.0.1 localhost.localdomain localhost
192.168.X.10 relay.aulautil.tk relay
192.168.X.2  mail.aulautil.tk mail
Instalamos dnsmaq

apt install dnsmasq
Ahora cambiamos la resolución DNS del PMG en

Configuración --> Red/tiempo --> DNS --> Editar
    Busqueda de dominio: aulautil.tk
    Servidor DNS 1: 127.0.0.1
Comprobar resolución

dig mail.aulautil.tk
Configurar reenvío de consultas hacia un servidor DNS público

vim /etc/dnsmasq.d/localhost.conf
server=8.8.8.8
Configurar resolución MX y TXT de un dominio

vim /etc/dnsmasq.d/aulautil.tk.conf
mx-host=aulautil.tk,mail.aulautil.tk,10
txt-record=aulautil.tk,"v=spf1 a mx -all"
systemctl restart dnsmasq
Comprobación

dig google.com
dig MX aulautil.tk
dig TXT aulautil.tk
Nota

Para refrescar los cambios que se hace en /etc/hosts se debe reinicia el dnsmasq

systemctl restart dnsmasq
Habilitar arranque automático de dnsmasq

systemctl enable dnsmasq
4.4.1.2.- Horario Podemos editar la zona horaria en

Configuración --> Red/Tiempo --> Horario --> Editar
4.4.2.- Opciones
Podemos editar la zona horaria en

Configuración --> Opciones
    Send admin daily reports: Sí
    Usar filtros avanzados de estadística: Sí
    User static time life (days):  60
    Correo del administrador:  admin@aulautil.tk
    Http proxy: Ninguno
4.4.3.- Copia de Seguridad/Restaurar
En esta opción podemos generar backup de la configuración de PMG, luego descargarla. También podemos restaurar un backup previamente descargado.

Configuración --> Copia de Seguridad/Restaurar
    Click en el botón "Copia de Seguridad"
    Luego se podrá descargar, el formato es tgz
4.4.4.- Configuración: Proxy de correo
4.4.4.1.- Transmitiendo

No se hacen cabios
4.4.4.2.- Dominios de Retransmisión

Son los dominios en la que PMG va a aceptar la recepción y el envío
4.4.4.3.- Puertos

SMTP Externo (Recepción): 25
SMTP Interno (Envío): 26
4.4.4.4.- Opciones

Tamaño del mensaje: 31457280 (Ej: 30 M)
Rechazar clientes desconocidos: Sí   (reject_unknown_client_hostname)
Rechazar remitentes desconocidos: Sí
SMTP Helo Pruebas: No
DNSBL: b.barracudacentral.org,zen.spamhaus.com 
DNSBL Threshold: 1          Profundida de consulta de RBLs
Comprueba recipientes: No   Si se habibilta, genera un rebote 450 0 550 cuando no existe un recipiente
Utilice Greylisting: Sí
Utilice SPF: Sí    (valida el SPF, para evitar spoofing al final tiene que ser sí o sí "-all")
Esconde Hosts interno: No 
Tiempo de advertencia de retardo: 1    (tiempo en horas que se queda un correo en diferido antes de rebotarlo)
Límite de recuento de conexión de cliente: 50
Límite de tasa de conexión de cliente: 10 (máxima cantidad de conexiones que puede establecer un cliente en un intervalo de 1 minuto, 0 es ilimitado)
Límite de tasa de conexión de cliente: 5 (máxima cantidad de mensaje que un cliente puede enviar en un intervalo de 1 minuto, 0 es ilimitado)
SMTPD Banner: ESMTP Proxmox   (nombre con que se muestra el relay, se puede cambiar para despistar a un posible atacante)
Send NDR o Blocked E-mails: No  (Mensaje de reporte de No entregado)
4.4.4.5.- Transportes

Opción para definir la entrega (recepción) de correos de un dominio hacia el MTA de backend
4.4.4.6.- Redes

Opción de Redes de Confianza (mynetwors) de postfix que permite que IP o red pueda enviar a traves de PMG sin autenticarse
4.4.4.7.- TLS

Hace que el envío de mensajes se haga en un canal encriptado, habilitar todas las opciones
4.4.4.8.- DKIM

Se verá luego
4.4.4.9.- Lista Blanca

Permite crear una excepción de una IP o Host o cuenta de correo a nivel de MTA para sobreescribir un bloqueo 
de postfix, SPF, RBL o graylist.
4.4.5. Detector Spam
4.4.5.1.- Opciones

Utilice auto-whitelist: Sí   (Esto habilita el filtro AWL en proxmox para autoaprendizaje y determinar 
                              si un dominio o mensaje se autoclasifica como spam; se puede deshabilitar a criterio)
Utilizar filtro bayesiano: Sí (Obligatorio)
Utilice pruebas RBL: Sí (verificación RBL a nivel de spamassassin)
Usar Razor2 checks: Sí  (complemento para Spamassassin)
Tamaño máximo de spam (bytes): 1048576
Idiomas: all   (Podemos elegir los idiomas en que aceptamos correos y rechazar los idiomas desconocidos)
Puntuación de retrodipersión: 0 (https://cwiki.apache.org/confluence/display/spamassassin/VBounceRuleset)
Puntuación heurística: 3   Score for ClamAV heuristics (Encrypted Archives/Documents, Google Safe Browsing database, PhishingScanURLs, …).
4.4.5.2.- Cuarentena

Vida útil:     7  (días en que un spam se queda en la caurentena antes de ser borrado)
Modo de autenticación: ticket  (Authentication mode to access the quarantine interface. Mode ticket allows login using 
                                tickets sent with the daily spam report. Mode ldap requires to login using an LDAP account. 
                                Finally, mode ldapticket allows both ways.)
User Spamreport Style:  Detallado 
Quarentena de host:     Ninguno
Correo De:              Ninguno
Ver imágenes:           Sí 
Aceptar HREFs:          Sí  (Aceptar urls en mensajes que estan en la quarentena)
4.4.5.3.- Estado

Permite ver y ejecutar manualmente las actualizaciones de los filtros de spamassassin
4.4.5.4.- Custom Scores

Permite crear o redefinir un score de SpamAssassin si observamos que marca con score diferente al que quisieramos.
Ej: 
SUBJ_ALL_CAPS se usa muy seguido en una empresa en los asuntos con máyuscula y el PMG lo esta marcando y quiero 
deshabilitar ese score

Click en Crear
    Nombre: SUBJ_ALL_CAPS
    Score: 0    (el score puede ser positivo o negativo, mientras mas positivo es mas valoración de spam)
    Comentario: Asunto en mayúsculas
    Crear

Click en Apply Custom Scores
    Restart pmg-smtp-filter: Check
4.4.6. Detector de Virus
4.4.6.1.- Opciones

Blocked encrypted archives and document: No
Recursión Max: 5   (máxima recursión de archivos empaquetados)
Max archivos:   1000
Tamaño máximo de archivo 30000000
Tamaño máximo de escaneo: 100000000
Max números de tarjeta de crédito: 0   (plugin de Clamav para tarjetas de crédito)
4.4.6.2.- ClamAV

Espejo en base de datos: database.clamav.net (podemos usar un mirror alterno)
Google Safe Browsing: Sí
Estado: Nos muestra el estado de las actualizaciones del antivirus
        Podemos forzar la actualización dando click en "Actualice"
4.4.6.3.- Quarentena

Vida útil: 7  (7 días antes de ser borrado)
Ver Imágenes: Sí
Aceptar HREFs: Sí
4.4.7. Gestión de Usuarios
4.4.7.1.- Crear usuario local con un rol auditor

Ir Configuración --> Gestión de Usuarios --> Local
  Agregar:
       Nombre de usuario: tuxito
       Nombre de Pila: Tuxito
       Apellido:  Linuxero
       Contraseña:  Lolcito.123
       correo: tuxito@aulautil.tk
       Rol: Auditor
       Caducar: Nunca
Probar autenticación de usuario logueándose con el usuario a:

https://relay.aulautil.tk:8006

4.4.7.2.- Integrar con servidor LDAP para que los usuarios puedan ver su quarentena

El el servidor zimbra network, abrimos en el firewalld el servicio ldap

firewall-cmd --add-service=ldap --permanent
firewall-cmd --reload
Luego accedemos al PMG y configuramos la integración LDAP

Ir Configuración --> Gestión de Usuarios --> LDAP
  Crear:
       Nombre de perfil: Zimbra
       Protocolo: LDAP
       Servidor: mail.aulautil.tk
       Servidor: mail.aulautil.tk
       Puerto: 389
       Nombre de Usuario: uid=zimbra,cn=admins,cn=zimbra
       Contraseña { ---- Obtener ejecutando en el servidor zimbra  zmlocalconfig -s|grep zimbra_ldap_password
       Comentario: Integración con LDAP de Zimbra
       Activar: Check
       DN de Base: dc=aulautil,dc=tk
       DN de Base para grupos: dc=aulautil,dc=tk
       Nombre de atributo para el correo: mail
       Nombre de Atributos de la cuenta: mail
       Grupo objectclass: zimbraMailForwardingAddress
  Agregar
Probar autenticación de usuario logueándose con el correo a:

https://relay.aulautil.tk:8006/quarantine

Si no se tiene un usuario creado; crearlo con:

zmprov ca tuxito@aulautil.tk Lolcito.123 displayName "Tuxito Linuxero"
5. Filtrado de Correo
Ejercicios:

5.1.- Creación de una regla antispoofing
Crear regla WHO del propio dominio

Ir Mail Filter --> Who Objects
  Create
       Name: aulautil.tk
            Derecha: Add
                    Domain: aulautil.tk
Crear regla Mail Filter antispoofing

Ir Mail Filter --> Add
  Name: bloquear spoofing aulautil.tk
  Priority: 99
  Direction: IN
  Active: check
         Seleccionar regla creada
                  Available Objects
                        From:   aulautil.tk
                        To: aulautil.tk
                        Action:
                                Block
                                Notify admin
Para probar la regla quitamos las RBL y SPF

Ir Configuration --> Mail Proxy --> Options
      DNSBLSItes: none
      Use SPF: No 
      Reject Unknown Clients: No
      Reject Unknown Senders: No
      Use Greylisting: No
Realizar ataque de spoofing desde otro equipo

telnet relay.aulautil.tk 25
HELO mail.tuxito.com
MAIL FROM:<admin@aulautil.tk>
RCPT TO:<admin@aulautil.tk>
DATA
From: <admin@aulautil.tk>
To: <admin@aulautil.tk>
Subject: Hola admin
como te va
.
QUIT
Verificar en tracking y en la cuenta de admin

Ir a Administration--> Tracking Center
Nota:

Siempre para depurar verificar los logs

less /var/log/mail.log
tail -f /var/log/mail.log
5.2.- Crear políticas de filtrado de entrada y salida
Con PMG podemos crear fácilmente políticas de envío y recepción para restringir correo de entrada o salida a determinados usuarios o grupos.

5.2.1.- Política de filtrado de entrada
Queremos evitar que las listas de correo reciban desde internet

1.- Crear objeto de listas de correo

Ir Filtro  de correo--> Objetos Quién
      Crear
              Nombre: Listas de correo

      Seleccionar la regla creada y a la derecha 
      Agregar
              Correo:  todos@aulautil.tk
2.- Crear objeto de acción para notificar bloqueo de envío

Ir Filtro  de correo--> Objetos de acción
      Agregar --> Notificación
              Nombre: Notificación de Bloqueo de Envío a listas desde internet
              Descripción: Bloquea el envio de correos a las listas desde internet
              Receptor:    __SENDER__
              Asunto:      Notificacion: Correo Bloqueado __SUBJECT__
              Cuerpo:      Su correo ha sido bloqueado
                          Ud no esta autorizado a enviar a:
                          Receiver: __RECEIVERS__
                          Targets:  __TARGETS__

                          Subject: __SUBJECT__

                          Matching Rule: __RULE__

                          __RULE_INFO__

               Crear.
3.- Crear filtro de correo que bloquee envío externo a la lista y notifique al sender

Ir Filtro  de correo--> Derecha Agregar
      Nombre: Bloquear envío externo a las listas
      Prioridad: 100
      Dirección: Entrada
      Activo: Check

Seleccionar regla creada e ir panel de la derecha
      Objetos Disponibles: A
           Seleccionar "Listas de correo"  ---> click en "+"
      Objetos Disponibles: Acción
           Seleccionar "Block"  ---> click en "+"
      Objetos Disponibles: Acción
           Seleccionar "Notificación de Bloqueo de Envío a listas desde Internet"
5.2.2.- Política de filtrado de salida
Queremos evitar que cierto grupo de usuarios pueda enviar correos hacia internet.

1.- Crear objeto de usuarios internos

Ir Filtro  de correo--> Objetos Quién
      Crear
              Nombre: Usuarios solo internos

      Seleccionar la regla creada y a la derecha 
      Agregar
              Correo:  tuxito@aulautil.tk
2.- Crear objeto de acción para notificar bloqueo de envío

Ir Filtro  de correo--> Objetos de acción
      Agregar --> Notificación
              Nombre: Notificación de Bloqueo de Envío de usuarios internos
              Descripción: Bloquea el envío hacia internet a los usuarios que son solo internos
              Receptor:    __SENDER__
              Asunto:      Notificacion: Correo Bloqueado __SUBJECT__
              Cuerpo:      Su correo ha sido bloqueado
                          Ud no esta autorizado a enviar a:
                          Receiver: __RECEIVERS__
                          Targets:  __TARGETS__

                          Subject: __SUBJECT__

                          Matching Rule: __RULE__

                          __RULE_INFO__

               Crear.
3.- Crear filtro de correo que bloquee envío externo y notifique envío a usuarios internos

Ir Filtro  de correo--> Derecha Agregar
      Nombre: Bloquear envío externo usuarios internos
      Prioridad: 100
      Dirección: Saliente
      Activo: Check

Seleccionar regla creada e ir panel de la derecha
      Objetos Disponibles: De
           Seleccionar "Usuarios solo internos"  ---> click en "+"
      Objetos Disponibles: Acción
           Seleccionar "Block"  ---> click en "+"
      Objetos Disponibles: Acción
           Seleccionar "Notificación de Bloqueo de Envío de usuarios internos"
5.3.- Crear reglas de filtrado por cabeceras y contenido del mensaje
Proxmox no cuenta con mecanismos para hacer reglas de filtrado por cabeceras o cuerpo del mensaje; sin embargo se puede crear estas reglas con spamassassin y combinarlas con el sistema de reglas de proxmox y el resultado es bastante eficiente.

5.3.1.- Crear reglas de filtrado en spamassassin
Las reglas de spam assassin se crean en base una sintaxis específica con uso de expresiones regulares en perl; podemos acceder al enlace de referencia para mayor información
https://cwiki.apache.org/confluence/display/SPAMASSASSIN/writingrules

vim /etc/mail/spamassassin/custom.cf
# se puede redefinir el score de un filtro propio de spamassassin
score SUBJ_ALL_CAPS 0.0

# regla por asunto de mensaje
header SPAM_SUBJECT Subject =~ /(ch1na|new apartment|locuras de ofertas)/i
score SPAM_SUBJECT 5.0
describe SPAM_SUBJECT  Asunto con Spam

# regla por asunto de mensaje
header SPAM_PORNO_SUBJECT Subject =~ /(viagra|sexy|masaje)/i
score SPAM_PORNO_SUBJECT 5.0
describe SPAM_PORNO_SUBJECT  Asunto con Porno

# regla por cuerpo de mensaje
body SPAM_PORNO_BODY   /(viagra|sexy|masaje)/i
score SPAM_PORNO_BODY 5.0
describe SPAM_PORNO_BODY  Correo con contenido Porno

# regla por cuerpo de mensaje
body SPAM_BODY   /(verisure)/i
score SPAM_BODY 5.0
describe SPAM_BODY Spam por contenido del mensaje

# regla por cuerpo de mensaje para bloquear secuestro virtual
body SECUESTRO_BODY   /(bitcoin| BTC )/i
score SECUESTRO_BODY 40.0
describe SECUESTRO_BODY Secuestro Virtual por contenido del mensaje

# regla de Phishing por asunto
header PHISHING_SUBJECT Subject =~ /(Zimbra Administrator|Caducidad de la Contraseña)/i
score PHISHING_SUBJECT 40.0
describe PHISHING_SUBJECT  Asunto con Phishing

# regla de Phishing por cuerpo de Mensaje
header PHISHING_SUBJECT Subject =~ /(Zimbra Administrator|Caducidad de la Contraseña)/i
score PHISHING_SUBJECT 40.0
describe PHISHING_SUBJECT  Asunto con Phishing

# regla de Phishing por cuerpo de Mensaje
header PHISHING_BODY  /(Zimbra Administrator|Su contraseña expirará)/i
score PHISHING_BODY 40.0
describe PHISHING_BODY  Cuerpo de Mensaje con Phishing
Verificar la sintaxis de las reglas de spamassassin

spamassassin --lint
Reiniciamos el servicio de filtrado

systemctl restart pmg-smtp-filter.service
Crear regla WHAT de spam level 20 o más

Ir Mail Filter --> What Objects
  Create
       Name: Spam (Level 20)
            Derecha: Add
                    Spam Filter:  Level 20
Crear regla Mail Filter de notificación de correo peligroso

Ir Mail Filter --> Add
  Name: notificar correo peligroso
  Priority: 97
  Direction: IN
  Active: check
         Seleccionar regla creada
                  Available Objects
                        What Objects:  Spam (level 20)
                        To: aulautil.tk
                        Action Objects:
                                Quarantine
                                Notify admin
Para probar la regla enviamos un correo cualquiera con la palabra bitcoin en el cuerpo del mensaje

5.4.- Crear regla de filtrado para bloquear spam de salida
El objetivo es bloquear el spam de salida que pudieran generar cuentas comprometidas, un mecanismo es reducir la cantidad de mensajes consecutivos mediante rate limits; que configuraremos en policyd y otro método es capturar el spam que sale de nuestro servidor y notifique al administrador

Crear filtro de correo que bloquee spam saliente

Ir Filtro  de correo--> Derecha Agregar
      Nombre: Bloqueo de spam de salida
      Prioridad: 100
      Dirección: Saliente
      Activo: Check

Seleccionar regla creada e ir panel de la derecha
      Objetos Disponibles: Que
           Seleccionar "Spam (Level 10)"  ---> click en "+"
      Objetos Disponibles: Acción
           Seleccionar "Quarantine"  ---> click en "+"
      Objetos Disponibles: Acción
           Seleccionar "Notify Admin"
Realizar la prueba enviando desde un usuario de zimbra un correo que tenga en el cuerpo la palabra «bitcoin»

6.- Gestión de colas en Proxmox Mail Gateway
En PMG tenemos en Administración —> Colas la opción de poder visualizar la cola de correo demorados y sin entregar. Tenemos opcion para hacer un flush (reenvío), eliminar todos los mensajes o descartar la base de datos de direcciones. Sin embargo no tenemos opción para borrar mensajes individuales o en base a un patrón de búsqueda. Esto lo podemos subsanar con comandos en consola.

6.1.- Comandos en consola para administración de la cola
Ver la cola actual

mailq
Reenviar la cola actual

postsuper -r ALL
postqueue -f
Borrar toda la cola

postsuper -d ALL 
Borrar correos de la cola en base a un patrón de búsqueda

Creamos primero un script borracola

vim /usr/bin/borracola
#!/usr/bin/perl
$REGEXP = shift || die "no email-adress given (regexp-style, e.g. bl.*\@yahoo.com)!";

@data = qx</usr/sbin/postqueue -p>;
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
open(POSTSUPER,"|/usr/sbin/postsuper -d -") || die "couldn't open postsuper" ;

foreach (keys %Q) {
  print POSTSUPER "$_\n";
};
close(POSTSUPER);
Luego le damos permisos de ejecución y probamos

chmod 775 /usr/bin/borracola
Borrar todos los correos de pepito que estan en la cola

borracola pepito
Borrar todos los correos enviados a yahoo.es que estan en la cola

borracola yahoo.es
Borrar todos los correos con estado «Connection timed out» que estan en la cola

borracola "Connection timed out"
7.- Configuración de registros DKIM en Proxmox Mail Gateway
Proxmox Mail Gateway cuenta con un asistente para generar registros DKIM y permitir que los mensajes salientes a traves del relay sean firmados mejorando la posibilidad de que los correos lleguen al buzón del destinario al poder verificarse la autenticidad del mensaje.

7.1.- Habilitar el DKIM en PMG
Ir a COnfiguración --> Proxy de Correo
  DKIM

Primero crear un selector (cadena simple, puede ser el SQDN)
  Doble click en "Selector"
    Selector: relay
    Key Size: 2048
    Actualizar

Luego dar click en "Enable DKIM Signing"
    Sign Outgoing Mails: Check

Luego click en "Sign all Outgoing Mail"
    Sign all Outgoing Mail: Check
7.2.- Firmar dominios con DKIM en PMG
Ir a COnfiguración --> Proxy de Correo
  DKIM

Ir a "Sign Domains"
    Click en "Crear"
        Sign Domain: aulautil.tk
        Crear

Copiar el registro DNS y borrar todas las comillas,paréntesis y saltos de línea
Debe quedar algo similar a
relay._domainkey    IN  TXT v=DKIM1; h=sha256; k=rsa; p=MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEA0Kol6GpDuweIBG89g5t0gRmWCYmLj631MWp8WuHo1VblLixw2Z0SWjcjzQ6FRQrnhAd/avhP9XKKViT3zbvoj1+1GYEvynh0+H+mRoftU1W3d+Qc6GnaGSdy8Yc+YMUSGW5qoLCnkY9fB8z1VYi9GTkE7yg3SprRiwLUaVU8qZMry1QFKmlqSc8mTyUuKO9KR232FkeP239U82AEFxvLSKVAuP5lBMP9A3CHX4DlrgdbZjzP5AJdYK95yPufgtxFgLGcLmRt/KBlMJkpwF6nb4cypqQMYOnAYSb6jJj3LmwRtsxoAhv51m7qe3mvDoIhJwHTSyuOFoAmBDgHD0f8bQIDAQAB
7.3.- Crear el registro DNS público para el DKIM
Debemos crear un registro DNS para relay._domainkey para cada dominio agregado en el DKIM La firma de DKIM en el DNS lo obtenemos de:

Ir a COnfiguración --> Proxy de Correo
  DKIM

Click en "View DNS Record"
Ver el DNS TXT Record
Ahora ir al panel de control de nuestro DNS Público

En Freenom ir
    My Domains
        Seleccionar Dominio y click en "Manage Domain"
        Manage Freenom DNS
            Add Records
                Name: relay._domainkey
                Type: TXT
                TTL:  3600
                Target:  v=DKIM1; h=sha256; k=rsa; p=MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEA0Kol6GpDuweIBG89g5t0gRmWCYmLj631MWp8WuHo1VblLixw2Z0SWjcjzQ6FRQrnhAd/avhP9XKKViT3zbvoj1+1GYEvynh0+H+mRoftU1W3d+Qc6GnaGSdy8Yc+YMUSGW5qoLCnkY9fB8z1VYi9GTkE7yg3SprRiwLUaVU8qZMry1QFKmlqSc8mTyUuKO9KR232FkeP239U82AEFxvLSKVAuP5lBMP9A3CHX4DlrgdbZjzP5AJdYK95yPufgtxFgLGcLmRt/KBlMJkpwF6nb4cypqQMYOnAYSb6jJj3LmwRtsxoAhv51m7qe3mvDoIhJwHTSyuOFoAmBDgHD0f8bQIDAQAB
            Save Changes
En Namecheap ir
        Domain List
            Seleccionar Dominio click en "Manage"
                Advanced DNS
                    HOSTS RECDORDS
                        Click en "Add New Record"
                            TXT Record
                                Host:  relay._domainkey
                                Value: v=DKIM1; h=sha256; k=rsa; p=MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEA0Kol6GpDuweIBG89g5t0gRmWCYmLj631MWp8WuHo1VblLixw2Z0SWjcjzQ6FRQrnhAd/avhP9XKKViT3zbvoj1+1GYEvynh0+H+mRoftU1W3d+Qc6GnaGSdy8Yc+YMUSGW5qoLCnkY9fB8z1VYi9GTkE7yg3SprRiwLUaVU8qZMry1QFKmlqSc8mTyUuKO9KR232FkeP239U82AEFxvLSKVAuP5lBMP9A3CHX4DlrgdbZjzP5AJdYK95yPufgtxFgLGcLmRt/KBlMJkpwF6nb4cypqQMYOnAYSb6jJj3LmwRtsxoAhv51m7qe3mvDoIhJwHTSyuOFoAmBDgHD0f8bQIDAQAB
                            Click en Check
Esperar entre 5 a 10 minutos que refresque el DNS en Freenom, en Namecheap si es inmediato y para verificar el registro ejecutar:

dig TXT relay._domainkey.aulautil.tk  @8.8.8.8
Nos sale similar a esto

relay._domainkey.aulautil.tk. 3600 IN TXT  "v=DKIM1; h=sha256; k=rsa; p=MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEA0Kol6GpDuweIBG89g5t0gRmWCYmLj631MWp8WuHo1VblLixw2Z0SWjc" "jzQ6FRQrnhAd/avhP9XKKViT3zbvoj1+1GYEvynh0+H+mRoftU1W3d+Qc6GnaGSdy8Yc+YMUSGW5qoLCnkY9fB8z1VYi9GTkE7yg3SprRiwLUaVU8qZMry1QFKmlqSc" "8mTyUuKO9KR232FkeP239U82AEFxvLSKVAuP5lBMP9A3CHX4DlrgdbZjzP5AJdYK95yPufgtxFgLGcLmRt/KBlMJkpwF6nb4cypqQMYOnAYSb6jJj3LmwRtsxoAhv51" "m7qe3mvDoIhJwHTSyuOFoAmBDgHD0f8bQIDAQAB"
7.4.- Realizar prueba y verificación del registro DKIM
Loguearse como tuxito y enviar un correo a gmail

Abrir el correo en gmail, luego click en los "..."
  "Mostrar Original"
    Debe verse en las cabeceras el mensaje
       ARC-Authentication-Results: i=1; mx.google.com;
       dkim=pass
       


