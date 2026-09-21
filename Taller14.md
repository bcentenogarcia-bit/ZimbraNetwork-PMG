ActiveSync y Aprovisionamiento de móviles
1.- Habilitar sincronización móvil NG para un COS
Al habilitar Mobile NG para todos los usuarios en una Clase de servicio, usted autorice a todos los usuarios de esa COS a utilizar todas las funciones móviles de Mobile NG.

2.- Cómo habilitar Mobile NG para todos los usuarios en una clase de servicio
2.1.- Desde la consola de administración
Para habilitar Mobile NG para todos los usuarios en un COS de la Administración Consola:

Abra la consola de administración de Zimbra.
Haga doble clic en la Clase de servicio que desea editar (a la izquierda, en Configuración â † ‘Clase de servicio).
Haz clic en la pestaña Móvil.
Marque el botón Habilitar sincronización móvil.
2.2.- Desde la CLI de Zimbra
Para habilitar Mobile NG para todos los usuarios en un COS desde la CLI:

Como el usuario zimbra ejecuta: zmprov mc COSName zimbraFeatureMobileSyncEnabled TRUE
3.- Cómo deshabilitar Mobile NG para todos los usuarios en una clase de servicio
3.1.- Desde la consola de administración
Para deshabilitar Mobile NG para todos los usuarios en un COS de la Administración Consola:

Abra la consola de administración de Zimbra.
Haga doble clic en la Clase de servicio que desea editar (a la izquierda, en Configuración â † ‘Clase de servicio).
Haga clic en la pestaña Móvil y desactive la casilla `Habilitar móvil botón de sincronización
3.2.- Desde la CLI de Zimbra
Para deshabilitar Mobile NG para todos los usuarios en un COS desde la CLI:

Como el usuario zimbra ejecuta: zmprov mc COSName zimbraFeatureMobileSyncEnabled FALSE
Nota sobre la jerarquía de configuración
La configuración de nivel COS se anula por la configuración de nivel de usuario.

4.- Habilitar Mobile NG para un solo usuario
Al habilitar el Módulo Mobile NG para un solo usuario, autoriza un usuario único para usar todas las funciones móviles del módulo Mobile NG.

4.1.- Cómo habilitar Mobile NG para un solo usuario
4.1.1.- Desde la consola de administración de Zimbra
Para habilitar Mobile NG para un solo usuario desde la Consola de administración:

Abra la consola de administración de Zimbra.
Haga doble clic en el usuario que desea editar (a la izquierda, en Administrar â † ‘ Cuentas)
Haz clic en la pestaña Móvil.
Marque Habilitar sincronización móvil.
4.1.2.- De la CLI de Zimbra
Para habilitar Mobile NG para un solo usuario desde la CLI:

Como el usuario zimbra ejecuta: **
zmprov ma user@domain.tld zimbraFeatureMobileSyncEnabled TRUE
4.2.- Cómo deshabilitar Mobile NG para un solo usuario
4.2.1.- Desde la consola de administración de Zimbra
Para deshabilitar Mobile NG para un solo usuario desde la CLI:

Abra la consola de administración de Zimbra.
Haga doble clic en el usuario que desea editar (a la izquierda, en Administrar â † ‘ Cuentas)
Haga clic en la pestaña Mobile NG y desmarque `Habilitar móvil sincronización ».
4.2.4.- De la CLI de Zimbra
Para deshabilitar Mobile NG para un solo usuario desde la CLI:

Como el usuario zimbra ejecuta: zmprov ma user@domain.tld zimbraFeatureMobileSyncEnabled FALSE
Nota sobre la jerarquía de configuración
La configuración de nivel de usuario anula la configuración de nivel de COS.

5.- La función de contraseña móvil
5.1.- Contraseñas móviles y usted
La función «Contraseña móvil» permite que los administradores globales y delegados establezcan una contraseña adicional para una cuenta que se utilizará para Exchange ActiveSync solo autenticaciones

Los principales beneficios de usar esta función son:

Haga cumplir las contraseñas seguras set-and-forget, independientemente de cualquier otra política de contraseñas, para que no necesite cambiar la contraseña guardada en todos los dispositivos móviles sincronizados con una cuenta si esto cambio de contraseña de Zimbra de la cuenta.
Evite que se revele la contraseña real en caso de no autorización acceso al dispositivo / cliente.
Una Contraseña móvil no será válida para los inicios de sesión de Webmail / POP3 / IMAP / SMTP, y la contraseña de la cuenta no será válida para inicios de sesión móviles.

5.2.- Cómo establecer una contraseña móvil para un buzón
Establecer una contraseña móvil es fácil:

