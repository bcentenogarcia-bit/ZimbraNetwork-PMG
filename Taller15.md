Zimbra ActiveSync y Zimbra Connector para Outlook
1. Introducción
Dependiendo de las licencias adquiridas para Zimbra, outlook se puede configurar con las siguientes opciones

Zimbra Network Standard Edition
  ActiveSync + LDAP

Zimbra Network Professional Edition
  MAPI --> conector colaborativo para Outlook en Windows
  EWS  --> conector colaborativo para Outlook en MAC
1.1.- Zimbra ActiveSync para Outlook
Zimbra ActiveSync permite que un outook desde versión 2013 para arriba pueda crear una cuenta de tipo «Exchange ActiveSync»

1.1.1.- Crear Cuenta Exchange ActiveSyn para Outlook
Ir al panel de control de windows

Seleccionar ícono "mail" o "correo"
    Click en "cuentas de correo electrónico"
        Correo Electrónico ---> Click en Nuevo
            Cuenta de Correo electrónico
                Configuración manual o tipo de servidores adicionales
                    Servicio compatible con outlook.com o Exchange ActiveSync
                        Su Nombre: Tuxito Linuxero
                        Dirección de Correo: tuxito@aulautil.tk
                        Servidor de Correo:  mail.aulautil.tk
                        Nombre de Usuario:   tuxito@aulautil.tk
                        Contraseña:          Lolcito.123
                        Recordar contraseña: check
                        Correo para mantener Todo
                        sin conexión:        
Nota Si no aparece el ícono de Mail/Correo en el panel de control ver estas dos opciones.

1.- Ir al Panel de Control y poner Ver por Categoría

Windows 7: User Accounts and Family Safety
Windows 8: User Accounts and Family Safety
Windows 10: User Accounts
2.- Abrir CMD o PowerShell y ejecutar

outlook.exe /manageprofiles
1.1.2.- Sincronizar la GAL por LDAP (recomendable solo para red local)
En clase abrir en e firewall local el puerto 389 del zimbra

firewall-cmd --add-service=ldap --permanent
firewall-cmd --reload
Natear el servicio LDAP desde el firewall Sophos hacia la IP interna de Zimbra (SOLO PARA LA CLASE)

Proteger --> Firewall
    Editar regla "Nat Zimbra Network"
        Servicios
            Añadir LDAP
Ir al panel de control de windows

Seleccionar ícono "mail" o "correo"
    Click en "cuentas de correo electrónico"
        ir a la pestaña "Libreta de Direcciones"
            Nuevo
                Servicio de directorio o internet (LDAP)
                    Información del Servidor
                        Nombre del Servidor: mail.aulautil.tk
                            Información de Inicio de Sesión
                                El servidor necesita que inicie sesión:  Check
                                Nombre de usuario: uid=tuxito,ou=people,dc=aulautil,dc=tk
                                Contraseña: Lolcito.123
                            Click en "mas configuraciones"
                                Conexión
                                    El nombre para mostrar según aparece en la libreta de direcciones
                                        "Libreta Global de Direcciones"                                    
                                Buscar
                                    Habilitar exploración si el servidor lo admite: Check
                            Aplicar
                        OK
                Siguiente
Luego para probar al redacar un mensaje

Click en Para 
  Buscar:   Esta en mas columnas 
  Libreta de Direcciones:  Libreta Global de Direcciones
  Caja para buscar:   a    click en "Ir"
    Buscará nombres de contacto que contengan letra "a"
Quita el Nateo del servicio LDAP desde el firewall Sophos hacia la IP interna de Zimbra

Proteger --> Firewall
    Editar regla "Nat Zimbra Network"
        Servicios
            Eliminat LDAP
1.2.- Zimbra Connector para Outlook
Zimbra Connector for Outlook (ZCO) proporciona sincronización bidireccional en tiempo real de mensajes de correo electrónico, carpetas, etiquetas, libretas de direcciones, tareas y calendario entre Outlook y su cuenta de Zimbra.

ZCO almacena una copia de los datos de su cuenta en su computadora en una base de datos llamada ZDB. Esto le permite trabajar con sus datos incluso cuando no está conectado al servidor Zimbra.

Esta guía cubre la instalación, configuración, sincronización y otras características de ZCO.

ZCO es compatible con los siguientes sistemas operativos de Microsoft:

    Windows 10

    Windows 8.1

    Windows 7, Service Pack 1 (Fin de vida: 14 de enero de 2020).

    Todos los sistemas operativos deben tener las últimas actualizaciones de Microsoft.
1.3.- Versiones compatibles de Microsoft Outlook®
ZCO es compatible con las siguientes versiones de Microsoft Outlook:

    Outlook 2019: ediciones de 32 bits y 64 bits de Microsoft Office, incluido Click para ejecutar.

    Outlook 2016: ediciones de 32 bits y 64 bits de Microsoft Office, incluidas las versiones Office365 y Click para ejecutar.

    Outlook 2013: ediciones de 32 bits y 64 bits de Microsoft Office.

    Outlook 2010: ediciones de 32 bits y 64 bits de Microsoft Office (Fin de la vida útil: 13 de octubre de 2020).

    Todas las instalaciones de Microsoft Outlook y Microsoft Office deben tener las últimas actualizaciones de Microsoft.
    Si Outlook se actualiza a una versión diferente, ZCO debe desinstalarse y reinstalarse. Esto es particularmente importante después de una actualización a OL2013, ya que ZCO debe instalarse en una ubicación diferente en la computadora cliente.
    Nota: Haga clic para ejecutar versiones de Outlook 2010 no son compatibles.
