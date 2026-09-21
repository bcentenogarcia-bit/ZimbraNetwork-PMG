Migración de Servidores con Backup NG
Descripción
Esta guía describe cómo realizar una migración incremental usando Backup NG.
Está diseñado específicamente para la migración de una producción. entorno, minimizando el tiempo de inactividad y con el objetivo de ser transparente para los usuarios
Si se planificó y ejecutó correctamente, su sistema de correo no sufrirá cualquier tiempo de inactividad, y el impacto en los usuarios será cercano a cero.
El servidor de origen de la migración requiere la Suite Zextras o Zimbra Suite Plus.
Esta guía aplica para migraciones desde cualquier versión de Zimbra compatible con cualquiera de los de Zimbra 8.8.
Todos los comandos de CLI en esta guía deben ejecutarse como Zimbra usuario a menos que se especifique lo contrario.
¿Qué se migrará?
Correo electrónico y carpetas de correo electrónico
Contactos y libretas de direcciones
Nombramientos y calendarios
Tareas y listas de tareas
Archivos y maletines
Compartir información
Preferencias del usuario
Ajustes de usuario
Configuración de clase de servicio
Configuración del dominio
¿Qué NO se migrará?
Configuración del servidor (migrado para referencia pero no restaurado)
Configuración global (migrada para referencia pero no restaurada)
Personalizaciones (Postfix, Jetty, etc.).
Los elementos movidos o eliminados durante el proceso no se moverán o eliminado en el servidor de destino.
Las preferencias (por ejemplo, las contraseñas) cambiadas durante el proceso serán restablecer a cada importación.
Advertencia

La migración incremental no está diseñada para configurar un servidor a servidor reflejo. Uso de múltiples importaciones para crear una copia reflejada de el servidor de origen no creará una copia reflejada , ya que no las eliminaciones son realizadas por el proceso de importación.

Datos fuente
Los datos del servidor de origen se obtienen a través de Zextras Suite o Zimbra Suite Plus.

Zextras Suite se puede obtener en el sitio web de Zextras en https://www.zextras.com
Zimbra Suite Plus se puede obtener en el sitio web de Zimbra en https://www.zimbra.com
Instalación de software
Una vez que obtenga Zextras Suite o Zimbra Suite Plus, siga esto proceso de instalación:

Copie el paquete a su servidor, en un directorio propiedad de la raíz usuario.
Desempaquete el paquete usando tar zxf.
Ingrese el directorio recién creado llamado zextras_suite- [versión] o zimbra_suite_plus- [versión].
Como root, ejecute el script de instalación ./install.sh all.
Siga el asistente de instalación, acepte el software EULA y instalando el Core y el componente Zimlet.
Una vez completada la instalación, continúe con la Guía.
Advertencia

La instalación de Zimbra Suite Plus o Zextras Suite requiere un buzón reinicio del servicio, que se realizará durante la instalación.

Verificaciones previas a la migración
Servidores
Servidor fuente: cualquier servidor Zimbra puede ser la fuente de su migración, siempre que ejecute Backup NG o Zimbra Suite Más.
Servidor de destino: cualquier servidor Zimbra puede ser el destino de su migración, siempre que ejecute Backup NG.
Almacenamiento
Servidor de origen: si Backup NG no está habilitado actualmente en el origen servidor, asegúrese de tener una cantidad de espacio libre en el disco comparable al tamaño de la carpeta /opt/zimbra/store/ (los datos exportados son comprimido a través del algoritmo gzip, y todos los elementos de zimbra son deduplicado, generalmente reduciendo el tamaño de exportado al 70% de El tamaño original).
Servidor de destino: asegúrese de tener una cantidad de espacio libre mayor que el tamaño de /opt/zimbra/store/ y de Carpetas exportar en el servidor fuente combinadas.
Transferencia de datos
Si bien puede elegir transferir los datos de cualquier manera, rsync es nuestro método de elección, ya que es un buen compromiso entre velocidad y conveniencia.

La transferencia de datos principal se ejecuta mientras el servidor de origen todavía está Activo y funcional. Sin embargo, dado que la transferencia se realiza a través de red, planifique cuidadosamente su transferencia por adelantado para que tenga transfirió todos sus datos antes de migrar.

Formas alternativas de transferir sus datos
Cualquier cosa que abarque desde el montaje remoto hasta el movimiento físico de la unidad está bien, siempre y cuando se adapte a sus necesidades.

