Backup y restauración en Zimbra Network 9
En este capítulo pasamos a Configurar y Administrar Zimbra Network Edition a través del panel de administración. Para referencia completa del manual de administración, revisar la documentación oficial de Zimbra Network en} https://zimbra.github.io/adminguide/latest/

1.- Backup de zimbra network con ZxBackup
Acceder a https://mail.aulautil.tk:7071

user: admin
clave: Tuxito1.23
Nos vamos a la opción «Zimbra Network» –>Backup

Tenemos las siguientes opciones

"Ruta del Backup": /opr/zimbra/backup   EL directorio de backup debe estar enlazado a un storage local o en red (NFS, iscsi), 
                                        un cambio en el mismo requerirá reinicialización del backup
"Umbral de Espacio Mínimo": Umbral de espacio mínimo requerido para que opere ZxBackup. Cuando es alcanzado, ZxBackup ignorará todas 
                            las operaciones para evitar saturar el disco, dejar en 2048
"Política de Retención de datos": Establece por cuánto tiempo se mantiene la información después de eliminada (por defecto 30 días). 
                                  Cada operación de purga removerá los elementos respaldados que fueron eliminados antes del tiempo 
                                  aquí establecido.
"Real Time Scanner": Gestiona el Real Time Scanner; debe estar en modo Habilitado
"Administración de COS": Habilita o deshabilita la función de backup para una o más COS.
"Importar Backup": Inicia una operación de External Restore.
"Exportar Backup": Inicia una operación de hacer un backup Externo.
"Last Full Scan": Muestra información sobre el último Full Scan.
"Stop ALL Backup Operations": Detiene todas las operaciones y vacía las operaciones en cola. Utilicelo cuando se hace necesario apagar 
                              o reiniciar, o cuando la carga de la máquina es elevada.
"Restore": Inicia una operación de Recuperación en una Nueva cuenta .
"Purge": Inicia una operación de purga del backup.
"Enable Scan Operation Scheduling": Habilita/desabilita el Smartscan nocturno y la Purga semanal.
"Run Smartscan": Inicia una operación de Smartscan .
1.1.- Definiendo un Volumen para el backup
Agregamos un disco local al servidor zimbra network de 100GB y luego lo definimos como volumen LVM asociado al punto de montaje /opt/zimbra/backup

Instalar LVM (si no estuviese habilitado en el CentoS)

yum -y install lvm2
Listar los discos; y ahí podemos ver el nuevo disco

parted -l
Particionar el nuevo disco

parted /dev/sdc
print
mklabel gpt
mkpart
   nombre: data1
   tipo: ext2
   inicio: 1
   fin:    107GB
quit
Crear volumen físico

pvcreate /dev/sdc1
Crear Grupo de Volumen

vgcreate backup /dev/sdc1
Crear Volumen lógico para backup

lvcreate backup -n data1 -l+100%FREE
Formatear volumen lógico

mkfs.xfs /dev/mapper/backup-data1 
Agregar como punto de montaje permanente

echo "/dev/mapper/backup-data1 /opt/zimbra/backup xfs defaults 0 0" >> /etc/fstab
Montar almacenamiento de backup

mount /opt/zimbra/backup
Cambiar dueño del punto de montaje

chown zimbra.zimbra /opt/zimbra/backup
Verificar el montaje del nuevo store

df -h
1.2.- Inicializar la ruta del backup
Configurar la ruta del backup e inicializar

Ir a Panel de Administración --> Zimbra Network --> Backup 
    Ruta del backup: /opt/zimbra/backup
    Click en botón "Inicializar Ahora"
2.- Escaneo en tiempo real
¿Qué es el escáner en tiempo real?
El Real Time Scanner es la gran innovación en Backup NG. Cada evento en el sistema se graba en vivo en el RedoLog de Zimbra y se guarda con Backup NG, lo que significa que siempre es posible revertir una cuenta a un estado previo. Gracias al Real Time Scanner, todos los modos de restauración trabajar con precisión de fracción de segundo.

¿Como funciona el escaneo en tiempo real?
El escáner en tiempo real lee casi todos los eventos del servidor de correo en tiempo real siguiendo el flujo de información proporcionado por RedoLog. Luego replica las mismas operaciones en su propia estructura de datos, creando elementos o actualizando sus metadatos. No hay información nunca sobrescrito en la copia de seguridad, por lo que cada elemento tiene su propio historial completo.

2.1.- Administrar el escáner en tiempo real
2.1.1.- Habilitación del escáner en tiempo real
A través de la Administración Zimlet
Seleccione la pestaña Copia de seguridad NG.
En Escáner en tiempo real, presione el botón Activar.
Nota

Cuando el escáner en tiempo real se habilita por primera vez o se vuelve a habilitar después de una parada, se requiere un análisis completo en vivo. Una advertencia será aparece después de habilitar el Escáner en tiempo real, y usted será se le solicita que inicie el Análisis completo.