2. Instalación y configuración de ZCO
Este capítulo proporciona información sobre cómo configurar ZCO para acceder a su cuenta de correo electrónico de Zimbra a través de Outlook. Hay tres etapas:

    Instale ZCO utilizando el archivo ZCO MSI (instalador).

    Cree un perfil de Outlook ZCO: consulte a continuación para obtener más información sobre los perfiles.

    Inicie Outlook contra el perfil anterior. En este momento, ZCO realizará una sincronización inicial para extraer los datos de su cuenta a la ZDB. El tiempo que esto tarda varía con la cantidad de contenido en su cuenta, el tamaño de la Lista global de direcciones, el número de otros usuarios que también se sincronizan y las condiciones de la red. Si debe interrumpir una sincronización en progreso, más tarde se reanudará donde lo dejó.
2.1.- Preparación para instalar ZCO
Debe tener privilegios administrativos en su computadora para instalar ZCO. Si no lo hace, puede obtenerlos temporalmente del administrador del sistema.

Antes de instalar ZCO, asegúrese de conocer la siguiente información
        El nombre de host del servidor Zimbra

        Si el servidor requiere una conexión segura

        Tu dirección de correo electrónico de Zimbra

        La contraseña de tu cuenta Zimbra
Cuando inicie Outlook por primera vez con un nuevo perfil, debe asegurarse de que su computadora esté conectada al servidor utilizando una conexión confiable (preferiblemente por cable) para que la inicialización pueda llevarse a cabo rápidamente.

Si está ejecutando un software antivirus, podría ralentizar InitialSync, por lo que, si es posible, considere desactivarlo durante la duración de InitialSync
2.2.- Instalar ZCO
Para instalar ZCO, debe descargar el archivo de instalación MSI de ZCO en su computadora. El MSI normalmente se puede obtener navegando a:

https:///downloads/index.html

Alternativamente, el administrador del sistema podrá proporcionárselo.

El instalador lo guía a través del proceso de instalación de ZCO.

Si Outlook está abierto, ciérrelo.

Haga doble clic en el archivo de instalación de MSI en su computadora para ejecutar el instalador.

Haga clic en Siguiente.

Lea el acuerdo de licencia de usuario final y haga clic en Acepto los términos del Acuerdo de licencia.

Haga clic en Siguiente.

Para comenzar la instalación, haga clic en Siguiente.

Cuando termine de instalar, aparecerá el cuadro de diálogo Instalación completa. Haz clic en Cerrar.
2.3.- Crear un perfil de Outlook para ZCO
Microsoft Outlook utiliza lo que se conoce como un perfil de Outlook (o, a veces, «MAPI» o «correo») para conectarse a su cuenta de correo electrónico. El perfil le dice a ZCO qué cuenta de correo electrónico se utilizará, cómo conectarse al servidor y otras configuraciones. Puede configurar varios perfiles, cada uno para una cuenta de correo electrónico diferente. Por ejemplo, puede tener un perfil para su cuenta de correo electrónico relacionada con el trabajo y otro para su cuenta de correo electrónico personal. Puede ver los perfiles que están configurados en su máquina utilizando la opción Correo en el Panel de control de Windows.

Mientras ZCO está instalado, si no hay perfiles de Zimbra existentes, el instalador de ZCO crea automáticamente un perfil de ZCO vacío (generalmente llamado Zimbra) para usted.

Para hacer uso de esto, siga los pasos a continuación.

Abra Outlook

Elija el perfil llamado Zimbra. Outlook muestra el cuadro de diálogo Configuración de configuración del servidor Zimbra.

Ingrese el nombre del servidor.

A menos que su administrador le indique lo contrario, deje la casilla Usar casilla de verificación segura seleccionada.

Ingrese su dirección de correo electrónico de Zimbra.

Ingresa tu contraseña.

(Opcional) También puede ir a las pestañas Configuración de conexión, Configuración de descarga y Archivos de datos para obtener más opciones. Consulte la sección Configuración avanzada del perfil a continuación para obtener más información al respecto.

Haga clic en Aceptar para iniciar Outlook. Outlook comienza a buscar los datos de su cuenta del servidor y los almacena en su ZDB. Para verificar su progreso, abra la cinta de Zimbra y haga clic en Mostrar progreso de Zimbra.

Tenga en cuenta que estos son solo pasos típicos. Es posible que su administrador haya configurado el servidor y / o el instalador de una manera que requiera menos pasos. En ese caso, proporcionarán detalles por separado
Crear perfiles adicionales de ZCO

Puede crear perfiles ZCO adicionales de la siguiente manera:

Panel de control> Correo> Mostrar perfiles, asegúrese de que esté seleccionada la opción Solicitar un perfil ... y luego haga clic en Agregar.

Especifique el nombre del nuevo perfil.

Seleccione Configuración manual del servidor o tipos de servidor adicionales y haga clic en Siguiente.

Haga clic en Otro y elija Zimbra Collaboration Server y haga clic en Siguiente.

Complete el perfil como se especifica en la sección Creación de un perfil de ZCO anterior.

Si está utilizando Outlook 2016, consulte este artículo https://support.microsoft.com/en-us/help/3189194/how-to-disable-simplified-account-creation-in-outlook-2016
Configuraciones de perfil avanzadas

Su administrador le dirá si necesita cambiar alguna de las siguientes configuraciones.

Configuración de conexión: ZCO utiliza la configuración de proxy de Windows Internet Explorer (IE) de forma predeterminada. Si desea configurar manualmente la configuración del proxy, puede ingresar un proxy HTTP y un número de puerto o seleccionar no usar proxy.

Configuración de descarga: esta configuración determina cómo se descargan los mensajes. Para ahorrar espacio de almacenamiento en su computadora, puede configurar ZCO para que descargue solo el encabezado del mensaje de correo electrónico en lugar del mensaje completo. El valor predeterminado es descargar el mensaje completo, incluidos los archivos adjuntos.

Archivos de datos: estas son las configuraciones de perfil móvil para su cuenta. Configurar un perfil móvil le permite acceder a su correo de Outlook desde más de una computadora. Almacena la información y la configuración de su perfil de Outlook en una máquina remota.
Actualización de ZCO

Si el administrador de su sistema ha instalado una versión más nueva de ZCO en el servidor, y si ya tiene una versión de ZCO instalada en su sistema, la próxima vez que inicie Outlook se le preguntará si desea actualizar ZCO.