Nunca subestimes el ancho de banda de una camioneta llena de cintas
por la carretera
--Tanenbaum, Andrew S. (1996). Red de computadoras. Nueva Jersey: Prentice-Hall.
pags. 83. ISBN 0-13-349945-6.
DNS
Establezca el valor TTL de su registro MX en 300 en su DNS real. Esta voluntad permitir un cambio rápido entre los servidores de origen y destino.

Configurando la Migración
Paso 1: Verificaciones de coherencia
Para evitar posibles problemas relacionados con los datos, ejecute las siguientes comprobaciones en el servidor de origen:

Comprueba la coherencia entre los metadatos de Zimbra y los BLOB.

zmblobchk start
https://wiki.zimbra.com/wiki/Ajcody-Notes-No-Such-Blob#Zmblobchk_for_5.0.6.2B_Systems

Comprueba la integridad de la base de datos de Zimbra.

/opt/zimbra/libexec/zmdbintegrityreport
https://wiki.zimbra.com/wiki/Zmdbintegrityreport

Repare cualquier error encontrado como se describe en la documentación oficial de Zimbra. También se sugiere ejecutar una reindexación de todos los buzones.

Paso 2: Configuración de Network NG
Deshabilite el escáner en tiempo real en ambos servidores:

zxsuite backup setProperty ZxBackup_RealTimeScanner false
Advertencia

Se recomienda encarecidamente un dispositivo dedicado para la exportación de datos a mejorar el rendimiento de las exportaciones y reducir el impacto en el rendimiento del sistema en ejecución.

Cualquier dispositivo debe montarse en la ruta /opt/zimbra/backup/, y el El usuario de Zimbra debe tener permisos r/w.

Paso 3: Exportación de datos (SmartScan)
Ejecute un SmartScan en el servidor de origen:

zxsuite backup doSmartScan
Todos sus datos se exportarán a la ruta de respaldo predeterminada (/opt/zimbra/backup/).

Consejo profesional: exportación de dominios únicos
También puede elegir migrar solo uno o más dominios en lugar de todos de ellos. Para hacerlo, ejecute el siguiente comando en lugar del Escaneo inteligente:

zxsuite backup doExport /path/to/export/folder/ domains yourdomain.com, yourdomain2.com [..]
Tenga en cuenta que si comienza con el método SmartScan, tendrá que llevar sobre la migración con tal método. Si comienzas con el Método de dominios, tendrá que continuar la migración con este. Los dos métodos no se pueden mezclar.

Exportación de datos (SmartScan) a través del Zimlet de administración
También puede optar por exportar sus datos utilizando el Zimlet de Administración como sigue:

Paso 4: sincronización de datos
Advertencia

Cuando mueva los datos exportados al servidor de destino, asegúrese de que la carpeta de destino no es la ruta de respaldo de Backup NG en el servidor de destino, para evitar problemas si ya usa Backup NG o planee hacerlo en el servidor de destino.

(Puede omitir este paso si elige transferir sus datos por otro significa que rsync.) *
Usando rsync , copie los datos contenidos en /opt/zimbra/backup/ a un directorio en el servidor de destino (asegúrese de que el usuario de Zimbra tenga r/w permisos en la carpeta). [ejecuta este comando como Root]

rsync -avH /opt/zimbra/backup/ root@desinationserver:/backup/
Método de sincronización alternativo
Si bien el método sugerido es ideal para situaciones de gran ancho de banda, el La primera sincronización puede involucrar muchos datos. Si sientes que el El método rsync es demasiado lento, puede considerar un movimiento físico del dispositivo (o el archivo de disco adecuado si se ejecuta en un entorno virtual).

Después de mover el disco, puede volver a montarlo de forma remota en la fuente servidor (por ejemplo, a través de SSHFS), como las sincronizaciones adicionales necesarias para La migración implicará mucha menos información. En este caso, asegúrese de Vuelva a montar el dispositivo en el servidor de origen como /backup/ con Todos los permisos debidos.

Paso 5: primera importación
Importe todos los datos exportados al servidor de destino:

zxsuite backup doExternalRestore /backup/
Ahora siéntese y relájese mientras Network NG importa sus datos en el servidor de destino

‘Advertencia: no edite ni elimine la ruta de respaldo después de este paso.’

Primera importación a través de la Administración Zimlet
También puede optar por importar sus datos utilizando el Zimlet de Administración. Al importar a través de la Administración Zimlet, asegúrese de eliminar todos cuentas del sistema (como GalSync, Ham, Spam, Cuarentena, etc.) desde lista de cuentas importadas.

