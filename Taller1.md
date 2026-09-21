Instalación de Firewall Sophos XG Home.
1.- Descargar el ISO de Sophos XG Home
Loguearse gráficamente al proxmox y abrir navegador Firefox

A.- Crear primero un Sophos ID en https://id.sophos.com/web/register/

B.- Luego acceder a enlace de descargas de Sophos XG https://www.sophos.com/en-us/support/downloads/firewall-installers
Software Installers: Firewall OS Software ISO for Intel Hardware —> esta es la versión genérica instalable en PC, Servidor o Hypervisor
Nos dá opción a descargar el iso SW-18.5.1_MR-1-326.iso

C.- Luego obtener un key de Sophos XG Home en
https://www.sophos.com/es-es/products/free-tools/sophos-xg-firewall-home-edition.aspx

Click en "Póngase manos a la obra"
Poner datos de correo; ahi verificar el Key para la instalación.
2.- Instalar Sophos XG Home
Iniciamos la máquina virtual, damos click en consola
Nos pregunta si queremos instalar y borrar todos los datos: y
Luego de instalar poner «y» para reiniciar.

3.- Configuración de Sophos XG
3.1.- Cambiar la IP privada de Sophos XG
Por proxmox acceder al sophos click en consola
Password: admin
Aceptar Licencia: Accept
Loguearse con password: admin
Opcion 1: Network Configuration
   1: Interface Configuration
      Enter
      Enter
      Set IPv4: y
        En interfaz de LAN cambiar:
          IP por defecto: 172.16.16.16
          Nueva IP:       192.168.Y.1 (IP de LAN FW del Excel)
          Mascara de red: 255.255.255.0 (Emter)
      Set IPv6: n
      Exit: 0
3.2.- Configurar VM Xubuntu para acceder a la configuración de Sophos
A) Iniciar la VM Xubuntu en modo de prueba en Español

B) Poner IP de LAN 192.168.Y.2 poder configurar el Sophos

C) Abrir firefox, poner la IP privada de LAN de sophos
https://192.168.Y.1:4444
Click e Avanzado --> Añadir excepcion
3.3.- Configurar Sophos desde Firefox
A) Cambiar el Idioma: Español.
"Haga click para comenzar"

B) Crear Nueva cuenta de administrador
  Contraseña del admin: Poner una clave fuerte
  Check en "Acepto acuerdo de Licencia", 
Siguiente

C) Conexión a Internet
Configuración Manual 
  Seleccione un puerto para configurar
    Port 2
  Tipo de Interfaz
    Dirección IP estática
  Dirección IP
    51.68.161.X
  Subred
    25 (255.255.255.128)
  Dirección IP de la puerta de enlace
    51.68.161.130
  Nombre de la puerta de enlace
    wan
  Servidor DNS 1
    1.1.1.1
  Servidor DNS 2
    8.8.8.8
  Aplicar 
  Aceptar
Siguiente

D) Nos pide que actualizemos obligatoriamente el Firmware
Le damos Aceptar

E) Volver a entrar a https://192.168.200.1:4444
y continuar configurando

Nombre del Firewall: sophos XG
  Zona Horaria: Elegir su pais
Siguiente

F) Registrar su Firewall (copiarlo del correo de registro)
  Tengo un número de serie existente: XXXXXXXXXX   
Siguiente

G) Sophos ID
  Loguearse con la cuenta previamente creada
    Sign In: 
  Your Device: XXXXXXXXXX   (sale la licencia)
  pasar el validador de google
Siguiente

Si sale error "Access Denied" ir al paso (C) nuevamente y 
en el paso E poner "No deseo Registrar Ahora"

(Si sale "Access Denied" en el registro, se tendrá que hacer desde la Laptop)
-------------------------------------------------------
H) Formulario de completar el registro
  Llenar datos que pide
Siguiente

I) Device Details 
Confirm Registration
    Si sale un error de carita triste sar siguiente y luego volverse a loguear

J) Initiate License Synchronization    (Si se cuelga, refrescar el navegador)
  Nos muestra la validez de las licencia
  quitar check de "participar en mejora de programa del cliente"
--------------------------------------------------------

K) Configuración básica terminada (muestra las vigencias de las licencias)
    quitar check de "participar en mejora de programa del cliente"
    Siguiente

L) Configuración de red LAN
  Quitar "Habilitar DHCP"

M) Protección de Redes
   Siguiente

N) Notificaciones y Copias de seguridad
  Destinatario del correo: correoldesophosid@dominio.com
  Reitente del correo: correoldesophosid@dominio.com
  Siguiente

