Configuración de Dominio de Internet y Registros Públicos de DNS
Asumimos que ya instalamos previamente un firewall UTM, donde tenemos la Ip pública nateando hacia un servidor CentOS 7 en zona DMZ para instalar y configurar nuestro servidor Zimbra. Primero debemos registrar un dominio público y configurar un DNS público.

Tabla de Contenidos
Conceptos Básicos de Resolución de Nombres
Nombre de Host del equipo local
Resolución Local de Hostnames y dominios
Resolución de hosts y dominios en red
Registros de DNS y Cliente DNS
Cliente de DNS
Registros de DNS
Comprobación de registros DNS con dig
Registro A
Registro NS
Registro MX
Registro PTR
Registro TXT
Verificación de todos los registros mediante transferencias de zonas
Configuración del dominio público de Internet, selección de la Ip pública
Seleccionar una Ip pública
Registrar un dominio público gratuito en freenom
Configuración de Registros de DNS en Freenom
Configuración de registros A
Crear registros A para www y mail
Crear registro MX y SPF para el dominio
Configuración de Registro PTR
1.- Conceptos Básicos de Resolución de Nombres
Los nombres de host se pueden resolver por 2 métodos

De forma local, poniendo nombre al equipo con hostnamectl y editando el archivo /etc/hosts
En red, con el uso de un servidor DNS y configurando el cliente en /etc/resolv.conf
1.1- Nombre de Host del equipo local
La resolución local de host del equipo, se hace con el nombre de host y se asigna con el comando hostnamectl

hostnamectl set-hostname mail.aulautil.tk
exit 

#Abrir un nuevo terminal y comprobar el nuevo nombre con
hostnamectl
1.2.- Resolución Local de Hostnames y dominios
La resolución local de nombres de hosts y dominios se hace con el archivo /etc/hosts Ej.

vim /etc/hosts
127.0.0.1 localhost localhost.localdomain
192.168.3.201 mail.aulautil.tk mail
190.81.56.202 www.cualquierdominio.com www.quesemeocurra.com

ping mail.aulautil.tk
1.3.- Resolución de hosts y dominios en red
Para resolver nombres de hosts y dominios en red, usamos un servidor DNS. Los servidores DNS puede estar desplegados en diferentes zonas

1.3.1.- DNS WAN:
Es el servidor DNS que resuelve con IP públicas los nombres de nuestros dominios Ej:

mail.aulautil.tk –> 51.68.161.X

1.3.2.- DNS DMZ:
Es el servidor DNS que resuelve con IP privadas los nombres de los servidores de la DMZ solo se usa en DMZ, no debe ser usado en la LAN

Ej: mail.aulautil.tk –> 192.168.3.201

1.3.3.- DNS LAN:
Es el servidor DNS que resuelve con IP privadas los nombres de los pcs y servidores de la LAN; así como de los servidores DMZ con sus IP privadas; el DNS de LAN no debe ser usado en la DMZ

Ej:
mail.aulautil.tk  --> 192.168.3.201
pc1.aulautil.tk  --> 192.168.100.20
2.- Registros de DNS y Cliente DNS
2.1.- Cliente de DNS
El cliente DNS se configura en el archivo /etc/resolv.conf

vim /etc/resolv.conf
nameserver 8.8.8.8
En CentOS 7 el archivo resolv.conf es generado por la utilidad de red, por eso hay que editar el archivo de configuración de red y agregar el parámetro DNS1

Ej: Tenemos una tarjeta de red llamada ens18 (esto se sabe con el comando ip addr show)

vim /etc/sysconfig/network-scripts/ifcfg-ens18
...
DNS1=8.8.8.8
....

