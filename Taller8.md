Zimbra Suite Plus, módulos de Zextras para Zimbra Network
Zimbra Suite Plus es un conjunto de herramientas integradas en Network Edition, y que Zextras vende como una extensión para Zimbra Onpen source licenciado por usuario. Zimbra Suite Plus fue agregado con funcionalidades tan importantes como pueden ser Backup, ActiveSync, HSM o Delegación de Administradores. Actualmente Zextras tiene una nueva versión de Suite Plus para Open Source que integra videoconferencia y chat corporativo con canales.

1.- Instalacion de Zimbra Suite Plus
1.1.- Instalación en Zimbra Network
Se instala como una opción elegible dentro del instalador de zimbra network

1.2.- Instalación de Zextras Suite en Zimbra Open Source
Se instala para Zimbra Open Source como un paquete que se descarga y descomprime

Descargar instalador, conectándose al servidor Open Source como root

wget http://www.zextras.com/download/zextras_suite-latest.tgz
1.2.1.- Componentes
Zextras Suite está comformado por tres componentes principales:

Core: es un archivo.jar para extender Zimbra
Zimlet: es un zimlet administrativo que accesa y administra el Core.
ZAL La capa de abstracción Open Source para Zimbra.
El script de instalación sigue la estructura mencionada arriba, permitiéndole tres opciones de instalación: core, zimlet y all. La última es equivalente a instalar el Core y a continuación el zimlet.

1.2.2.- Guía rápida de instalación
descomprima el paquete de instalación

tar -zxvf zextras_suite-latest.tgz
Acceder a la carpeta de instalación

cd zextras_suite-latest
Ejecute el script de instalación install.sh con la opción «all»

./install.sh all
El instalador se hará cargo de bajar la última versión disponible del Core, del ZAL y los zimlets de acuerdo con su versión Zimbra.

1.2.3- Verificación de la versión de ZxSuite
Loguearnos como zimbra

su - zimbra
Verificar la versión de zimbra y zxsuite ejutando

zxsuite |grep version 
ó

zxsuite core getVersion
Para saber los cambios (changelog) que tiene la versión de Zextras Suite revisar https://docs.zextras.com/suite/html/changelog.html

2.- Utilidad ZxSuite Interfaz de línea de comandos de módulos Network NG
2.1.- Uso básico de zxsuite
Cada módulo de Network NG, incluido el núcleo, tiene su propio conjunto de
zxsuite comandos. Todos los comandos zxsuite se invocan con el
siguiente sintaxis:
: zxsuite [–host|–offline] [–json] [–progress] [–sync] {module} {action} [options]
Opciones disponibles:

–host [hostname|ip] especifique un host de destino para el comando. Déjelo en blanco para localhost. Use todos los servidores para transmitir el comando a todos los servidores.
sin conexión: se usa si Zimbra no se inicia. Algunos comandos no lo harán trabajo
–json: la salida del comando se presentará en formato JSON. Bueno para guiones.
–progress: imprime los comentarios de la operación directamente en STDOUT. Presione Ctrl + C para interrumpir la salida. La operación en sí misma no será interrumpido
–sync: ejecuta el comando en modo síncrono, esperando el la ejecución de la operación para finalizar y devolver un código de salida relevante con el resultado de la operación entre los siguientes:
0 – Exitoso
1 – Falló
2 – Detenido
3 – Eliminado
4 – Interrumpido
Ayuda en línea: Una ayuda en línea para cada comando está disponible a través de

zxsuite help {módulo} [comando] *. Omitir el comando mostrará La lista completa de comandos disponibles.
3.- Core CLI
Uso Básico:

zxsuite core {action} [opciones]

Comandos principales
:: getVersion– Versión de módulos de Red de Display NG