Puede elegir actualizar, omitir la versión o nunca actualizar. Si selecciona Actualizar, la última versión se descarga en su computadora, Outlook se reinicia automáticamente y comienza la actualización. Debe tener privilegios administrativos en su computadora para actualizar ZCO. Si no lo hace, puede obtenerlos temporalmente del administrador del sistema. Desinstalar ZCO

En algunas circunstancias, por ejemplo, antes de actualizar una instalación de Outlook existente a Outlook 2013 o posterior, deberá desinstalar ZCO.

Para hacer esto, seleccione la opción Programas y características en el Panel de control de Windows y luego haga doble clic en la entrada de Zimbra Connector en la lista de programas instalados.

Desinstalar ZCO no afecta a ningún perfil de Outlook existente. Debe tener privilegios administrativos en su computadora para desinstalar ZCO. Si no lo hace, puede obtenerlos temporalmente del administrador del sistema.

Sincronización con el servidor Zimbra
La primera vez que abre Outlook después de instalar y configurar ZCO, su buzón de Outlook realiza automáticamente una sincronización inicial con el servidor.

La sincronización inicial puede tardar unos minutos o más, dependiendo del tamaño del buzón, la velocidad de la red y otros factores discutidos en la sección anterior. En redes lentas, la sincronización inicial de buzones grandes puede llevar varias horas.

Es ventajoso instalar ZCO mientras está conectado a una red empresarial rápida y confiable. También se recomienda encarecidamente que el software antivirus esté deshabilitado durante la sincronización inicial; de lo contrario, el software antivirus puede prolongar la inicialización mediante mensajes de verificación de virus a medida que se colocan en el ZDB.

El progreso de su sincronización se muestra en el cuadro de diálogo Enviar / Recibir progreso de Zimbra.

En general, puede usar Outlook durante la sincronización porque ZCO se sincroniza en segundo plano, pero para la sincronización inicial se recomienda que espere a que se complete la sincronización.

Durante la sincronización, el nuevo correo recibido durante la sincronización se entrega de inmediato.

Si la sincronización no se inicia automáticamente, puede iniciarla manualmente presionando F9 o haciendo clic en Enviar / Recibir todas las carpetas en la cinta Enviar / Recibir o presionando la tecla F9. Para obtener el mejor rendimiento, no debe interrumpir la sincronización inicial mientras está en progreso, por ejemplo, reiniciando Outlook o desconectándose de la red. Si lo hace, se reanudará desde donde lo dejó. ¿Qué información se sincroniza?

ZCO sincroniza todas sus carpetas, incluido el correo electrónico en su Bandeja de entrada, el correo electrónico que ha archivado en otras carpetas, Elementos enviados, Borradores y Papelera. También sincroniza sus carpetas Calendario, Contactos, Tareas y Notas.

Además, sincroniza cualquier firma de correo electrónico que haya creado y el contenido de las pestañas Remitentes seguros y Remitentes bloqueados del cuadro de diálogo Opciones de correo electrónico no deseado de Outlook, junto con las preferencias seleccionadas, por ejemplo, sus preferencias de Lectura de recibo. Después de su sincronización inicial

Después de su primera sincronización, puede acceder a su cuenta de Zimbra con Outlook.

Las carpetas de correo de Zimbra se muestran en el panel de navegación. Puede ver su calendario, contactos y tareas haciendo clic en la barra de aplicaciones debajo de sus carpetas de correo, ubicadas en la parte inferior izquierda del panel.

El correo electrónico recibido más reciente se muestra en su Bandeja de entrada en el panel Contenido. El panel Contenido también muestra los detalles de citas, contactos y tareas, según la barra de aplicaciones activa.

Sus próximas citas y tareas se muestran en la barra Tareas pendientes en el lado derecho de la vista.
Sincronizando tu cuenta

Los cambios que realice utilizando Outlook o Zimbra Web Client se sincronizan automáticamente entre sí en aproximadamente un minuto.

La excepción a esta regla es que los cambios realizados por los propietarios de las carpetas que le han compartido (consulte Compartir carpetas más adelante en esta guía) se sincronizan con Outlook de acuerdo con el cuadro de diálogo «Enviar / recibir grupos» de Outlook (de forma predeterminada, cada 30 minutos). Puede forzar una sincronización manual en cualquier momento haciendo clic en Enviar / Recibir o presionando F9. Resincronizando la GAL para el soporte del grupo de contacto

Para obtener soporte completo para los Grupos de contacto para los perfiles que se crearon con versiones anteriores de ZCO, es necesario realizar un restablecimiento completo de GAL en su cliente ZCO para garantizar que los grupos de contacto y sus miembros estén sincronizados correctamente con ZCS.

Zimbra Ribbon> Sincronizar lista global de direcciones> Restablecer lista global de direcciones

Funciones avanzadas de ZCO
Este capítulo describe las características avanzadas de ZCO. Agregar otras cuentas a su perfil de Zimbra

Un solo perfil de correo puede combinar cuentas Zimbra, IMAP, Exchange y POP, pero tenga en cuenta que no puede agregar más de una cuenta Zimbra a un perfil individual.

Para agregar una nueva cuenta, vaya a:

Inicio â † ‘Configuración â †’ Panel de control â † ‘Correo â †’ Mostrar perfiles â † ‘Agregarâ € ¦â € ‹

y siga las instrucciones para agregar una nueva cuenta de correo electrónico. Compactando su ZDB

Con el tiempo, el ZDB puede fragmentarse, ocupando más espacio del que necesita y afectando el rendimiento, particularmente después de la eliminación de una gran cantidad de elementos.

Por lo tanto, puede ser beneficioso compactar su ZDB periódicamente. La compactación cada pocos meses debería ser suficiente en la mayoría de los casos. Para los buzones grandes, esta operación puede llevar un tiempo considerable, una hora o incluso más, durante el cual no podrá usar Outlook, por lo que debe hacerlo en un momento en que no necesite usar Outlook.

