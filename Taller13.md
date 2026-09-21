Administración Multitenant con Admin NG
1.- Crear Clases de Servicios con diferentes cuotas y funciones
basico, 1GB de quota, sin Mapi, sin connect
estandar, 5 GB de quota, sin Mapi, sin connect
profesional, 10 GB de quota, con Mapi, sin connect
premium, 30GB de quota, con Mapi, con connect
2.- Crear un dominio adicional y usuario para prueba de la administración delegada
su - zimbra
zmprov cd aulautil.ml
basico=$(zmprov gc basico|grep -i zimbraId:|cut -d' ' -f2)
zmprov ca admin@aulautil.ml Lolcito.123 displayName "Tuxito Linuxero" zimbraCOSId $basico
Editar las clases de servicio del dominio

Ir a Panel de Administración
    Network module NG
        Admin
            Dominio
                aulautil.ml
                    Límite de cuenta Global:  unlimited
                    Cuota del dominio:           ulimited
                    Límites de la COS
                        basico           20
                        estandar      10
                        premium      10
3.- ¿Qué es el aprovisionamiento delegado de administrador?
El aprovisionamiento de administrador delegado es el conjunto de operaciones que le permiten conceder, editar y revocar derechos de administrador de dominio a un usuario administrador con nivel delegado.

Todas las operaciones de aprovisionamiento de administrador delegado se pueden realizar:

Desde la pestaña Admin NG del Zimlet de Administración
Desde la Consola, ejecutando el comando zxsuite como usuario zimbra
4.- Conceder derechos de administrador delegados a un usuario
4.1.- Desde el Panel de Administración
En la sección Administradores delegados de la pestaña Admin NG en el Administración Zimlet, haga clic en el botón Agregar.

Se le solicitará la siguiente información:

Cuenta: la dirección de correo electrónico a la que desea otorgar Delegado Derechos de administrador.
Dominio: el dominio sobre el cual el administrador delegado tendrá el control.
Autenticación delegada: marque esta casilla para permitir que el administrador delegado use las funciones Ver correo en cualquier buzón en el dominio seleccionado.
Límite de concesión: la cuota de buzón máxima que este administrador delegado puede asignar a un usuario.
Funciones de edición: define si el administrador delegado puede editar el contenido de la pestaña Características para sus usuarios asignados.
Crear admin delegado para el dominio aulautil.ml

Cuenta:                              admin@aulautil.ml
Dominio:                            aulautil.ml
Delegated Auth:               Permite "Ver correo de los usuarios del dominio" (habilitar a criterio)
Editar funcionalidades:   NO check (permite editar funciones individuales de los usuarios)
Establecer Límites:          Ninguno (valor por defecto) ---> Seleccionar en clase, las cuotas las definen las clases de servicio
                                            Personalizado
                                                  Valor:           30GB --> valor máximo de quota que puede asignar a un usuario
Probar la administración delegada accediendo al panel de administrado como usuario admin@aulautil.ml

Observamos que el usuario admin@aulautil.ml solo puede ver cuentas de su dominio

Crear una cuenta de aulautil.ml
    Administrar --> Cuentas
        Click en la rueda superior a la derecha
            Nuevo
                Elegir clase de servicio: 30GB de quota, con mapi, con connect
                    Nombre de cuenta:   gerente@aulautil.ml
                    Nombre:                      Gerentazo
                    Apellido:                      Maloso
                    Contraseña:                Lolcito.123
Ver Cuentas con sus cuotas

Configurar
    Dominios
        Doble click a aulautil.ml
            Espacio de almacenamiento del buzón de correo
Advertencia

Si la Cuota de dominio es inferior alLímite de concesión, Se ignorará el valor del «Límite de concesión».
El espacio en disco y los límites de cuota se pueden ingresar en Gigabytes (gb), Megabytes (mb) o Kilobytes (kb).