Abra la consola de administración de Zimbra.
Haga clic con el botón derecho en el usuario para el que desea establecer una contraseña móvil y seleccione Editar.
En la pestaña Móvil dentro de la configuración del usuario, marque la casillaHabilitar casilla de verificación de contraseña móvil.
Ingrese la contraseña deseada en el campo Contraseña móvil e ingrese nuevamente en el campo \ Confirmar contraseña móvil. También puedes elegir para generar una contraseña móvil aleatoria haciendo clic en \Generar botón de contraseña aleatoria «.
Salvar.
6.- Administración de dispositivos móviles, también conocido como aprovisionamiento móvil
6.1.- ¿Qué es la gestión de dispositivos móviles?
La administración de dispositivos móviles (MDM, también conocida como aprovisionamiento) permite un administrador para definir un conjunto de reglas y configuraciones de seguridad que son aplicado por aire a uno o más dispositivos móviles, desde PIN políticas para las listas de aplicaciones permitidas / bloqueadas e incluidos los comandos one time, como el borrado remoto de todo el dispositivo.

MDM efectivamente permite a los administradores limitar y restringir el uso de dispositivos móviles corporativos para evitar comportamientos riesgosos o inapropiados.

MDM también es una ayuda invaluable para `Trae tu Políticas corporativas del dispositivo propio, lo que permite a los usuarios conectar su personal dispositivos móviles a los servidores corporativos, al tiempo que reduce el riesgo de violaciones de seguridad al mínimo.

6.1.1.- Características de aprovisionamiento disponibles en su cliente
No todas las funciones de aprovisionamiento están disponibles en todos los clientes.

6.2.- Network NG y MDM
Network NG presenta funciones avanzadas de MDM a través de Exchange Protocolo ActiveSync versión 14+.

Las políticas móviles se pueden habilitar en los niveles de COS y buzón, lo que permite tanto Una configuración rápida uno para muchos y una gestión personalizada basada en el usuario. En En ambos casos, las Opciones de administración móvil están disponibles en la pestaña Móvil.

6.2.1.- Opciones de aprovisionamiento
Las siguientes opciones de aprovisionamiento están disponibles:

Activar administración de dispositivos móviles: active o desactive el uso de dispositivos móviles políticas para el usuario actual / COS.
Permitir dispositivos no aprovisionables: permite al usuario sincronizar cualquier dispositivo que no admite aprovisionamiento.
Permitir la aplicación parcial de políticas en el dispositivo: Permitir al usuario sincronizar cualquier dispositivo que no sea compatible con uno o más políticas
Consejo

Por defecto, MDM está deshabilitado en NG MobileSync. Para habilitar navegar a Módulos de red NG â † ‘Mobile â †’ Configuración avanzada y marque la casilla â € œHabilitar Gestión de dispositivos móviles opción

6.2.2.- Políticas exigibles
Las políticas exigibles están disponibles justo debajo de los Dispositivos móviles lista, agrupada en las siguientes categorías:

Configuración de sincronización: establece intervalos y límites de sincronización.
Configuración del dispositivo: habilite o deshabilite funciones del dispositivo como la cámara, WiFi, almacenamiento extraíble o Bluetooth.
Configuración de seguridad del dispositivo: forzar un código de desbloqueo y definir el requisitos mínimos para el código en sí.
Aplicaciones del dispositivo: active o desactive el dispositivo estándar aplicaciones como el navegador y el cliente POP / IMAP o sin firmar aplicaciones
También hay dos listas disponibles para la lista blanca / negra de la aplicación. administración:

Aplicaciones aprobadas: una lista personalizable de aplicaciones aprobadas.
Aplicaciones bloqueadas: una lista personalizable de aplicaciones bloqueadas eso no será utilizable en el dispositivo.
6.2.3.- Contraseña móvil
Aunque conceptualmente similar, la función de contraseña móvil no es parte de Mobile Device Management y se puede usar con cualquier versión de EAS protocolo.

7.- SyncStates
7.1.- Mobile NG y SyncState
SyncState (abreviatura de Estado de sincronización) es un conjunto de información mantenido en el servidor sobre la sincronización con un dispositivo móvil. Cada cada vez que un dispositivo establece una conexión con Mobile NG, lo siguiente los pasos tienen lugar:

El dispositivo solicita una operación folderSync para sincronizar el Carpetas locales con las del servidor.
\ Se envía una SyncKey por carpeta local (o una sola SyncKey establecida en * 0 * si esta es la primera conexión entre el dispositivo y el servidor) \

El servidor responde con una lista de carpetas disponibles.
\ El servidor envía una SyncKey por carpeta. \

Luego, el dispositivo solicita una operación itemSync para sincronizar Todos los artículos vencidos.
\ El servidor almacena los elementos sincronizados en SyncState. \

Después de completar la operación itemSync, el dispositivo envía un Comando ping para mantener viva la conexión.
\ `El paso 4 se repite siempre que no ocurran cambios en la sincronización cuenta.

Cada vez que se almacena un nuevo elemento en el buzón o un elemento antiguo es modificado, el servidor notifica la disponibilidad al dispositivo, que cierra la conexión activa (la que mantiene vivo el comando ping) y repite los pasos 3 y 4.