zxsuite core getVersion
: getProperty– Obtener propiedades de configuración
zxsuite core getProperty [attr1 value1 [attr2 value2 ...
: setProperty– Establece la propiedad de configuración
zxsuite core setProperty {nombre_propiedad} {valor_propiedad}
: getLicenseInfo– Información de licencia de los módulos Zimbra Network NG
zxsuite core getLicenseInfo
: doUploadLicense– Carga una licencia de Network NG
zxsuite core doUploadLicense {license_path}
: doLicenseChecks– Fuerza la verificación de la licencia Network NG
zxsuite core doLicenseChecks
: getAccountStats– Muestra información estadística sobre lo proporcionado nombre de cuenta o ID
zxsuite core getAccountStats {cuenta}
: getNotification– Muestra notificaciones de Network NG
zxsuite core getNotification [attr1 value1 [attr2 value2 ...
: doDeleteAllDelegatedRights– Este comando elimina tanto Admin NG y la propia configuración de Delegación de administrador de Zimbra
zxsuite core doDeleteAllDelegatedRights {cadena de confirmación}

4.- Update CLI
Uso Básico:

zxsuite core {acción} [opciones]

Comandos de actualización
:: doCheckUpdate– Obtener información de actualización

zxsuite core doCheckUpdate
: doStartService– inicia un servicio determinado
zxsuite core doStartService {service_name}
: doStopService– detiene un servicio determinado
zxsuite core doStopService {service_name}

5.- Backup NG CLI
Uso Básico:

zxsuite backup {action} [opciones]

Comandos Backup NG
:: getProperty– Obtener propiedades de configuración

zxsuite backup getProperty [attr1 value1 [attr2 value2 ...
: setProperty– Establece la propiedad de configuración
zxsuite backup setProperty {nombre_propiedad} {valor_propiedad}
: doSmartScan– Realizar un escaneo inteligente
zxsuite backup doSmartScan [attr1 value1 [attr2 value2 ...
: doAccountScan– Realizar un análisis completo en una sola cuenta
zxsuite backup doAccountScan {cuenta} [attr1 value1 [attr2 value2 ...
: doExport– Realizar una exportación limitada por dominios
zxsuite backup doExport {destination_path} [attr1 value1 [attr2 value2 ...
: doRestoreOnNewAccount– Realizar una restauración en una cuenta nueva
zxsuite backup doRestoreOnNewAccount {source_account} {destination_account} {dd/MM/aaaa HH:mm:ss|last} [attr1 value1 [attr2 value2 ...
: doEnableDisableCOS– Activa o desactiva la copia de seguridad para un determinado COS
zxsuite backup doEnableDisableCOS {cos_name} {enable|disable}
: doUndelete– Realizar una restauración de recuperación
zxsuite backup doUndelete {cuenta} dd/MM/aaaa HH:mm:ss|primero} dd/MM/aaaa HH:mm:ss|last} [attr1 value1 [attr2 value2 ...
: doItemSearch– Busca un artículo
zxsuite backup doItemSearch {cuenta} [attr1 value1 [attr2 value2 ...
: doItemRestore– Restaura un solo elemento
zxsuite backup doItemRestore {account_name} {item_id}
: doExternalRestore– Realizar una restauración externa
zxsuite backup doExternalRestore {source_path} [attr1 value1 [attr2 value2 ...
: doStopOperation– Detiene una sola operación en ejecución
zxsuite backup doStopOperation {operation_uuid}
: doStopAllOperations– Detiene todas las operaciones en ejecución y vacía la cola de operaciones
zxsuite backup doStopAllOperations
: doCheckShares– Verifique todos los recursos compartidos en cuentas locales
zxsuite backup doCheckShares
: doFixShares– Intenta arreglar todos los recursos compartidos en cuentas locales
zxsuite backup doFixShares {import_idmap_file}
: doFixOrphans– Eliminar archivos de resumen huérfanos
zxsuite backup doFixOrphans [attr1 value1 [attr2 value2 ...
: doCoherencyCheck– Verificar la coherencia de la copia de seguridad
zxsuite backup doCoherencyCheck {backup_path} [attr1 value1 [attr2 value2 ...
: getAccountInfo– Muestra la información de una cuenta
zxsuite backup getAccountInfo {cuenta} [attr1 valor1 [attr2 valor2 ...
: getBackupInfo– Muestra información sobre el sistema de respaldo
zxsuite backup getBackupInfo [attr1 valor1 [attr2 valor2 ...
: getMap– Muestra el objeto Map binario como tabla legible por humanos
zxsuite backup getMap {ruta_archivo}
: getItem– Muestra un elemento en un formato legible para humanos.
zxsuite backup getItem {cuenta} {item} [attr1 value1 [attr2 value2 ...
: getAvailableAccounts– Muestra todas las cuentas disponibles para restaurar
zxsuite backup getAvailableAccounts [attr1 value1 [attr2 value2 ...
: getAvailableDomains– Muestra todos los dominios disponibles para restaurar
zxsuite backup getAvailableDomains {dd/MM/aaaa HH:mm:ss|last} {backup_path}
: getServerConfig– Proporciona una lista de configuraciones de servidor almacenadas
zxsuite backup getServerConfig dd/MM/aaaa HH:mm:ss|last|all} {standard|customizations} [attr1 value1 [attr2 value2 ...
: getCOSBackupStatus– Muestra el estado de la copia de seguridad de todos los COS
zxsuite backup getCOSBackupStatus [attr1 value1 [attr2 value2 ...
: getAllOperations– Muestra todas las operaciones en ejecución y en cola
zxsuite backup getAllOperations
: monitor– Supervisar una operación en ejecución
zxsuite backup monitor {operation_uuid} [attr1 value1 [attr2 value2 ...
: getServices– Muestra el estado actual de todos los módulos servicios
zxsuite backup getServices
: doBackupLDAP– Copia de seguridad LDAP
zxsuite backup doBackupLDAP
: doRestartService– Reinicia un servicio dado
zxsuite backup doRestartService {nombre_servicio}
: doRestoreBlobs– Inicia una operación de «restauración de blobs» que intenta restaurar las gotas de zimbra rotas.
zxsuite backup doRestoreBlobs {volume_id} [attr1 value1 [attr2 value2 ...]]

6.- Mobile NG CLI
Uso Básico:

zxsuite mobile {action} [opciones]

Comandos Mobile NG
:: getProperty– Obtener propiedades de configuración

zxsuite mobile getProperty [attr1 value1 [attr2 value2 ...
: setProperty– Establece la propiedad de configuración
zxsuite mobile setProperty {nombre_propiedad} {valor_propiedad}
: getDeviceList– Muestra todos los dispositivos para la cuenta proporcionada
zxsuite mobile getDeviceList {cuenta}
: getDeviceInfo– Muestra información sobre el dispositivo proporcionado
zxsuite mobile getDeviceInfo {cuenta} {device_id}
: doResetAccount– Restablece TODOS los estados del dispositivo para la cuenta proporcionada
zxsuite mobile doResetAccount {cuenta}
: doResetDevice– Restablece SyncState del dispositivo para un solo cuenta
zxsuite mobile doResetDevice {cuenta} [attr1 value1 [attr2 value2 ...
: getAllSessions– Devuelve información sobre todos los dispositivos móviles almacenados sesiones
zxsuite mobile getAllSessions
: getActiveSessions– Devuelve todas las sesiones móviles activas
zxsuite mobile getActiveSessions
: doRemoveDevice– Elimina todo el SyncState y el historial del dispositivo del servidor
zxsuite mobile doRemoveDevice {cuenta} {device_id}
: getAccountLoggers– Devuelve información sobre todas las cuentas madereros
zxsuite mobile getAccountLoggers
: doRemoveLogger– Elimina un registrador de cuenta
zxsuite mobile doRemoveLogger {logger_id|all_loggers}
: doAddAccountLogger– Agregar un registrador de cuenta
zxsuite mobile doAddAccountLogger {cuenta} {debug|info|warn|err|crit} {log_file}

7.- HSM NG CLI
Uso Básico:

zxsuite hsm {acción} [opciones]

Comandos HSM NG
:: + setHsmPolicy– Agrega una política adicional a HSM.

zxsuite hsm + setHsmPolicy {hsm_policy}
: addS3Store– Agrega una tienda compatible con S3
zxsuite hsm addS3Store {Nombre de la tienda zimbra} [attr1 value1 [attr2 value2 ...
: doCheckBlobs– Inicia la operación doCheckBlobs.
zxsuite hsm doCheckBlobs {inicio} [attr1 valor1 [attr2 valor2 ...
: doCreateVolume– Crea un volumen en el servidor
zxsuite hsm doCreateVolume {primario|secundario|índice} {nombre_volumen} {ruta_volumen} {verdadero|falso} {compresión_threshold_bytes}
: doDeduplicate– Inicia una operación de deduplicación.
zxsuite hsm doDeduplicate {volume_name} [attr1 valor1 [attr2 valor2 ...
: doDeleteVolume– Eliminar un volumen específico en el servidor
zxsuite hsm doDeleteVolume {volume_id}
: doMoveBlobs– Inicia la operación doMoveBlob.
zxsuite hsm doMoveBlobs [attr1 valor1 [attr2 valor2 ...
: doRemoveHsmPolicy– Eliminar una política de HSM.
zxsuite hsm doRemoveHsmPolicy {hsm_policy}
: doRestartService– reinicia un servicio dado
zxsuite hsm doRestartService {nombre_servicio}
: doStartService– inicia un servicio determinado
zxsuite hsm doStartService {nombre_servicio}
: doStopAllOperations– Detiene todas las operaciones en ejecución y vacía la cola de operaciones
zxsuite hsm doStopAllOperations
: doStopOperation– Detiene una sola operación en ejecución
zxsuite hsm doStopOperation {operation_uuid}
: doStopService– detiene un servicio determinado
zxsuite hsm doStopService {nombre_servicio}
: doUpdateVolume– Actualiza un volumen específico en el servidor
zxsuite hsm doUpdateVolume {volume_id} [attr1 value1 [attr2 value2 ...
: doVolumeToVolumeMove– Inicia la operación doMoveVolumeBlobs.
zxsuite hsm doVolumeToVolumeMove {source_volume_name} {destination_volume_name}
: getAllOperations– Muestra todas las operaciones en ejecución y en cola
zxsuite hsm getAllOperations [attr1 value1 [attr2 value2 ...
: getAllVolumes– Imprime todos los volúmenes presentes en el servidor
zxsuite hsm getAllVolumes
: getHsmPolicy– Imprime todas las políticas
zxsuite hsm getHsmPolicy
: getProperty– Obtener propiedades de configuración
zxsuite hsm getProperty [attr1 value1 [attr2 value2 ...
: getServices– muestra el estado actual de todos los servicios para este módulo
zxsuite hsm getServices
: getVolumeStats– Muestra la ocupación del espacio, el número de elementos y blobs contenido en un volumen
zxsuite hsm getVolumeStats {volume_id} [attr1 value1 [attr2 value2 ...
: monitor– Supervisar una operación en ejecución
zxsuite hsm monitor {operation_uuid} [attr1 value1 [attr2 value2 ...
: setHSMPolicy– Establece la política de HSM predeterminada
zxsuite hsm setHSMPolicy {hsm_policy}
: setProperty– Establece la propiedad de configuración
zxsuite hsm setProperty {nombre_propiedad} {valor_propiedad}
: testS3Connection– Prueba la conexión a un depósito S3.
zxsuite hsm testS3Connection {s3BucketConfigurationUuid}

8.- Admin NG CLI
Uso Básico:

zxsuite admin {action} [opciones]

Comandos de administración NG
:: getDelegationSettings– Muestra administradores delegados con sus dominios de competencia, el atributo viewMail y la cuota asignable por el administrador

zxsuite admin getDelegationSettings [attr1 value1 [attr2 value2 ...
: doEditDelegationSettings
zxsuite admin doEditDelegationSettings {cuenta} {dominio} [attr1 value1 [attr2 value2 ...
: doAddDelegationSettings
zxsuite admin doAddDelegationSettings {cuenta} {dominio} [attr1 value1 [attr2 value2 ...
: \ * doRemoveDelegationSettings
zxsuite admin doRemoveDelegationSettings {cuenta} {dominio}
: getDomainSettings– Muestra el nombre de todos los dominios, el número de cuenta límite y cuota máxima de la cuenta
zxsuite admin getDomainSettings
: setDomainSettings– Establece la cuenta y la cuota máxima de la cuenta del dominio límite
zxsuite admin setDomainSettings {dominio} [attr1 valor1 [attr2 valor2 ...
: resetDomainSettings– Restablece el límite global de la cuenta, el límite de la cuenta por cos y cuota de cuenta de dominio para el dominio seleccionado
zxsuite admin resetDomainSettings {dominio}
: doShowAdminActivity– Muestra toda la actividad administrativa registrada
zxsuite admin doShowAdminActivity [attr1 value1 [attr2 value2 ...
: doEnableDisableAdminLogging– Activa o desactiva el registro de actividades administrativas
zxsuite admin doEnableDisableAdminLogging {enable|disable}
: setProperty– Establece la propiedad de configuración
zxsuite admin setProperty {property_name} {property_value}
: getProperty– Obtener propiedades de configuración
zxsuite admin getProperty [attr1 value1 [attr2 value2 ...
: doMonthlyReport– Genera un informe de actividad administrativa en el mes especificado
zxsuite admin doMonthlyReport [attr1 value1 [attr2 value2 ...
: getMonthlyReport– Muestra un informe de actividad administrativa en el especificado \ * mes
zxsuite admin getMonthlyReport [attr1 value1 [attr2 value2 ...
: doSetZimletRights– Corrige los derechos de administración de Zimlet para Todos los administradores delegados.
zxsuite admin doSetZimletRights
: getAllOperations– Muestra todas las operaciones en ejecución y en cola
zxsuite admin getAllOperations
: doStopAllOperations– Detiene todas las operaciones en ejecución y vacía la cola de operaciones
zxsuite admin doStopAllOperations
: doStopOperation– Detiene una sola operación en ejecución
zxsuite admin doStopOperation {operation_uuid}
: monitor– Supervisar una operación en ejecución
zxsuite admin monitor {operation_uuid} [attr1 value1 [attr2 value2 ...