4.2.- Desde la Consola
Para otorgar derechos de administrador delegado a un usuario, use el Comando doAddDelegationSettings:

    Sintaxis:
       zxsuite admin doAddDelegationSettings {cuenta} {dominio} [attr1 value1 [attr2 value2 ...]]

    LISTA DE PARÁMETROS

    NOMBRE TIPO VALORES ESPERADOS POR DEFECTO
    cuenta (M) Cadena
    cadena de dominio (M)
    viewMail (O) Boolean verdadero | falso falso
    editFeatures (O) Boolean verdadero | falso falso
    adminQuota (O) Cadena -1

    (M) == parámetro obligatorio, (O) == parámetro opcional
4.2.1.- Ejemplo de uso:
Agrega a John como administrador delegado del dominio example.com, con el derecho de ver el correo del usuario en dicho dominio y sin derecho a otorgar cuotas a los usuarios.

zxsuite admin doAddDelegationSettings john@example.com example.com viewMail true adminQuota -1
Agrega a John como administrador delegado del dominio example.com, con el derecho de asignar cuotas ilimitadas a los usuarios.

zxsuite admin doAddDelegationSettings john@example.com example.com adminQuota 0
Agrega a John como administrador delegado del dominio example.com, con el derecho de asignar cuotas de hasta 10 gb a cada usuario.

zxsuite admin doAddDelegationSettings john@example.com example.com adminQuota 10gb
5.- Edición de los derechos de un administrador delegado existente
5.1.- Desde el panel de Administración
En la sección Administradores delegados de la pestaña Admin NG en el Administración Zimlet, seleccione una entrada en la lista y haga clic en Editar botón.

También puede hacer doble clic en una entrada de la lista para editarla.

5.2.- Desde la Consola
Para editar los derechos de un administrador delegado existente, use el Comando doEditDelegationSettings:

    Sintaxis:
       zxsuite admin doEditDelegationSettings {cuenta} {dominio} [attr1 value1 [attr2 value2 ...]]

    LISTA DE PARÁMETROS

    NOMBRE TIPO VALORES ESPERADOS
    cuenta (M) Cadena
    cadena de dominio (M)
    viewMail (O) Boolean verdadero | falso
    editFeatures (O) Boolean verdadero | falso
    Cadena adminQuota (O)

    (M) == parámetro obligatorio, (O) == parámetro opcional
5.2.1.- Ejemplo de uso:
Edita los derechos de delegación de John para el dominio example.com, con el derecho de ver el correo del usuario en dicho dominio y sin derecho a grandes cuotas para los usuarios.

zxsuite admin doEditDelegationSettings juan@ejemplo.com example.com viewMail true adminQuota -1
Edita los derechos de delegación de John para el dominio example.com, con el derecho de asignar cuotas ilimitadas a los usuarios.

zxsuite admin doEditDelegationSettings john@example.com example.com adminQuota 0
Edita los derechos de delegación de John para el dominio example.com, con el derecho de asignar cuotas de hasta 10 gb a cada usuario.

zxsuite admin doEditDelegationSettings john@example.com example.com adminQuota 10gb
6.- Revocar derechos de administrador delegados de un usuario
6.1.- Desde el Panel de Administración
En la sección Administradores delegados de la pestaña Admin NG en el Administración Zimlet, seleccione una entrada en la lista y haga clic en el Botón «Eliminar».

6.2.- Desde la Consola
Para revocar los derechos de administrador delegado de un usuario, use el Comando doRemoveDelegationSettings:

    zxsuite admin doRemoveDelegationSettings {cuenta} {dominio}

    LISTA DE PARÁMETROS

    NOMBRE TIPO
    cuenta (M) Cadena
    cadena de dominio (M)

    (M) == parámetro obligatorio, (O) == parámetro opcional
6.2.1.- Ejemplo de uso:
John ya no administra el dominio example.com

    zxsuite admin doRemoveDelegationSettings john@example.com example.com
7.- Gestión de cuotas
Admin NG permite al administrador global establecer dos tipos diferentes de límites de cuota: el Límite de concesión y laCuota de dominio.

Ni la «Cuota de dominio» ni el «Límite de concesión» son obligatorios, lo que significa que un administrador delegado puede conceder cualquier cuota a un usuario y que un dominio puede carecer de un límite de cuota máxima.

