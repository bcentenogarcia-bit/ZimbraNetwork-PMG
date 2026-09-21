Recuperación de Desastres
1.- Recuperación de desastres
1.1.- El desastre
Qué puede ir mal
Para clasificar un problema como Desastre, uno o más de los siguientes deben sucedió:

Falla de hardware de uno o más sistemas de archivos vitales (como / o /opt/zimbra /)
Contenido de un sistema de archivos vital inutilizable por interno o externo factores (como un descuidado ** rm \ *** o una intrusión externa)
Falla de hardware de la máquina física que aloja el servicio Zimbra o de la infraestructura de virtualización relacionada
Una falla crítica en una actualización / actualización de software o sistema operativo
Minimizando las posibilidades
Algunas sugerencias para minimizar las posibilidades de un desastre:

Mantenga siempre sistemas de archivos vitales en diferentes unidades (a saber, /, /opt/zimbra y su ruta de Backup NG)
Use una herramienta de monitoreo / alerta para que su servidor tome conciencia de problemas tan pronto como aparezcan
Planifique cuidadosamente sus actualizaciones y migraciones
1.2.- La recuperación
12.2.1.- Cómo recuperar su sistema
La recuperación de un sistema se divide en 2 pasos:

Recuperación del sistema base (instalación y configuración del sistema operativo, Zimbra instalación y configuración base)
Recuperación de datos (reimportación de los últimos datos disponibles al Zimbra servidor, incluidas las configuraciones de dominio y usuario, datos COS y contenido del buzón)
1.2.2.- ¿Cómo puede Backup NG ayudar con la recuperación?
La función ‘Importar copia de seguridad’ de Backup NG proporciona una manera fácil y segura realizar el paso 2 de una recuperación.

Usar la ruta de respaldo del servidor anterior como la ruta de importación le permite restaurar una instalación básica de Zimbra al último momento válido de su antiguo servidor

Este es solo un posible escenario de recuperación de desastres: más avanzado Los escenarios y técnicas se describen en la Wiki de Zimbra.

1.2.3.- El proceso de recuperación
Instale Zimbra en un nuevo servidor y configure el Servidor y Global ajustes
Instalar módulos Network NG en el nuevo servidor.
Monte la carpeta de respaldo del servidor antiguo en el nuevo. Si esto no está disponible, use la última copia de seguridad externa disponible o última copia de cualquiera.
Comience una restauración externa en el nuevo servidor utilizando el siguiente comando:
zxsuite backup doExternalRestore /path/to/the/old/store
La operación de restauración externa creará inmediatamente los dominios, cuentas y listas de distribución, tan pronto como la primera parte de la La restauración se ha completado (verifique las notificaciones de sus módulos de red NG), El sistema estará listo para sus usuarios. Correos electrónicos y otros buzones Los mensajes serán restaurados después.
1.2.4.- Configuraciones y más configuraciones
Se realiza una copia de seguridad de la configuración global y del servidor, pero no se restaura automáticamente. La integración de alto nivel de Backup NG con Zimbra le permite para restaurar sus datos a un servidor con un sistema operativo / Zimbra diferente Configuración de lanzamiento / conexión en red / almacenamiento sin ninguna otra restricción que la Se requiere una versión mínima de Zimbra para ejecutar los módulos Network NG.

Ya sea que desee crear una copia perfecta del servidor anterior o simplemente tomar una indicación de la configuración del antiguo servidor para adaptarlas a una nueva entorno, Backup NG viene con un comando CLI muy útil: «getServerConfig»