A través de la CLI
Para habilitar el escáner en tiempo real a través de la CLI, el Se debe establecer la propiedad ZxBackup_RealTimeScanner del módulo Backup NG a TRUE:

zxsuite backup setProperty ZxBackup_RealTimeScanner TRUE
2.1.2.- Deshabilitar el escáner en tiempo real
A través de la Administración Zimlet
- Seleccione la pestaña Copia de seguridad NG.

- En el Escáner en tiempo real, presione el botón `Desactivar`.
A través de la CLI
Para deshabilitar el escáner en tiempo real a través de la CLI, el Se debe establecer la propiedad ZxBackup_RealTimeScanner del módulo Backup NG a FALSE:

zxsuite backup setProperty ZxBackup_RealTimeScanner FALSE
¿Por qué debería desactivar el escáner en tiempo real?
El único momento en que debe deshabilitar el Escáner en tiempo real es mientras realizar una restauración externa de múltiples dominios. Esto es una seguridad medida para evitar una gran carga en su servidor. Después de la importación, vuelva a habilitar Real Time Scanner y realice un SmartScan cuando se le solicite.

2.2.- Limitaciones y escaneo de seguridad
La principal limitación al restaurar datos adquiridos a través del escaneo en tiempo real es:

Carpeta vacía – cuando un usuario usa el botón Vaciar carpeta en el menú contextual del botón derecho
En este caso, y en cualquier momento Backup NG no puede determinar el estado de un leyendo los metadatos guardados por el Escaneo en tiempo real, una cuenta La exploración en la cuenta dada se activa ANTES de la restauración.

Esto corrige los datos desalineados y desinfecta los metadatos respaldados para el buzon.

3.- SmartScan
¿Qué es el SmartScan?
SmartScan es el principal control de coherencia para el estado de su copia de seguridad sistema. Es ‘Inteligente’ porque funciona solo en cuentas modificadas desde el último SmartScan, mejorando así el rendimiento del sistema y disminuyendo escanear el tiempo exponencialmente.

Por defecto, un SmartScan está programado para ejecutarse cada noche (si Escanear Operation Scheduling está habilitado en la sección Backup NG del Administración Zimlet). Una vez a la semana, en un día establecido por el usuario, una Purga se ejecuta junto con SmartScan para borrar el almacén de datos de Backup NG de cualquier elemento eliminado que haya excedido el período de retención.

¿Como funciona?
El motor Backup NG escanea todos los elementos en el almacén de datos de Zimbra, buscando elementos modificados después del último SmartScan. Actualiza cualquier entrada obsoleta y crea cualquier elemento que aún no esté presente en la copia de seguridad mientras marcando como eliminado cualquier elemento encontrado en la copia de seguridad y no en Zimbra Almacén de datos.

Finalmente, actualiza todos los metadatos de configuración en la copia de seguridad, de modo que dominios, cuentas, COS y configuraciones de servidor se almacenan junto con un volcado de todos los datos LDAP y config.

¿Cuándo se ejecuta un SmartScan?
- Cuando se inicia el módulo Backup NG.

- Diariamente, si la programación de la operación de escaneo está habilitada en
    Administración Zimlet.

- Cuando el escáner en tiempo real se vuelve a habilitar a través de la Administración
    Zimlet después de haber sido deshabilitado previamente.
3.1.- Ejecutando un SmartScan
3.1.- Inicio del escaneo a través del Zimlet de administración
Para iniciar un SmartScan a través del Zimlet de Administración,

- Abra el Zimlet de Administración.

- Haga clic en la pestaña Backup NG (asegúrese de tener una licencia válida).