FIN

Esperar que rebootee

OJO: Si falló el registro hacer el paso 5. y luego contectarse con la laptop a la IP pública 
y continuar desde el paso (E) para sincronizar la licencia
3.4.- Acceder al sophos por la IP privada y habilitar conexión por la WAN
Entar a 
https://192.168.Y.1:4444
user:  admin
clave: la que se asignó

A) Habilitar la conexión por WAN
  Ir a Administración ---> Acceso al dispositivo
  Https: check en "WAN" y "VPN"
Aplicar, Aceptar

B) Cambiar los puertos de acceso al firewall
  Ir a Administración ---> Configuración de administración
    Puerto Https de consola de administración: 10443
    Puerto HTTPS de portal del usuario: 9443
Aplicar, Aceptar

Desde la laptop acceder a la Ip pública del Sophos XG
https://51.68.161.X:10443

4.- Configurar DMZ en Sophos
Nos vamos a Configurar --> RED
En la tarjeta 3 activamos la interfaz
Zona: DMZ
IP: 192.168.X.1  
Mascara: 24
4.1.- Crear regla para dar salida a Internet al servidor Zimbra Network
PROTEGER --> Reglas y Políticas
    Reglas de Firewall 
        Click en "Añadir regla de Firewall"
            Nueva Regla de Firewall
                Nombre de regla:                Salida Zimbra Network
                Posición de la regla:           Arriba
                Grupo:
                    Add to group
                        New Group
                            Nombre de Grupo: Zimbra Network
                Acción:                         Aceptar
                Registrar tráfico de firewall:  CHECK

                ORIGEN
                    Zonas de origen:                DMZ
                    Dispositivos y Redes de Origen:
                        Añadir Nuevo Elemento
                                Crear Nuevo: IP
                                        Nombre: 192.168.X.2_ZimbraNetwork
                                        Direccion IP: 192.168.X.2
                                Guardar
                    Durante la hora programada:     Siempre

                DESTINO y Servicios
                    Zona de Destino:                WAN
                    Redes de Destino:               Cualquiera
                    Servicios:                      Cualquiera

                Crear Regla NAT Vinculada:          Dar Click
                    Nombre de la Regla:             Salida Zimbra Network
                    Posición de la Regla:           Arriba

                    Configuración de traducción
                        Origen Traducido (SNAT):    MASQ 
                    Guardar
            Guardar
4.2.- Crear una regla de NAT hacia el servidor Zimbra Network en DMZ
PROTEGER --> Reglas y Políticas
    Reglas de Firewall 
        Click en "Añadir regla de Firewall"
             Nueva Regla de Firewall
                Nombre de regla:                Nat Zimbra Network
                Descripción:                    Nat Zimbra Network SSH, HTTP y HTTPS
                Posición de la regla:           Arriba
                Grupo de Reglas:                Zimbra Network
                Acción:                         Aceptar
                Registrar tráfico de firewall:  CHECK

                ORIGEN
                    Zonas de origen:                WAN
                    Dipositivos y redes de origen:  Cualquiera
                    Durante la hora programada:     Siempre

                DESTINO y Servicios
                    Zona de Destino:                DMZ
                    Redes de Destino:
                        Añadir Nuevo Elemento
                                Crear Nuevo: IP
                                        Nombre: 51.68.161.X
                                        Direccion IP: 51.68.161.X
                                Guardar
                    Servicios:                      SSH, HTTP, HTTPS                    

            Guardar

    Reglas de NAT 
        Click en "Añadir regla de NAT"
            Nueva Regla NAT
                Nombre de regla:                Nat Zimbra Network
                Descripción:                    Nat Zimbra Network SSH, HTTP y HTTPS
                Posición de la regla:           Arriba

            Configuración de traducción
                Origen original:                Cualquiera
                Destino original:               51.68.161.X
                Servicio original:              SSH, HTTP, HTTPS

            Destino Traducido (DNAT):           192.168.X.2_ZimbraNetwork

            Servicio Traducido (PAT):           Se usa solo cuando el puerto de reenvío es distinto al de Servicio Original.

            Criterios de coincidencia de interfaz
                Interfaz de entrada:            Port2
                Interfaz de salida:             Cualquiera

        Guardar
4.3.- Borrar regla autogenerada de Sophos de Intercepción SMTP
Acceder al Firewall

PROTEGER --> Reglas y Políticas 
    Reglas de Firewall
        Regla 6
            Auto added.... 
                Click en "..."
                    Eliminar