En Outlook, seleccione Archivo> Configuración de la cuenta.

En el cuadro de diálogo Configuración de la cuenta, haga clic en la pestaña Correo electrónico.

Seleccione el perfil de Zimbra para el que desea compactar el ZDB y haga clic en Cambiar.

En el cuadro de diálogo Configuración de servidor Zimbra, seleccione la pestaña Archivos de datos.

Haga clic en archivos compactos. Se abre una ventana de progreso y luego se cierra después de que se completa la compactación ZDB.

Haga clic en Aceptar.

Los administradores de Zimbra ahora pueden habilitar la compactación automática de bases de datos. Si el administrador ha deshabilitado el cuadro de diálogo de progreso, la compactación continúa en segundo plano y es posible que vea la pantalla de presentación de Outlook un poco más de lo habitual.
Autenticación

Cuando Outlook se inicia, se conecta al servidor para verificar sus credenciales de inicio de sesión. Este proceso se conoce como Autenticación.

ZCO admite cuatro tipos de autenticación:

Nombre de usuario y contraseña

Inicio de sesión único

Autenticación de dos factores

Autenticación de certificado
Los administradores de Zimbra configuran el tipo de autenticación que utiliza. Nombre de usuario y contraseña

La forma más común de autenticación en la que ingresa su nombre de usuario y contraseña para completar el proceso de verificación. Iniciar sesión no requiere otra información. Inicio de sesión único

En Single Sign-on, ZCO usa sus credenciales de inicio de sesión de Windows. Si está disponible en su organización, verá una casilla de verificación en la primera página de propiedades de Zimbra:

Conectar usando mis credenciales de inicio de sesión de Windows Autenticación de dos factores

Si su cuenta del servidor Zimbra se configuró para usar la autenticación de dos factores, se le pedirá que proporcione un código de acceso urgente durante el inicio de sesión.

El código de acceso debe generarse utilizando una aplicación de terceros (por ejemplo, Authy, Google Authenticator, Microsoft Authenticator, etc.), que los administradores de Zimbra configuran para su cuenta.

Al ingresar el código de acceso, se le dará la opción de Recordar este dispositivo. Si selecciona esta opción, no necesitará proporcionar un código nuevamente para esta cuenta en su máquina actual durante 30 días de manera predeterminada. Autenticación de certificado

Si su cuenta del servidor Zimbra está configurada para usar autenticación de certificado, entonces necesita un certificado apropiado para su cuenta Zimbra configurado en su PC. Luego se le pedirá que seleccione este certificado cada vez que inicie sesión en su cuenta del servidor Zimbra con Outlook. Sincronizar notas de Outlook

Las notas de Outlook se sincronizan con el servidor donde aparecen dentro de las carpetas de Maletín. Allí, se pueden ver, pero no se pueden editar con Zimbra Web Client. Cada carpeta de Outlook Notes aparece como una carpeta de Maletín.

Las carpetas de maletín creadas con Zimbra Web Client no aparecen en Outlook a menos que contengan Notas. Personas

Una Persona de Zimbra le permite crear una identidad de correo electrónico separada para administrar diferentes cuentas de correo electrónico. Por ejemplo, puede crear una persona para su correo electrónico de trabajo y otra para sus correos electrónicos no comerciales.

Al usar una persona, puede especificar una dirección de origen diferente y una dirección de respuesta a. Todos los correos electrónicos salientes muestran la dirección de correo electrónico de la persona que está utilizando. Usted elige la persona haciendo clic en el campo De en la ventana Componer correo electrónico de Outlook.

Puede ver, agregar, editar y eliminar sus Personas Zimbra de Outlook. Si anteriormente creó personas usando el cliente web de Zimbra, las personas se sincronizarán con Outlook. Agregar una Persona

Haga clic en la pestaña Zimbra â † 'Personas

Haz clic en Agregar.

Ingrese el nombre de la nueva persona y haga clic en Aceptar.

(Opcional) En el área Configuración de Personas, puede elegir lo que aparece en los campos De y Responder de los mensajes de correo electrónico.

Desactive la casilla Establezca el campo "Responder a" de los mensajes de correo electrónico en: para establecer la misma configuración de persona para nuevos correos electrónicos y respuestas.

    Para tener una información de respuesta diferente, marque la casilla e ingrese un nombre de remitente y una dirección de correo electrónico diferentes.

Haga clic en Aceptar para guardar sus cambios.
Sincronizando Personas

Al redactar o responder a un correo electrónico, es posible que deba sincronizar sus personajes si no los ve en Cuentas (Outlook 2007 y anteriores) o en De (Outlook 2010 y posteriores).

Haga clic en la pestaña Zimbra â † 'Personas

Haz clic en Agregar.

Confirme que su cuenta está en la lista de Personas. Si su persona no está en la lista, debe agregar una persona.

Haga clic en Aceptar. Esta acción sincroniza personas y muestra el menú Cuenta en el cuadro de diálogo Nuevo mensaje.
Eliminar una Persona

Haga clic en la pestaña Zimbra â † 'Personas

Haz clic en Agregar.

Seleccione la persona que desea eliminar.

Haga clic en Eliminar para eliminar la persona seleccionada.

Haga clic en Aceptar.

Para recuperar una persona eliminada antes de aplicar los cambios, haga clic en Cancelar.

No puede eliminar la Persona predeterminada.
No puede recuperar personas una vez que las haya eliminado y guardado los cambios.
Uso de etiquetas Zimbra y categorías de Outlook

En Outlook, puede asignar categorías a mensajes de correo electrónico, contactos, tareas y citas. Estas categorías se sincronizan con su cuenta y aparecen en Zimbra con etiquetas de colores conocidas como etiquetas.

Puede usar categorías para organizar y encontrar elementos particulares. Un artículo puede pertenecer a múltiples categorías.

Para explorar su cuenta por categoría, vaya a su carpeta de correo de Zimbra en el panel de navegación de Outlook