SyncState es la combinación de SyncKeys guardadas en el paso 2 y el itemIds guardado en el paso 3. El servidor lo guarda según el userId / deviceId par único.

7.1.1- Solicitud de sincronización
La solicitud de sincronización es el proceso de sincronización real, iniciado por ya sea Mobile NG o por el cliente. Durante una solicitud de sincronización, cualquier cambio en el buzón que ocurrió desde la última solicitud se sincroniza con el dispositivo y viceversa.

Se emite una solicitud de sincronización cuando:

El SyncState cambia.
Una sincronización es forzada en el lado del cliente.
El ‘ping’ actual caduca y el dispositivo envía uno nuevo (el La duración de la actividad está definida por el cliente).
7.2.- Administrar los estados de sincronización
7.2.1.- A través de la Administración Zimlet
Mobile NG ofrece dos opciones en el Zimlet de Administración para administrar Los estados de sincronización de los dispositivos móviles sincronizados:

Restablecer dispositivo: restablece el SyncState del dispositivo para una sola cuenta, forzando una resincronización completa la próxima vez que el dispositivo se conecte al servidor
Limpiar dispositivo: elimina todo el SyncState y el historial del dispositivo del servidor. Útil cuando un dispositivo móvil ya no se usa o es asignado a un empleado diferente en la misma empresa.
7.2.2.- A través de la CLI
Para administrar los SyncStates de dispositivos móviles sincronizados a través de la CLI, use uno de los siguientes comandos:

7.2.2.1.- El comando doRemoveDevice
    Sintaxis:
       zxsuite mobile doRemoveDevice {cuenta} {device_id}

    LISTA DE PARÁMETROS

    NOMBRE TIPO
    cuenta (M) Nombre de cuenta
    device_id (M) Cadena

    (M) == parámetro obligatorio, (O) == parámetro opcional
Ejemplo de uso:
    zxsuite mobile doRemoveDevice john@example.com Appl79032X2WA4S
    Elimina el SyncState del dispositivo Appl79032X2WA4S de John
7.2.2.2.- El comando doResetAccount
    Sintaxis:
       zxsuite mobile doResetAccount {cuenta}

    LISTA DE PARÁMETROS

    NOMBRE TIPO
    cuenta (M) Nombre de cuenta

    (M) == parámetro obligatorio, (O) == parámetro opcional
Ejemplo de uso:
    zxsuite mobile doResetAccount john@example.com
Restablece todos los estados del dispositivo para la cuenta de John

