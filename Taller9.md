Gestión de Storage y Volúmenes con HSM
1.- Gestión de almacenamiento HSM
1.1.- La técnica de gestión de almacenamiento jerárquico (HSM)
HSM es una técnica de almacenamiento de datos que mueve datos entre diferentes stores. de acuerdo con una política definida.

El uso más común de la técnica HSM es el movimiento de datos más antiguos de un dispositivo de almacenamiento más rápido pero costoso a uno más lento pero más barato basado en las siguientes premisas:

El almacenamiento rápido cuesta más.
El almacenamiento lento cuesta menos.
Se accederá a los datos antiguos con mucha menos frecuencia que los datos nuevos.
Las ventajas de la técnica HSM son claras: reducir el total costo de almacenamiento ya que solo una pequeña parte de sus datos debe ser costosa almacenamiento y mejorar la experiencia general del usuario.

Stores, volúmenes y políticas
El uso de HSM requiere una comprensión clara de algunos términos relacionados:

Almacenamiento principal: el almacenamiento rápido pero caro donde se encuentran todos sus datos inicialmente alojado.
Almacenamiento secundario: el almacenamiento lento pero barato donde los datos más antiguos deben ser trasladados
Corrección de carpetas que requiere HSM Ejecutar como root, crear directorio cache

mkdir /opt/zimbra/cache 
chown zimbra.zimbra /opt/zimbra/cache
Ejecutar como root, crear directorio incoming

mkdir /opt/zimbra/incoming
chown zimbra.zimbra /opt/zimbra/incoming
2.- Zimbra Stores
2.1- Fundamentos: tipos de stores y sus usos
Zimbra permite dos tipos diferentes de stores:

Index Store: una store que contiene información sobre sus datos que utiliza Apache Lucene para proporcionar indexación y funciones de búsqueda
Data Store: un store que contiene todos sus datos de Zimbra organizados en una base de datos MySql.
Puede tener varias stores de cada tipo, pero solo un store de índice, un almacén de datos primario y un almacén de datos secundario se pueden configurar como Actual (lo que significa que Zimbra lo usa actualmente).

2.2.- Almacenes de datos primarios y secundarios
Un almacén de datos en Zimbra puede ser un Almacén de datos primario o secundario.

Los datos se mueven entre el Almacenamiento de Datos Primarios y secundarios de acuerdo con una política definida.

2.2.1.- Definiendo un Volumen Secundario
Agregamos un disco local al servidor zimbra network de 100GB y luego lo definimos como volumen LVM asociado al punto de montaje /opt/zimbra/secundario

Instalar LVM (si no estuviese habilitado en el CentoS)

yum -y install lvm2
Listar los discos; y ahí podemos ver el nuevo disco

parted -l
Particionar el nuevo disco

parted /dev/sdb
print
mklabel gpt
mkpart
   nombre: secundario
   tipo: ext2
   inicio: 1
   fin:    107GB
quit
Crear volumen físico

pvcreate /dev/sdb1
Crear Grupo de Volumen

vgcreate store /dev/sdb1
Crear Volumen lógico para data secundaria

lvcreate store -n secundario -l+100%FREE
Formatear volumen lógico

mkfs.xfs /dev/mapper/store-secundario 
Agregar como punto de montaje permanente

echo "/dev/mapper/store-secundario /opt/zimbra/secundario xfs defaults 0 0" >> /etc/fstab
Crear directorio de punto de montaje

mkdir /opt/zimbra/secundario
Montar almacenamiento secundario

mount /opt/zimbra/secundario
Cambiar dueño del punto de montaje

chown zimbra.zimbra /opt/zimbra/secundario
Verificar el montaje del nuevo store

df -h
Agregar el storage como almacenamiento secundario

Ir a Panel de Administración --> Network Modules NG --> HSM 
Volúmenes Secundarios ---> Añadir
   Tipo de Volumen
      Local
         Nombre de Volumen: secundario
         Ruta de Volumen: /opt/zimbra/secundario
         Habilitar compresion: check
         Umbral de compresión: 4.0 KB
   Terminar
Establecer como volumen secundario actual el volumen agregado
Volúmenes Secundarios ---> 
   secundario ---> Editar
   actual: poner check
2.3.- HSM NG: Mover items entre stores
La característica principal del módulo HSM NG es la capacidad de aplicar definido Políticas de HSM.

El movimiento se puede activar de tres maneras:

Haga clic en el botón Aplicar política en el Zimlet de administración.
Inicie la operación doMoveBlobs a través de la CLI.
Habilite la programación de aplicaciones de políticas en el Zimlet de administración y espere a que se inicie automáticamente.
Una vez que se inicia el movimiento, se realizan las siguientes operaciones:

HSM NG escanea a través de la Store principal para ver qué items cumplen con la política definida
Todos los blobs de los items encontrados en el primer paso se copian en el Store Secundaria.
Las entradas de la base de datos relacionadas con los items copiados se actualizan a Refleja el movimiento.
Si el segundo y el tercer paso se completan con éxito (y solo en este caso), los blobs antiguos se eliminan del primario Store.
La operación Move es con estado: cada paso se ejecuta solo si el El paso anterior se ha completado con éxito, por lo que el riesgo de pérdida de datos durante una operación Move no existe.

3.- HSM doMoveBlobs
La operación doMoveBlobs de HSM NG
DoMoveBlobs es el corazón de HSM NG.

Mueve items entre la store principal actual y la actual Store secundaria de acuerdo con la política de HSM adecuada.

El movimiento se realiza mediante un algoritmo transaccional. Debe un error ocurrir durante uno de los pasos de la operación, se produce una reversión y no se realizarán cambios en los datos.

Una vez que HSM NG identifica los items a mover, los siguientes pasos son realizado:

Se crea una copia del Blob en el Almacén secundario actual.
La base de datos Zimbra se actualiza para notificar a Zimbra sobre el nuevo artículo. posición.
El blob original se elimina de la store principal actual.
¿Qué se mueve?
Cada elemento que cumpla con la política HSM especificada se mueve.

Ejemplo:

La siguiente política

mensaje, documento: antes: -20 días
mensaje: antes: -10day tiene: archivo adjunto
moverá todos los correos electrónicos y documentos de más de 20 días junto con todos correos electrónicos de más de 10 días que contienen un archivo adjunto.

Orden de política
Todas las condiciones para una política se ejecutan en el orden exacto en que se encuentran. especificado. HSM NG realizará un bucle en todos los items de la Store principal actual y aplique cada condición por separado antes de comenzar la siguiente.

Esto significa que las siguientes políticas

mensaje, documento: antes: -20 días
mensaje: antes: -10day tiene: archivo adjunto

mensaje: antes: -10day tiene: archivo adjunto
mensaje, documento: antes: -20 días
aplicado diariamente en un servidor de muestra que envía / recibe un total de 1000 correos electrónicos por día, 100 de los cuales contienen uno o más archivos adjuntos, tendrán El mismo resultado final. Sin embargo, el tiempo de ejecución de la segunda política probablemente será un poco más alto (o mucho más alto, dependiendo del número y tamaño de los correos electrónicos en el servidor).

Esto es porque en la primera política, la primera condición (mensaje, documento: antes: -20 días) se repetirá en todos los items y moverá muchos de ellos a la store secundaria actual, dejando menos items para el segundo condición para bucle.

Del mismo modo, tener el mensaje: antes: -10 días tiene: archivo adjunto como el primero La condición dejará más items para que la segunda condición se repita.

Esto es solo un ejemplo y no se aplica a todos los casos, pero da un idea de la necesidad de planificar cuidadosamente su política de HSM.

3.1.- Ejecutar la operación doMoveBlobs (también conocido como Aplicar la Política HSM)
Aplicar una política * significa ejecutar la operación doMoveBlobs en orden para mover items entre la store primaria y secundaria de acuerdo con política definida
HSM NG le ofrece tres opciones diferentes:

A través de la Administración Zimlet
A través de la CLI
A través de la programación
Aplicar la política de HSM a través del Zimlet de administración
Para aplicar la Política HSM a través del Zimlet de Administración:

Inicie sesión en la consola de administración de Zimbra.
Haga clic en la entrada HSM NG en el Zimlet de Administración.
Haga clic en el botón Aplicar política .
Aplicar la política HSM a través de la CLI
Para aplicar la Política HSM a través de la CLI, ejecute el siguiente comando como

zimbra * usuario:
\ zxsuite hsm doMoveBlobs \

Aplicar la política HSM a través de la programación
Para programar una ejecución diaria de la operación doMoveBlobs:

Inicie sesión en la consola de administración de Zimbra.
Haga clic en la entrada HSM NG en el Zimlet de Administración.
Habilite la programación seleccionando Habilitar programación de sesión HSM: botón.
Seleccione la hora para ejecutar la operación en Sesión HSM programada para:.
3.2.- Estadísticas e información de doMoveBlobs
Información sobre ahorro de espacio en disco, rendimiento de operaciones y más están disponibles haciendo clic en el botón Estadísticas debajo de `Secundario Lista de volúmenes en la pestaña HSM NG del Zimlet de Administración.

4.- Gestión de volúmenes
Se pueden crear volúmenes primarios y secundarios en cualquier almacenamiento local o en soluciones de almacenamiento de terceros compatibles.

4.1.- Volúmenes Zimbra
Un volumen es una entidad (ruta) distinta en un sistema de archivos con todos los propiedades asociadas que contienen Zimbra Blobs.

4.1.1.- Propiedades de volumen
Todos los volúmenes de Zimbra están definidos por las siguientes propiedades:

Nombre: un identificador único para el volumen.
Ruta: la ruta donde se guardarán los datos.
Importante

El usuario zimbra debe tener permisos r/w en esta ruta. * Compresión: active o desactive la compresión de archivos para volumen. * Umbral de compresión: el tamaño mínimo de archivo que desencadenar la compresión. Los archivos de menos de este tamaño nunca serán comprimido incluso si la compresión está habilitada. * Actual: A Current volume es un volumen donde se escribirán los datos aplicación de política de llegada (corriente primaria) o HSM (secundaria Actual).

4.1.2.- Gestión de volúmenes con HSM NG – Administración Zimlet
4.1.2.1.- Crear un nuevo volumen con el Zimlet de administración
Para crear un nuevo volumen desde la pestaña HSM NG del Zimlet de administración:

Haga clic en la opción Agregar apropiada en la Gestión de volúmenes sección de acuerdo con el tipo de volumen que desea crear.
Seleccione el tipo de store, eligiendo entre punto de montaje local o S3 Bucket.
Ingrese el nombre del nuevo volumen.
Ingrese una ruta para el nuevo volumen.
Marque el botón Activar compresión si desea activar la compresión de datos en el nuevo volumen.
Seleccione el umbral de compresión.
Si está utilizando un Bucket S3, es posible almacenar información para buckets múltiples
Presione OK para crear el nuevo volumen. Si la operación falla, Se generará una notificación que contenga cualquier error relacionado.
4.1.2.2.- Edición de un volumen con el Zimlet de administración
Para editar un volumen desde el Zimlet de Administración, simplemente seleccione un volumen existente y presione el botón apropiado Editar.

4.1.2.3.- Eliminando un Volumen con el Zimlet de Administración
Para eliminar un volumen del Zimlet de Administración, seleccione un existente volumen y presione el botón correspondiente Eliminar . Recuerda eso solo Los volúmenes vacíos se pueden eliminar.

4.1.3.- Gestión de volúmenes con HSM NG: desde la CLI
Importante

A partir de la versión 8.8.9, todos los comandos de creación y actualización de volúmenes se han actualizado, ya que ahora se requiere el argumento storeType.

El argumento storeType es obligatorio, siempre es el primero posición y acepta cualquier valor correspondiente a [Compatible con S3 Servicios] (# servicios compatibles con s3) enumerados anteriormente. Los argumentos que seguir en el comando ahora depende del storeType seleccionado.

4.1.3.1.- FileBlob (Local)
Se actualizó la sintaxis zxsuite para crear un nuevo volumen de zimbra FileBlob:

# Añadir volumen, ejecutar como usuario de zimbra
zxsuite hsm doCreateVolume FileBlob nombre secundario /ruta/a/store
# Eliminar volumen
zxsuite hsm doDeleteNombre de volumen
# actual conjunto
zxsuite hsm doUpdateVolume FileBlob nombre current_volume true
zxsuite hsm doCreateVolume FileBlob

Sintaxis:
   zxsuite hsm doCreateVolume FileBlob {volume_name} {primary|secondary|index} {volume_path} [attr1 value1 [attr2 value2...]]

LISTA DE PARÁMETROS

NOMBRE TIPO VALORES ESPERADOS POR DEFECTO
cadena_nombre_volumen (M)
volume_type (M) Opción múltiple primaria | secundaria | índice
volume_path (M) Ruta
volume_compressed (O) Boolean verdadero | falso falso
Compression_threshold_bytes (O) Long 4096

(M) == parámetro obligatorio, (O) == parámetro opcional

Ejemplo de uso:

zxsuite hsm doCreateVolume FileBlob volumeName secondary /path/to/store volume_compressed true compression_threshold_bytes 4096
zxsuite hsm doUpdateVolume FileBlob

Sintaxis:
    zxsuite hsm doUpdateVolume FileBlob {current_volume_name} [attr1 value1 [attr2 value2...]]

LISTA DE PARÁMETROS

NOMBRE TIPO VALORES ESPERADOS POR DEFECTO
Cadena current_volume_name (M)
volume_type (O) Cadena primaria | secundaria | índice
cadena_nombre_volumen (O)
volume_path (O) Ruta
current_volume (O) Boolean verdadero | falso falso
Cadena volume_compressed (O)
cadena de umbral de compresión (O)

(M) == parámetro obligatorio, (O) == parámetro opcional
4.1.3.2.- S3 (Amazon y cualquier solución compatible con S3 no compatible explícitamente)
# Añadir volumen, ejecutar como usuario de zimbra
zxsuite hsm doCreateVolume S3 name secondary bucket_name bucket access_key accessKey secret secretString region EU_WEST_1
# Eliminar volumen
zxsuite hsm doDeleteVolume name
# establecer volumen actual
zxsuite hsm doUpdateVolume S3 name current_volume true
zxsuite hsm doCreateVolume S3

Sintaxis:
    zxsuite hsm doCreateVolume S3 {Nombre de la store zimbra} {primario | secundario} [attr1 valor1 [attr2 valor2 ...]]

LISTA DE PARÁMETROS

NOMBRE TIPO VALORES ESPERADOS
volume_name (M) String Nombre de la store zimbra
volume_type (M) Opción múltiple primaria | secundaria
bucket_name (O) String Amazon AWS bucket
nombre de usuario del servicio de cadena access_key (O)
Contraseña de servicio de cadena secreta (O)
server_prefix (O) Cadena Prefijo al ID del servidor utilizado en todas las claves de objetos
bucket_configuration_id (O) String UUID para credenciales de servicio S3 ya existentes
                                                    (zxsuite config global get attribute s3BucketConfigurations)
region (O) Cadena Amazon AWS Region
url (O) String S3 API compatible url de servicio (ej: s3api.service.com)
prefijo (O) Prefijo de cadena agregado a las teclas de blobs
use_infrequent_access (O) Booleano verdadero | falso
Cadena infrequent_access_threshold (O)

(M) == parámetro obligatorio, (O) == parámetro opcional

Ejemplo de uso:

S3 bucket AWS:
    zxsuite hsm doCreateVolume S3 volumeName primario bucket_name bucket access_key accessKey secret secretKey prefix objectKeysPrefix region EU_WEST_1 user_infrequent_access TRUE infrequent_access_threshold 4096

Almacenamiento de objetos compatibles con S3:
    zxsuite hsm doCreateVolume S3 volumeName nombre_de_bote primario nombre_de_bote clave_acceso accessKey secret secretKey url http: // host / service

Usando la configuración de cubeta existente:
    zxsuite hsm doCreateVolume S3 volumeName primario bucket_configuration_id 316813fb-d3ef-4775-b5c8-f7d236fc629c
zxsuite hsm doUpdateVolume S3

Sintaxis:
    zxsuite hsm doUpdateVolume S3 {current_volume_name} [attr1 value1 [attr2 value2 ...]]

LISTA DE PARÁMETROS

NOMBRE TIPO VALORES ESPERADOS POR DEFECTO
Cadena current_volume_name (M)
cadena_nombre_volumen (O)
volume_type (O) Cadena primaria | secundaria
server_prefix (O) Cadena Prefijo al ID del servidor utilizado en todas las claves de objetos
bucket_configuration_id (O) String UUID para credenciales de servicio ya existentes
                                                    (zxsuite config global get attribute s3BucketConfigurations)
use_infrequent_access (O) Booleano verdadero | falso
Cadena infrequent_access_threshold (O)
current_volume (O) Boolean verdadero | falso falso

(M) == parámetro obligatorio, (O) == parámetro opcional
4.1.3.3.- Scality (almacenamiento de objetos compatible con S3)
# Añadir volumen, ejecutar como usuario de zimbra
zxsuite hsm doCreateVolume ScalityS3 nombre secundario bucket_name mybucket access_key accessKey1 secreto verySecretKey1 url http: // {IP_ADDRESS}: {PORT}
# Eliminar volumen
zxsuite hsm doDeleteNombre de volumen
# actual conjunto
zxsuite hsm doUpdateVolume ScalityS3 nombre current_volume true
zxsuite hsm doCreateVolume ScalityS3

Sintaxis:
    zxsuite hsm doCreateVolume ScalityS3 {nombre_volumen} {primario | secundario} [attr1 valor1 [attr2 valor2 ...]]

LISTA DE PARÁMETROS

NOMBRE TIPO VALORES ESPERADOS
cadena_nombre_volumen (M)
volume_type (M) Opción múltiple primaria | secundaria
bucket_name (O) Cadena Nombre del depósito
url (O) String S3 API compatible url de servicio (ej: s3api.service.com)
nombre de usuario del servicio de cadena access_key (O)
Contraseña de servicio de cadena secreta (O)
server_prefix (O) Cadena Prefijo al ID del servidor utilizado en todas las claves de objetos
bucket_configuration_id (O) String UUID para credenciales de servicio ya existentes
                                                    (zxsuite config global get attribute s3BucketConfigurations)
prefijo (O) Prefijo de cadena agregado a las teclas de blobs

(M) == parámetro obligatorio, (O) == parámetro opcional

Ejemplo de uso:

zxsuite hsm doCreateVolume ScalityS3 volumeName primario bucket_name url de bucket http: // host / service access_key accessKey secret secretKet
zxsuite hsm doCreateVolume ScalityS3 volumeName primary bucket_configuration_id uuid
zxsuite hsm doUpdateVolume ScalityS3

Sintaxis:
    zxsuite hsm doUpdateVolume ScalityS3 {current_volume_name} [attr1 value1 [attr2 value2 ...]]

LISTA DE PARÁMETROS

NOMBRE TIPO VALORES ESPERADOS POR DEFECTO
Cadena current_volume_name (M)
cadena_nombre_volumen (O)
volume_type (O) Cadena primaria | secundaria
server_prefix (O) Cadena Prefijo al ID del servidor utilizado en todas las claves de objetos
bucket_configuration_id (O) String UUID para credenciales de servicio S3 ya existentes
                                                    (zxsuite config global get attribute s3BucketConfigurations)
current_volume (O) Boolean verdadero | falso falso

(M) == parámetro obligatorio, (O) == parámetro opcional
4.1.3.4.- EMC (almacenamiento de objetos compatible con S3)
# Añadir volumen, ejecutar como usuario de zimbra
zxsuite hsm docreatevolume Nombre de EMC secundario nombre_de_cubeta clave_cube_cceso ACCESSKEY secreto SECRETO url https://url.of.storage
# Eliminar volumen
zxsuite hsm doDeleteNombre de volumen
# actual conjunto
zxsuite hsm doUpdateVolume EMC nombre current_volume true
zxsuite hsm doCreateVolume EMC

Sintaxis:
    zxsuite hsm doCreateVolume EMC {nombre_volumen} {primario | secundario} [attr1 valor1 [attr2 valor2 ...]]

LISTA DE PARÁMETROS

NOMBRE TIPO VALORES ESPERADOS
cadena_nombre_volumen (M)
volume_type (M) Opción múltiple primaria | secundaria
bucket_name (O) Cadena Nombre del depósito
url (O) String S3 API compatible url de servicio (ej: s3api.service.com)
nombre de usuario del servicio de cadena access_key (O)
Contraseña de servicio de cadena secreta (O)
server_prefix (O) Cadena Prefijo al ID del servidor utilizado en todas las claves de objetos
bucket_configuration_id (O) String UUID para credenciales de servicio ya existentes
                                                    (zxsuite config global get attribute s3BucketConfigurations)
prefijo (O) Prefijo de cadena agregado a las teclas de blobs

(M) == parámetro obligatorio, (O) == parámetro opcional

Ejemplo de uso:

zxsuite hsm doCreateVolume EMC volumeName nombre_de_bote primario url de bucket http: // host / service access_key accessKey secret secretKet
zxsuite hsm doCreateVolume EMC volumeName primary bucket_configuration_id uuid
zxsuite hsm doUpdateVolume EMC

Sintaxis:
    zxsuite hsm doUpdateVolume EMC {current_volume_name} [attr1 value1 [attr2 value2 ...]]

LISTA DE PARÁMETROS

NOMBRE TIPO VALORES ESPERADOS POR DEFECTO
Cadena current_volume_name (M)
cadena_nombre_volumen (O)
volume_type (O) Cadena primaria | secundaria
server_prefix (O) Cadena Prefijo al ID del servidor utilizado en todas las claves de objetos
bucket_configuration_id (O) String UUID para credenciales de servicio ya existentes
                                                    (zxsuite config global get attribute s3BucketConfigurations)
current_volume (O) Boolean verdadero | falso falso

(M) == parámetro obligatorio, (O) == parámetro opcional
4.1.3.5.- OpenIO
# agregar volumen, ejecutar como usuario de zimbra
zxsuite hsm doCreateVolume OpenIO name secondary http://{IP_ADDRESS} ZeXtras OPENIO
# Eliminar volumen
zxsuite hsm doDeleteVolume name
# actual conjunto
zxsuite hsm doUpdateVolume OpenIO name current_volume true
zxsuite hsm doCreateVolume OpenIO

Sintaxis:
    zxsuite hsm doCreateVolume OpenIO {nombre_volumen} {primario | secundario} {url} {cuenta} {espacio de nombres} [attr1 valor1 [attr2 valor2 ...]]

LISTA DE PARÁMETROS

NOMBRE TIPO VALORES ESPERADOS
cadena_nombre_volumen (M)
volume_type (M) Opción múltiple primaria | secundaria
Cadena url (M)
cuenta (M) Cadena
Cadena de espacio de nombres (M)
proxy_port (O) Entero
account_port (O) Entero

(M) == parámetro obligatorio, (O) == parámetro opcional

Ejemplo de uso:

zxsuite hsm doCreateVolume OpenIO volumeName primario http: // host / service
accountName namespaceString proxy \ _port 6006 cuenta \ _port 6009

Sintaxis:
zxsuite hsm doUpdateVolume OpenIO {current_volume_name} [attr1 value1
[valor de attr22 ...]]
LISTA DE PARÁMETROS
NOMBRE TIPO VALORES ESPERADOS POR DEFECTO
Cadena current_volume_name (M)
cadena_nombre_volumen (O)
volume_type (O) Cadena primaria | secundaria
url (O) String
cuenta (O) Cadena
Cadena de espacio de nombres (O)
proxy_port (O) Entero
account_port (O) Entero
current_volume (O) Boolean verdadero | falso falso

(M) == parámetro obligatorio, (O) == parámetro opcional
4.1.3.6.- Swift
# agregar volumen, ejecutar como usuario de zimbra
zxsuite hsm doCreateVolume Swift nombre secundario http: // {IP_ADDRESS}: 8080 / auth / v1.0 / usuario: nombre de usuario contraseña maxDeleteObjectsCount 100
# Eliminar volumen
zxsuite hsm doDeleteNombre de volumen
# actual conjunto
zxsuite hsm doUpdateVolume Nombre de swift current_volume true
zxsuite hsm doCreateVolume Swift

Sintaxis:
    zxsuite hsm doCreateVolume Swift {volumen_nombre} {primario | secundario} {url} {nombre de usuario} {contraseña} [attr1 valor1 [attr2 valor2 ...]]

LISTA DE PARÁMETROS

NOMBRE TIPO VALORES ESPERADOS POR DEFECTO
cadena_nombre_volumen (O)
volume_type (O) Cadena primaria | secundaria
url (O) String
cadena de nombre de usuario (O)
cadena de contraseña (O)
maxDeleteObjectsCount (O) Entero Número de objeto en una sola solicitud de eliminación masiva
                                                                    500

(M) == parámetro obligatorio, (O) == parámetro opcional

Ejemplo de uso:

zxsuite hsm doCreateVolume Swift volumeName primario http: // host / service accountName contraseña max_delete_objects_count 100
zxsuite hsm doUpdateVolume Swift

Sintaxis:
    zxsuite hsm doUpdateVolume Swift {current_volume_name} [attr1 value1 [attr2 value2 ...]]

LISTA DE PARÁMETROS

NOMBRE TIPO VALORES ESPERADOS POR DEFECTO
Cadena current_volume_name (M)
cadena_nombre_volumen (O)
volume_type (O) Cadena primaria | secundaria
url (O) String
cadena de nombre de usuario (O)
cadena de contraseña (O)
maxDeleteObjectsCount (O) Entero Número de objeto en una sola solicitud de eliminación masiva
                                                                    500
current_volume (O) Boolean verdadero | falso falso

(M) == parámetro obligatorio, (O) == parámetro opcional
4.1.3.7.- Cloudian (almacenamiento de objetos compatible con S3)
# agregar volumen, ejecutar como usuario de zimbra
zxsuite hsm doCreateVolume Nombre de Cloudian secundario nombre_de_cubeta_cube_acceso clave de acceso SECRETO secreto URL URL https://url.of.storage
# Eliminar volumen
zxsuite hsm doDeleteNombre de volumen
# actual conjunto
zxsuite hsm doUpdateVolume Nombre de Cloudian current_volume true
zxsuite hsm doCreateVolume Cloudian

Sintaxis:
    zxsuite hsm doCreateVolume Cloudian {nombre_volumen} {primario | secundario} [attr1 valor1 [attr2 valor2 ...]]

LISTA DE PARÁMETROS

NOMBRE TIPO VALORES ESPERADOS
cadena_nombre_volumen (M)
volume_type (M) Opción múltiple primaria | secundaria
bucket_name (O) Cadena Nombre del depósito
url (O) String S3 API compatible url de servicio (ej: s3api.service.com)
nombre de usuario del servicio de cadena access_key (O)
Contraseña de servicio de cadena secreta (O)
server_prefix (O) Cadena Prefijo al ID del servidor utilizado en todas las claves de objetos
bucket_configuration_id (O) String UUID para credenciales de servicio ya existentes
                                                    (zxsuite config global get attribute s3BucketConfigurations)
prefijo (O) Prefijo de cadena agregado a las teclas de blobs

(M) == parámetro obligatorio, (O) == parámetro opcional

Ejemplo de uso:

zxsuite hsm doCreateVolume Cloudian volumeName primario bucket_name bucket url http: // host / service access_key accessKey secret secretKet
zxsuite hsm doCreateVolume Cloudian volumeName primary bucket_configuration_id uuid
zxsuite hsm doUpdateVolume Cloudian

Sintaxis:
    zxsuite hsm doUpdateVolume Cloudian {current_volume_name} [attr1 value1 [attr2 value2 ...]]

LISTA DE PARÁMETROS

NOMBRE TIPO VALORES ESPERADOS POR DEFECTO
Cadena current_volume_name (M)
cadena_nombre_volumen (O)
volume_type (O) Cadena primaria | secundaria
server_prefix (O) Cadena Prefijo al ID del servidor utilizado en todas las claves de objetos
bucket_configuration_id (O) String UUID para credenciales de servicio ya existentes
                                                    (zxsuite config global get attribute s3BucketConfigurations)
current_volume (O) Boolean verdadero | falso falso

(M) == parámetro obligatorio, (O) == parámetro opcional
4.1.4.- Eliminación de volumen
zxsuite hsm doDeleteVolume

Sintaxis:
    zxsuite hsm doDeleteVolume {volume_name}

LISTA DE PARÁMETROS

NOMBRE TIPO
cadena_nombre_volumen (M)

(M) == parámetro obligatorio, (O) == parámetro opcional

Ejemplo de uso:

zxsuite hsm dodeletevolume hsm
Borra el volumen con el nombre hsm
4.1.5.- Mover todos los datos de un volumen a otro
Sintaxis:
    zxsuite hsm doVolumeToVolumeMove {source_volume_name} {destination_volume_name}

LISTA DE PARÁMETROS

NOMBRE TIPO
source_volume_name (M) Cadena
destination_volume_name (M) Cadena

(M) == parámetro obligatorio, (O) == parámetro opcional

Ejemplo de uso:

zxsuite hsm doVolumeToVolumeMove sourceVolume destVolume
Mueve todo el contenido de sourceVolume a destVolume
5.- Almacenamiento centralizado
La función de almacenamiento centralizado permite usar un depósito S3 para alojar datos proveniente de múltiples servidores al mismo tiempo compartiendo el mismo directorio estructura, a diferencia de los volúmenes «independientes» que son independientes y cuya estructura de directorios está estrictamente relacionada con el servidor y volumen en sí mismo.

Esto permite una mejor gestión de datos en grandes entornos de varios pisos. y mejora enormemente la velocidad de movimiento del buzón.

5.1.- Habilitar el almacenamiento centralizado
Cree el volumen centralizado en cualquiera de sus servidores utilizando el Comando zxsuite hsm doCreateVolume.
Todos los tipos de volumen, excepto FileBlob, son compatibles;
Asegúrese de agregar el indicador TRUE centralizado para establecer el volumen como un almacenamiento centralizado;
La sintaxis completa para el comando depende del tipo de almacenamiento;
Una vez que se ha creado el Volumen centralizado, use el Comando zxsuite doCreateVolume Centralized en todos los demás buzones servidores para copiar la configuración del Volumen centralizado del primer servidor y agréguelo a la lista de volúmenes.
1)

5.2.- Estructura de almacenamiento centralizada
Los datos de la estructura de almacenamiento se almacenan en un volumen centralizado claramente, como El directorio principal del volumen contiene un único directorio vacío para cada servidor conectado al volumen y un directorio para cada buzón almacenado en ella al mismo nivel.

En el siguiente ejemplo, los servidores 3aa2d376-1c59-4b5a-94f6-101602fa69c6 y 595a4409-6aa1-413f-9f45-3ef0f1e560f5 están conectados a la misma Volumen centralizado, donde se almacenan 3 buzones. Como puede ver, el el servidor efectivo donde se alojan los buzones es irrelevante para el almacenamiento.

_ _
| - 3aa2d376-1c59-4b5a-94f6-101602fa69c6 /
| - 595a4409-6aa1-413f-9f45-3ef0f1e560f5 /
| - ff46e039-28e3-4343-9d66-92adc60e60c9 /
\
 | - 357-104.msg
 | - 368-115.msg
 | - 369-116.msg
 | - 373-120.msg
 | - 374-121.msg
 | - 375-122.msg
 | - 376-123.msg
 | - 383-130.msg
| - 4c022592-f67d-439c-9ff9-e3d48a8c801b /
\
 | - 315-63.msg
 | - 339-87.msg
 | - 857-607.msg
 | - 858-608.msg
 | - 859-609.msg
 | - 861-611.msg
 | - 862-612.msg
 | - 863-613.msg
 | - 864-614.msg
 | - 865-615.msg
 | - 866-616.msg
 | - 867-617.msg
 | - 868-618.msg
| - dafd5569-4114-4268-9201-14f4a895a3d5 /
\
 | - 357-104.msg
 | - 368-115.msg
 | - 369-116.msg
 | - 373-120.msg
 | - 374-121.msg
 | - 375-122.msg
 | - 376-123.msg
 | - 383-130.msg
 | - 384-131.msg
6.- Gestión de políticas
6.1.- ¿Qué es una política?
Una política HSM es un conjunto de reglas que definen qué items se moverán desde la store primaria a la store secundaria cuando los doMoveBlobs La operación de HSM NG se activa, ya sea manualmente o mediante programación.

Una política puede consistir en una sola regla que sea válida para todos los tipos de items (Política simple ) o varias reglas válidas para uno o más tipos de items ( Compuesto política). Además, se puede definir una sub-regla adicional utilizando la [búsqueda de Zimbra sintaxis] (http://wiki.zimbra.com/wiki/Zimbra_Web_Client_Search_Tips).

Ejemplos de políticas
Aquí hay algunos ejemplos de políticas. Para ver cómo crear las políticas en el Módulo HSM NG, ver abajo.

Mover todos los items de más de 30 días *
Mover correos electrónicos anteriores a 15 días y items de todos los demás tipos anteriores de 30 días *
Mover items del calendario anteriores a 15 días, items del maletín anteriores a 20 días y todos los correos electrónicos en la carpeta «Archivo» *
6.2.- Definiendo una política
Las políticas se pueden definir tanto desde la pestaña HSM NG de la Administración Zimlet y de la CLI. Puede especificar una búsqueda de Zimbra en ambos casos.

De la Administración Zimlet
Para definir una política desde la Administración Zimlet:

Inicie sesión en la consola de administración de Zimbra.
Haga clic en HSM NG en el Zimlet de administración.
Haga clic en el botón Agregar en la sección Política de administración de almacenamiento.
Seleccione los Tipos de items de la lista Elementos para mover:.
Ingrese la edad del elemento desde el cuadro Mover items anteriores a:.
–OPCIONAL: Agregue una búsqueda de Zimbra en el cuadro Opciones adicionales .

Puede agregar varias líneas para reducir su política. Cada
línea * se evaluará y ejecutará después de que la línea anterior haya sido aplicado.
De la CLI
Hay dos comandos de administración de políticas disponibles en la CLI:

setHsmPolicy
setHsmPolicy
zxsuite hsm setHsmPolicy {policy}

Este comando restablece la política actual y crea una nueva como especificado por el parámetro política .

El parámetro policy debe especificarse en la siguiente sintaxis

itemType1 [, itemType2, itemtype3, etc.]: consulta

zxsuite hsm + setHsmPolicy {política}

Este comando agrega la consulta especificada por el parámetro policy al Política HSM actual.

El parámetro policy debe especificarse en la siguiente sintaxis

itemType1 [, itemType2, itemtype3, etc.]: consulta

7.- Volúmenes en servicios compatibles con Amazon S3
7.1.- HSM NG y S3 buckets
Los volúmenes primario y secundario creados con HSM NG se pueden alojar en S3 buckets, moviendo efectivamente la mayor parte de sus datos para asegurar y almacenamiento en la nube duradero.

Servicios compatibles con S3
Si bien cualquier servicio de almacenamiento compatible con la API de Amazon S3 debería funcionar fuera de la caja con HSM NG, enumerados aquí son los únicos oficialmente plataformas soportadas:

FileBlob (volumen local estándar)
Amazon S3
EMC
<! – ->

OpenIO
Swift
Scality S3
Cloudian
S3 personalizado (cualquier solución no compatible con S3)
7.1.1.- Volúmenes primarios y el directorio «Entrante»
Para crear un Almacén primario remoto en un servidor de buzones de correo local El directorio «entrante» debe existir en ese servidor. El directorio predeterminado es / opt / zimbra / entrante, pero puede verificar o modificar el valor actual usando estos comandos:

El servidor de configuración zxsuite obtiene el atributo $ (zmhostname) entrantePath
El servidor de configuración de zxsuite establece el atributo $ (zmhostname) entrante Valor de ruta / ruta / a / dir
7.1.2.- Caché local
Almacenar un volumen en soluciones de almacenamiento remoto de terceros requiere un directorio local que se utilizará para el almacenamiento en caché de items, que debe ser legible y escribible por el usuario zimbra .

El directorio local debe crearse manualmente y su ruta debe ser ingresado en la sección HSM NG del Zimlet de Administración en el Consola de administración de Zimbra.

Si el directorio de caché local no está configurado, no podrá crear ningún volumen secundario en un dispositivo o servicio compatible con S3.

Advertencia

Si no se configura correctamente el directorio de caché, los items sea irrecuperable, lo que significa que los usuarios recibirán un error No such BLOB al intentar acceder a cualquier elemento almacenado en un volumen S3.

7.2.- Volúmenes locales
Los volúmenes locales (es decir, tipo FileBlob) se pueden alojar en cualquier punto de montaje en el sistema independientemente del destino del punto de montaje y se definen por las siguientes propiedades:

Nombre: Un identificador único para el volumen.
Ruta: La ruta donde se guardarán los datos. El zimbra el usuario debe tener permisos r / w en esta ruta.
Compresión: Activar o desactivar la compresión de archivos para el volumen.
Umbral de compresión: el tamaño mínimo de archivo que activará La compresión.
Importante

Los archivos por debajo de este tamaño nunca se comprimirán, incluso si la compresión está habilitado.

7.3.- Volúmenes actuales
Un Volumen actual es un volumen donde los datos se escribirán a la llegada (Corriente primaria) o Aplicación de política HSM (Corriente secundaria). Volúmenes no establecido como Actual no se escribirá excepto por un manual específico operaciones como el movimiento de volumen a volumen.

7.4.- Configuración del bucket
HSM NG no necesita ninguna configuración o configuración dedicada en el S3 lateral, por lo que configurar un bucket para sus volúmenes es fácil. Aunque creando no se requiere un depósito de usuario dedicado y una política de acceso, son muy recomendable porque hacen que sea mucho más fácil de administrar.

Todo lo que necesita para comenzar a almacenar sus volúmenes secundarios en S3 es:

Un bucket S3. Necesita saber el nombre y la región del bucket para poder para usarlo
La clave de acceso y el secreto de un usuario.
Una política que otorga al usuario todos los derechos sobre su depósito.
7.4.1.- Gestión de buckets
Una interfaz de usuario centralizada de gestión de cubetas está disponible en Zimbra Consola de administración. Esto facilita guardar la información del bucket para reutilizado al crear un nuevo volumen en un almacenamiento compatible con S3 en lugar de ingresando la información cada vez.

Para acceder a la IU de gestión de buckets:

Acceda a la consola de administración de Zimbra
Seleccione la entrada «Configurar» en el menú de la izquierda
Seleccione la entrada «Configuración global»
Seleccione la entrada S3 Buckets
Cualquier depósito agregado al sistema estará disponible al crear un nuevo volumen del siguiente tipo: Amazon S3, Cloudian, EMC, Scality S3, S3 personalizado.

7.4.2.- Trayectorias de bucket y nombres
Los archivos se almacenan en un depósito de acuerdo con una ruta bien definida, que puede personalizar a voluntad para facilitar el contenido de su bucket Comprender incluso en entornos de servidores múltiples con múltiples secundarios volúmenes:

/ Nombre del depósito / Ruta de destino / [Prefijo de volumen-] serverID /

El Nombre del depósito y laRuta de destinono están vinculados al volumen en sí mismo, y puede haber tantos volúmenes bajo el mismo ruta de destino como desee.
El Prefijo de volumen, por otro lado, es específico para cada volumen y es una forma rápida de diferenciar y reconocer diferentes volúmenes dentro del bucket.
7.5.- Crear volúmenes con HSM NG
Para crear un nuevo volumen con HSM NG de la Administración de Zimbra Consola:

Ingrese a la Sección HSM del Zimlet de Administración NG en Zimbra Consola de Administracion
Haga clic en Agregar debajo de Volúmenes primarios o Secundario Lista de volúmenes
Seleccione el Tipo de volumen entre las opciones de almacenamiento disponibles
Ingrese la información de volumen requerida
Importante

Cada tipo de volumen requerirá que se configure información diferente, consulte los recursos en línea de su proveedor de almacenamiento para obtener esos detalles.

7.6.- Edición de volúmenes con HSM NG
Para editar un volumen con HSM NG desde la consola de administración de Zimbra:

Ingrese a la Sección HSM del Zimlet de Administración NG en Zimbra Consola de Administracion
Seleccione un volumen
Haga clic en Editar
Cuando termine, haga clic en Guardar
7.7.- Eliminar volúmenes con HSM NG
Para eliminar un volumen con HSM NG de la consola de administración de Zimbra:

Ingrese a la Sección HSM del Zimlet de Administración NG en Zimbra Consola de Administracion
Seleccione un volumen
Haga clic en Eliminar
Nota

Solo se pueden eliminar volúmenes vacíos.

7.8.- Consejos de Amazon S3
7.8.1.- Bucket
Almacenar sus volúmenes secundarios de Zimbra en Amazon S3 no tiene ningún requisitos específicos del depósito, pero le sugerimos que cree un sitio dedicado bucket y deshabilite el alojamiento web estático para una administración más fácil.

7.8.2.- Usuario
Para obtener una clave de acceso y el secreto relacionado, un `Programmatic Se necesita acceso al usuario. Sugerimos que cree un usuario dedicado en El servicio IAM de Amazon para una administración más fácil.