systemctl restart network
cat /etc/resolv.conf
2.2.- Registros de DNS
Registro	Valor
A	Zona de dominio (ej: mail.dominio.com –> 149.56.218.3)
NS	Name Server (Servidores DNS del dominio)
SOA	Servidor DNS principal (Server of Authorization)
MX	Servidores de correo (Mail Exchange)
TXT	Información adicional del dominio (ej: SPF definir las IP que pueden enviar correo del dominio)
CNAME	Alias de una zona de dominio (ej: web –> www.dominio.com)
PTR	Resolución Inversa (ej: 149.56.218.3 –> mail.dominio.com)
2.3.- Comprobación de registros DNS con dig
Instalar dig

yum -y install bind-utils
Dig tiene las siguientes Secciones

**QUESTION SECTION**
  consulta que se está realizando
**ANSWER SECTION**
  respuesta (si la hubiera de la consulta)
**AUTHORITY SECTION**
  detalle de los servidores de autorización (SOA)
**ADDITIONAL SECTION**
  información adicional de zonas y otros
Ejercicios:

2.4.- Registro A
Verificar la zona de dominio www.dominio.com

dig www.dominio.com

;; QUESTION SECTION:
;www.dominio.com.              IN      A       # consulta

;; ANSWER SECTION:
www.dominio.com.       5       IN      A       190.102.150.200   # respuesta
2.5.- Registro NS
Verificar los DNS Server de dominio.com

dig NS dominio.com

; EDNS: version: 0, flags:; udp: 4096
;; QUESTION SECTION:
;dominio.com.                  IN      NS

;; ANSWER SECTION:
dominio.com.           2257    IN      NS      ns2.telmex.net.pe.
dominio.com.           2257    IN      NS      ns1.telmex.net.pe.
2.6.- Registro MX
Verificar los servidores de correo de dominio.com

dig MX dominio.com

;; QUESTION SECTION:
;dominio.com.                  IN      MX

;; ANSWER SECTION:
dominio.com.           6245    IN      MX      10 mx1.dominio.com.
dominio.com.           6245    IN      MX      10 mx2.dominio.com.
dominio.com.           6245    IN      MX      10 mx4.dominio.com.
dominio.com.           6245    IN      MX      10 mx5.dominio.com.
dominio.com.           6245    IN      MX      20 mx3.dominio.com.
dominio.com.           6245    IN      MX      20 mx6.dominio.com.
Ejemplo de Configuración de los Registros MX
Balanceo de Carga
dominio.com.    IN MX 10 ns.dominio.com.
dominio.com.    IN MX 10 ns1.dominio.com.
Alta Disponibilidad
dominio.com.    IN MX 10 ns.dominio.com.
dominio.com.   IN MX 20 ns1.dominio.com.
2.7.- Registro PTR
El PTR verifica que una IP tenga un nombre de hosts, normalmente se aplica a los servidores de correo

Primero verificamos cuales son los servidores de correo de un dominio

dig MX dominio.com

dominio.com.        6245    IN  MX  10 mx1.dominio.com.     
Ahora preguntamos por el registro A de uno de los servidores de correo

dig mx1.dominio.com

;; QUESTION SECTION:
;mx1.dominio.com.              IN      A

;; ANSWER SECTION:
mx1.dominio.com.       1310    IN      A       181.65.173.25
Vemos que la IP de mx1.dominio.com es 181.65.173.25 pasamos ahora a preguntar por el PTR de ese IP

dig -x 181.65.173.25

;; QUESTION SECTION:
;25.173.65.181.in-addr.arpa.    IN      PTR

;; ANSWER SECTION:
25.173.65.181.in-addr.arpa. 3596 IN     PTR     mx1.dominio.com.
2.8.- Registro TXT (SPF)
Verificar si hay registro SPF en el dominio dominio.com

dig TXT dominio.com

;; QUESTION SECTION:
;dominio.com.                  IN      TXT

;; ANSWER SECTION:
dominio.com.           7199    IN      TXT     "v=spf1 mx include:spf.masterbase.com ~all"
Para crear un registro SPF ir a www.spfwizard.net

Verificar TODOS los registros de DNS del dominio dominio.com