Seleccione Buscar carpetas â † 'Correo categorizado. El correo aparece agrupado por categoría en el panel Contenido.
Reglas

Las reglas le permiten filtrar o administrar automáticamente el correo electrónico entrante o existente en su cuenta.

Una regla consta de una o más condiciones que debe cumplir un correo electrónico y una o más acciones que se aplicarán cuando lo haga. Por ejemplo, puede seleccionar entre las siguientes acciones:

Mueva automáticamente un correo electrónico entrante a una carpeta específica

Eliminar un mensaje

Reenviar un mensaje

Asignar un correo electrónico a una categoría.

Marcar un mensaje

Marcar un mensaje como leído
Hay dos tipos de reglas:

Reglas del servidor Zimbra

Reglas locales de Outlook
Las reglas del servidor Zimbra son ejecutadas por el servidor incluso cuando su Outlook no se está ejecutando. Por esta razón, las reglas del servidor son generalmente la forma preferida de automatizar la administración del correo electrónico, y ZCO desactiva las reglas de Outlook de manera predeterminada.

Para configurar las Reglas del servidor, use Herramientas â † ‘Reglas del servidor Zimbra. Crear una nueva regla de correo electrónico

Haga clic en la pestaña Zimbra â † 'Reglas del servidor Zimbra.

En el cuadro de diálogo Reglas, haga clic en Nuevo. Aparece un cuadro de diálogo del asistente de reglas.

En la sección Seleccionar condición (es), seleccione las condiciones que debe cumplir el mensaje. A medida que selecciona las condiciones, se muestran en la sección Editar la descripción de la regla.

Si la condición que seleccionó requiere un valor, como con palabras específicas en el cuerpo, debe ingresar los valores o parámetros para la condición:

    Haga clic en la línea de condición en la sección Editar la descripción de la regla.

    En el cuadro de diálogo que se abre, ingrese, agregue o seleccione los valores o parámetros para la condición y haga clic en Aceptar.

Haga clic en Siguiente.

En la sección Seleccionar acciones, seleccione una acción para realizar. La acción seleccionada aparece en la sección Editar la descripción de la regla.

Si la acción que seleccionó requiere un valor, debe ingresar los valores o parámetros para la acción:

    Haga clic en la línea de acción en la sección Editar la descripción de la regla.

    En el cuadro de diálogo que se abre, ingrese, agregue o seleccione los valores o parámetros para la acción y haga clic en Aceptar.

Haga clic en Siguiente.

En la sección Seleccionar excepciones, seleccione las excepciones que el mensaje debe coincidir. A medida que selecciona las excepciones, se muestran en la sección Editar la descripción de la regla.

Si la excepción que seleccionó requiere un valor, debe ingresar los valores o parámetros para la excepción:

    Haga clic en la línea de excepción en la sección Editar la descripción de la regla.

    En el cuadro de diálogo que se abre, ingrese, agregue o seleccione los valores o parámetros para la condición y haga clic en Aceptar.

Haga clic en Siguiente.

Ingrese un nombre para su nueva regla en el campo Especificar un nombre para esta regla.

Seleccione si se aplicará la regla a los mensajes que cumplan Cualquier condición o Todas las condiciones.

(Opcional) Seleccione No procesar reglas de filtro adicionales si corresponde.

Revise la descripción de su regla.

    Para hacer cambios, haga clic en Atrás.

    Si la descripción de su regla es correcta, haga clic en Finalizar.
Su nueva regla aparece en la lista de reglas. Editar una regla de correo electrónico

Haga clic en la pestaña Zimbra â † 'Reglas del servidor Zimbra.

Seleccione la regla que desea editar.

Haz clic en Editar.

Seleccione la condición, acción, excepción u otro detalle que desea editar.

Haga clic en Siguiente.

Haga clic en Finalizar cuando haya terminado de editar la regla.

Haga clic en Aplicar para salir del Asistente de reglas y guardar los cambios.
Eliminar una regla de correo electrónico

Haga clic en la pestaña Zimbra â † 'Reglas del servidor Zimbra.

Seleccione la regla que desea eliminar.

Haz clic en Eliminar.

Haga clic en Aplicar para salir del Asistente de reglas y guardar los cambios.
Ejecutar una regla de correo electrónico

Puede ejecutar reglas a su conveniencia o en correos electrónicos ya recibidos.

Haga clic en la pestaña Zimbra â † 'Reglas del servidor Zimbra.

Seleccione la regla para ejecutar.
    ZCO ejecuta la regla resaltada independientemente de si ha marcado la casilla de verificación.

Haga clic en Ejecutar ahora a la derecha.

Elija la carpeta en la que ejecutar la regla.

Haga clic en Aceptar para ejecutar la regla.
Mientras el servidor ejecuta su regla seleccionada en una carpeta, un cuadro de diálogo de progreso le muestra cuántos correos electrónicos se han procesado y afectado. Puede detener la ejecución de la regla haciendo clic en Cancelar en el cuadro de diálogo.

Cuando se hayan procesado todos los correos electrónicos de la carpeta, se le mostrará un resumen de la ejecución. Haga clic en Aceptar para confirmar y cerrar el cuadro de diálogo. El filtro se ejecuta en el servidor Zimbra, por lo que el cliente debe estar en línea, y los cambios para los mensajes afectados deben sincronizarse con el cliente de Outlook antes de que sean visibles. Compartir carpetas

Puede compartir su correo electrónico, libreta de direcciones, calendario, tareas y carpetas de notas con otras personas de su organización, invitados externos o el público. Crear acciones internas, externas y públicas

Cuando comparte su carpeta, identifica si desea compartir su carpeta internamente, externamente o crear un recurso compartido público.

Uso compartido interno: los usuarios o grupos de Zimbra que figuran en la Lista global de direcciones (GAL) pueden ver su carpeta. Selecciona los tipos de privilegios para otorgar a los usuarios internos.

Uso compartido externo: los usuarios externos pueden ver su carpeta utilizando su dirección de correo electrónico y una contraseña que usted asigne. Los usuarios externos no pueden realizar ningún cambio en la información que ven.