7.2.2.3.- El comando doResetDevice
    Sintaxis:
       zxsuite mobile doResetDevice {cuenta} [attr1 value1 [attr2 value2 ...

    LISTA DE PARÁMETROS

    NOMBRE TIPO PREDETERMINADO
    cuenta (M) Nombre de cuenta
    device_id (O) Cadena todo

    (M) == parámetro obligatorio, (O) == parámetro opcional
Ejemplo de uso:
    zxsuite mobile doResetDevice john@example.com Appl79032X2WA4S
Restablece el SyncState del dispositivo Appl79032X2WA4S de John

8.- Ajustes avanzados
8.1.- Ajuste de rendimiento de NG móvil
Mobile NG proporciona tres opciones útiles para ajustar Mobile NG según al rendimiento del sistema.

8.1.1.- Configuraciones disponibles
Latencia de notificaciones (ZxMobile \ _NotificationsLatency): los segundos de retraso entre un evento en el servidor y su notificación al dispositivo móvil.
Use notificaciones instantáneas (ZxMobile \ _UseInstantNotficiations): Habilitar / deshabilitar notificaciones instantáneas. Anula las notificaciones Latencia si es cierto.
Max Ping Heartbeat (ZxMobile \ _MaxPingHeartbeat): intervalo máximo entre ping comandos.
Todas las configuraciones se pueden editar en el Zimlet de Administración o mediante CLI usando El comando setProperty.

8.1.2.- Cuándo editar la configuración de ajuste de rendimiento
La configuración predeterminada debe ser óptima para la mayoría de las situaciones. Si tu experimenta uno o más de los problemas a continuación, aplique el correcto solución.

Problema	Solución
Alta carga del sistema	Deshabilitar notificaciones instantáneas
Alta carga del sistema después de deshabilitar las notificaciones instantáneas	Elevar la latencia de notificación
Los usuarios móviles experimentan un alto uso de la red	Deshabilitar notificaciones instantáneas y ajustar latencia de notificaciones
Los dispositivos pueden conectarse, pero las sesiones se interrumpen con frecuencia	Ajuste Max Ping Heartbeat de acuerdo con su configuración de red
Los elementos se sincronizan de servidor a dispositivo con un retraso excesivo	Reduce la latencia de notificación o habilita las notificaciones instantáneas
9.- Carpetas compartidas
9.1.1- Carpetas compartidas y usted (y su dispositivo móvil)
Con Network NG, es posible sincronizar carpetas que no son propiedad por el propio usuario a dispositivos móviles. Esto se aplica a todos los tipos de artículos. disponible a través del protocolo Exchange ActiveSync, por lo que podrá sincronizar cualquier carpeta de correo electrónico compartida, libreta de direcciones, calendario o lista de tareas a dispositivos móviles.

Las funciones específicas disponibles en dispositivos móviles pueden diferir, según cliente en uso.

Advertencia

No todos los clientes admiten la sincronización de múltiples libretas de direcciones, calendarios o listas de tareas a través de Exchange ActiveSync.

9.2.- Cómo sincronizar una carpeta compartida con sus dispositivos móviles
Para permitir un mayor nivel de control sobre la sincronización, los usuarios son se le permite elegir qué carpetas compartidas se sincronizarán con sus dispositivos móviles.

Habilitar sincronización móvil para una carpeta
Para habilitar la sincronización móvil para una carpeta compartida:

Inicie sesión en el cliente web de Zimbra.
Haga clic derecho en la carpeta compartida que desea sincronizar.
Seleccione Configuración de sincronización de carpetas en el menú desplegable.
Marque la casilla de verificación Habilitar sincronización para esta carpeta.
Presiona OK.
La nueva carpeta se sincronizará con cualquier dispositivo móvil conectado a la cuenta.

9.3.- Restricciones
Las siguientes restricciones se aplican a la sincronización de carpetas compartidas:

No es posible sincronizar un punto de montaje haciendo referencia a una cuenta completa compartir.
No es posible sincronizar una subcarpeta de una carpeta compartida, como lo hace entonces devolvería un árbol de carpetas incompleto.
No es posible sincronizar un recurso compartido de solo lectura, ya que Exchange El protocolo ActiveSync no visualiza el concepto de solo lectura recurso. Sincronizar una carpeta de solo lectura causará graves inconsistencias entre el cliente y el servidor, junto con muchas errores
10.- Filtros EAS
En el protocolo EAS, la versión del protocolo utilizada para la sincronización se define durante el apretón de manos inicial y nunca cambia. El servidor presenta una lista de todas las versiones de protocolo disponibles y el cliente elige uno entre esa lista.

Los filtros EAS son una forma de limitar la versión EAS disponible para un subconjunto de usuarios o clientes para asegurarse de que se utiliza la versión adecuada.

Se pueden configurar múltiples filtros EAS y se evaluarán en secuencia orden (consulte los comandos getAllEASFilters ydoMoveEASFilter a continuación).

10.1.- Anatomía de un filtro EAS
Un filtro EAS se compone de 5 partes:

Tipo: define el tipo de regla de filtro.
Parámetro: el identificador de filtrado (por ejemplo, marca del dispositivo o correo electrónico habla a).
Modo: define si el software limitará la disponibilidad versiones o proporcionar una lista fija.
campo easversions: contiene las versiones de protocolo aplicadas por el filtrar.
Valor booleano Blocking: define si se ejecutan otros filtros una vez que el actual coincida con éxito.
10.2.- Administrar filtros EAS
Los filtros EAS se administran a través de la CLI utilizando los siguientes cuatro comandos dedicados

### zxsuite mobile getAllEASFilters

Este comando enumera todos los filtros existentes.

Salida de muestra:

            filtros

                    ID 0
                    modo fijo
                    regla [tipo = o; reglas = [[tipo = contiene; regla = perspectiva /] O [tipo = contiene; regla = microsoft.outlook]]
                    easversions 14.0
                    bloqueo verdadero

                    ID 1
                    límite de modo
                    regla [tipo = contiene; regla = samsung]
                    easversions 2.5
                    bloqueo falso

                    ID 2
                    límite de modo
                    regla [tipo = siempre]
                    easversions 14.1
                    bloqueo falso

### zxsuite mobile doAddEASFilter

Este comando agrega un nuevo filtro EAS.

    zxsuite mobile doAddEASFilter

    Sintaxis:
       zxsuite mobile doAddEASFilter {y | o | regex | contiene | cuenta} {text | people@example.com | cuenta = ejemplo @ ff.com, contiene = android} {agregar | restar | fijo | límite} {easversions} [attr1 value1 [valor de attr22 ...]]

    LISTA DE PARÁMETROS

    NOMBRE TIPO VALORES ESPERADOS
    tipo (M) Opción múltiple y | o | regex | contiene | cuenta
    parámetro (M) Texto de cadena | people@example.com | cuenta = ejemplo @ ff.com, contiene = android
    modo (M) Opción múltiple sumar | restar | fijo | límite
    easversions (M) Cadena [, ..]
    bloqueo (O) booleano verdadero | falso

    (M) == parámetro obligatorio, (O) == parámetro opcional

    Ejemplo de uso:

    zxsuite mobile doAddEASFilter contiene Android fijo 2.5,12.0,14.1
    Agrega un filtro de protocolo que restringirá el conjunto de versiones EAS disponibles a 2.5, 12.0 y 14.1 si el nombre del agente de usuario
    contiene la cadena 'android'.

    zxsuite mobile doAddEASFilter y account = user @ example.com, contiene = android fijo 14.1 bloqueo verdadero
    Agrega un filtro de protocolo que restringirá el conjunto de versiones EAS disponibles a 14.1 si el nombre del agente de usuario
    contiene la cadena 'android' solo para usuario@ejemplo.com. No se evaluarán más filtros EAS después de este debido a la directiva de 'bloqueo'.

### zxsuite mobile doDeleteEASFilter

Este comando elimina un filtro EAS existente.

    zxsuite mobile doDeleteEASFilter
    El comando doDeleteEASFilter requiere más parámetros

    Sintaxis:
       zxsuite mobile doDeleteEASFilter {id}

    LISTA DE PARÁMETROS

    NOMBRE TIPO
    id (M) Entero

    (M) == parámetro obligatorio, (O) == parámetro opcional

    Ejemplo de uso:

    zxsuite mobile doDeleteEASFilter 2
    Elimina el filtro con id = 2.
    Para mostrar una lista de los filtros, use el
            zxsuite mobile getAllEASFilters
    mando.

### zxsuite mobile doMoveEASFilter

Este comando se utiliza para mover los filtros EAS a una posición diferente en el
cola de filtro

    zxsuite mobile doMoveEASFilter
    El comando doMoveEASFilter requiere más parámetros

    Sintaxis:
       zxsuite mobile doMoveEASFilter {desde} {a}

    LISTA DE PARÁMETROS

    NOMBRE TIPO
    de (M) Entero
    a (M) Entero

    (M) == parámetro obligatorio, (O) == parámetro opcional

    Ejemplo de uso:

    zxsuite mobile doMoveEASFilter 0 5
    Mueve el filtro con id = 0 a la posición 5.
    Para mostrar una lista de los filtros, use el
            zxsuite mobile getAllEASFilters
    mando.
11.- Registradores de cuentas móviles
Los registradores de cuentas móviles son registradores dedicados que pueden generar la totalidad del EAS de un usuario se registra en un archivo de registro dedicado, con un archivo diferente verbosidad que la del sync.log. Esto permite más rápido solución de problemas.

Al crear un registrador de cuenta, los siguientes parámetros deben ser especificado:

El objetivo cuenta.
El log_level (verbosidad) del registro.
El log_file dedicado.
El window_size para aplicar en todos los dispositivos que se sincronizan con el cuenta mientras se ejecuta el registrador.
Advertencia

Los registradores de cuentas se eliminan automáticamente cuando se detiene el buzón o reiniciado y no suele sobrevivir a un bloqueo del buzón. Archivos de registro no se verá afectado.

Gestión de registrador de cuenta
Los registradores de cuentas solo se pueden administrar a través de la CLI a través de lo siguiente comandos:

zxsuite mobile doAddAccountLogger
zxsuite mobile doAddAccountLogger
El comando doAddAccountLogger requiere más parámetros

Sintaxis:
   zxsuite mobile doAddAccountLogger {cuenta} {debug | info | warn | err | crit} {log_file} [attr1 value1 [attr2 value2 ...]]

LISTA DE PARÁMETROS

NOMBRE TIPO VALORES ESPERADOS
cuenta (M) Nombre de cuenta
log_level (M) Opción múltiple depuración | información | advertir | err | crit
ruta_archivo_log (M)
window_size (O) Entero un valor> 0

(M) == parámetro obligatorio, (O) == parámetro opcional

Ejemplo de uso:

zxsuite mobile doaddaccountlogger john@example.com info / tmp / john_logger
Crea un registrador de cuenta de información para la cuenta de john en el archivo / tmp / john_logger

zxsuite mobile doaddaccountlogger john@example.com info / tmp / john_logger window_size 1
Crea un registrador de cuenta de información para la cuenta de john en file / tmp / john_logger con el tamaño de ventana establecido en 1.
zxsuite mobile doRemoveLogger
zxsuite mobile doRemoveLogger
El comando doRemoveLogger requiere más parámetros

Sintaxis:
   zxsuite mobile doRemoveLogger {logger_id | "all_loggers"}

LISTA DE PARÁMETROS

NOMBRE TIPO VALORES ESPERADOS
logger_id (M) Opción múltiple logger_id | "all_loggers"

(M) == parámetro obligatorio, (O) == parámetro opcional

Ejemplo de uso:

zxsuite mobile doremovelogger 5
Elimina el registrador de cuenta con ID = 5
zxsuite mobile getAccountLoggers
Salida de muestra:

zxsuite mobile getAccountLoggers

        madereros

                id 7
                nivel de depuración
                nombre AccountLogger
                Descripción Cuenta de registro usuario@dominio.com usando depuración de nivel, archivo de registro /tmp/user.log
                eliminar el comando zxsuite mobile doRemoveLogger 7
Servicio ABQ
La función «Permitir / Bloquear / Cuarentena» permite el control de acceso granular de dispositivos móviles que se conectan al servidor. Es un tipo «preventivo» de característica de seguridad, lo que significa que actúa sobre la primera conexión a la servidor y está hecho para garantizar que solo los dispositivos autorizados puedan finalizar sincronización con el servidor. Esto permite que un administrador completo mantenga seguimiento de todos los dispositivos móviles utilizados en su red. Actualmente solo CLI se proporcionan herramientas; se lanzará una GUI web en el futuro.

Componentes
La función ABQ se compone de tres componentes lógicos principales:

una lista de control de dispositivos
un motor de autorización
un conjunto de herramientas CLI
Lista de control de dispositivos
La Lista de control de dispositivos, también conocida como «Lista ABQ», contiene el información sobre dispositivos permitidos dentro del motor de configuración NG. Dispositivos se puede agregar a la Lista de control de dispositivos a través de CLI en función de su «Dispositivo IDâ € ?? que se puede obtener a través de CLI.

También es posible limitar aún más el acceso limitando las cuentas que puede sincronizarse con el servidor en un dispositivo específico.

Nota

Al iniciar el módulo, si la Lista de control de dispositivos está vacía, todos los dispositivos móviles los dispositivos previamente reconocidos por el servidor Zimbra se importarán como Permitido .

Motor de autorización
El motor de autorización se encarga de verificar los dispositivos contra Lista de control de dispositivos y configuración de su estado ABQ al apropiado valor.

Cada regla se aplica a todas las cuentas que se conectan usando un dispositivo, es un Identificación del dispositivo. Se aplica a una cuenta específica que se conecta usando ese dispositivo si tiene el formato dispositivo \ _id / account \ _id o device \ _id / accountName

Conjunto de herramientas CLI.
CLI Toolset permite a los administradores interactuar con el dispositivo lista de control y con el estado de sincronización de un dispositivo, específicamente a:

Mostrar la lista de control de dispositivos
Mostrar todos los dispositivos en cuarentena y bloqueados
Agregue uno o más dispositivos a la Lista de control de dispositivos
Mueva un dispositivo desde «Cuarentena» a «Permitido» o â € œBloqueadoâ € ??
Cambiar el estado de sincronización de un dispositivo
Cada vez que el administrador cambia el estado de un dispositivo en un ABQ habilitado entorno, dependiendo del estado emitido, el dispositivo se verá obligado a volver a sincronizar carpetas con el servidor dando como resultado una redirección inmediata a ya sea un [ buzón virtual ficticio ] (# datos ficticios) que explicará usuario lo que ha sucedido, o al buzón real para realizar la resincronización.

Modos ABQ
La función ABQ se activa para cada dispositivo móvil que intente sincronizar con el servidor, y se puede configurar en uno de los cuatro modos posibles: «Permisivo», «Interactivo», «Estricto» y «Deshabilitado». Este atributo es Global para todo el clúster.

Modo «permisivo»:
El motor de autorización no está activo, por lo que después de autenticar al usuario y verificando el estado de su cuenta por razones de seguridad, el La sincronización continuará. Todavía es posible bloquear específicos dispositivos pero dispositivos no bloqueados siempre se les permitirá sincronizar.

«Modo interactivo:
Después de autenticar al usuario y verificar el estado de su cuenta para razones de seguridad, el sistema de control de dispositivos verificará la «ID del dispositivo» enviado por el dispositivo contra la lista de dispositivos permitidos:

si la pareja dispositivo / usuario está en la lista «permitida» La sincronización continuará.
si la pareja dispositivo / usuario no está en la lista de dispositivos pero el dispositivo está en la lista «permitida» continuará la sincronización.
si el dispositivo no está en el «permitido» enumerar la sincronización será ser pausado, un correo electrónico ficticio que notifica al usuario de su «Cuarentena» se enviará el estado y la conexión se establecerá en «Cuarentena» estado.
Los administradores pueden ser notificados a intervalos regulares, y cada el correo electrónico de notificación solo incluirá nuevos dispositivos en cuarentena. Lo harán luego podrá permitir o denegar la sincronización para cada dispositivo usando Las herramientas de CLI apropiadas.

«Modo estricto:
Después de autenticar al usuario y verificar el estado de su cuenta para razones de seguridad, el sistema de control de dispositivos verificará la «ID del dispositivo» enviado por el dispositivo contra la lista de dispositivos permitidos:

si la pareja dispositivo / usuario o el dispositivo en sí mismo está en el lista «permitida» la sincronización continuará.
si el dispositivo no está en el «permitido» enumerar la sincronización será se pondrá en estado «Bloqueado», no se sincronizarán los datos y se creará un ficticio el correo electrónico que notifica al usuario sobre el estado «Bloqueado» del dispositivo será expedido.
Modo «deshabilitado»:
ABQ está deshabilitado, no se activan verificaciones y no se aplican políticas.

Control de modo ABQ
El modo actual se puede verificar ejecutando el siguiente comando:

zxsuite config global get atributo abqMode
El modo ABQ se puede cambiar ejecutando el siguiente comando:

zxsuite config conjunto global atributo abqMode valor [Permisivo | Interactivo | Estricto | Desactivado]
Datos ficticios
La función hace uso de «correos electrónicos falsos» y un «buzón falso» poner dispositivos en espera mientras esperan autorización (Modo interactivo) o notificar a sus «bloqueados» estado (Modo permisivo, Modo interactivo y Modo estricto).

El buzón ficticio es un buzón virtual que consta de solo una «Bandeja de entrada» carpeta que se sincronizará con el dispositivo mientras esté en Estado de cuarentena o bloque. Los correos electrónicos ficticios son mensajes de correo electrónico predefinidos que están sincronizados con un dispositivo en estado de Cuarentena o Bloqueo para alertar el usuario. Por ahora estos mensajes no son personalizables, y serán localizado en el futuro. Cada vez que se cambia el estado ABQ de un dispositivo, el estado de sincronización del dispositivo se restablecerá.

Esto fue diseñado para asegurarse de que el usuario sepa lo que está sucediendo, el alternativa que obliga a la sincronización a fallar sin respuesta descriptiva para el propio usuario, lo que probablemente causaría una gastos generales significativos en llamadas de soporte.

Notificaciones
Los administradores pueden ser notificados por correo electrónico de los dispositivos en cuarentena en un intervalo específico definido por el abqNotificationsInterval NG atributo de configuración, expresado en milisegundos:

El intervalo se puede verificar ejecutando el siguiente comando:

zxsuite config global get attribute abqNotificationsInterval
El intervalo se puede cambiar ejecutando el siguiente comando:

zxsuite config conjunto global atributo abqNotificationsInterval valor [retraso en milisegundos]
Por defecto, el abqNotificationsInterval está establecido en 0, lo que significa que no Las notificaciones serán entregadas.

Estado del servicio ABQ
El estado del servicio ABQ se puede verificar ejecutando el siguiente comando:

getServices móviles de zxsuite
El servicio se puede detener o iniciar utilizando el control de servicio predeterminado del módulo Mobile NG:

zxsuite mobile doStartService abq
zxsuite mobile doStopService abq
Cuando el modo está deshabilitado, el servicio ABQ no se iniciará automáticamente y los dispositivos siempre se les permite sincronizar.

ABQ CLI
Se puede mostrar una lista de todos los comandos ABQ CLI en ejecución:

$ zxsuite mobile abq

Permitir / bloquear / poner en cuarentena la gestión de dispositivos móviles

    list: enumera los dispositivos.
                              Lista ABQ de zxsuite mobile [attr1 value1 [attr2 value2 ...]]

    agregar - agregar / importar dispositivos
                              zxsuite mobile ABQ add [attr1 value1 [attr2 value2 ...]]

    allow - Permitir sincronización para un dispositivo en cuarentena
                              zxsuite mobile ABQ allow {device_id}

    bloque - Denegar sincronización para un dispositivo en cuarentena
                              bloque ABQ móvil de zxsuite {device_id}

    set: establece el estado de sincronización de un dispositivo
                              zxsuite mobile ABQ set {device_id} {Permitido | Bloqueado | En cuarentena}

    delete - Eliminar dispositivo de ABQ
                              zxsuite mobile ABQ delete {device_id}

    setNotificationInterval: establece el intervalo de notificación para los nuevos dispositivos en cuarentena
                              zxsuite mobile ABQ setNotificationInterval {45m | 6h | 1d | 0}
Comando «lista» ABQ
Listar todos los dispositivos estado ABQ. El argumento «estado» filtrará la lista para mostrar solo dispositivos en ese estado específico.

Lista de abq de $ zxsuite mobile
Lista de dispositivos.

Sintaxis:
   Lista ABQ de zxsuite mobile [attr1 value1 [attr2 value2 ...]]

LISTA DE PARÁMETROS

NOMBRE TIPO VALORES ESPERADOS
status (O) Cadena permitida | Bloqueada | En cuarentena

(M) == parámetro obligatorio, (O) == parámetro opcional
Ejemplo:

[zimbra @ mail ~] $ zxsuite mobile abq list

        dispositivos

                device_id androidc133785981
                estado en cuarentena

                device_id androidc1024711770
                estado bloqueado

                device_id SAMSUNG1239862958
                estado permitido
Comando «importar» ABQ
Este comando importa una lista de identificadores de dispositivo de un archivo, y siempre requiere dos parámetros: un archivo de entrada con una lista de ID de dispositivo separados por una nueva línea y el «estado» de los dispositivos importados serán ajustado a.

[zimbra @ mail ~] $ zxsuite mobile abq import
la importación de comandos requiere más parámetros

Sintaxis:
    zxsuite mobile ABQ import {Ruta del archivo} {Permitido | Bloqueado | En cuarentena}

LISTA DE PARÁMETROS

NOMBRE TIPO VALORES ESPERADOS
input_file (M) Cadena Ruta al archivo
status (M) Cadena permitida | Bloqueada | En cuarentena

(M) == parámetro obligatorio, (O) == parámetro opcional

Ejemplo de uso:

zxsuite mobile ABQ import / path / to / file Permitido
Ejemplo:

[zimbra @ mail ~] $ zxsuite mobile abq import / tmp / list Permitido
3 dispositivos agregados

[zimbra @ mail ~] $ cat / tmp / list
androidc133785981
androidc1024711770
SAMSUNG1239862958/user@domain.com
En el ejemplo anterior, los dispositivos androidc133785981 y androidc1024711770 puede sincronizarse por completo independientemente de cuenta, mientras que el dispositivo SAMSUNG1239862958 solo puede sincronizar cuenta usuario @ dominio.com

Comando «permitir» ABQ
Este es un comando específico para el dispositivo en cuarentena y establece el dispositivo estado a Permitido .

$ zxsuite mobile abq allow
Permitir sincronización para un dispositivo en cuarentena

Sintaxis:
   zxsuite mobile ABQ allow {device_id} [attr1 value1 [attr2 value2 ...]]

LISTA DE PARÁMETROS

NOMBRE TIPO VALORES ESPERADOS
device_id (M) Cadena
cuenta (O) Cadena 27ee8dd9-d813-4ca7-a988-580df0027a58|user1@example.com

(M) == parámetro obligatorio, (O) == parámetro opcional
Comando «bloque» ABQ
Este es un comando específico para el dispositivo en cuarentena y establece el dispositivo estado a Bloqueado .

$ zxsuite mobile abq block
Denegar la sincronización de un dispositivo en cuarentena

Sintaxis:
   bloque ABQ móvil de zxsuite {device_id} [attr1 value1 [attr2 value2 ...]]

LISTA DE PARÁMETROS

NOMBRE TIPO VALORES ESPERADOS
device_id (M) Cadena
cuenta (O) Cadena 27ee8dd9-d813-4ca7-a988-580df0027a58|user1@example.com

(M) == parámetro obligatorio, (O) == parámetro opcional
Comando «set» ABQ
Establezca cualquier estado para cualquier dispositivo individual (ya sea conocido o desconocido).

$ zxsuite set abq móvil
Establecer el estado de sincronización para un dispositivo

Sintaxis:
   zxsuite mobile ABQ set {device_id} {Permitido | Bloqueado | En cuarentena} [attr1 value1 [attr2 value2 ...]]

LISTA DE PARÁMETROS

NOMBRE TIPO VALORES ESPERADOS
device_id (M) Cadena
status (M) Cadena permitida | Bloqueada | En cuarentena
cuenta (O) Cadena 27ee8dd9-d813-4ca7-a988-580df0027a58|user1@example.com

(M) == parámetro obligatorio, (O) == parámetro opcional
Comando «eliminar» ABQ
Eliminar un dispositivo de todas las listas.

$ zxsuite help mobile abq eliminar
Eliminar dispositivo de ABQ

Sintaxis:
   zxsuite mobile ABQ delete {device_id} [attr1 value1 [attr2 value2 ...]]

LISTA DE PARÁMETROS

NOMBRE TIPO VALORES ESPERADOS
device_id (M) Cadena
cuenta (O) Cadena 27ee8dd9-d813-4ca7-a988-580df0027a58|user1@example.com

(M) == parámetro obligatorio, (O) == parámetro opcional
Comando ABQ «setNotificationInterval»
Establezca el intervalo de notificación para los nuevos dispositivos en cuarentena.

$ zxsuite mobile abq setNotificationInterval
El comando setNotificationInterval requiere más parámetros

Sintaxis:
    zxsuite mobile ABQ setNotificationInterval {45m | 6h | 1d}

LISTA DE PARÁMETROS

NOMBRE TIPO VALORES ESPERADOS
intervalo (M) Cadena 45m | 6h | 1d

(M) == parámetro obligatorio, (O) == parámetro opcional

Ejemplo de uso:

Establecer notificaciones de nuevos dispositivos en cuarentena cada 45 minutos
    zxsuite mobile abq setNotificationInterval 45m
Establecer notificaciones de nuevos dispositivos en cuarentena cada 6 horas
    zxsuite mobile abq setNotificationInterval 6h
Establecer notificaciones de nuevos dispositivos en cuarentena una vez al día
    zxsuite mobile abq setNotificationInterval 1d
Deshabilitar notificaciones de nuevos dispositivos en cuarentena
    zxsuite mobile abq setNotificationInterval 0