7.8.3.- Gestión de Derechos
En el IAM de Amazon, puede establecer políticas de acceso para sus usuarios. Es obligatorio que el usuario de su clave de acceso y secreto tenga un conjunto de derechos apropiados tanto en el bucket como en su contenido. por gestión más fácil, recomendamos otorgar derechos completos como se muestra en el siguiente ejemplo:

{
    `Versión`:` [ÚLTIMA VERSIÓN DE API] `,
    `Declaración`: [
        {
            `Sid`:` [GENERADO AUTOMÁTICAMENTE] `,
            `Efecto`:` Permitir`,
            `Acción`: [
                `s3: *`
            ],
            `Recurso`: [
                `[ARN DE CUBO] / *`,
                `[ARN DE CUBO]`
            ]
        }
    ]
}
Advertencia

Esta no es una política de configuración válida. No lo copie y pegue en la configuración de su usuario, ya que no se validará.

Si solo desea otorgar permisos mínimos, cambie la Acción sección para:

"Acción": [
                `s3: PutObject`,
                `s3: GetObject`,
                `s3: DeleteObject`,
                `s3: AbortMultipartUpload`
              ],
El ARN del depósito se expresa de acuerdo con la denominación estándar de Amazon formato: arn: partición: servicio: región: id-cuenta: recurso. Para más información sobre este tema, consulte la documentación de Amazon.