Recursos compartidos públicos: cualquiera puede ver su carpeta utilizando su URL. Los espectadores no pueden realizar cambios en la información que ven.
Asignación de roles y privilegios de acceso

Los roles son combinaciones preestablecidas de privilegios de acceso. Los privilegios de acceso se definen de la siguiente manera:

Leer: Ver elementos en la carpeta.

Editar: cambia el contenido de la carpeta.

Crear: agrega elementos a la carpeta.

Eliminar: elimine elementos de la carpeta.

Actuar sobre el flujo de trabajo: responder a las solicitudes.

Administrar carpeta: modifique los permisos de la carpeta.
Los siguientes son los roles que un usuario puede permitir al compartir: Permisos de rol

Administrador

Leer, editar, crear, eliminar, actuar en el flujo de trabajo, administrar la carpeta. El concesionario tiene el mismo acceso a la carpeta que el propietario.

Delegar

Leer, editar, crear, eliminar, actuar en el flujo de trabajo

Crítico

Leer. Este rol es el predeterminado. A todos los recursos compartidos públicos y externos se les asigna el rol de revisor, y no se puede cambiar. Comparta múltiples carpetas usando permisos primarios

De manera predeterminada, los beneficiarios y los privilegios de acceso asignados a una carpeta solo se aplican a esa carpeta y no a ninguna carpeta almacenada dentro de la carpeta (carpetas secundarias). La opción Heredar permisos de padre en la pestaña Compartir le permite aplicar los mismos beneficiarios y privilegios de acceso que la carpeta padre. Esta opción le permite asignar beneficiarios y privilegios de acceso a múltiples carpetas sin configurar recursos compartidos individuales para cada una. Compartir una carpeta con otro usuario de Zimbra

Haga clic derecho en la carpeta que desea compartir y seleccione Propiedades.

Haz clic en la pestaña Compartir.

Haz clic en Agregar.

Seleccione Interno y haga clic en Aceptar.

En el campo Buscar, comience a escribir un nombre para mostrar la lista de acceso. Asegúrese de seleccionar la libreta de direcciones desde la que desea buscar.

Seleccione el usuario al que desea asignar acceso delegado.

Haga clic en Aceptar. El nombre se muestra en el cuadro de diálogo Propiedades.

Nota: Solo puede agregar una persona a la vez. Para compartir con más de una persona, repita estos pasos.

En el menú Nivel de permiso, seleccione la función que define los privilegios de acceso:

Administrador

    Puede crear, leer, editar y eliminar elementos; También puede administrar una carpeta y actuar sobre los elementos del flujo de trabajo.
Delegar

    Puede crear, leer, editar y eliminar elementos y actuar sobre elementos de flujo de trabajo.
Crítico

    Tiene privilegios de solo lectura. Pero si desea tener el mismo nivel de permisos que la carpeta principal, marque Heredar permisos del padre.

Haz clic en Aplicar.

Haga clic en Aceptar.
La persona recibe un correo electrónico de notificación con la información compartida. Compartir una carpeta con un usuario externo

Haga clic derecho en la carpeta para compartir y seleccione Propiedades.

Haz clic en la pestaña Compartir.

En la pestaña Compartir, haga clic en Agregar.

Seleccione externo.

En el campo * Dirección de correo electrónico *, escriba la dirección de correo electrónico del usuario externo.

En el campo Contraseña, escriba una contraseña para que la persona la use para acceder a esta carpeta compartida.

Haga clic en Aceptar. La pestaña Compartir enumera la dirección de correo electrónico del usuario externo. El nivel de permiso es Revisor y no se puede cambiar.

Haz clic en Aplicar. Para agregar otros usuarios a la lista Compartir, repita estos pasos.
La persona recibe un correo electrónico de notificación con la información compartida. Crear una acción pública

La creación de un recurso compartido público permite el acceso a usuarios externos sin una contraseña.

Haga clic derecho en la carpeta para compartir y seleccione Propiedades.

Vaya a la pestaña Compartir.

En la pestaña Compartir, haga clic en Agregar.

Selecciona Público.

Haga clic en Aceptar. Aparece una opción Público en la pestaña Compartir. El nivel de permiso es Revisor y no se puede cambiar.

Haga clic en Aceptar.
Para acceder a este recurso compartido, los usuarios deben ingresar la URL de la carpeta en un navegador web. La URL es la dirección de su servidor de correo, seguida de / home /:

Por ejemplo, si su servidor de correo es https://mail.example.com/, el nombre de usuario es Joe y la carpeta que está compartiendo es Tareas, la URL para compartir es

https://mail.example.com/home/Joe/Tasks.html.

Cambiar o revocar el acceso

Puede cambiar la función que asignó y eliminar el acceso.

Haga clic derecho en la carpeta que ha compartido y elija Propiedades.

Haga clic en la pestaña Compartir para ver quién tiene permiso para acceder a la carpeta.

Seleccione el nombre y cambie los niveles de permiso. Para cancelar el acceso, seleccione el nombre y haga clic en Eliminar.

Haga clic en Aceptar.
La próxima vez que un concesionario se sincronice con el servidor Zimbra, la carpeta compartida se actualizará con nueva información. Si eliminó el acceso, la carpeta ya no aparece cuando el concesionario se sincroniza. Si ha eliminado un recurso compartido externo o público, el concesionario no puede acceder a la carpeta. Aceptar o rechazar una acción

Cuando se recibe una nueva invitación para compartir, los botones Aceptar compartir y Rechazar compartir aparecen en la pestaña principal (Inicio) de la cinta de la ventana de Outlook. Estos botones también aparecen en la pestaña Zimbra y en la pestaña principal de una ventana de mensaje abierta.

Si hace clic en Aceptar compartir o Rechazar compartir, debe confirmar su acción.