7.1.- El Límite de concesión
El «Límite de concesión» es una de las propiedades de un administrador delegado.

Especifica la cuota máxima de buzones que el administrador delegado puede conceder a un buzón y puede configurarse y modificarse en el Administrador Delegado ajustes

Hay tres opciones disponibles:

Ninguno: el administrador delegado no puede editar el atributo Cuota de un buzón.
Personalizado: el administrador delegado puede otorgar hasta el valor especificado. Esto anula cualquier configuración de cuota de dominio / COS.
Ilimitado: el administrador delegado puede otorgar cualquier cuota al buzón. Esto anula cualquier configuración de cuota de dominio / COS.
7.2.- La Cuota de dominio
La Cuota de dominio es una propiedad que especifica el buzón máximo cuota que cualquier administrador puede otorgar a un buzón en el dominio.

Advertencia

Asignar una cuota ilimitada a un buzón anulará el Dominio Configuración de cuota.

8.- Grant Limit vs Domain Quota
Las propiedades Grant Limit y Domain Quota son mutuamente excluyentes de forma restrictiva.

Esto significa que pueden ocurrir los siguientes escenarios:

Un administrador global otorga a un usuario una cuota más alta que el dominio permitido Cuota
Un administrador delegado otorga a un usuario una cuota superior a la permitida Cuota de dominio
El límite de concesión de un administrador delegado es inferior a la cuota de dominio
Examinemos estos escenarios uno por uno.

8.1.- Un administrador global otorga a un usuario una cuota más alta que la cuota de dominio permitida
Dado que la cuota de dominio se aplica a un dominio determinado, no a un administrador determinado, la cuota efectiva para el usuario será la cuota máxima permitida por la configuración de «Cuota de dominio».

8.2.- Un administrador delegado otorga a un usuario una cuota más alta que la cuota de dominio permitida
En este caso, la cuota efectiva para el usuario será la cuota máxima permitido por la configuración de «Cuota de dominio», incluso si el administrador delegado El límite de concesión es más alto que la cuota de dominio.

8.3.- El límite de concesión de un administrador delegado es inferior a la cuota de dominio
En este caso, la cuota máxima que el administrador delegado puede otorgar a un usuario será el definido por el Límite de concesión, incluso si el Dominio La cuota es mayor. Un administrador global, que no está sujeto a ningún límite de concesión restricción, se le permitirá asignar cualquier cuota de buzón al usuario hasta el límite permitido por la cuota de dominio.

9.- Límites de dominio
La gestión de límites de dominio es una característica del módulo Admin NG. Permite un Administrador global para establecer límites de nivel de dominio que no se pueden exceder por cualquier administrador.

La única forma de superar un límite de dominio es cambiar el límite de dominio sí mismo.

9.1.- Tipos de Límites de dominio
Límite de cuenta global: el número máximo de cuentas que se pueden creado en este dominio.
Cuota de dominio: la cuota máxima de buzón que cualquier administrador puede conceder a un buzón en el dominio.
Límites de COS: defina qué clases de servicio se pueden usar para los usuarios en el dominio y el número máximo de usuarios por clase de servicio.
9.2.- Editar los límites de un dominio
9.2.1.- Desde el Panel de Administración
Todos los dominios en la infraestructura de Zimbra se enumeran en el Dominio Configuración en la pestaña Admin NG del Zimlet de Administración.

Para editar los límites de un dominio, seleccione el dominio desde el Dominio Configuración y presione el botón Editar.

9.2.2.- Desde la Consola
Para editar los límites de un dominio a través de la CLI, use el Comando setDomainSettings:

Sintaxis:
       zxsuite admin setDomainSettings {dominio} [attr1 valor1 [attr2 valor2 ...
    LISTA DE PARÁMETROS

    NOMBRE TIPO VALORES ESPERADOS POR DEFECTO
    cadena de dominio (M)
    account_limit (O) Integer no cambia la configuración
    domain_account_quota (O) La cadena no cambia la configuración
    cos_limits (O) Cadena cosname1: limit1, cosname2: limit2 no cambia la configuración

    (M) == parámetro obligatorio, (O) == parámetro opcional

    Ejemplo de uso:
    zxsuite admin setDomainSettings example.com account_limit 100 domain_account_quota 100mb cos_limits cos1: 30, cos2: 80
Establece un límite de cuenta global en el dominio example.com de 100 cuentas,
con una cuota de cuenta de dominio de 100 megabytes
y con cos cuenta límites de 30 para cos1 y 80 para cos2.

Nota: Un límite de cos de -1 elimina el límite de cos.
9.3.- Restablecer los límites de un dominio
9.3.1.- Desde el Panel de Administración
Todos los dominios en la infraestructura de Zimbra se enumeran en el Dominio Configuración en la pestaña Admin NG del Zimlet de Administración.

Para restablecer los límites de un dominio, seleccione el dominio desde el Dominio Configuración y presione el botón Restablecer, luego haga clic enAceptar en el ventana emergente de confirmación.

9.3.2.- Desde la Consola
Para restablecer los límites de un dominio a través de la CLI, use el Comando resetDomainSettings:

    Sintaxis:
       administrador zxsuite resetDomainSettings {dominio}

    LISTA DE PARÁMETROS

    NOMBRE TIPO
    cadena de dominio (M)

    (M) == parámetro obligatorio, (O) == parámetro opcional
10.- Administración de Zimbra como administrador delegado
10.1.- Acceso a la consola de administración de Zimbra como administrador delegado
Para acceder a la consola de administración de Zimbra, conéctese al puerto 7071 de su servidor de correo con un navegador web e inicie sesión con su Zimbra cartas credenciales.

Por ejemplo: https://mail.domain.com:7071

10.2.- Delegado Admin CAN y CAN’T T Table
Aquí hay una referencia rápida de lo que un administrador delegado PUEDE y NO PUEDE hacer a través del módulo Admin NG.

PUEDE	NO PUEDE
Ver la lista de cuentas de cualquier dominio para el que se les otorgan derechos de administrador delegado	Ver la lista de cuentas que pertenece a cualquier otro dominio
Edite cualquier cuenta de usuario en cualquier dominio para el que se le otorguen derechos de administrador delegado	Edite cualquier cuenta de usuario que pertenezca a cualquier otro dominio
Edite cualquier alias, lista de distribución o recurso en cualquier dominio para el que se le otorguen derechos de administrador delegado	Edite cualquier alias, lista de distribución o recurso que pertenezca a cualquier otro dominio
Editar cualquier cuenta de administrador global
Conceder derechos de administrador global o administrador delegado a cualquier usuario
Crear una cuenta en un dominio para el que se les otorgan derechos de administrador delegado	Cree una cuenta en cualquier otro dominio
Seleccione la clase de servicio de una cuenta entre las disponibles para el dominio de esa cuenta	Establecer arbitrariamente la clase de servicio de una cuenta entre las disponibles en el servidor
Editar configuración de COS
Editar la configuración del dominio que puede interferir con el correcto funcionamiento del servidor
Ver o editar cualquier configuración del servidor
Ver o editar cualquier configuración global
10.3.-Descripción general de la consola de administración de Zimbra para administradores delegados
Administrar:
Cuentas: administre las cuentas que pertenecen a cualquier dominio para qué derechos delegados de administrador se han otorgado.
Alias: gestiona los alias de las cuentas que pertenecen a cualquier dominio para los cuales se han otorgado derechos administrativos delegados.
Listas de distribución: gestiona las listas de distribución que pertenecen a cualquier dominio para el que se hayan otorgado derechos administrativos delegados.
Resources: gestione los recursos que pertenecen a cualquier dominio para qué derechos delegados de administrador se han otorgado.
Configurar: ver la configuración de cualquier dominio para el que Se han otorgado derechos administrativos delegados.
Buscar: realiza búsquedas avanzadas.
Network N
Mobile NG: gestiona la sincronización de dispositivos móviles y clientes que pertenecen a cualquier dominio para el que delegan derechos de administrador ha sido consedido.
Admin NG: vea la lista de administradores delegados que pertenecen a cualquier dominio para el que se han otorgado derechos administrativos delegados como así como información de uso de cuotas.
Barra de búsqueda: realiza búsquedas rápidas.
[nombre de usuario]: cierre sesión en la consola de administración de Zimbra.
11.- Navegación de registro de administrador delegado
11.1.- ¿Qué es la navegación de registro de administrador delegado?
Admin NG permite que un administrador global realice un seguimiento fácil de todos los administradores ‘ actividad a través de un navegador de registro gráfico basado en búsquedas.

11.2.- El navegador de registro de Admin NG
Se puede acceder al Navegador de registros de Admin NG haciendo clic en Examinar registros en la pestaña Admin NG del Zimlet de Administración. La ventana emergente Filter Log se abrirá un cuadro de diálogo que le permitirá aplicar algunos filtros a los registros que quiere navegar.

Los filtros disponibles son:

Filtros básicos
Admin: filtra los registros para ver solo las operaciones realizadas por un Administrador de dominio único.
Acción: filtre los registros para ver solo una acción en particular. Vea a continuación las acciones disponibles.
Filtros avanzados
Client IP: filtra los registros para mostrar solo las operaciones realizadas desde una determinada dirección IP.
Mostrar inicios de sesión: seleccione esta casilla de verificación para mostrar también cuándo el Dominio Los administradores inician sesión en Zimbra Web Client.
Resultado: filtra los registros para mostrar todas las operaciones, operaciones exitosas u operaciones fallidas.
Inicio y Fin: limita los registros que se muestran a un intervalo de tiempo específico (predeterminado: el día actual).
Al hacer clic en el botón Detalles se aplicarán los filtros seleccionados y se mostrará El navegador de registro.

11.2.1.- El filtro Acción
Cualquier operación que pueda realizar un administrador está disponible en el menú desplegable menú del filtro Acción.

Todas estas operaciones son importantes para realizar un seguimiento de los administradores. acciones y para solucionar problemas.

Auth: todas las autenticaciones ZWC.
DelegateAuth: todas las autenticaciones delegadas, ya sea a través de Botón Ver correo o mediante la opción-z del zmmailbox mando.
CreateAccount: todas las creaciones de cuenta.
DeleteAccount: todas las eliminaciones de cuenta.
Establecer contraseña: todos los cambios de contraseña del buzón.
RemoveAccountAlias: todas las eliminaciones de alias.
DeleteDistributionList: todas las eliminaciones de listas de distribución.
12.- Informes
12.1.- Admin NG Informes mensuales
El módulo Admin NG incluye una función muy útil de Informes mensuales que permite a los administradores globales realizar un seguimiento de ambos administradores delegados operaciones y estado de dominio para un mes determinado.

12.2.- ¿Cómo funciona el sistema de informes mensuales?
El primer día de cada mes, el módulo Admin NG automáticamente crea un informe basado en los datos recopilados en el registro de Admin NG.

Este informe mensual incluye:

INFORME GLOBAL	
Primera acción registrada	Marca de tiempo de la primera acción realizada por un administrador este mes
Última acción registrada	Marca de tiempo de la última acción realizada por un administrador este mes
Último inicio de sesión de administrador por	Última marca de tiempo de inicio de sesión administrativo
Administrador más activo	Nombre del administrador con el mayor número de acciones registradas
Dirección más utilizada	Dirección IP más común para inicios de sesión de administrador
Cuentas totales	Número total de buzones
Total de cuentas creadas	Número de buzones creados durante el mes
Total de cuentas eliminadas	Número de buzones eliminados durante el mes
Total de dominios creados	Número de dominios creados durante el mes
Total de listas de distribución creadas	Número de listas de distribución creadas durante el mes
Total de listas de distribución eliminadas	Número de listas de distribución eliminadas durante el mes
INFORME DE DOMINIO	
Domain	El nombre del dominio al que se refieren estos datos
Último inicio de sesión de administrador	Última marca de tiempo de inicio de sesión administrativo
Cuenta / cuentas máximas	Número actual y máximo de cuentas
Tamaño de dominio actual	Suma de las cuotas utilizadas por todos los buzones del dominio
Tamaño máximo de dominio	Suma de la cuota máxima de todos los buzones (excluyendo Ilimitado buzones)
Cuentas sin límite de cuota	Número de buzones que no tienen un límite de cuota
Tamaño total de cuentas sin límite de cuota	Suma de las cuotas utilizadas por todos los buzones sin límite de cuota
Recursos del sistema en el dominio	Número de cuentas de recursos del sistema en el dominio
Recursos de calendario en el dominio	Número de cuentas de recursos de calendario en el dominio
Acciones de dominio exitosas	Número de acciones exitosas realizadas por los administradores en este dominio
Acciones de dominio fallidas	Número de acciones fallidas realizadas por los administradores en este dominio
INFORME DE ADMINISTRACIÓN	
Admin	El nombre del administrador al que se refieren estos datos
Inicios de sesión exitosos	Número de inicios de sesión exitosos en la Consola de administración
Inicios de sesión fallidos	Número de inicios de sesión fallidos en la Consola del administrador
Ver correos	Número de veces que este administrador usó la función Ver correo durante el mes
Último inicio de sesión	Marca de tiempo del último inicio de sesión de este administrador en la Consola de administración
Dirección más utilizada	La dirección de correo electrónico más utilizada por este administrador para iniciar sesión
Acciones totales	El número de acciones realizadas por este administrador durante el mes
Cuentas creadas	Número de cuentas creadas por este administrador durante el mes
Cuentas eliminadas	Número de cuentas eliminadas por este administrador durante el mes
12.3.- Cómo acceder a los informes mensuales
12.3.1.- Desde el Panel de Administración
Para acceder a los Informes mensuales:

Inicie sesión en la consola de administración de Zimbra como administrador global.
En la pestaña Admin NG del Zimlet de Administración, haga clic en el Botón `Informes mensuales ‘en la esquina superior derecha de la página.
Seleccione el mes que desea ver y haga clic en Mostrar Informe.
12.3.2.- Desde la Consola
Para ver los informes mensuales de la CLI, use el getMonthlyReport mando:

    zxsuite admin getMonthlyReport [attr1 value1 [attr2 value2 ...

    LISTA DE PARÁMETROS

    NOMBRE TIPO VALORES ESPERADOS POR DEFECTO
    mes (O) Cadena mm / aaaa 12/2012
    local (O) booleano verdadero | falso falso

    (M) == parámetro obligatorio, (O) == parámetro opcional

    Ejemplo de uso:

    administrador de zxsuite getMonthlyReport
    Muestra el informe mensual del mes anterior.

    zxsuite admin getMonthlyReport mes 11/2012
    Muestra el informe mensual del mes '11 / 2012 '
12.4.- Reportes parciales
Para crear un informe parcial para el mes actual, use el Comando doMonthlyReport:

    zxsuite admin doMonthlyReport [attr1 value1 [attr2 value2 ...

    LISTA DE PARÁMETROS

    NOMBRE TIPO VALORES ESPERADOS POR DEFECTO
    mes (O) Cadena mm / aaaa 12/2012
    force (O) Boolean verdadero | falso falso

    (M) == parámetro obligatorio, (O) == parámetro opcional

    Ejemplo de uso:

    zxsuite admin doMonthlyReport
    Genera el informe mensual del mes anterior y lo guarda en la ruta de registro de Admin NG actual

    zxsuite admin doMonthlyReport mes 01/2013
    Genera un informe mensual PARCIAL para el mes actual, sin guardarlo en el disco

    ** NOTA**

    Este comando se ejecuta automáticamente una vez al mes para generar un archivo que contiene
    el informe para el
    mes anterior. Para sobrescribir un archivo de informe existente, establezca el parámetro 'forzar' en verdadero.
12.5.- La ruta de registro de Admin NG
El módulo Admin NG almacena todos los informes mensuales, junto con los registros utilizado para generar los informes mensuales y para proporcionar información a través de La función Admin Log Browser, en una ruta dentro de/ opt / zimbra / conf / carpeta (predeterminado / opt / zimbra / conf / zextras / zxadmin /). Este particular se ha elegido la ruta predeterminada porque es el único directorio que NO SE PUEDE eliminar durante una actualización de Zimbra.

12.5.1.- Estructura y contenido de la ruta de registro de Admin NG
La ruta de registro de Admin NG es un directorio plano que contiene lo siguiente archivos:

Uno o más archivos YYYY_MM que contienen los registros de los archivos mes del mismo nombre.
Cero o más archivos YYYY_MM.report que contienen el informe mensual para el mes del mismo nombre del archivo.
Cero o más archivos YYYY_MM.X que contienen registros parciales para el mes del mismo nombre del archivo. Estos archivos se crean al cambiar el Admin NG Ruta de registro.
12.5.2.- Cambio de la ruta de acceso del administrador NG
Advertencia

Lea atentamente este párrafo antes de cambiar la ruta de acceso del administrador NG. Cualquier error en el procedimiento causará una posible pérdida de registro que represente las funciones de Informe mensual yMostrar registros de administración altamente poco confiable.

Para cambiar de forma segura la ruta de registro de Admin NG, siga estos pasos:

Cree la carpeta que contendrá los registros:
La propiedad de la carpeta debe ser zimbra: zimbra.
El usuario zimbra debe tener permisos de lectura y escritura para carpeta.
La carpeta debe estar vacía.
Inicie sesión en la consola de administración de Zimbra como administrador global.
Abra la pestaña Admin NG en el Zimlet de Administración.
En la sección Configuración básica del módulo, haga clic enCambiar botón cerca de la línea de ruta de registro de administrador.
Ingrese la nueva ruta y haga clic en Cambiar ruta.
Si no se muestran errores, mueva todo el contenido de la ruta de registro anterior.
Es perfectamente normal ver solo los archivos .report y.X en la ruta de registro anterior, ya que el archivo de registro actual recibirá el .1 extensión para marcarlo como parcial. Cualquier archivo .X anterior tienen su número de extensión aumentado en 1.
13.- Restablecer configuración
13.1.- ¿Qué es el restablecimiento de configuración de Admin NG?
El restablecimiento de configuración de Admin NG es una función gratuita de Admin NG módulo que permite a un administrador global borrar completamente derechos de delegación del servidor.

Esta no es una función de «reversión» que limpia el módulo Admin NG configuración. Restablecer la configuración del administrador afectará a ambos administradores NG y derechos de delegación de Zimbra.

Advertencia

El uso de la función Restablecer configuración de administrador borrará completamente configuración de delegación del servidor, devolviéndola al estado de una instalación nueva. Solo la configuración de Delegación de administrador será borrado, ningún otro tipo de datos se verá afectado.

13.2.- ¿Qué borra el restablecimiento de la configuración de administración?
El restablecimiento de la configuración de administrador borra las siguientes configuraciones:

La propiedad de cuenta isDelegatedAdmin para todas las cuentas en el servidor
Todas las entradas de control de acceso y todas las listas de control de acceso para
Usuarios
Dominios
Clases de servicio
Configuración local
Configuración del servidor
Zimlets
13.3.- ¿Cuándo debo usar el restablecimiento de configuración de administrador?
El restablecimiento de la configuración de administrador solo debe usarse en los siguientes casos:

Para restablecer completamente una situación comprometida
Si una o más configuraciones incorrectas de ACL o ACE causan que su Zimbra La consola de administración debe ser inestable o no mostrar correctamente (por ejemplo, mostrar una página en blanco o faltar una o más IU elementos), use el restablecimiento de la configuración de administración como final resolución.
Si planea dejar de usar el módulo Admin NG
La opción de reinicio está disponible incluso si no hay una red válida NG La licencia está activa. Recuerde que esto también borrará cualquier establecer manualmente la configuración de delegación.
13.4.- ¿Cómo uso el restablecimiento de configuración de administrador?
Si realmente desea restablecer la configuración de Delegación de administrador, simplemente ejecute este comando CLI:

zxsuite core doDeleteAllDelegatedRights
Se le pedirá que ingrese una cadena de confirmación para evitar cualquier accidente uso del comando.
