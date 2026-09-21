Instalación del Servidor CentOS 7.9 minimal para Zimbra Network en DMZ.
2.1.- Requerimientos Necesarios de hardware para Zimbra Network Standalone
    50 Usuarios
        CPU:    4 cores
        RAM:    8 RAM
        Disco:  5 GB por usuario; total 250 GB

    150 Usuarios
        CPU:    4 cores
        RAM:    8 a 12 GB
        Disco:  750 GB

    500 Usuarios
        CPU     4 cores a 6 cores
        RAM:    12 a 16 GB
        Disco:  3 TB

    1,000 Usuarios
        CPU     6 o 8 cores
        RAM:    16 a 32 GB
        Disco:  5 TB

    2,000 Usuarios a más
        Instalar zimbra en multiservidor (se verá posteriormente)
2.2.- Instalación de CentOS 7
Descargar el CentOS ISO Minimal https://www.centos.org/download

Iniciar la instalación de CentOS con el iso del minimal
    Seleccionar Install CentOS
    Welcome to CentOS
        Spanish     ---> Español (País donde te encuentres)
    Continuar

    Resumen de la Instalación

    KDUMP
            Quitar el check "Habilitar Kdump"
        Listo

    DESTINO DE LA INSTALACIÓN
            Otras Opciones de Almacenamiento
                Check en "Voy a configurar las particiones"
            Discos estandares locales
                doble click en el disco (fondo azul y en check)
            Listo
Si nuestro firmware se inción con BIOS o Legacy, nuestra tabla de particiones será DOS el cual solo soporta hasta 2 TB de disco y hay que crear particiones para el raíz /, el swap y el /opt Al día de hoy, comunmente las máquinas virtuales inician en BIOS.

        Particionar Legacy:
            Crear Nuevos puntos de Montaje
                Combo: Partición estándar

            Click en el ícono + abajo a la izquierda
                Punto de Montaje:   /
                Capacidad deseada:  30G
            Añadir punto de montaje

            Click en el ícono + abajo a la izquierda
                Punto de Montaje:   swap
                Capacidad deseada:  4G
            Añadir punto de montaje

            Click en el ícono + abajo a la izquierda
                Punto de Montaje:   /opt
                Capacidad deseada:  Dejar vacio  (ocupa el espacio restante)
            Añadir punto de montaje

            Seleccionar /opt
                Tipo de Dispositivo:
                    LVM 
                Sistema de archivos:
                    xfs
        Listo
Si tenems un servidor físico actual, este iniciará por defecto con UEFI, lo único que se recomienda aquí es deshabilitar el arranque seguro.

           
        Particionar UEFI:
            Crear Nuevos puntos de Montaje
                Combo: Partición estándar

                Click en el ícono + abajo a la izquierda
                                Punto de Montaje:       /boot/efi
                                Capacidad deseada:      250M
                        Añadir punto de montaje

            Click en el ícono + abajo a la izquierda
                Punto de Montaje:   /
                Capacidad deseada:  30G
            Añadir punto de montaje

            Click en el ícono + abajo a la izquierda
                Punto de Montaje:   swap
                Capacidad deseada:  4G
            Añadir punto de montaje

            Click en el ícono + abajo a la izquierda
                Punto de Montaje:   /opt
                Capacidad deseada:  Dejar vacio  (ocupa el espacio restante)
            Añadir punto de montaje

            Seleccionar /opt
                Tipo de Dispositivo:
                    LVM 
                Sistema de archivos:
                    xfs
        Listo
        Aceptar Cambios 
Una vez particionado el disco, seguimos con la instalación

       
    RED NOMBRE DE EQUIPO

        Click boton de abajo a la derecha "Configurar"
                Ir a la pestaña General
                    Check en conectarse automáticamente a esta red cuando este disponible
                Ir a a la Pestaña **Ajustes IPv4**
                    Método: Manual
                    Click en **Añadir**
                        Dirección:      192.168.2XX.2
                        Mascara de Red:     24
                        Puerta de Enlace:   192.168.2XX.1
                        Servidores DNS:     8.8.8.8 
                    Guardar

            Dar click en el botón Ethernet (que se ponga azul)
        Listo

    Empezar Instalación

    Contraseña de root
        Que aparezca como robusta
        Listo   

Una vez que terminó click en Reiniciar
2.3.- Configurar la tarjeta de red
2.3.1.- Activar la tarjeta de red si no inicia automáticamente
Loguearse por consola como root y ejecutar Para saber el nombre de la tarjeta (ejemplo: eth0)

ip link show
Habilitar arranque automático

sed -i 's/ONBOOT="no"/ONBOOT="yes"/g' /etc/sysconfig/network-scripts/ifcfg-eth0
Reiniciar tarjetas de red

systemctl restart network
Ping a la pasarela

ping 192.168.X.1
Ping a google

ping 8.8.8.8
2.3.2.- Configurar la tarjeta de red si nos olvidamos de configurarla
vi /etc/sysconfig/network-scripts/ifcfg-eth0
TYPE=Ethernet
DEVICE=eth0
BOOTPROTO=static
ONBOOT=yes
IPADDR=192.168.X.2
PREFIX=24
DNS1=8.8.8.8
GATEWAY=192.168.X.1
Reiniciar tarjetas de red

systemctl restart network
Ping a la pasarela

ping 192.168.X.1
Ping a google

ping 8.8.8.8
2.4.- Actualizar Paquetes de CentOS
2.4.1.- Natear el puerto 22 hacia el zimbra
Conectarse al servidor con putty o ssh

ssh root@149.56.218.X
Actualizar todos los paquetes del servidor zimbra

yum -y update
Si actualizó el kernel, reiniciar

reboot
Instalar paquete útiles para el CentOS minimal

yum -y install vim elinks wget net-tools 
Cambiar la apariencia del prompt

echo "PS1='\[\033[1;33m\]\u\[\033[1;31m\]@\[\033[1;34m\]\H:\[\033[1;35m\]\w\[\033[1;31m\]$\[\033[0m\] '" >> /etc/bashrc
2.5.- Instalar y habilitar firewalld
yum -y install firewalld
systemctl start firewalld 
Ver las reglas de firewalld

iptables -S