Si elige Aceptar la acción:

    La carpeta compartida se abre en Outlook.

    Se crea un enlace correspondiente en Zimbra Collaboration (de modo que la misma carpeta compartida también está disponible en un segundo perfil de Outlook y el Cliente Web de Zimbra).

    El mensaje compartido se elimina.

Si elige Rechazar el recurso compartido, el mensaje compartido se elimina después de la confirmación.

Compartir mensajes presentes en el perfil de Outlook (antes de una actualización de ZCO) no mostrará los botones Aceptar compartir o Rechazar compartir.

Si el almacenamiento compartido ya está abierto en Outlook, recibirá una notificación y ZCO eliminará el mensaje compartido.

Las copias de los mensajes compartidos salientes (generalmente en Elementos enviados) no muestran los botones Aceptar compartir o Rechazar compartir.
Abrir una carpeta compartida

Si alguien ha compartido su carpeta con usted, siga los pasos a continuación para acceder a ella.

En la barra de menú de Outlook, vaya a Zimbra Ribbon â † 'Abrir el buzón de otro usuario. El cuadro de diálogo de la libreta de direcciones se abre con la Lista global de direcciones que se muestra.

Navegue hasta el nombre de la persona que le ha otorgado acceso. También puede ingresar su dirección de correo electrónico.

Haga clic en Aceptar. La carpeta compartida ahora aparece en la parte inferior de la lista de carpetas.

Para ver todas las carpetas en el buzón que se comparten, vea la Lista de carpetas en el panel de navegación.
Visualización del estado del recurso compartido

Para ver el estado de un recurso compartido, vaya a:

Zimbra Ribbon â † ‘Compartir estado

Al elegir esto, se muestra un cuadro de diálogo que enumera todos los buzones a cuyas carpetas puede acceder. Las entradas se agregan a esta lista cada vez que utiliza Abrir otro buzón de usuario. Los nuevos listados desde la última vez que accedió a Outlook aparecen en rojo.

A la izquierda de la entrada hay una casilla de verificación. Puede desmarcar esto para dejar de compartir todas las carpetas de ese buzón. Luego puede volver a verificarlo para comenzar a compartir. Recuperar elementos eliminados

Recuperar elementos eliminados le permite recuperar elementos eliminados. Esta característica, cuando está habilitada por un administrador del sistema, es útil para recuperar archivos que ha eliminado.

Solo puede recuperar correos electrónicos y contactos. Recuperando un elemento eliminado

Seleccione Zimbra Ribbon â † 'Recuperar.

Seleccione Mensajes o Contactos.

Haga clic en Aceptar.

Seleccione los elementos que desea recuperar.

Haga clic en Aceptar.
Los elementos recuperados aparecen en la carpeta Papelera, desde donde puede moverlos a la carpeta deseada. Permitir que otros envíen correos electrónicos en su nombre

Si desea que otros envíen un correo electrónico en su nombre, debe agregarlos a su lista de Delegados. Una vez que haya agregado un usuario, puede usar la función Delegados para compartir carpetas, como Calendario y Tareas, desde su cuenta de correo electrónico. Compartir permisos y roles se describieron anteriormente.

Seleccione Delegados de la cinta Zimbra.

Haga clic en Agregar para seleccionar sus delegados.

Haga clic en Permisosâ € ¦â € ‹para otorgar permisos al usuario seleccionado.
    De manera predeterminada, un nuevo delegado tiene acceso ilimitado a las carpetas Calendario y Tareas.

Haga clic en Aceptar.
Puede permitir que múltiples delegados envíen un correo electrónico en su nombre. Estos delegados no pueden acceder a su cuenta a menos que les conceda permisos de carpeta. Reenvío de invitaciones

Si reenvía una invitación a la reunión a otros usuarios, también recibirán una notificación para la convocatoria de reunión inicial. Crear un mensaje de correo electrónico fuera de la oficina

Puede enviar una respuesta automática a las personas cuando esté fuera de la oficina. También puede tener diferentes respuestas para las personas que no están en su organización.

Haga clic en la pestaña Zimbra â † 'Asistente para fuera de oficina.

Seleccione Estoy actualmente fuera de la oficina.

En el cuadro de texto, escriba el mensaje que se enviará, como "Estoy de vacaciones del 1 al 6 de junio y no buscaré mensajes".

Establezca las fechas y horas de inicio y finalización para usar este mensaje.

En Remitentes externos hay cuatro opciones en el menú desplegable.

Enviar el mismo mensaje de respuesta

    El mensaje redactado arriba es el mismo que se envía a las personas fuera de su organización.
Enviar respuestas diferentes a todos los que están fuera de mi organización

    Elija esta opción y redacte un mensaje separado que le gustaría enviar a las personas que no están en su organización.
Enviar respuestas diferentes a todos los que están fuera de mi organización a menos que estén en la libreta de direcciones

    Las personas que no están en su organización ni guardan en su libreta de direcciones reciben una respuesta diferente a las personas en su organización.
No envíe respuestas a remitentes externos

    Elija esta opción si no desea que los remitentes externos reciban una respuesta fuera de la oficina.

Haga clic en Aceptar. El mensaje Fuera de la oficina comienza en la fecha y hora que especificó. Los remitentes externos pueden no recibir una respuesta según su elección anterior.
Visualización de la cuota de almacenamiento de correo

Puede almacenar mensajes de correo electrónico en el servidor Zimbra hasta la cuota configurada para su buzón. Los mensajes de correo electrónico, los archivos adjuntos a los mensajes, las libretas de direcciones, los calendarios, las tareas y los elementos de su carpeta Papelera contribuyen a su cuota utilizada. Cuando alcanza su cuota, es posible que no reciba nuevos correos electrónicos.

Puede consultar su cuota de buzón de Zimbra desde Outlook:

Herramientas> Cuota de buzón Recuperando espacio de almacenamiento

Elimine mensajes de correo electrónico y citas antiguas del calendario de su cuenta. Vacíe su papelera después de eliminar elementos para actualizar su espacio de almacenamiento.