- Haga clic en `Ejecutar Smartscan`.
3.2.- Inicio del escaneo a través de la CLI
Para iniciar un FullScan a través de la CLI, use el comando doSmartScan:

    Sintaxis:
       zxsuite backup doSmartScan [attr1 value1 [attr2 value2 ...

    LISTA DE PARÁMETROS

    NOMBRE TIPO
    notificaciones (O) Dirección de correo electrónico [, ..]

    (M) == parámetro obligatorio, (O) == parámetro opcional
Ejemplo de uso:

zxsuite backup dosmartscan notifications user1@example.com,user2@example.com
Realiza un escaneo inteligente y envía notificaciones a user1@example.com y user2@example.com

3.3.- Comprobación del estado de una exploración en ejecución
Para verificar el estado de un escaneo en ejecución a través de la CLI, use el comando monitor:

    Sintaxis:
       zxsuite backup monitor {operation_uuid} [attr1 value1 [attr2 value2 ...

    LISTA DE PARÁMETROS

    NOMBRE TIPO
    operation_uuid (M) Uiid
    operación_host (O) Cadena

    (M) == parámetro obligatorio, (O) == parámetro opcional
4.- Purga
¿Qué es la purga de respaldo?
La purga de respaldo es una operación de limpieza que elimina del respaldo Ruta de acceso a cualquier elemento eliminado que exceda el tiempo de retención definido por la «Política de retención de datos».

¿Como funciona?
El motor de purga escanea los metadatos de todos los elementos eliminados y elimina cualquier elemento cuya última marca de tiempo de actualización (eliminación) sea superior a la tiempo de retención.

Si un elemento BLOB todavía está referenciado por uno o más archivos de metadatos válidos, debido a la deduplicación incorporada de Backup NG, el BLOB en sí no será eliminado

Las personalizaciones de SPostfix respaldadas por Backup NG también siguen la copia de seguridad políticas de purga del camino. Esto se puede cambiar en «Backup NG» sección de Administración Zimlet desmarcando Casilla de verificación Purgar viejas personalizaciones.

¿Cuándo se ejecuta una purga de respaldo?
Semanalmente, si la programación de la operación de escaneo está habilitada en Administración Zimlet.

Cuando se inicia manualmente a través de la Consola de administración o la CLI

Retención infinita
En caso de que la Política de retención de datos se establezca en0, lo que significa infinito retención, la purga de copia de seguridad se cerrará inmediatamente ya que no se eliminó ningún elemento alguna vez excederá el tiempo de retención.

4.1.- Ejecutar una purga de respaldo
4.1.1.- Inicio de la purga de respaldo a través del Zimlet de administración
Para iniciar un BackupPurge a través del Zimlet de administración:

- Haga clic en la pestaña Backup NG (asegúrese de tener una licencia válida).

- Haga clic en el botón `Ejecutar purga` en la parte superior derecha de la IU.
4.1.2.- Inicio de la purga de respaldo a través de la CLI
Para iniciar un BackupPurge a través de la CLI, use el comando doPurge:

    Sintaxis:
       zxsuite backup doPurge [attr1 value1 [attr2 value2 ...

    LISTA DE PARÁMETROS

    NOMBRE TIPO
    Cadena purgeDays (O)
    backup_path (O) Ruta

    (M) == parámetro obligatorio, (O) == parámetro opcional
Ejemplo de uso:

    zxsuite backup dopurge purgeDays 30 backup_path /opt/zimbra/backup/backup_name
4.1.3.- Comprobación del estado de una purga de copia de seguridad en ejecución
Para verificar el estado de una Purga en ejecución a través de la CLI, use el monitor mando:

    Sintaxis:
       zxsuite backup monitor {operation_uuid} [attr1 value1 [attr2 value2 ...

    LISTA DE PARÁMETROS

    NOMBRE TIPO
    operation_uuid (M) Uiid
    operación_host (O) Cadena

    (M) == parámetro obligatorio, (O) == parámetro opcional
5.- Copia de seguridad externa
¿Qué es la copia de seguridad externa?
El respaldo externo es uno de los métodos de respaldo de Backup NG. Eso crea una instantánea del sistema de correo, que está listo para ser utilizado migración o para la recuperación de desastres. Los datos exportados se deduplican y comprimido para optimizar la utilización del disco, los tiempos de transferencia y las velocidades de E / S.

¿Como funciona?
El motor Backup NG escanea todos los datos en el almacén de datos Zimbra, guardando todos los elementos (deduplicados y comprimidos) en una carpeta de su elección.

5.1.- Permisos de carpeta para respaldo externo
Crear ruta del backup (preferente una carpeta local enlazado a un disco NFS o iscsi)

mkdir /opt/zimbra/backup/export
Dar permisos al usuario zimbra

chown zimbra.zimbra  /opt/zimbra/backup/export
5.2.- Preparando la migración de copia externa
Para minimizar el riesgo de errores, realice el siguiente mantenimiento procedimientos antes de migrar:

Verifique los permisos de Zimbra con el siguiente comando (debe ser ejecutado como root):
/opt/zimbra/libexec/zmfixperms --verbose --extended
Reindexar todos los buzones.
Verifique la consistencia BLOB con el comando
zxsuite hsm doCheckBlobs start
5.3.- Ejecutar una copia de seguridad externa
A través de la Administración Zimlet
Para iniciar una copia de seguridad externa a través del Zimlet de administración:

- Haga clic en la pestaña Copia de seguridad NG.

- Haga clic en el botón `Exportar copia de seguridad` debajo de` Importar/Exportar` para abrir el
    Asistente de exportación de copia de seguridad.

- Ingrese la Ruta de destino en el cuadro de texto y presione Siguiente. los
    el software verificará si la carpeta de destino está vacía y si
    el usuario *zimbra* tiene permisos R/W.

- Seleccione los dominios que desea exportar y presione Siguiente.

- Verifique todas sus opciones en la ventana Resumen de operaciones. Usted puede
    también agregue direcciones de correo electrónico adicionales para ser notificadas cuando la restauración
    La operación ha finalizado. Tenga en cuenta que la cuenta de administrador y el
    el usuario que inició el procedimiento de restauración recibe una notificación de forma predeterminada.
A través de la CLI
Para iniciar una copia de seguridad externa a través de la CLI, use el comando doExport:

    Sintaxis:
       zxsuite backup doExport {destination_path} [attr1 value1 [attr2 value2 ...

    LISTA DE PARÁMETROS

    NOMBRE TIPO PREDETERMINADO
    ruta_destino (M) Ruta
    dominios (O) Nombre de dominio [, ..] todos
    notificaciones (O) Dirección de correo electrónico [, ..]

    (M) == parámetro obligatorio, (O) == parámetro opcional
Ejemplo de uso:

zxsuite backup doexport /opt/zimbra/backup/ domains example.com notifications admin@aulautil.tk
6.- Restaurar respaldo en nueva cuenta
¿Qué es la restauración en una cuenta nueva?
El procedimiento Restaurar en nueva cuenta le permite restaurar el contenido y preferencias de un buzón como lo fue en un momento en el tiempo, en un Cuenta completamente nueva. La cuenta de origen no se cambia de ninguna manera, así que Es posible recuperar uno o más elementos eliminados en la cuenta de un usuario sin realmente deshacer todo el buzón. Cuando corres este tipo de restauración, puede optar por ocultar la cuenta recién creada del GAL como medida de seguridad.

¿Como funciona?
Cuando se inicia una Restauración en una cuenta nueva, se crea una cuenta nueva (el cuenta de destino). Todos los elementos existentes en la cuenta de origen en los momentos seleccionados se recrean en la cuenta de destino, incluidos la estructura de carpetas y todos los datos del usuario. Todos los mensajes restaurados serán creado en la tienda principal actual a menos que el cuadro Obedecer Política HSM sea comprobado.

Advertencia

Al restaurar datos en una nueva cuenta, la coherencia de los elementos compartidos no es conservado. Esto se debe a que las reglas de uso compartido originales se refieren a ID de la cuenta original, no a la cuenta restaurada.

6.1.- Ejecutar una restauración en una nueva cuenta a través del Zimlet de administración
Una restauración en una cuenta nueva se puede ejecutar de dos maneras.

De la lista de cuentas
Ejecutar Restaurar desde la pestaña Cuentas en la Administración de Zimbra La consola le permite operar en usuarios actualmente existentes en el servidor.
Si necesita restaurar un usuario eliminado, proceda a Restaurar a través de Administración Zimlet.

- Seleccione `Cuentas` en el panel izquierdo de la Consola de administración para
    muestra la lista de cuentas.

- Examine la lista y haga clic en la cuenta que se va a restaurar (Fuente).

- En la barra superior, presione la rueda y luego el botón \ `Restaurar \`.

- Seleccione `Restaurar en nueva cuenta` como Modo de restauración e ingrese el
    nombre de la nueva cuenta (Destino) en el cuadro de texto. Usted puede
    luego elija si Ocultar en GAL la nueva cuenta o no. Cuando
    Cuando haya terminado de elegir, presione `Siguiente`.

- Elija la fecha de restauración. El día/mes/año se puede seleccionar a través de un
    minical, la hora a través de un menú desplegable y minuto y segundo a través de dos
    cajas de texto. Haga clic en "Siguiente".

- Verifique todas sus elecciones en la ventana Resumen de operaciones. Tú también puedes
    agregue direcciones de correo electrónico adicionales para ser notificado cuando la restauración
    La operación ha finalizado. Tenga en cuenta que la cuenta de administrador y el
    el usuario que inició el procedimiento de restauración recibe una notificación de forma predeterminada.
Haga clic en «Finalizar» para iniciar la restauración.

6.2.- Ejecutar una restauración en una nueva cuenta a través de la CLI
Para iniciar una restauración en una cuenta nueva a través de la CLI, use el Comando doRestoreOnNewAccount:

    Sintaxis:
       zxsuite backup doRestoreOnNewAccount {source_account} {destination_account} {"dd / MM / aaaa HH: mm: ss" | last} [attr1 value1 [attr2 value2 ...

    LISTA DE PARÁMETROS

    NOMBRE TIPO VALORES ESPERADOS
    source_account (M) Nombre de cuenta
    destination_account (M) Nombre / ID de cuenta
    fecha (M) Fecha `dd / MM / aaaa HH: mm: ss` | último
    restore_chat_buddies (O) Booleano verdadero | falso
    notificaciones (O) Dirección de correo electrónico [, ..]

    (M) == parámetro obligatorio, (O) == parámetro opcional
Ejemplo de uso:

zxsuite backup dorestoreonnewaccount John NewJohn `28/09/2012 10: 15: 10`
Restaura la cuenta de John en una nueva cuenta llamada NewJohn

7.- Recuperar Restauración
¿Qué es recuperar restauración?
Recuperar restauración es uno de los modos de restauración disponibles en Backup NG. Eso permite a un administrador restaurar todos los elementos eliminados de un buzón en un período de tiempo y ponerlos en una carpeta dedicada de Zimbra dentro de la buzón en sí.

¿Como funciona?
Durante una restauración de recuperación, el motor Backup NG busca la copia de seguridad almacén de datos para elementos marcados como DELETED y los restaura en un carpeta dedicada en el buzón seleccionado.

Advertencia

Para tratar los correos electrónicos eliminados de IMAP de una manera más cómoda para el usuario, el indicador IMAP eliminado ahora se eliminará de cualquier restauración elemento para que el elemento en sí sea visible en Zimbra Web Client.

7.1.- Ejecutar una restauración de recuperación
7.2.- A través de la consola de administración
Seleccione \ Cuentas \ en el panel izquierdo de la Consola de administración para muestra la lista de cuentas.
Examine la lista y haga clic en la cuenta que se va a restaurar (Fuente).
En la barra superior, presione la rueda y luego el botón \ Restaurar \ «.
Seleccione «Recuperar» como modo de restauración y presione «Siguiente».
Elija la ranura de restauración de fecha y hora. Se puede seleccionar día / mes / año a través de un minical, la hora a través de un menú desplegable y el minuto y segundo a través de dos cuadros de texto. Haga clic en «Siguiente».
Verifique sus opciones en la ventana Resumen de operaciones. Tú también puedes agregue direcciones de correo electrónico adicionales para ser notificado cuando la restauración La operación ha finalizado. Tenga en cuenta que la cuenta de administrador y el el usuario que inició el procedimiento de restauración recibe una notificación de forma predeterminada.
Haga clic en «Finalizar» para iniciar la restauración.
7.3.- A través de la CLI
Para iniciar una operación de restauración de restauración, use el comando doUndelete:

Sintaxis:
   zxsuite backup doUndelete {cuenta} {"dd / MM / aaaa HH: mm: ss" | primero} {"dd / MM / aaaa HH: mm: ss" | último} [attr1 value1 [attr2 value2 ...

LISTA DE PARÁMETROS

NOMBRE TIPO VALORES ESPERADOS
cuenta (M) Nombre de cuenta
fecha_inicio (M) Fecha `dd / MM / aaaa HH: mm: ss` | primero
end_date (M) Fecha `dd / MM / aaaa HH: mm: ss` | last
notificaciones (O) Dirección de correo electrónico [, ..]

(M) == parámetro obligatorio, (O) == parámetro opcional

Ejemplo de uso:

zxsuite backup doundelete John `10/08/2012 10: 15: 00` last
Realiza una recuperación en la cuenta de John de todos los elementos creados entre el 10/08/2012 10:15:00 y los últimos datos disponibles
8.- Restauración Externa
¿Qué es la restauración externa?
La restauración externa es uno de los modos de restauración de Backup NG.

¿Como funciona?
La restauración externa agrega al servidor Zimbra actual todos los datos, metadatos y datos de configuración almacenados en una copia de seguridad externa.

El flujo de trabajo del procedimiento de importación es el siguiente:

FASE 1

- * '* Operación iniciada *' notificación *

- Leer datos de copia de seguridad del servidor

- Crear dominios vacíos

- Cree el COS necesario (solo aquellos efectivamente utilizados por los importados
    cuentas)

- Crear DL vacías

- Crear cuentas vacías

- Restaurar todos los atributos de las cuentas

- Restaurar todos los atributos de los dominios

- Restaura todos los atributos de los DL y comparte información

- * '* Comentarios de FASE1 *' Notificación *
FASE 2

Restaurar todos los elementos
FASE 3

- Restaurar todos los puntos de montaje y fuentes de datos

- Notificación * '* Operation Ended *' con comentarios completos *
Antes de que empieces
Si Backup NG ya está inicializado en el servidor de destino, deshabilite RealTime Scanner para mejorar el uso de memoria y el rendimiento de E/S.

Para reducir la sobrecarga de E/S y la cantidad de espacio en disco utilizado para migración, los usuarios avanzados pueden modificar o deshabilitar RedoLog de Zimbra para duración de la importación.

Para reducir aún más la cantidad de espacio en disco utilizado, es posible habilitar compresión en su volumen primario actual antes de comenzar la importación. Si no desea utilizar un volumen primario comprimido después de la migración, es posible crear un volumen primario nuevo y sin comprimir, configúrelo en Current y cambie el anterior aSecondary. Todo esto se puede hacer utilizando el módulo HSM NG.

8.1- Ejecutar una restauración externa
A través de la Administración Zimlet
- Haga clic en la pestaña Copia de seguridad NG.

- Haga clic en el botón `Importar copia de seguridad` debajo de` Importar / Exportar` para abrir el
    Asistente de importación de copia de seguridad.

- Ingrese la Ruta de destino en el cuadro de texto y presione Adelante. los
    el software verificará si la carpeta de destino contiene un archivo válido
    copia de seguridad y si el usuario * zimbra * tiene permisos de lectura.

- Seleccione los dominios que desea importar y presione Reenviar.

- Seleccione las cuentas que desea importar y presione Reenviar.

- Verifique todas sus opciones en la ventana Resumen de operaciones. Usted puede
    también agregue direcciones de correo electrónico adicionales para ser notificadas cuando la restauración
    La operación ha finalizado. Tenga en cuenta que la cuenta de administrador y el
    el usuario que inició el procedimiento de restauración recibe una notificación de forma predeterminada.
A través de la CLI
Para iniciar una operación de restauración externa, use el comando doExternalRestore mando:

Sintaxis:
   zxsuite backup doExternalRestore {source_path} [attr1 value1 [attr2 value2 ...

LISTA DE PARÁMETROS

NOMBRE TIPO VALORES ESPERADOS POR DEFECTO
source_path (M) Ruta
cuentas (O) Nombre de cuenta [, ..] todos
dominios (O) Nombre de dominio [, ..] todos
filter_deleted (O) Boolean verdadero | falso verdadero
skip_system_accounts (O) Boolean verdadero | falso verdadero
skip_aliases (O) Boolean verdadero | falso falso
skip_distribution_lists (O) Boolean verdadero | falso falso
provisioning_only (O) Boolean verdadero | falso falso
skip_coses (O) Boolean verdadero | falso falso
notificaciones (O) Dirección de correo electrónico

(M) == parámetro obligatorio, (O) == parámetro opcional
Ejemplo de uso:

 zxsuite backup doexternalrestore /opt/zimbra/backup/export/ accounts tuxito@aulautil.tk,gerente@aulautil.tk domains aulautil.tk filter_deleted false skip_system_accounts false
Restaura el dominio example.com, incluidas todas las cuentas del sistema, y ​​las cuentas tuxito@aulautil.tk y gerente@aulautil.tk desde una copia de seguridad ubicada en /opt/zimbra/backup/export

8.2.- Acelerar la restauración a través de subprocesos múltiples
El parámetro concurrent_accounts le permite restaurar múltiples cuentas al mismo tiempo, acelerando enormemente el proceso de restauración. Esta función no está disponible a través de la Consola de administración .

Advertencia

Aunque el consumo de recursos no crece linealmente con el número de cuentas restauradas al mismo tiempo, pueden convertirse fácilmente en impuestos. comienzo desde un número bajo de cuentas concurrentes, y elevarlo de acuerdo con el rendimiento de su servidor.

Ejemplo de uso:

zxsuite backup doExternalRestore /tmp/external1 domains example0.com,example1.com concurrent_accounts 5
Restaura el dominio example0.com y example1.com, excluyendo las cuentas del sistema, restaurando 5 cuentas al mismo tiempo desde una copia de seguridad ubicada en / tmp / external1

8.3.- Después de la restauración: deduplicación de mensajes
Ejecutar una deduplicación de todo el volumen con el módulo HSM NG es altamente recomendado después de una restauración externa, desde la deduplicación nativa el sistema puede ser ineficaz al importar cuentas secuencialmente.

9.- Restaurar cuenta eliminada
¿Qué es la cuenta restablecida eliminada?
El procedimiento Restaurar cuenta eliminada le permite restaurar el contenido y las preferencias de un buzón, como cuando se eliminó dicho buzón, en una cuenta completamente nueva.

¿Como funciona?
Cuando se inicia una cuenta eliminada de restauración, se crea una nueva cuenta (el Cuenta de destino) y todos los elementos existentes en la cuenta de origen en el momento de la eliminación se recrean en la cuenta de destino, incluyendo la estructura de carpetas y todos los datos del usuario. Todo restaurado los mensajes se crearán en la tienda principal actual a menos que `Obey HSM La casilla Política está marcada.

Advertencia Al restaurar datos en una nueva cuenta, la coherencia de los elementos compartidos no es conservado. Esto se debe a que las reglas de uso compartido originales se refieren a ID de la cuenta original, no a la cuenta restaurada.

Desde la pestaña Backup NG
Para restaurar una cuenta eliminada desde el panel ejecutamos

- Seleccione "Backup NG" en el panel izquierdo de la Administración
    Consola para mostrar la pestaña Copia de seguridad NG.

- En la barra superior, presione el botón `Restaurar cuenta eliminada`.

- Elija la fecha de restauración. El día/mes/año se puede seleccionar a través de un
    minical, la hora a través de un menú desplegable y el minuto y segundo a través de
    Dos cuadros de texto. Haga clic en "Siguiente".

- Examine la lista y haga clic en la cuenta que se va a restaurar (Fuente).

- Ingrese el nombre de la nueva cuenta (Destino) en el cuadro de texto. Tú
    luego puede elegir si Ocultar en GAL la nueva cuenta o no. Cuando
    Cuando haya terminado de elegir, presione `Siguiente`.

- Verifique todas sus opciones en la ventana Resumen de operaciones. Usted puede
    también agregue direcciones de correo electrónico adicionales para ser notificadas cuando la restauración
    La operación ha finalizado. Tenga en cuenta que la cuenta de administrador y el
    el usuario que inició el procedimiento de Restauración recibe una notificación de forma predeterminada.

- Haga clic en "Finalizar" para iniciar la restauración.
10.- Restaurar mensaje
¿Qué es la restauración de mensajes?
La restauración de elementos es uno de los modos de restauración de Backup NG.

¿Como funciona?
Se restaura un solo elemento desde la copia de seguridad a la cuenta del propietario. Alguna El tipo de elemento se puede restaurar de esta manera.

Ejecutar una restauración de mensaje
A través de la Administración Zimlet
La restauración de elementos solo está disponible a través de la CLI.

A través de la CLI
Para iniciar una operación de restauración de elementos, use el comando doItemRestore:

    Sintaxis:
       zxsuite backup doItemRestore {account_name} {item_id} [attr1 value1 [attr2 value2 ...

    LISTA DE PARÁMETROS

    NOMBRE TIPO
    nombre_cuenta (M) Nombre de cuenta
    item_id (M) Entero
    cadena de restore_folder (O)

    (M) == parámetro obligatorio, (O) == parámetro opcional
Ejemplo de uso:

zxsuite backup doitemrestore tuxito@aulautil.tk 4784
Restaura el elemento 4784 en el buzón john @ example.com

Cómo obtener el itemID
El itemID es uno de losmetadatos de un elemento que consiste en un Código unívoco que identifica un elemento en un buzón.

Junto con todos los demás metadatos, se almacena en un archivo dentro de los elementos directorio de la cuenta adecuada en

[ruta de respaldo]/accounts/[accountID]/items/[últimos 2 dígitos del itemID]/[itemID]

p.ej:

Artículo 2057 de la cuenta 4a217bb3-6861-4c9f-80f8-f345ae2897b5, predeterminado ruta de respaldo
/opt/zimbra/backup/ng/accounts/4a217bb3-6861-4c9f-80f8-f345ae2897b5/items/57/2057

Los metadatos se almacenan en un archivo de texto sin formato, por lo que herramientas como grep y find puede usarse para buscar contenidos. Para ver los metadatos contenidos en un archivo en un formato más legible, puede usar el zxsuite comando getItem de respaldo:

    Sintaxis:
    zxsuite backup getItem {cuenta} {item} [attr1 value1 [attr2 value2 ...

    LISTA DE PARÁMETROS

    NOMBRE TIPO VALORES ESPERADOS POR DEFECTO
    cuenta (M) Nombre / ID de cuenta
    ítem (M) Entero
    backup_path (O) Ruta / opt / zimbra / backup / ng /
    dump_blob (O) Booleano verdadero | falso falso
    fecha (O) Fecha dd / mm / aaaa hh: mm: ss | todas las últimas

    (M) == parámetro obligatorio, (O) == parámetro opcional
Ejemplo de uso:

    zxsuite backup getitem a7300a00-56ec-46c3-9773-c6ef7c4f3636 1
Muestra el elemento con id=1 perteneciente a la cuenta a7300a00-56ec-46c3-9773-c6ef7c4f3636

‘Real Life *’ Ejemplo
Supongamos que un usuario mueve un elemento a la papelera …

2013-07-18 15:22:01,495 INFO [btpool0-4361://localhost/service/soap/MsgActionRequest [name=user@domain.com;mid=2538;oip=258.236.789.647;ua=zclient/7.2.4_GA_2900;] mailop - moving Message (id=339) to Folder Trash (id=3)

… y vacía la basura.

2013-07-18 15:25:08,962 INFO [btpool0-4364://localhost/service/soap/FolderActionRequest] [name=user@domain.com;mid=2538;oip=258.236.789.647;ua=zclient/7.2.4_GA_2900;] mailbox - Emptying 9 items from /Trash, removeSubfolders=true.

Luego llama al administrador para restaurar el elemento eliminado. Conocimiento el itemID y la dirección de correo electrónico, el administrador ejecuta lo siguiente como el usuario zimbra para restaurar el elemento faltante:

zxsuite backup doItemRestore usuario@dominio.com 339

11.- Restauración sin procesar
La operación «Restauración sin procesar» es una nueva operación de restauración de tipo DR que es compatible con copias de seguridad estándar y sin problemas. En contraste con modos de restauración similares, como la restauración externa, la restauración sin procesar opera en un nivel inferior restaurando todos los metadatos del elemento manteniendo el ID originales para todos los objetos restaurados.

La restauración sin procesar restaura el almacenamiento centralizado del servidor de origen configuración. Este paso garantiza que cualquier información almacenada dentro de un El almacenamiento centralizado está disponible de inmediato. Si está utilizando local o volúmenes independientes de terceros, es fácil mover los BLOB de elementos de el almacenamiento primario o para restaurarlos desde una copia de seguridad usando el Blob Restaurar la operación.

Diferencias entre restauración externa y restauración sin procesar

Restauración externa	Restauración sin procesar
Se puede usar en cualquier versión de Zimbra independientemente de la fuente	Debe coincidir con la misma versión de Zimbra y nivel de parche que los del servidor de origen
No restaura ninguna configuración	Restaura la configuración de almacenamiento centralizado
No admite rutas de copia de seguridad sin bloques	Está diseñado para rutas de respaldo sin problemas y compatible con rutas de respaldo estándar
Restaura elementos BLOB	No restaura los BLOB de elementos
Los objetos restaurados se crean de nuevo	Los objetos restaurados mantienen su ID original
Qué será restaurado
Configuración y configuraciones de almacenamiento centralizado
Dominios
Clases de servicio
Listas de distribución
Buzones
Preferencias de buzón
Metadatos del mensaje
Lo que no será restaurado
Blobs de mesajes
11.1.- Ejecutar una restauración sin procesar
La restauración sin procesar solo está disponible a través de la herramienta CLI zxsuite:

    [zimbra @ mail ~] $ zxsuite backup doRawRestore
    Realizar una recuperación ante desastres

    Sintaxis:
       zxsuite backup doRawRestore {source_path} [attr1 value1 [attr2 value2 ...]]

    LISTA DE PARÁMETROS

    NOMBRE TIPO VALORES ESPERADOS POR DEFECTO
    source_path (M) Cadena
    notificaciones (O) Dirección de correo electrónico [, ..]
    skipProvisioning (O) Booleano verdadero | falso falso
    deleteWhenConflict (O) Boolean verdadero | falso falso

    (M) == parámetro obligatorio, (O) == parámetro opcional
Ejemplo de uso:

zxsuite backup doRawRestore /my/backup/path notifications user1 @ example.com, user2 @ example.com skipProvisioning false deleteWhenConflict false
Realiza una restauración sin procesar sin restaurar el aprovisionamiento o eliminar un buzón cuando los ID están en conflicto, y envía notificaciones a user1@example.com y user2@example.com La operación de recuperación de desastres no realiza la restauración de blobs, use doRestoreBlobs cuando sea necesario.

11.2.- Escenarios de uso
Restauración de una infraestructura de servidor único

Configure un nuevo servidor (instale Zimbra, configure Global y Server ajustes).
Cree manualmente cualquier volumen de terceros local o independiente como era en el servidor original
Inicie una restauración sin procesar utilizando para restaurar dominios, buzones de correo de CoS y metadatos del elemento (los buzones no serán accesibles hasta este paso completa).
Si la copia de seguridad de origen no se estaba ejecutando en modo Blobless, ejecute zxsuite copia de seguridad de doRestoreBlobs para todos los volúmenes para restaurar elementos BLOBS.
Pérdida de un solo nodo de buzón en una infraestructura multiservidor

Agregue un nuevo nodo de buzón a la infraestructura.
Cree manualmente cualquier volumen de terceros local o independiente como era en el servidor original
Inicie una restauración sin procesar utilizando el parámetro skipProvisioning true para restaurar los metadatos del elemento (los buzones no serán accesibles hasta este paso completa).
Si la copia de seguridad de origen no se estaba ejecutando en modo Blobless, ejecute zxsuite copia de seguridad de doRestoreBlobs para todos los volúmenes para restaurar elementos BLOBS.
Pérdida de múltiples servidores de buzones en una infraestructura

Configure una nueva infraestructura vacía (todos los servidores y roles, configurando Configuración global y del servidor).
Elimine las cuentas predeterminadas admin,gal, ham yspam.
En todos los servidores de buzones, cree manualmente cualquier local o independiente Volumen de terceros como estaba en el servidor original.
En el primer servidor de buzón, inicie una Restauración sin procesar utilizando para restaurar dominios, buzones de CoS y metadatos de elementos (los buzones no serán accesible hasta que se complete este paso).
En todos los demás servidores de buzones, inicie una Restauración sin formato utilizando Parámetro skipProvisioning true para restaurar los metadatos del elemento.
Una vez que se hayan completado los pasos 3 y 4, si la copia de seguridad de origen no se estaba ejecutando en Blobless Mode ejecuta zxsuite backup doRestoreBlobs para todos los volúmenes en todos los servidores de buzones para restaurar elementos BLOBS.