dig ANY dominio.com

;; QUESTION SECTION:
;dominio.com.           IN  ANY

;; QUESTION SECTION:
;dominio.com.                  IN      ANY

;; ANSWER SECTION:
dominio.com.           6926    IN      TXT     "v=spf1 mx include:spf.masterbase.com ~all"
dominio.com.           5362    IN      MX      20 mx6.dominio.com.
dominio.com.           5362    IN      MX      10 mx1.dominio.com.
dominio.com.           5362    IN      MX      10 mx2.dominio.com.
dominio.com.           5362    IN      MX      10 mx4.dominio.com.
dominio.com.           5362    IN      MX      10 mx5.dominio.com.
dominio.com.           5362    IN      MX      20 mx3.dominio.com.
dominio.com.           1088    IN      NS      ns1.telmex.net.pe.
dominio.com.           1088    IN      NS      ns2.telmex.net.pe.
Verificar si hay zona de dominio configurado para el dominio raiz dominio.com

dig dominio.com

;; QUESTION SECTION:
;dominio.com.                  IN      A

;; AUTHORITY SECTION:
dominio.com.           1734    IN      SOA     ns1.telmex.net.pe. dnsmaster.telmex.net.pe. 2014101302 5400 600 90000 7200
Vemos que no está configurado el dominio raíz como zona A (mala práctica)

2.9.- Verificación de todos los registros mediante transferencias de zonas
dig axfr @DNSSERVER DOMINIO
dig axfr @ns1.telmex.net.pe dominio.com
3.- Configuración del dominio público de Internet, selección de la Ip pública
Los dominios públicos se adquieren en un proveedor de Dominios (register) Godaddy : com, org, net, cloud, etc … RCP : pe, com.pe, org.pe … https://www.freenom.com : tk, ml … (dominios gratuitos por 1 año)

3.1.- Seleccionar una Ip pública
Las Ip pública para el servidor de correo Zimbra deben estar limpia, usaremos como ejemplo la IP 51.68.161.X

Adicionalmente para verificar si la Ip está en listas negras ir a: MultiRBL http://multirbl.valli.org/

Y a mxtoolbox https://mxtoolbox.com/blacklists.aspx

También debemos configurar en el Firewall perimetral:

Un Nat de entrada (DNAT) desde la Ip pública hacia la Ip privada del servidor zimbra (Ej: 192.168.3.201). Ej con iptables:
# smtp
iptables -t nat -I PREROUTING -p tcp -d 51.68.161.X --dport 25 -j DNAT --to 192.168.3.201:25
# Https
iptables -t nat -I PREROUTING -p tcp -d 51.68.161.X --dport 443 -j DNAT --to 192.168.3.201:443
Un Nat de salida (SNAT) desde la Ip privada del servidor Zimbra para que salga a la WAN con la IP pública asignada. Ej con iptables:

iptables -t nat -I POSTROUTING -s 192.168.3.201 -j SNAT --to 51.68.161.X
3.2.- Registrar un dominio público gratuito en freenom
Crear una cuenta en [https://www.freenom.com](https://www.freenom.com)    (usar la cuenta google)
Ir a la derecha superior, click en registrarse
Loguearse con cuenta google, facebook, o ms
3.2.1.- Registrar un nuevo dominio
Service --> Register a New Domain   ej: aulautil.tk
Selected  y luego checkout
Period:  12 Months Free   ---> Continue
LLENAR DATOS del Formulario
Dar checkc en: I have read and agree to the Terms & Conditions
Finalizar con Complete Order
Click here to go to client area
3.3.2.- Comprobar Exitoso Registro del Dominio
Services –> My Domains

Nota Si no podemos registrar un dominio en Freenom, debemos comprar un dominio (.xyz, .club, .shop) en

https://namecheap.com
https://godaddy.com
4.- Configuración de Registros de DNS en Freenom
Una vez creado el dominio en FreeNom, comprobamos los servidores DNS creados por defecto para el dominio, en este caso son los servidores DNS de Freenom

dig NS aulautil.tk @8.8.8.8
4.1.- Crear registro A para mail
apuntamos a la misma IP pública del VPS

Ej: Registro A para mail.aulautil.tk
        Ir a Services --> My Domains
        Elegir aulautil.tk ----> Click en 'Manage Domain'

        Add Records
        Name:   mail
        Type:   A
        TTL:    3600 (valor por defecto)
        Target: 51.68.161.X
Comprobar ahora los registros con dig

dig mail.aulautil.tk @8.8.8.8
4.2.- Crear registro MX y SPF para el dominio
4.2.1.- Crear un registro MX en Freenom para recibir correo
        Ir a Services --> My Domains
        Elegir aulautil.tk ----> Click en 'Manage Domain'

        Luego click en la pestaña "Manage Frenom DNS"

        Add Records  
           Name                  Type      TTL  Target              Priority
           aulautil.tk.          MX       3600  mail.aulautil.tk.   10

Comprobar con dig

dig MX aulautil.tk @8.8.8.8
4.2.2.- Crear un registro SPF para el dominio para enviar correo
        Ir a Services --> My Domains
        Elegir aulautil.tk ----> Click en 'My Domains'

        Luego click en la pestaña "Manage Frenom DNS"

        Add Records  
          Name                  Type      TTL  Target             
          aulautil.tk.          TXT       3600  "v=spf1 mx a ip4:51.68.161.Y -all"

Comprobar con dig

dig TXT aulautil.tk @8.8.8.8
5.- Configuración de Registros de DNS en NameCheap
Una vez creado el dominio en Namecheap, comprobamos los servidores DNS creados por defecto para el dominio, en este caso son los servidores DNS de Namecheap

dig NS aulautil.club @8.8.8.8
5.1.- Crear registro A para mail
apuntamos a la misma IP pública del VPS

Ej: Registro A para mail.aulautil.club
        Ir a Domain List
        Elegir aulautil.club ----> Click en 'Manage'
            Ir a "Advanced DNS"
            Hosts Records
                Clik "Add New Record"
                    Type:               "A Record"
                    Host:               mail
                    IP Address:    51.68.161.X
                    TTL:                Automatic
                    Click en ícono "CHECK"
Comprobar ahora los registros con dig

dig mail. aulautil.club @8.8.8.8
5.2.- Crear registro MX y SPF para el dominio
5.2.1.- Crear un registro MX en Namecheap para recibir correo
Ej: Registro MX para aulautil.club
        Ir a Domain List
        Elegir aulautil.club ----> Click en 'Manage'
            Ir a "Advanced DNS"
                Mail Setting "Custom MX"
                    "MX Record"
                    Host:               @
                    Value:              mail.aulautil.club
                    Priority:           10
                    TTL:                 Automatic
                    Click en "Save ALL Changes"
Comprobar con dig

dig MX  aulautil.club @8.8.8.8
5.2.2.- Crear un registro SPF para el dominio para enviar correo
Ej: Registro A para mail.aulautil.club
        Ir a Domain List
        Elegir aulautil.club ----> Click en 'Manage'
            Ir a "Advanced DNS"
            Hosts Records
                Clik "Add New Record"
                    Type:               "TXT Record"
                    Host:               @
                    Value:              v=spf1 a mx +ip4:51.68.161.X -all
                    TTL:                Automatic
                    Click en ícono "CHECK"
Comprobar con dig

dig TXT  aulautil.club @8.8.8.8
6.- Configuración de Registro PTR
El PTR debe configurarse; solicitando al Proveedor de Internet que ponga un nombre de host a la IP pública. Es probable que si contratamos servidores cloud, tengamos una opción para poner el PTR a las IP públicas.

Para comprobar si el proveedor creó la PTR de nuestra IP pública ejecutamos

dig -x 51.68.161.X @8.8.8.8