7.8.4.- Rutas de bucket y nombres
Los archivos se almacenan en un depósito de acuerdo con una ruta bien definida, que puede personalizar a voluntad para facilitar el contenido de su bucket comprender (incluso en entornos de servidores múltiples con múltiples secundarios volúmenes):

/Nombre del depósito/Ruta de destino/ID de servidor/

El Nombre del depósito y laRuta de destino no están vinculados al volumen en sí mismo, y puede haber tantos volúmenes bajo la misma ruta de destino como desées.

El Prefijo de volumen, por otro lado, es específico para cada volumen y es una forma rápida de diferenciar y reconocer diferentes volúmenes dentro de el bucket.

7.8.5.- Clase de almacenamiento de acceso poco frecuente
HSM NG es compatible con el Amazon S3 Standard - Acceso poco frecuente clase de almacenamiento y establecerá cualquier archivo más grande que el `Acceso poco frecuente Valor de umbral para esta clase de almacenamiento siempre que la opción haya sido habilitado en el volumen.

Para obtener más información sobre el acceso poco frecuente, consulte el [Amazon S3 oficial Documentación] (https://aws.amazon.com/s3/storage-classes).

Clase de almacenamiento inteligente por niveles
HSM NG es compatible con el almacenamiento Amazon S3 - Intelligent Tiering clase y establecerá el indicador de nivelación inteligente apropiado en todos archivos, siempre que la opción se haya habilitado en el volumen.

Para obtener más información acerca de los niveles inteligentes, consulte el [Amazon S3 oficial Documentación] (https://aws.amazon.com/about-aws/whats-new/2018/11/s3-intelligent-tiering/).

Desduplicación del artículo
¿Qué es la desduplicación de items?
La deduplicación de items es una técnica que le permite ahorrar espacio en disco al almacenar una sola copia de un elemento y hacer referencia a él varias veces en lugar de almacenar múltiples copias del mismo elemento y hacer referencia a cada copiar solo una vez.

Esto puede parecer una mejora menor. Sin embargo, en uso práctico, hace una diferencia significativa

Deduplicación de items en Zimbra
Zimbra realiza la deduplicación del artículo en el momento de almacenar un nuevo elemento en el volumen primario actual.

Cuando se crea un nuevo elemento, su ‘ID de mensaje’ se compara con una lista de items en caché. Si hay una coincidencia, un enlace duro al caché el BLOB del mensaje se crea en lugar de un BLOB completamente nuevo para el mensaje.

El caché de deduplicación se administra en Zimbra a través de la siguiente configuración atributos:

zimbraPrefDedupeMessagesSentToSelf

Se usa para establecer el comportamiento de deduplicación para los mensajes enviados a uno mismo.

<attr id = "144" name = "zimbraPrefDedupeMessagesSentToSelf" type = "enum" value = "dedupeNone, secondCopyifOnToOrCC, dedupeAll" cardinality = "single"
optionalIn = "account, cos" flags = "accountInherited, domainAdminModifiable">
  <defaultCOSValue> dedupeNone </defaultCOSValue>
  <desc> dedupeNone | secondCopyIfOnToOrCC | moveSentMessageToInbox | dedupeAll </desc>
</attr>
zimbraMessageIdDedupeCacheSize

Número de ID de mensajes en caché.

<attr id = "334" name = "zimbraMessageIdDedupeCacheSize" type = "integer" cardinality = "single" optionalIn = "globalConfig" min = "0">
  <globalConfigValue> 3000 </globalConfigValue>
  <desc>
    Número de valores de encabezado de Id de mensaje para mantener en la caché de deduplicación LMTP.
    Intentos posteriores de entregar un mensaje con un Id. De mensaje coincidente
    al mismo buzón será ignorado. Un valor de 0 desactiva la deducción.
  </desc>
</attr>
zimbraPrefMessageIdDedupingEnabled

Administre la deduplicación a nivel de cuenta o COS.

<attr id = "1198" name = "zimbraPrefMessageIdDedupingEnabled" type = "boolean" cardinality = "single" optionalIn = "account, cos" flags = "accountInherited"
 desde = "8.0.0">
  <defaultCOSValue> TRUE </defaultCOSValue>
  <desc>
    Cambio de nivel de cuenta que permite la deducción de mensajes. Vea zimbraMessageIdDedupeCacheSize para más detalles.
  </desc>
</attr>
zimbraMessageIdDedupeCacheTimeout

Tiempo de espera para cada entrada en el caché de deduplicación.

<attr id = "1340" name = "zimbraMessageIdDedupeCacheTimeout" type = "Duration" cardinality = "single" optionalIn = "globalConfig" since = "7.1.4">
  <globalConfigValue> 0 </globalConfigValue>
  <desc>
    Tiempo de espera para una entrada de Id. De mensaje en el caché de deduplicación LMTP. Un valor de 0 indica que no hay tiempo de espera.
    El límite de zimbraMessageIdDedupeCacheSize se ignora cuando se establece en un valor distinto de cero.
  </desc>
</attr>
(las versiones anteriores de Zimbra pueden usar diferentes atributos o carecer de algunas ellos)

Desduplicación del artículo y HSM NG
El HSM NG presenta una operación doDeduplicate que analiza un objetivo volumen para encontrar y deduplicar cualquier elemento duplicado.

Al hacerlo, ahorrará aún más espacio en el disco, como mientras que Zimbra es automático la deduplicación está vinculada a un caché limitado, la deduplicación de HSM NG también encuentre y cuide múltiples copias del mismo correo electrónico independientemente de cualquier caché o tiempo.

Ejecutar la operación doDeduplicate también es muy recomendable después de un migración o una gran importación de datos para optimizar su almacenamiento uso.

Ejecución de una deduplicación de volumen
A través de la Administración Zimlet
Para ejecutar una deduplicación de volumen a través del Zimlet de Administración, simplemente haga clic en la pestaña HSM NG , seleccione el volumen que desea deduplicar y presione el botón Deduplicar .

A través de la CLI
Para ejecutar una deduplicación de volumen a través de la CLI, use doDeduplicate mando:

zimbra @ mailserver: ~ $ zxsuite hsm doDeduplicate

El comando doDeduplicate requiere más parámetros

Sintaxis:
   zxsuite hsm doDeduplicate {volume_name} [attr1 value1 [attr2 value2 ...

LISTA DE PARÁMETROS

NOMBRE TIPO VALORES ESPERADOS POR DEFECTO
volume_name (M) Cadena [, ..]
dry_run (O) Boolean verdadero | falso falso

(M) == parámetro obligatorio, (O) == parámetro opcional

Ejemplo de uso:

zxsuite hsm dodeduplicate secondvolume
Inicia una deduplicación en el volumen segundo volumen
Para enumerar todos los volúmenes disponibles, puede usar el zxsuite hsm comando getAllVolumes .

Estadísticas doDeduplicate
La operación doDeduplicate es un objetivo válido para elmonitor comando, lo que significa que puede ver las estadísticas del comando mientras está ejecutando el comando zxsuite hsm monitor [operationID].

Salida de muestra *
Pase actual (Prefijo implícito): 63/64 Buzones verificados: 148/148 Blobs deduplicados / duplicados: 64868/137089 Blobs ya desduplicados: 71178 Blobs omitidos: 0 Digests inválidos: 0 Espacio total guardado: 21.88 GB

Current Pass (Digest Prefix) *: el comando doDeduplicate analizar los BLOBS en grupos basados ​​en el primer personaje de su resumen (nombre).
Buzones marcados *: el número de buzones analizados para Pase actual.
Blobs deduplicados / duplicados *: Número de BLOBS deduplicados por operación actual / Número total de items duplicados en el volumen.
Blobs ya deduplicados *: número de blobs deduplicados en el volumen (blobs duplicados que han sido deduplicados por un previo correr).
Blobs omitidos *: BLOBs que no han sido analizados, generalmente porque de un error de lectura o falta de archivo.
Digests inválidos *: BLOBs con un mal digest (nombre diferente del resumen real del archivo).
Espacio total guardado *: Cantidad de espacio en disco liberado por doDeduplicate operación.
Mirando la salida de muestra anterior, podemos ver que:

La operación está ejecutando el penúltimo pase en el último buzón.
Se han encontrado 137089 BLOB duplicados, 71178 de los cuales ya ha sido deduplicado previamente.
La operación actual deduplicado 64868 BLOBs, para un disco total Ahorro de espacio de 21.88GB.
Operaciones de volumen avanzadas
HSM NG: más de lo que parece
A primera vista, HSM NG parece estar estrictamente dedicado a HSM. Sin embargo, También presenta algunas herramientas muy útiles relacionadas con el volumen que no son directamente relacionado con HSM.

Debido a los riesgos implícitos en la gestión del volumen, estas herramientas son solo disponible a través de la CLI.

Operaciones de volumen de un vistazo
Las siguientes operaciones de volumen están disponibles:

doCheckBlobs: realiza comprobaciones de coherencia BLOB en uno o más volúmenes.

doDeduplicate: Iniciar la deduplicación de items en un volumen.

doVolumeToVolumeMove: mueve todos los items de un volumen a otro.

getVolumeStats: muestra información sobre el tamaño y el número de un volumen de ellos contenidos items / blobs.

Análisis de operación de volumen
doCheckBlobs
Uso

zimbra @ mail: ~ $ zxsuite hsm doCheckBlobs

El comando doCheckBlobs requiere más parámetros

Sintaxis:
   zxsuite hsm doCheckBlobs {inicio} [attr1 valor1 [attr2 valor2 ...

LISTA DE PARÁMETROS

NOMBRE TIPO VALORES ESPERADOS POR DEFECTO
acción (M) Inicio de cadena
volume_ids (O) Entero [, ..] 1,3
Mail_ids (O) Entero [, ..] 2,9,27
missing_blobs_crosscheck (O) Boolean verdadero | falso verdadero
trazado (O) booleano verdadero | falso falso

(M) == parámetro obligatorio, (O) == parámetro opcional

Ejemplo de uso:

Ejemplos de uso:
Inicio de zxsuite hsm doCheckBlobs: realice una comprobación de coherencia BLOB en todos los volúmenes de mensajes.

zxsuite hsm doCheckBlobs start volume_ids 1,3: Realice una comprobación de coherencia BLOB en los volúmenes 1 y 3.

zxsuite hsm doCheckBlobs start bucle_ids 2,9,27: Realice una comprobación de coherencia BLOB en los buzones 2,9 y 27.

zxsuite hsm doCheckBlobs start missing_blobs_crosscheck false: realice una comprobación de coherencia BLOB sin comprobar otros volúmenes.

zxsuite hsm doCheckBlobs comienza a rastrearse como verdadero: realice una comprobación de coherencia BLOB, registrando incluso los items marcados correctos.
Descripción y consejos

La operación doCheckBlobs se puede utilizar para ejecutar comprobaciones de coherencia BLOB en volúmenes y buzones. Esto puede ser útil al experimentar problemas relacionados con items rotos o no visibles, que a menudo son causados ​​porque Zimbra no puede encontrar o acceder al archivo BLOB relacionado con un elemento o Hay un problema con el contenido BLOB en sí.

Específicamente, se realizan las siguientes verificaciones:

Coherencia DB-a-BLOB: para cada entrada de artículo en la base de datos de Zimbra, marque si existe el archivo BLOB apropiado.
Coherencia BLOB-to-DB: para cada archivo BLOB en un volumen / buzón, marque si existen los datos de base de datos apropiados.
Coherencia de nombre de archivo: comprueba la coherencia del nombre de archivo de cada BLOB con su contenido (ya que los BLOB se nombran después del hash SHA de su archivo).
Coherencia de tamaño: para cada archivo BLOB en un volumen / buzón, verifica si el tamaño del archivo BLOB es coherente con el tamaño esperado (almacenado en el DB).
Importante

El antiguo comando zmblobchk está en desuso y se reemplaza por zxsuite hsm doCheckBlobs en todas las infraestructuras que utilizan el módulo HSM NG.

doDeduplicate
Uso

zimbra @ mail: ~ $ zxsuite hsm doDeduplicate

El comando doDeduplicate requiere más parámetros

Sintaxis:
   zxsuite hsm doDeduplicate {volume_name} [attr1 value1 [attr2 value2 ...

LISTA DE PARÁMETROS

NOMBRE TIPO VALORES ESPERADOS POR DEFECTO
volume_name (M) Cadena [, ..]
dry_run (O) Boolean verdadero | falso falso

(M) == parámetro obligatorio, (O) == parámetro opcional

Ejemplo de uso:

zxsuite hsm dodeduplicate secondvolume
Inicia una deduplicación en el volumen segundo volumen
doVolumeToVolumeMove
Uso

zimbra @ mail: ~ $ zxsuite hsm doVolumeToVolumeMove

El comando doVolumeToVolumeMove requiere más parámetros

Sintaxis:
   zxsuite hsm doVolumeToVolumeMove {source_volume_name} {destination_volume_name}

LISTA DE PARÁMETROS

NOMBRE TIPO
source_volume_name (M) Cadena
destination_volume_name (M) Cadena

(M) == parámetro obligatorio, (O) == parámetro opcional

Ejemplo de uso:

zxsuite hsm doVolumeToVolumeMove sourceVolume destVolume
Mueve todo el sourceVolume a destVolume
Descripción y consejos

Este comando puede resultar muy útil en todas las situaciones en las que necesita dejar de usar un volumen, como:

Desmantelamiento de hardware antiguo: si desea deshacerse de un disco antiguo en un servidor físico, cree nuevos volúmenes en otros / nuevos discos y mueve tus datos allí.
Arreglando pequeños errores : si accidentalmente crea un nuevo volumen en En el lugar equivocado, mueva los datos a otro volumen.
Centralizar volúmenes: centralice y mueva los volúmenes como desee, para ejemplo, si rediseñó su infraestructura de almacenamiento o está ordenar tus volúmenes de Zimbra.
getVolumeStats
Uso

zimbra @ mail: ~ $ zxsuite hsm getVolumeStats

El comando getVolumeStats requiere más parámetros

Sintaxis:
   zxsuite hsm getVolumeStats {volume_id} [attr1 value1 [attr2 value2 ...

LISTA DE PARÁMETROS

NOMBRE TIPO VALORES ESPERADOS POR DEFECTO
volume_id (M) Entero
show_volume_size (O) Boolean verdadero | falso falso
show_blob_num (O) Boolean verdadero | falso falso

(M) == parámetro obligatorio, (O) == parámetro opcional

Ejemplo de uso:
TEN CUIDADO Las opciones show_volume_size y show_blob_num son intensivas en E / S y, por lo tanto, están deshabilitadas de forma predeterminada

zxsuite hsm getVolumeStats 2
Muestra estadísticas para el volumen con ID igual a 2
Descripción y consejos

Este comando proporciona la siguiente información sobre un volumen:

nombre	descripción
id	El ID del volumen
name	El nombre del volumen
path	La ruta del volumen
compressed	Compresión habilitada / deshabilitada
threshold	Umbral de compresión (en bytes)
lastMoveOutcome	Estado de salida de la última operación de doMoveBlobs
lastMoveTimestamp	Fecha y hora de finalización de la última operación de doMoveBlobs
lastMoveDuration	Duración de la última operación de doMoveBlobs
lastItemMovedCount	Número de items movidos al volumen secundario actual durante la última operación de doMoveBlobs
bytesSaved	Cantidad total de espacio en disco liberado gracias a la deduplicación y compresión
bytesSavedLast	Cantidad de espacio en disco liberada gracias a la deduplicación y compresión durante la última operación de doMoveBlobs
Las opciones show_volume_size yshow_blob_num agregarán el siguientes datos a la salida:

opción	nombre	descripción
show_volume_size	totSize	Espacio total en disco utilizado por el volumen
show_blob_num	blobNumber	Número de archivos BLOB en el volumen
Mover buzones entre almacenes de correo
El comando doMailboxMove le permite mover un solo buzón o todos cuentas de un dominio determinado, de un servidor de buzón a otro dentro de la misma infraestructura de Zimbra.

Advertencia

Si el módulo HSM NG está instalado y habilitado, este comando reemplaza los viejos comandos zmmboxmove yzmmailboxmove. Usando cualquiera de los los comandos heredados devolverán un error y no moverán ningún dato.

Sintaxis

Sintaxis:
   zxsuite hsm doMailboxMove {un nombre de cuenta: john@example.com o un nombre de dominio: ejemplo.com} {destinationHost} [attr1 value1 [attr2 value2 ...]]

LISTA DE PARÁMETROS

NOMBRE TIPO VALORES ESPERADOS POR DEFECTO
Cadena destinationHost (M)
cuentas (O) Cadena [, ..] john @ example.com, smith @ example.com [, ...]
dominios (O) Cadena [, ..] example.com, test.com [, ...]
Cadena input_file (O)
etapas (O) Cadena [, ..] blobs | copia de seguridad | datos | cuenta datos = blobs + copia de seguridad [, ...] blobs, copia de seguridad, cuenta
comprimir (O) Boolean verdadero | falso verdadero
checkDigest (O) Boolean si es falso omita el cálculo del resumen y verifique verdadero
sobrescribir (O) booleano verdadero | falso falso
hilos (O) Entero 1
hsm (O) Boolean verdadero | falso verdadero
notificaciones (O) Dirección de correo electrónico
ignore_partial (O) Boolean verdadero | falso falso
drop_network_backup (O) Booleano verdadero | falso falso
read_error_threshold (O) Entero

(M) == parámetro obligatorio, (O) == parámetro opcional

Ejemplo de uso:

zxsuite HSM NG domailboxmove john@example.com mail2.example.com
Mueva el buzón de la cuenta john@example.com al host mail2.example.com
Detalles de doMailboxMove

Al mover un dominio, cada cuenta del servidor actual es enumerados y movidos secuencialmente.
El buzón está configurado en modo de mantenimiento cuando se mueve y será colocado en su estado original después de mover todos los correos electrónicos (después de Etapa LDAP).
La operación se detiene si se encuentran 5% o más errores de escritura en items que se mueven Tenga en cuenta que el buzón actual puede permanecer en modo de mantenimiento
Los movimientos de buzón único no se iniciarán si el servidor de destino lo hace no tiene suficiente espacio disponible o el usuario simplemente pertenece al host de destino
Todos los datos se mueven a un nivel bajo y no se cambiarán, excepto algunas cosas pequeñas como la identificación del buzón.
La operación se compone de 3 etapas: blobs | backup | cuenta. Para cada buzón:
blobs: todos los blobs se copian del servidor de origen al servidor de destino
copia de seguridad: todas las entradas de copia de seguridad se copian del servidor de origen a El servidor de destino.
cuenta: todas las entradas de la base de datos y LDAP se actualizan, efectivamente moviendo el buzón.
Todas las etapas se ejecutan secuencialmente. Si una sola etapa es especificado, el buzón está estacionado en modo de mantenimiento durante todo el Operación completa. En caso de éxito, el buzón se colocará en su estado original.
Inicialmente, todos los items de blob se almacenarán en el servidor de destino volumen primario
Al finalizar la etapa reindexar, se presenta una nueva operación HSM al servidor de destino, si no se especifica lo contrario.
Se toman las opciones de compresión de todos los volúmenes.
La operación MailboxMove se puede ejecutar si y solo si no hay otros Las operaciones se ejecutan en el servidor de origen.
La opción HSM aplica las políticas HSM actuales. Se ejecuta después de cada El buzón se ha movido correctamente. En cualquier ejecución nueva, los items se moverán.
Indización de archivos adjuntos HSM NG
Cómo funciona la indexación
Se ha agregado un nuevo motor de indexación a HSM NG para indexar archivos adjuntos contenido.

El motor de indexación funciona junto con el motor predeterminado de Zimbra. los El proceso principal de indexación de Zimbra analiza el contenido de un elemento, dividiéndolo en varias partes basadas en las partes MIME del objeto. Próximo, Zimbra maneja la indexación de contenidos conocidos – texto sin formato – y pasa el flujo de datos a los manejadores HSM NG para todos los demás contenidos.

El motor de indexación incluye una caché de indexación que acelera proceso de indexación de cualquier contenido que ya haya sido analizado. Los flujos de datos de más de 10 Kb se almacenan en caché de forma predeterminada, y el caché contiene 10000 entradas, mientras que los flujos de datos más pequeños no se almacenan en caché ya que la memoria caché se beneficia solo se aplica a grandes flujos de datos.

Formatos indexados
Web
Extensión	Analizador	Tipo de contenido
asp	HtmlParser	application/x-asp
htm	HtmlParser	application/xhtml+xml
html	HtmlParser	aplicación / xhtml + xml, texto / html
shtml	HtmlParser	application/xhtml+xml
xhtml	HtmlParser	application/xhtml+xml
Documentos
Extensión	Analizador	Tipo de contenido
rtf	RTFParser	application/rtf
pdf	PDFParser	application/pdf
pub	OfficeParser	application/x-mspublisher
xls	OfficeParser	application/vnd.ms-excel
xlt	OfficeParser	application/vnd.ms-excel
xlw	OfficeParser	application/vnd.ms-excel
ppt	OfficeParser	application/vnd.ms-powerpoint
pps	OfficeParser	application/vnd.ms-powerpoint
mpp	OfficeParser	application/vnd.ms-project
doc	OfficeParser	application/msword
dot	OfficeParser	application/msword
msg	OfficeParser	application/vnd.ms-outlook
vsd	OfficeParser	application/vnd.visio
vst	OfficeParser	application/vnd.visio
vss	OfficeParser	application/vnd.visio
vsw	OfficeParser	application/vnd.visio
xlsm	OOXMLParser	application/vnd.ms-excel.sheet.macroenabled.12
pptm	OOXMLParser	application/vnd.ms-powerpoint.presentation.macroenabled.12
xltx	OOXMLParser	application/vnd.openxmlformats-officedocument.spreadsheetml.template
docx	OOXMLParser	application/vnd.openxmlformats-officedocument.wordprocessingml.document
potx	OOXMLParser	application/vnd.openxmlformats-officedocument.presentationml.template
xlsx	OOXMLParser	application/vnd.openxmlformats-officedocument.spreadsheetml.sheet
pptx	OOXMLParser	application/vnd.openxmlformats-officedocument.presentationml.presentation
xlam	OOXMLParser	application/vnd.ms-excel.addin.macroenabled.12
docm	OOXMLParser	application/vnd.ms-word.document.macroenabled.12
xltm	OOXMLParser	application/vnd.ms-excel.template.macroenabled.12
dotx	OOXMLParser	application/vnd.openxmlformats-officedocument.wordprocessingml.template
ppsm	OOXMLParser	application/vnd.ms-powerpoint.slideshow.macroenabled.12
ppam	OOXMLParser	application/vnd.ms-powerpoint.addin.macroenabled.12
dotm	OOXMLParser	application/vnd.ms-word.template.macroenabled.12
ppsx	OOXMLParser	application/vnd.openxmlformats-officedocument.presentationml.slideshow
odt	OpenDocumentParser	application/vnd.oasis.opendocument.text
ods	OpenDocumentParser	application/vnd.oasis.opendocument.spreadsheet
odp	OpenDocumentParser	application/vnd.oasis.opendocument.presentation
odg	OpenDocumentParser	application/vnd.oasis.opendocument.graphics
odc	OpenDocumentParser	application/vnd.oasis.opendocument.chart
odf	OpenDocumentParser	application/vnd.oasis.opendocument.formula
odi	OpenDocumentParser	application/vnd.oasis.opendocument.image
odm	OpenDocumentParser	application/vnd.oasis.opendocument.text-master
ott	OpenDocumentParser	application/vnd.oasis.opendocument.text-template
ots	OpenDocumentParser	application/vnd.oasis.opendocument.spreadsheet-template
otp	OpenDocumentParser	application/vnd.oasis.opendocument.presentation-template
otg	OpenDocumentParser	application/vnd.oasis.opendocument.graphics-template
otc	OpenDocumentParser	application/vnd.oasis.opendocument.chart-template
otf	OpenDocumentParser	application/vnd.oasis.opendocument.formula-template
oti	OpenDocumentParser	application/vnd.oasis.opendocument.image-template
oth	OpenDocumentParser	application/vnd.oasis.opendocument.text-web
sxw	OpenDocumentParser	application/vnd.sun.xml.writer
Paquetes y archivos
Extensión	Analizador	Tipo de contenido
z	CompressorParser	application/x-compress
bz	CompressorParser	application/x-bzip
boz	CompressorParser	application/x-bzip2
bz2	CompressorParser	application/x-bzip2
gz	CompressorParser	application/gzip
gz	CompressorParser	application/x-gzip
gzip	CompressorParser	application/x-gzip
xz	CompressorParser	application/x-xz
tar	PackageParser	application/x-tar
jar	PackageParser	application/java-archive
7z	PackageParser	application/x-7z-compressed
cpio	PackageParser	application/x-cpio
zip	PackageParser	application/zip
rar	RarParser	application/x-rar-compressed
txt	TXTParser	text/plain
Controles de analizador
Los analizadores se pueden activar o desactivar cambiando el valor relacionado a verdadero o false a través del comando CLIzxsuite config.

Atributo	Analizadores
pdfParsingEnabled	PDFParser
odfParsingEnabled	OpenDocumentParser
archivesParsingEnabled	CompressorParser, PackageParser, RarParser
microsoftParsingEnabled	OfficeParser, OOXMLParser, OldExcelParser
rtfParsingEnabled	RTFParser
por ejemplo, para deshabilitar la ejecución del análisis de PDF:

zxsuite config server set server.domain.com atributo pdfParsingEnabled valor false
Por defecto, todos los analizadores están activos.