zxsuite backup getServerConfig
    El comando getServerConfig requiere más parámetros

    Sintaxis:
       zxsuite backup getServerConfig {estándar | personalizaciones} [attr1 value1 [attr2 value2 ...

    LISTA DE PARÁMETROS

    NOMBRE TIPO VALORES ESPERADOS POR DEFECTO
    tipo (M) Opción múltiple estándar | personalizaciones
    date (O) Cadena `dd / MM / aaaa HH: mm: ss` |" last "|" all "
    backup_path (O) Ruta / opt / zimbra / backup / ng /
    archivo (O) Cadena Ruta al archivo de copia de seguridad
    query (O) Cadena sección / id / clave
    verbose (O) String false
    colores (O) Cadena falsa

    (M) == parámetro obligatorio, (O) == parámetro opcional

    Ejemplo de uso:

    zxsuite backup getserverconfig standard date last
     Muestra los últimos datos de respaldo para el servidor y la configuración global.
    zxsuite backup getserverconfig standard file /path/to/backup/file
     Muestra el contenido de un archivo de respaldo en lugar del respaldo actual del servidor.
    zxsuite backup getserverconfig standard date last query zimlets/com_zimbra_ymemoticons colors true verbose true
     Muestra todas las configuraciones para el zimlet com_zimbra_ymemoticons, usando salida en color y alta verbosidad.
Específicamente, esto mostrará las últimas configuraciones respaldadas:

zxsuite backup getServerConfig standard backup_path /your/backup/path/ date last query / | less
Puede cambiar el argumento query para mostrar configuraciones específicas, p. Ej.

zxsuite backup getServerConfig standard date last backup_path /opt/zimbra/backup/ng/ query serverConfig/zimbraMailMode/test.domain.com
config date_______________________________________________________________________________________________28/02/2014 04:01:14 CET
test.domain.com____________________________________________________________________________________________________________both
Los directorio {zimbrahome}/conf/ y {zimbrahome}/postfix/conf/ también son respaldados con:

zxsuite backup getServerConfig customizations date last verbose true
ATENCIÓN: Estos archivos contienen los directorios {zimbraHome}/conf/ y {zimbraHome}/postfix/conf/ comprimido en un solo archivo comprimido.
          La restauración solo se puede realizar manualmente. Hazlo solo si sabes lo que estás haciendo.
1.3.- Máquinas virtuales e instantáneas
Gracias a la llegada de soluciones de virtualización altamente evolucionadas en el En los últimos años, las máquinas virtuales son ahora la forma más común de implementar soluciones de servidor como Zimbra Collaboration Suite.

La mayoría de los hipervisores cuentan con capacidades de instantánea personalizables y sistemas de copia de seguridad de VM basados ​​en instantáneas. En caso de desastre, siempre es posible volver a la última instantánea e importar los datos faltantes usando la función de ‘Restauración externa’ de Backup NG – usando el servidor ruta de respaldo como la ruta de importación.

12.3.1.- Recuperación ante desastres de un estado VM anterior
Los sistemas de respaldo basados ​​en instantáneas le permiten mantener una copia congelada de una VM en un estado válido y revertirlo a voluntad. Para garantizar al 100% los datos consistencia, es mejor tomar copias instantáneas de máquinas virtuales apagadas, Pero esto no es obligatorio.

Al usar este tipo de sistemas, es vital asegurarse de que La ruta de respaldo no forma parte de la instantánea (por ejemplo, configurando el disco virtual a \ `Independiente persistente en VMWare ESX / i) o alterado de alguna manera cuando retroceder para que los datos faltantes estén disponibles para la importación.

Para realizar una recuperación ante desastres de un estado de máquina anterior con Copia de seguridad NG, necesitas:

Restaurar la última copia de seguridad válida en una VM separada (clonar) en un red aislada, asegurándose de que los usuarios no puedan acceder a ella y que los correos electrónicos entrantes y salientes no se entregan.
Encienda el clon y espere a que Zimbra comience.
Desactive el escáner RealTime de Backup NG.
Conecte el disco virtual que contiene la ruta de respaldo no controlada a el clon y móntelo (en un camino diferente).
Inicie una restauración externa utilizando la ruta de respaldo como la ruta de importación.
Al hacerlo, se analizarán todos los elementos de la ruta de respaldo e importará los que faltan. unos, acelerando la recuperación ante desastres. Estos pasos se pueden repetir como tantas veces como sea necesario siempre que el acceso de los usuarios y el tráfico de correo sean inhibido

Una vez completada la restauración, asegúrese de que todo funcione y restaurar el acceso del usuario y el tráfico de correo.

1.4.- Las secuelas
¿Ahora que?
Si necesita restaurar cualquier contenido anterior al desastre, solo Inicialice una nueva ruta de respaldo y almacene la anterior.

2.- Mensajes no restaurables
2.1.- ¿Cómo puedo verificar si todos mis mensajes han sido restaurados?

Es muy facil. Marque la notificación apropiada de Operación completada recibió tan pronto como finalizó la operación de restauración. Se puede ver en la sección «Notificaciones» del Zimlet de Administración, y es también enviado por correo electrónico a la dirección que especificó en la sección Core del Administración Zimlet como la `Notificación Dirección del destinatario del correo electrónico ».

La sección `elementos omitidos ‘contiene una lista por cuenta de elementos no restaurados mensajes:

  [...]
  - estadísticas -
  Artículos restaurados: 15233
  Artículos omitidos: 125
  Artículos sin restaurar: 10

  - mensajes sin restaurar -
  cuenta: cuenta1@dominio.com
  mensajes sin restaurar: 1255,1369

  cuenta: cuenta2@dominio.com
  mensajes sin restaurar: 49965

  cuenta: cuenta14@dominio.com
  mensajes sin restaurar: 856,13339,45200, 45655
  [...]
2.1.1.- Elementos omitidos frente a elementos sin restaurar
Elemento Omitido: un elemento que ya ha sido restaurado, ya sea durante la restauración actual o en una anterior.
Elemento no restaurado: un elemento que no se ha restaurado debido a un problema en el proceso de restauración.
2.2.- ¿Por qué algunos de mis mensajes no han sido restaurados?
Existen diferentes causas posibles, las más comunes son:

Error de lectura : el elemento sin formato o el archivo de metadatos no es legible debido a una excepción de E / S o un problema de permiso.
Elemento roto : tanto el elemento sin procesar como el archivo de metadatos son legible por Backup NG pero su contenido está roto / dañado.
Elemento no válido : tanto el elemento sin procesar como el archivo de metadatos son legible y el contenido es correcto, pero Zimbra se niega a inyectar el objeto.
2.3.- ¿Cómo puedo identificar los mensajes sin restaurar?
Hay dos formas de hacerlo: a través de la CLI y a través del cliente web de Zimbra. La primera forma se puede utilizar para buscar el elemento dentro de ruta de respaldo / importación, y la segunda se puede usar para ver los elementos en el servidor de origen.

2.3.1.- Identificación de elementos no restaurables a través de la CLI
El comando CLI getItem puede mostrar un elemento y los metadatos relacionados, extraer toda la información de una ruta de respaldo / respaldo externo.

La sintaxis del comando es:

    zxsuite backup getItem {cuenta} {item} [attr1 value1 [attr2 value2 ...

    LISTA DE PARÁMETROS

    NOMBRE TIPO VALORES ESPERADOS POR DEFECTO
    cuenta (M) Nombre / ID de cuenta
    ítem (M) Entero
    backup_path (O) Ruta / opt / zimbra / backup / ng /
    dump_blob (O) Booleano verdadero | falso falso
    fecha (O) Fecha dd / mm / aaaa hh: mm: ss | todas las últimas

    (M) == parámetro obligatorio, (O) == parámetro opcional
Para extraer los datos sin procesar y la información de metadatos del elemento cuyo itemID es 49965 que pertenece a account2@domain.com, también incluye el volcado completo del BLOB del elemento, el comando sería:

zxsuite backup getItem account2@domain.com 49965 dump_blob true
2.3.2.- Identificación de elementos no restaurables a través del cliente web de Zimbra
La lista separada por comas de elementos no restaurados que se muestran en la `Operación La notificación completa se puede usar como argumento de búsqueda en Zimbra Cliente web para realizar una búsqueda de mensajes.

Para hacerlo:

Inicie sesión en la consola de administración de Zimbra en el servidor de origen.
Use la función Ver correo para acceder a la cuenta que contiene mensajes sin restaurar.
En el cuadro de búsqueda, ingrese elemento: seguido de la coma separada lista de ID de elemento.
eg
mensaje: 856,13339,45200,45655

Advertencia

Recuerde que cualquier búsqueda se ejecuta solo dentro de la pestaña ejecutado, así que si está ejecutando la búsqueda desde la pestaña Correo electrónico y no obtener resultados intente ejecutar la misma búsqueda en la Libreta de direcciones, Pestañas Calendario,Tareas y Maletín

2.4.- ¿Cómo puedo restaurar elementos sin restaurar?
Un elemento que no se está restaurando es una clara señal de un problema, ya sea con el elemento en sí o con su configuración actual de Zimbra. En algunos casos, hay buenas posibilidades de poder restaurar un elemento incluso si no fuera restaurado en el primer intento.

En los siguientes párrafos, encontrará una colección de consejos y trucos que pueden ser útiles cuando se trata con diferentes tipos de mensajes no restaurables

2.4.1.- Elementos no restaurados debido a un error de lectura
Se debe hacer una distinción obediente sobre los errores de lectura que pueden causar mensajes que no se restaurarán:

errores duros: fallas de hardware y todos los demás destructivos errores que causan una pérdida de datos irrecuperable.
errores suaves: errores ‘no destructivos’ como permisos incorrectos, errores del sistema de archivos, problemas de RAID (por ejemplo: duplicación de RAID1 rota), etc.
Si bien no hay mucho que hacer con los errores duros, puede prevenir o mitigue los errores suaves siguiendo estas pautas:

Ejecute una comprobación del sistema de archivos.
Si usa una configuración de disco RAID, verifique la matriz para ver si hay posibles problemas (según el nivel RAID).
Asegúrese de que el usuario zimbra tenga acceso a la copia de seguridad / importación ruta, todas sus subcarpetas y todos los archivos contenidos.
Verifique cuidadosamente la calidad del enlace de los sistemas de archivos compartidos en red. Si la calidad del enlace es deficiente, considere transferir los datos con rsync.
Si usa SSHfs para montar remotamente la ruta de respaldo / importación, asegúrese de ejecutar el comando mount como root usando la opción -o allow_other.
2.4.2.- Elementos no restaurados porque se identificaron como elementos rotos
Desafortunadamente, esta es la peor categoría de mensajes sin restaurar en términos de «salvabilidad».

Según el grado de corrupción del mensaje, podría ser posible recuperar un estado anterior o el objeto sin procesar (esto solo es válido para correos electrónicos). Para identificar el grado de corrupción, use la CLI getItem mando:

zxsuite backup getItem {cuenta} {item} [attr1 value1 [attr2 value2 ...

LISTA DE PARÁMETROS

NOMBRE TIPO VALORES ESPERADOS POR DEFECTO
cuenta (M) Nombre / ID de cuenta
ítem (M) Entero
backup_path (O) Ruta / opt / zimbra / backup / ng /
dump_blob (O) Booleano verdadero | falso falso
fecha (O) Fecha dd / mm / aaaa hh: mm: ss | todas las últimas

(M) == parámetro obligatorio, (O) == parámetro opcional
Buscando el elemento roto, estableciendo el parámetro backup_path en la ruta de importación y el parámetro date aall, mostrarán todos estados válidos para el mensaje.

zimbra @ test: ~ $ zxsuite backup getItem admin@example.com 24700 backup_path /mnt/import/date all itemStates
               fecha de inicio 07/12/2013 16:35:44
               escribir mensaje
               verdadero eliminado
               ruta de blob / mnt / import / items / c0 / c0, gUlvzQfE21z6YRXJnNkKL85PrRHw0KMQUqo, pMmQ =
               fecha de inicio 07/12/2013 17:04:33
               escribir mensaje
               verdadero eliminado
               ruta de blob / mnt / import / items / c0 / c0, gUlvzQfE21z6YRXJnNkKL85PrRHw0KMQUqo, pMmQ =
               fecha de inicio 15/07/2013 10:03:26
               escribir mensaje
               verdadero eliminado
               ruta de blob / mnt / import / items / c0 / c0, gUlvzQfE21z6YRXJnNkKL85PrRHw0KMQUqo, pMmQ =
Si el mensaje es un correo electrónico, podrá recuperar un .eml estándar presentar los siguientes pasos:

Identificar el último estado válido
<! – ->

/ mnt / import / items / c0 / c0, gUlvzQfE21z6YRXJnNkKL85PrRHw0KMQUqo, pMmQ =
              fecha de inicio 15/07/2013 10:03:26
              escribir mensaje
              verdadero eliminado
              ruta de blob / mnt / import / items / c0 / c0, gUlvzQfE21z6YRXJnNkKL85PrRHw0KMQUqo, pMmQ =
Identificar la ruta de acceso de blob
ruta de acceso de blob / mnt / import / items / c0 / c0, gUlvzQfE21z6YRXJnNkKL85PrRHw0KMQUqo, pMmQ =

Use gzip para descomprimir el archivo BLOB en un archivo .eml
<! – ->

Prueba zimbra @: ~ $ gunzip -c / mnt / import / items / c0 / c0, gUlvzQfE21z6YRXJnNkKL85PrRHw0KMQUqo, pMmQ => /tmp/restored.eml

prueba de zimbra @: ~ $ cat /tmp/restored.eml

Ruta de retorno: zimbra@test.example.com

Recibido: de test.example.com (LHLO test.example.com) (192.168.1.123)
por test.example.com con LMTP; Vie, 12 de julio de 2013 16:35:43 +0200 (CEST)

Recibido: por test.example.com (Postfix, de userid 1001) id 4F34A120CC4;
Vie, 12 de julio de 2013 16:35:43 +0200 (CEST)
Para: admin@ejemplo.com
De: admin@example.com
Asunto: El buzón de servicio comenzó en test.example.com
Id. De mensaje: <20130712143543.4F34A120CC4@test.example.com>
Fecha: viernes, 12 de julio de 2013 16:35:43 +0200 (CEST)

12 de julio 16:35:42 prueba zmconfigd [14198]: Cambio de estado del servicio: el buzón de prueba.example.com cambió de detenido a en ejecución
¡Hecho! Ahora puede importar el archivo .eml en el archivo apropiado buzón utilizando su cliente favorito.
2.5.- Elementos no restaurados porque se identificaron como elementos no válidos
Un elemento se identifica como No válido cuando, aunque sea formalmente correcto, es descartado por el Validator LMTP de Zimbra tras la inyección. Esto es comun al importar elementos creados en una versión anterior de Zimbra a una más nueva uno, las reglas de validación se actualizan muy a menudo, por lo que no todos los mensajes considerado válido por una determinada versión de Zimbra todavía se considera válido por una versión más nueva.

Si experimentaste muchos mensajes sin restaurar durante una importación, podría sea ​​una buena idea desactivar momentáneamente el validador LMTP y repetir el importar:

Para deshabilitar el Validator LMTP de Zimbra, ejecute el siguiente comando como Usuario de Zimbra:
zmlocalconfig -e zimbra_lmtp_validate_messages = false

Una vez que se completa la importación, puede habilitar el validador LMTP corriendo
zmlocalconfig -e zimbra_lmtp_validate_messages = true

Advertencia

Esta es una solución sucia, ya que los elementos considerados no válidos por el LMTP el validador puede causar errores de visualización o sincronización móvil. Usar en su propio riesgo.

3.- doCoherencyCheck
¿Qué es la verificación de coherencia?
La Comprobación de coherencia realiza una comprobación más profunda de una ruta de respaldo que la uno hecho por SmartScan.

Mientras que el SmartScan funciona ‘incrementalmente’ al verificar solo los elementos que se han modificado desde el último SmartScan, la verificación de coherencia realiza una verificación exhaustiva de todos los metadatos y BLOB en la ruta de respaldo.

Está específicamente diseñado para detectar metadatos y BLOB corruptos.

¿Como funciona?
La verificación de coherencia verifica la integridad de todos los metadatos en la copia de seguridad ruta y de los BLOB relacionados. Si se encuentran errores, ejecute el verificar con la opción fixBackup moverá a cualquier huérfano o corrupto metadatos / BLOB a un directorio dedicado dentro de la ruta de respaldo.

¿Cuándo se debe ejecutar una verificación de coherencia?

En períodos de intervalo para asegurarse de que todo esté bien (por ejemplo, cada 3 o 6 meses).
Después de un bloqueo del sistema.
Después del sistema de archivos o dispositivo de almacenamiento que contiene la ruta de respaldo Experimenta cualquier problema.
En caso de que SmartScan detecte un posible elemento dañado, una coherencia La verificación se iniciará automáticamente.

Advertencia

La verificación de coherencia consume mucha E / S, así que asegúrese de ejecutarla solo durante los períodos de menor actividad

3.1.- Ejecución de una verificación de coherencia
Inicio de la comprobación a través del Zimlet de administración
La verificación de coherencia no está disponible a través del Zimlet de administración.

Inicio de la verificación a través de la CLI
Para iniciar una verificación de coherencia a través de la CLI, use el comando doCoherencyCheck mando:

Sintaxis:
   zxsuite backup doCoherencyCheck {backup_path} [attr1 value1 [attr2 value2 ...

LISTA DE PARÁMETROS

NOMBRE TIPO VALORES ESPERADOS POR DEFECTO
backup_path (M) Ruta
cuentas (O) Nombre / ID de cuenta [, ..] todos
checkZimbra (O) Boolean verdadero | falso falso
fixBackup (O) Boolean verdadero | falso falso
notificaciones (O) Dirección de correo electrónico [, ..]

(M) == parámetro obligatorio, (O) == parámetro opcional

Ejemplo de uso:

zxsuite backup docoherencycheck /opt/zimbra/backup/ng/ accounts tuxito@aulautil.tk,gerente@aulautil.com
Realiza una verificación de coherencia en /opt/zimbra/backup/ng/ para las cuentas de Tuxito y Gerentazo
zxsuite backup docoherencycheck /opt/zimbra/backup/ng/ fixBackup true
Realiza una comprobación de coherencia en /opt/zimbra/backup/ng/ y saca de la copia de seguridad los archivos de copia de seguridad dañados y los archivos de blob a los que no hace referencia ningún metadato.
Comprobación del estado de una comprobación en ejecución
Para verificar el estado de un escaneo en ejecución a través de la CLI, use el monitor mando:

Sintaxis:
   zxsuite backup monitor {operation_uuid} [attr1 value1 [attr2 value2 ...

LISTA DE PARÁMETROS

NOMBRE TIPO
operation_uuid (M) Uiid
operación_host (O) Cadena

(M) == parámetro obligatorio, (O) == parámetro opcional
4.- Realización de copias de seguridad adicionales y externas del almacén de datos de Backup NG ================================================== ============

4.1.- ¿Quién vigila a los vigilantes?
Tener sistemas de respaldo es una gran medida de seguridad contra la pérdida de datos, pero cada sistema de respaldo debe ser parte de una «estrategia de respaldo» más amplia para asegurar El nivel de fiabilidad más alto posible. La falta de una copia de seguridad adecuada la estrategia da una falsa sensación de seguridad, mientras que en realidad se vuelve uniforme Los mejores sistemas de respaldo del mundo en otro punto de quiebre.

Diseñar una estrategia de respaldo no es fácil, y en algún momento lo harás Lo más probable es que se enfrente a la siguiente pregunta: ¿Qué pasa si yo pierdo los datos que hice una copia de seguridad?. Las posibilidades de que esto ocurra en última instancia, solo depende de cómo realice y administre sus copias de seguridad. Es es más probable que pierda todos sus datos respaldados si almacena tanto sus datos como sus copias de seguridad en un solo disco SATAII que si usted almacene sus datos respaldados en una SAN dedicada utilizando una configuración RAID 1 + 0.

Aquí hay algunas sugerencias y mejores prácticas para mejorar su copia de seguridad estrategia haciendo una copia de seguridad del almacén de datos de Backup NG y almacenándola Fuera del sitio.

4.2.- Realizar una copia de seguridad adicional del almacén de datos de Backup NG

Atomicidad : cualquier transacción se confirma y se escribe en el disco solo cuando se haya completado.
Consistencia : cualquier transacción confirmada es válida y no inválida la transacción se confirmará y se escribirá en el disco.
Aislamiento : todas las transacciones se ejecutan secuencialmente para que no Más de 1 transacción puede afectar el mismo mensaje a la vez.
Durabilidad : una vez que se confirma una transacción, permanecerá así incluso en caso de un choque (por ejemplo, pérdida de energía o falla de hardware).
Debido a esto, es muy fácil hacer una copia de seguridad. La mejor (y más fácil) forma para hacerlo es usando http://rsync.samba.org/ [rsync]. Opciones especificas y los parámetros dependen de muchos factores, como la cantidad de datos a ser sincronizado y el almacenamiento en uso, mientras se conecta a un demonio rsync en lugar de usar un shell remoto como transporte, generalmente es mucho más rápido en transfiriendo los datos.

No necesitará detener Zimbra o el Escáner en tiempo real para hacer un copia de seguridad adicional del almacén de datos de Backup NG usando rsync, y usted será siempre capaz de detener la sincronización en cualquier momento y repetirla luego si necesario.

4.3.- Almacenamiento de la copia de seguridad del almacén de datos de Backup NG fuera del sitio
Como se vio en la sección anterior, hacer una copia de seguridad de Backup NG’s El almacén de datos es muy fácil, y el uso de rsync hace que sea igual de fácil Almacene su copia de seguridad en una ubicación remota.

Para optimizar su estrategia de copia de seguridad cuando se trata con este tipo de configuración, Se recomiendan las siguientes mejores prácticas:

Si programa sus copias de seguridad rsync, asegúrese de dejar suficiente tiempo entre una instancia de rsync y la siguiente para que Transferencia a completar.
Utilice las opciones –delete para que los archivos que se hayan eliminado en el el servidor de origen se elimina en el servidor de destino para evitar inconsistencias
Si nota que usar la opción --delete toma demasiado tiempo, programe dos instancias rsync diferentes: una con --delete se ejecutará después de la purga semanal y una sin esto opción.
Asegúrese de transferir todo el árbol de carpetas de forma recursiva comenzando de la ruta de respaldo de Backup NG. Esto incluye copias de seguridad de la configuración del servidor y mapfiles.
Asegúrese de que el sistema de archivos de destino distingue entre mayúsculas y minúsculas (tal como La ruta de respaldo de Backup NG debe ser).
Si planea restaurar directamente desde la ubicación remota, asegúrese de que el usuario zimbra en su servidor tiene permisos de lectura y escritura en los datos transferidos.
Espere experimentar lentitud si su velocidad de transferencia es mucho mayor que su capacidad de almacenamiento (o viceversa).
4.4.- Backup Total Externo Completo con Rsync (configuración y datos; local y remoto)
Instalamos rsync y creamos un script que configure el backup completo del zimbra. Para este escenario la carpeta /backup debe ser un disco iscsi alojado en un servidor externo local o remoto que servirá como servidor de contingencia.

yum -y install rsync
4.4.1.- Backup Total de modo local
Crear script de backup de contingencia local

mkdir /opt/zimbra/scripts
vim /opt/zimbra/scripts/backup-total-local
#!/bin/bash

echo "Haciendo la copia en caliente"
rsync -avz --delete /opt/zimbra /backup --exclude 'zimbra/data/ldap/mdb/db/data.mdb'

echo "deteniendo zimbra"
su - zimbra -c "zmcontrol stop"

echo "hacer la copia incremental espejo"
rsync -avz --delete /opt/zimbra /backup --exclude 'zimbra/data/ldap/mdb/db/data.mdb'
rm -f /backup/zimbra/data/ldap/mdb/db/*
su - zimbra -c "mdb_copy /opt/zimbra/data/ldap/mdb/db/ /backup/zimbra/data/ldap/mdb/db/"

echo "levantando zimbra"
su - zimbra -c "zmcontrol start"
En clase crear /backup

mkdir /backup
Ejecutando el backup completo

chmod 755 /opt/zimbra/scripts/backup-total-local
/opt/zimbra/scripts/backup-total-local
4.4.2.- Backup Total de modo remoto
Crear script de backup de contingencia remota

mkdir /opt/zimbra/scripts
vim /opt/zimbra/scripts/backup-total-remoto
#!/bin/bash
# Script de backup hacia servidor externo por SSH
# Script creado por Clever Flores para aula útil
# copyright 2020

IPServidorNube=192.168.X.10
dirbackup=/backup

echo "Haciendo la copia en caliente hacia el servidor externo"
rsync -avz --delete /opt/zimbra $IPServidorNube:$dirbackup --exclude 'zimbra/data/ldap/mdb/db/data.mdb' --exclude 'zimbra/backup'

echo "deteniendo zimbra"
su - zimbra -c "zmcontrol stop"

echo "hacer la copia incremental espejo hacia el servidor externo"
rsync -avz --delete /opt/zimbra $IPServidorNube:$dirbackup --exclude 'zimbra/data/ldap/mdb/db/data.mdb' --exclude 'zimbra/backup'

if [ ! -d /tmp/mdb ]; then
  su - zimbra -c "mkdir /tmp/mdb"
else
  rm -f /tmp/mdb/data.mdb
fi

su - zimbra -c "mdb_copy /opt/zimbra/data/ldap/mdb/db/ /tmp/mdb"
rsync -avz /tmp/mdb/data.mdb $IPServidorNube:$dirbackup/zimbra/data/ldap/mdb/db/

echo "levantando zimbra"
su - zimbra -c "zmcontrol start"
Compartiendo clave compartida al servidor remoto Generar Key en el servidor zimbra de producción, dar enter, enter, enter

ssh-keygen
Copiar key al servidor remoto (en clase al relay)

ssh-copy-id 192.168.X.10
Crear carpeta /backup en el servidor remoto (en clase el relay)

ssh 192.168.X.10 mkdir /backup
Ejecutando el backup completo remoto

chmod 755 /opt/zimbra/scripts/backup-total-remoto
/opt/zimbra/scripts/backup-total-remoto
4.4.3.- Programando el backup total
crontab -e
# backup total imagen
0 2 * * * /opt/zimbra/scripts/backup-total-local
0 4 * * * /opt/zimbra/scripts/backup-total-remoto
4.5.- Restauración completa de datos y configuración
Instalar Centos y Zimbra (mismas versiones del servidor original) y asegurarse que el uid y gid de zimbra coincide entre los dos servidores.

su - zimbra -c "zmcontrol stop"
rm -fR /opt/zimbra
Restaurar el backup, montando el disco de contingencia donde se hizo el backup completo de rsync Backup de directorio

mount /dev/sdb1 /opt
/opt/zimbra/libexec/zmfixperms
su - zimbra -c "zmcontrol start"
Restaurar el backup en clase, desde el backup local

unalias cp
cp -Rpfv /backup/zimbra /opt
/opt/zimbra/libexec/zmfixperms
su - zimbra -c "zmcontrol start"
Habilitar el servidor de contingencia si el backup se direccionó previamente allí Previamente debimos haber direccionado el backup de rsync completo a ese servidor Ahora ejecutar el script de contingencia que tiene una apariencia similar a

vim /usr/bin/contingencia.sh
#!/bin/bash 
systemctl stop tgtd
systemctl disable tgtd
service zimbra stop
rm -fR /opt/zimbra
# Montamos el disco que se usó para el backup (ej: /dev/sdb1)
mount /dev/sdb1 /opt

# Iniciamos el dnsmaq y named (debe estar preconfigurado) con la ip del servidor de producción
systemctl start dnsmasq
systemctl enable dnsmasq
systemctl start named
systemctl enable named

# reconfiguramos la red de la tarjeta enp4s0
echo "DEVICE=enp4s0
TYPE=Ethernet
ONBOOT=yes
BOOTPROTO=static
IPADDR0=192.168.3.X
PREFIX0=24
GATEWAY0=192.168.3.1
DNS1=127.0.0.1">/etc/sysconfig/network-scripts/ifcfg-enp4s0
systemctl restart network

echo "127.0.0.1 localhost.localdomain localhost
192.168.3.X mail.dominio.com dominio.com">/etc/hosts

/opt/zimbra/libexec/zmfixperms
systemctl start zimbra
chmod 755 /usr/bin/contingencia.sh
Ejecutar la contingencia (solo se ejecuta 1 vez, tener cuidado)

contigencia.sh
Si da errores ejecutar

service zimbra stop
/opt/zimbra/libexec/zmfixperms
service zimbra start
4.6.- Backup programado del MySQL y de LDAP
La mayor cantidad de errores lógicos en zimbra se dan cuando se corrompen las bases de datos LDAP o MySQL Por ello es recomendable hacer un backup programado de esas bases de datos

Crear directorios de backup de ldap y mysql

mkdir -p /backup/ldap/db
mkdir /backup/mysql
chown -R zimbra.zimbra /backup/ldap
chown zimbra.zimbra /backup/mysql
vim /opt/zimbra/scripts/backup-dbs 
#!/bin/bash
# Script de backup local de LDAP y MySQL
# Script creado por Clever Flores para aula útil
# copyright 2020
echo backup del ldap
su - zimbra -c "/opt/zimbra/libexec/zmslapcat /backup/ldap"
su - zimbra -c "/opt/zimbra/libexec/zmslapcat -c /backup/ldap"
rm -f /backup/ldap/db/*
su - zimbra -c "mdb_copy /opt/zimbra/data/ldap/mdb/db/ /backup/ldap/db/"

echo backup de mysql
rootpass=`su - zimbra -c "zmlocalconfig -s | grep mysql_root_password|grep -v antispam|cut -d' ' -f3"`
/opt/zimbra/common/bin/mysqldump --user=root --password=$rootpass --socket=/opt/zimbra/data/tmp/mysql/mysql.sock --all-databases --single-transaction --flush-logs > /backup/mysql/all-`date +%Y-%m-%d-%H-%M`.sql
Dar permisos de ejecución

chmod 755 /opt/zimbra/scripts/backup-dbs
Ejecutar Script

/opt/zimbra/scripts/backup-dbs
Programar script de backup de base de datos

crontab -e
# backup total imagen
0 2 * * * /opt/zimbra/scripts/backup-total-local
0 4 * * * /opt/zimbra/scripts/backup-total-remoto
0 5 * * * /opt/zimbra/scripts/backup-dbs
4.7.- Restauración a partir del Backup del MySQL y de LDAP
4.7.1.- Restauración de LDAP con archivo mdb
Detener el ldap

su - zimbra -c "ldap stop"
Borrar mdb dañado

su - zimbra 
rm -f /opt/zimbra/data/ldap/mdb/db/data.mdb
exit
Restaurar backup de mdb

su - zimbra -c "mdb_copy /backup/ldap/db/ /opt/zimbra/data/ldap/mdb/db/"
Iniciar el ldap

su - zimbra -c "ldap start"
4.7.2.- Restauración de LDAP con backup LDIF
Detener el ldap

su - zimbra -c "ldap stop"
Mover mdb

su - zimbra 
cd /opt/zimbra/data/ldap
mv mdb mdb.old
mkdir -p mdb/db
Mover config de LDAP (solo si es necesario)

cd /opt/zimbra/data/ldap
mv config config.bak
mkdir config
Restaurar backup de configuración de LDAP

/opt/zimbra/libexec/zmslapadd -c /backup/ldap/ldap-config.bak
Restaurar datos de LDAP

/opt/zimbra/libexec/zmslapadd /backup/ldap/ldap.bak
Reiniciando el zimbra

zmcontrol restart
4.7.3.- Restauración de MySQL por el archivo sql
Detener mysql

mysql.server stop
Mover base de datos corrupta

mv /opt/zimbra/db/data /opt/zimbra/db/data_new
Cargar variables del mysql

source ~/bin/zmshutil
zmsetvars
Inicializar base de datos

/opt/zimbra/libexec/zmmyinit --sql_root_pw $mysql_root_password
Importar backup

mysql --user=root --password=$mysql_root_password < /backup/mysql/all-2019-MM-DD-HH-MI.sql
Obtener clave generada para el usuario zimbra de mysql

zimbrapass=`grep zimbra_mysql_password /opt/zimbra/conf/localconfig.xml -A1|grep value|cut -d '>' -f2|cut -d '<' -f1`
Cambiando clave del usuario zimbra del mysql

zmmypasswd $zimbrapass
Reiniciando el zimbra

zmcontrol restart
4.8.- MySQL Crash Recovery
A veces hay un daño lógico en la base de datos MysQL, pero recuperable, entonces podemos ejecutar este procedimiento

Detener servidor mysql

mysql.server stop
vim /opt/zimbra/conf/my.cnf
Forzar a MySQL que levante el servicio aún con errores lógicos, el valor puede ir de 1 a 6 (vamos de 1 hasta máximo 6, siendo que 6 puede perder datos)
La idea es que si podemos exportar la datc con valor 1, eso es suficiente. Si no permite exportar le ponemos 2 y así hasta un máximo de 6

[mysqld]
innodb_force_recovery = 1
Iniciar mysql

mysql.server start
Generar backup de las bases de datos

source ~/bin/zmshutil ; zmsetvars
mysql --batch --skip-column-names -e "show databases" | grep -e mbox -e zimbra -e chat > /tmp/mysql.db.list
mkdir /tmp/mysql.sql 
for db in `cat /tmp/mysql.db.list`; do
 ~/common/bin/mysqldump $db -S $mysql_socket -u root --password=$mysql_root_password > /tmp/mysql.sql/$db.sql
     echo "Dumped $db"
     sleep 2
done
Verificar el Backup
Verificar si se generó el backup (veriifcar que los archivos no tengo tamaño cero).
Si no generó bien el backup subir el valor innodb_force_recovery = 2 y volver a hacer el procedimiento

less /tmp/mysql.db.list
ls -l /tmp/mysql.sql/
Una vez que se verificó el backup, remover las bases de datos corruptas

for db in `cat /tmp/mysql.db.list |grep mbox`
do
    mysql -u root --password=$mysql_root_password -e "drop database $db"
    echo -e "Dropped $db"
done
mysql -u root --password=$mysql_root_password -e "drop database zimbra"
rm -rf /opt/zimbra/db/data/ib*
Borrar o comentar la línea añadida en la configuración

vim /opt/zimbra/conf/my.cnf
[mysqld]
#innodb_force_recovery = 1
Reiniciar mysql

mysql.server restart
Crear base de datos

for db in `cat /tmp/mysql.db.list`
do
    mysql -e "create database $db character set utf8"
    echo "Created $db"
done
Importar el backup

mysql zimbra < /tmp/mysql.sql/zimbra.sql

for sql in /tmp/mysql.sql/mbox*
do
    mysql `basename $sql .sql` < $sql
    echo -e "Updated `basename $sql .sql` \n"
done

mysql chat < /tmp/mysql.sql/chat.sql
Verificar que este bien la importación

mysql zimbra -e "select * from mailbox order by id desc limit 1"
Reiniciar el MySQL

mysql.server stop
zmcontrol restart
4.9.- Preguntas frecuentes adicionales sobre copias de seguridad externas
¿Por qué no debería usar la función «Exportar copia de seguridad» de Backup NG en lugar de rsync?

Por muchas razones:

La función Exportar copia de seguridad está diseñada para realizar migraciones. Eso exporta una ‘instantánea’ que es un fin en sí misma y no fue diseñada para ser gestionado de forma incremental. Cada vez que se ejecuta una copia de seguridad de exportación, probablemente tomará tanto tiempo como el anterior, mientras El uso de rsync es mucho más eficiente en el tiempo.
Al ser una operación Backup NG, cualquier otra operación comenzó mientras el Exportar copia de seguridad en ejecución se pondrá en cola hasta que se exporte terminado.
Una operación de ‘Exportar copia de seguridad’ tiene un mayor impacto en los recursos del sistema que un rsync
Si necesita detener una operación de Copia de seguridad de exportación, no estará capaz de repetirlo, y tendrás que comenzar desde cero.
¿Puedo usar esto para la recuperación de desastres?

Si. Obviamente, si su ruta de respaldo todavía está disponible. es mejor use eso, ya que restaurará todos los elementos y configuraciones a la última validez estado. Sin embargo, si pierde su ruta de respaldo, podrá usar su respaldo adicional / fuera del sitio.

¿Puedo usar esto para restaurar datos en el servidor al que pertenece la copia de seguridad?

Sí, pero no a través de la operación ‘Restauración externa’, ya que el elemento y Las identificaciones de carpeta son las mismas.

Los pasos más apropiados para restaurar datos de una copia de la copia de seguridad. La ruta al mismo servidor es la siguiente:

Detenga el escáner RealTime.
Cambie la ruta de respaldo a la copia que desea restaurar sus datos de.
Ejecute Restaurar en cuenta nueva o unaRestaurar cuenta eliminada.
Una vez que finalice la restauración, cambie la ruta de respaldo al original uno.
Inicie el escáner RealTime. Un SmartScan se activará para actualizar el los datos de copia de seguridad.
¿Puedo usar esto para crear una infraestructura activa / en espera?

No, porque la operación de ‘Restauración externa’ no realiza ninguna eliminaciones Al ejecutar varias restauraciones externas, terminarás llenando sus buzones con contenido no deseado, ya que los elementos eliminados de el buzón original no se eliminará en el servidor en espera.

La operación Restauración externa ha sido diseñada para que las cuentas estará disponible para su uso tan pronto como se inicie la operación, para que sus usuarios podrá enviar y recibir correos electrónicos incluso si la restauración se está ejecutando.

¿Hay alguna otra forma de hacer una copia de seguridad adicional / externa de mi ¿sistema?

Los hay con seguridad, y algunos de ellos incluso podrían ser mejores que el descrito aquí. Estas son solo pautas que se aplican a la mayoría de casos.