Paso 5 (alternativo): primera importación para migraciones grandes

Si va a migrar una infraestructura muy grande donde una exportación/importación dura horas o incluso días, hay una forma alternativa de manejar el migración desde este punto en adelante.

En lugar de importar todos sus datos al servidor de destino, puede ejecutar una importación de Solo aprovisionamiento que solo creará dominios, Clases de servicio y cuentas en el servidor de destino, omitiendo todos los buzones contenido.

zxsuite backup doExternalRestore /backup/ provisioning_only TRUE
Después de hacer esto, cambie el flujo de correo al nuevo servidor y, cuando el cambio se ha completado, inicie la importación real.

zxsuite backup doExternalRestore /backup/
De esta manera, sus usuarios ahora se conectarán al nuevo servidor donde hay nuevos correos electrónicos se entregará mientras se restauran los correos electrónicos antiguos.

Este enfoque tiene sus pros y sus contras, a saber:

Pros

Dado que los elementos solo se importan una vez y nunca se modifican o eliminan luego, el uso de este método generará menos discrepancias que la migración incremental estándar.
Esta es la opción que tiene menos impacto en el servidor de origen (p. Ej. bueno si tienes prisa por desmantelarlo).
Contras

Dependiendo del momento de la operación, este método tiene un mayor impacto en sus usuarios debido al hecho de que los elementos se restauran MIENTRAS ellos trabajan en su buzón.
Dado que la importación se realiza en un sistema en ejecución, es posible que observe algunas ralentizaciones
La situación hasta ahora
En este momento, la gran mayoría de los datos ya se han importado a El servidor de destino. El servidor de origen aún está activo y funcional, y ya está listo para realizar la migración real.

La migración
Paso 6: Verificaciones previas a la migración
Antes de cambiar el flujo de correo, SIEMPRE asegúrese de que el nuevo servidor esté listo para activarse (verifique su firewall, su configuración de DNS, su sistemas de seguridad, etc.).

Paso 7: el interruptor
¡Ya está, ha llegado el momento de la migración! Al final de este paso, el El servidor de destino estará activo y funcional.

Repita los pasos 3, 4 y 5 (solo se exportarán datos nuevos y sincronizado).
Cambie el flujo de correo al nuevo servidor.
Una vez que NO MÁS EMAILS lleguen al servidor de origen, repita el paso 3, paso 4 y paso 5.
El servidor de destino ahora está activo y funcional.

Paso 8: Verificaciones posteriores a la migración
Ejecute el siguiente comando para verificar las inconsistencias de recursos compartidos:

zxsuite backup doCheckShares
Si este comando reporta alguna inconsistencia, use el siguiente comando para analizar el archivo de mapa de importación utilizado como primer argumento y corregir cualquier acciones rotas.

zxsuite backup doFixShares
Los archivos de mapas se pueden encontrar en la ruta de respaldo del servidor de destino como map_ [source_serverID].

Paso 9: Galsync
Eliminar cualquier cuenta importada de GalSync de la Administración de Zimbra Consola, si es necesario, cree nuevas cuentas GalSync en todos los importados dominios y vuelva a sincronizar todas las cuentas de GalSync con el siguiente comando:

zmgsautil forceSync -a galsync.randomstring@domain.com -n [resourcename]
Inicialice Backup NG en el nuevo servidor para asegurarse de que todos sus datos es seguro.
Preguntas frecuentes sobre migración incremental
P: ¿Necesito una licencia válida para realizar una migración incremental?

Si. Puede ser una licencia de prueba o una comprada.

P: ¿Qué se migrará?
Todo excepto la configuración del servidor. Esto incluye:

Datos del usuario
Preferencias del usuario
Configuración de clases de servicio
Configuraciones de dominio
P: ¿Perderé mis acciones? ¿Tendré que volver a configurar todos mis recursos compartidos?

¡Absolutamente no!

P: ¿Cómo debo transferir los datos exportados entre mis servidores?

Una vez más, todo lo que se adapte a sus necesidades está bien. Solo necesitas ser muy seguro de cuáles son tus necesidades .

¿Necesita mover los datos muy rápido? Mover físicamente un disco USB entre sus servidores podría no ser una buena idea.

¿Necesita mover los datos de una manera muy confiable? Montando la exportación carpeta a través de SSHFS al servidor de destino podría no ser una buena idea si Su conexión a Internet es descuidada.