Descargue archivos adjuntos de correo electrónico y guárdelos en su computadora localmente. Luego puede eliminar el archivo adjunto de un correo electrónico para liberar espacio en el servidor.

Archivar mensajes en Outlook.

    La función de Autoarchivar de Outlook está deshabilitada para los perfiles y carpetas de Zimbra. Esta característica no debe estar habilitada. El archivado automático elimina los archivos del servidor, incluidos los elementos compartidos con otros. Esta eliminación puede causar pérdida de datos o problemas con citas recurrentes, objetos compartidos y datos históricos del calendario.

    La función de Autoarchivar global de Outlook permanece habilitada para las características de Outlook que no son específicas de Zimbra.
Después de eliminar elementos, use la función Actualizar para verificar la cuota más reciente. Trabajando sin conexión

Cuando trabajas sin conexión:

Solo están disponibles los elementos que están en la ZDB que están en su computadora.

Si configuró las opciones para descargar solo encabezados, solo estarán disponibles los mensajes que haya descargado.

El correo nuevo no se sincroniza con su computadora hasta que se conecte y se conecte al servidor Zimbra.

El correo electrónico que crea mientras trabaja sin conexión se guarda en su Bandeja de salida y se envía automáticamente cuando se conecta al servidor Zimbra.
Mostrando el progreso de Zimbra

Para ver el historial de la actividad de sincronización de ZCO, vaya a Cinta de Zimbra> Mostrar progreso de Zimbra. El diálogo de progreso estándar de Outlook no se aplica a ZCO.

Para ocultar el cuadro de diálogo de progreso cuando no hay actividad de sincronización de ZCO, haga clic en el marcador en la esquina inferior derecha. Sincronizar la lista global de direcciones

La Lista global de direcciones (GAL) contiene detalles de contacto de personas en su organización. De forma predeterminada, una copia de esta lista se almacena en su ZDB para que pueda acceder a estos detalles cuando esté desconectado.

Puede controlar con qué frecuencia se actualiza su GAL local. Vaya a Zimbra Ribbon â † ‘Sincronizar lista global de direcciones.

Las opciones incluyen:

Automático

Su GAL se actualiza cada vez que su buzón se sincroniza.
Manual

Su GAL se actualiza cuando realiza la sincronización manual (por ejemplo, presionando F9)

    Deshabilitado: la actualización está deshabilitada

    Actualizar lista global de direcciones: fuerza una actualización GAL inmediata

    Restablecer lista global de direcciones: Reconstruye su GAL desde cero. Su administrador puede solicitar que reconstruya la GAL después de cambios específicos en la GAL del servidor.

Restablecer su GAL puede tomar una hora o más, dependiendo del tamaño de su organización. Sin embargo, puede seguir utilizando Outlook mientras el restablecimiento está en curso.
Informes de error

Cuando ZCO encuentra un problema al sincronizar un elemento, se lo notifica creando un mensaje llamado Mensaje de falla. Puede controlar si ZCO debe colocar estos mensajes en la Bandeja de entrada o en la carpeta Problemas de sincronización.

Vaya a Zimbra Ribbon> Informes de errores Funciones de soporte

Para acceder a las funciones de soporte, vaya a Zimbra Ribbon> Grupo de soporte

Los elementos en los grupos anteriores se usan solo para la resolución de problemas. Su administrador puede explicarle cómo usarlos si encuentra problemas con ZCO. Informar un problema de ZCO al administrador del sistema

Zimbra Ribbon -> Grupo de soporte -> Registro -> Informar problema *
Un administrador del sistema debe habilitar esta función. Puede usarlo para informar cualquier problema de ZCO que experimente al administrador del sistema.

Haga clic en la opción de menú Informar problema para abrir el cuadro de diálogo Detalle de problema de ZCO. Ingrese los siguientes detalles para ayudar a investigar y resolver su problema.

Título del tema

Un resumen del problema (Max 220 caracteres)
descripcion del problema

detalles sobre cómo surgió el problema y las condiciones que contribuyen (Máx. 2048 caracteres)
Completar estos campos habilita el botón Enviar en el cuadro de diálogo.

Al hacer clic en el botón Enviar, se informará el problema de ZCO al administrador del sistema y se compartirá una copia de los registros operativos de su PC.

Al hacer clic en el botón Cancelar, saldrá de la operación Informar problema, después de obtener su confirmación.
Puede supervisar el estado de la operación de informes en el cuadro de diálogo Progreso de envío / recepción de Zimbra en la tarea Crear y cargar archivo de registro. Por lo general, demora unos minutos en completarse, pero podría tomar más dependiendo del tamaño de los archivos de registro y las velocidades de comunicación. La operación de emisión de informes crea y carga un archivo comprimido en la carpeta ZCOLogs en su maletín. Los administradores del sistema pueden acceder a esta carpeta para identificar el problema. Cambiar los permisos a la carpeta ZCOLogs puede restringir la accesibilidad de un administrador del sistema a esta carpeta. Comprobación de actualizaciones de ZCO

ZCO admite una función llamada Actualización automática. Cuando se inicia Outlook, ZCO se pone en contacto con el servidor para verificar si hay disponible una versión más reciente de ZCO. Obtiene las siguientes opciones cuando hay una actualización disponible.

Mejorar

Descarga la versión más reciente de ZCO, sale de Outlook e instala la última versión.
Omitir esta version

Suprime la actualización automática hasta que la próxima versión esté disponible.
Nunca actualice

Deshabilita la actualización automática
Además de la actualización automática, puede buscar manualmente una versión más nueva en cualquier momento desde Zimbra Ribbon â † ‘Buscar actualizaciones. Buscar actualizaciones también detecta las versiones de ZCO que omitiste usando Omitir esta versión más arriba. Sobre Zimbra

Para obtener información sobre la versión de Zimbra que está utilizando, vaya a Zimbra Ribbon â † ‘Acerca de Zimbra

Aquí puede ver información sobre su versión instalada actualmente de ZCO y obtener un enlace para acceder a documentación adicional.
