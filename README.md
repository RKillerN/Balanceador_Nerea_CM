# Balanceador_Nerea_CM
En esta práctica, se pretende desplegar un sistema en tres niveles en AWS, con las siguientes condiciones:

La primera capa consiste en un Balanceador de Carga de Apache, el cual tiene acceso exclusivo a internet y está configurado para comunicarse únicamente con la capa de backend.

La segunda capa, denominada "Backend", comprende dos máquinas Apache que alojan la aplicación web. Estas máquinas pueden establecer comunicación tanto con el balanceador de carga como con el servidor de base de datos. No cuentan con acceso directo a internet.

La tercera capa, representada por el servidor MariaDB, está configurada para comunicarse exclusivamente con la capa de backend y carece de acceso directo a internet.

Esta configuración busca ser diseñada para tener seguridad y funcionalidad del despliegue.

#Creación de las Instancias

Para establecer una VPC en AWS con tres subredes usando la gama de direcciones IP 192.168.1.0/26, procede de la siguiente manera:

Crea una nueva VPC, en el apartado de VPC de Amazon Web Services, especificando el rango de direcciones IP como 192.168.1.0/24. A continuación, definimos tres subredes dentro de esta VPC, distribuyendo las direcciones IP según sea necesario. Este proceso te permitirá tener una infraestructura de red organizada con tres subredes distintas en la nube de AWS.
![image](https://github.com/RKillerN/Balanceador_Nerea_CM/assets/146434664/61c4a412-4d39-46e4-bcd8-60c30d03fe63)
Al crear la VPC, asignaremos un nombre identificativo y especificaremos las direcciones IPv4 para la red de la VPC, excluyendo el uso de IPv6.
![image](https://github.com/RKillerN/Balanceador_Nerea_CM/assets/146434664/4db365f6-f326-464f-b6ea-cbc041cbca43)

## Subredes
En la sección de subredes dentro de VPC, generaremos las subredes requeridas para esta práctica.
![image](https://github.com/RKillerN/Balanceador_Nerea_CM/assets/146434664/d3b5e0d1-8d17-4387-8e5c-19eb79afc889)

En este apartado indicaremos la VPC que vamos a fragmentar en subredes.
![image](https://github.com/RKillerN/Balanceador_Nerea_CM/assets/146434664/aa49f32e-5993-4c94-8924-cd116312abac)

En la imagen, identificamos el nombre de cada subred y llevamos a cabo la creación de estas. La primera subred se tendrá el rango 192.168.1.0/26, la segunda con 192.168.1.64/26, y la tercera con 192.168.1.128/26. Este paso asegura la disponibilidad de tres subredes con rangos de direcciones IP en la estructura de la red.
![image](https://github.com/RKillerN/Balanceador_Nerea_CM/assets/146434664/7233cfb1-6172-483d-8cc3-8d0156efa0b2)

Una vez creada la VPC junto con sus subredes, avanzaremos al lanzamiento de las instancias. En la sección de EC2, específicamente en el área de "Instancias", iniciaremos el proceso de creación de una nueva instancia. Este paso nos permitirá desplegar las máquinas virtuales necesarias para nuestra infraestructura.
![image](https://github.com/RKillerN/Balanceador_Nerea_CM/assets/146434664/4c54af50-9d7d-4da0-a8cc-083ef8c5ec3f)


En priner lugar, asignaremos un nombre a la instancia, el cual debe estar directamente relacionado con la estructura y función previstas para la máquina. 
![image](https://github.com/RKillerN/Balanceador_Nerea_CM/assets/146434664/464b1fa3-f186-4886-8461-9cdef52409a0)
En el segundo paso, visualizamos la sección de AMIs, donde seleccionaremos la imagen de máquina virtual (AMI) correspondiente a Debian 12.
![image](https://github.com/RKillerN/Balanceador_Nerea_CM/assets/146434664/df6d020c-a113-4e8a-9329-74b682693184)

Luego, en la configuración de red, procedemos a editarla, especificando la VPC que emplearemos junto con la subred correspondiente. En este escenario, asignaremos la primera subred a la configuración del balanceador, la segunda subred al entorno de backend, y la última subred al servidor de base de datos.
![image](https://github.com/RKillerN/Balanceador_Nerea_CM/assets/146434664/0e61d0f5-8501-4c38-862f-07cb1acda416)
El script se realiza a través del servicio "User Data". Este servicio te permite ejecutar scripts o comandos al iniciar la instancia EC2. Al lanzar la instancia, puedes proporcionar directamente los datos de usuario que AWS ejecutará durante el inicio. Estos datos pueden incluir scripts escritos en Bash, PowerShell, Python u otros lenguajes de scripting compatibles con el sistema operativo de la instancia. Este enfoque facilita la personalización y configuración automática de instancias según tus necesidades específicas. (Aunque se realizó el aprovisionamiento a las instancias no se llegaron a provisionar)
![image](https://github.com/RKillerN/Balanceador_Nerea_CM/assets/146434664/7daba501-45ce-46a1-a8ac-e5111fa0861f)

Script de provisionamiento pensado para el balanceador de carga:
#!/bin/bash

#Actualizar el sistema e instalar Apache en Debian
sudo apt update
sudo apt install -y apache2

#Configurar el servicio Apache
sudo systemctl start apache2
sudo systemctl enable apache2

#Instalar y configurar el balanceador de carga
sudo apt install -y libapache2-mod-proxy-html
sudo a2enmod proxy
sudo a2enmod proxy_balancer
sudo a2enmod lrewrite
sudo a2enmod deflate
sudo a2enmod headers
sudo a2enmod proxy_connect
sudo a2enmod proxy_html
sudo a2enmod lbmethod_byrequest

#Configurar el archivo de configuración de Apache para el balanceador de carga
sudo cp /etc/apache2/sites-available/000-default.conf /etc/apache2/sites-available/practica3lampner.conf
echo "
<Proxy balancer://mycluster>
    BalancerMember http://192.168.1.84
    BalancerMember http://192.168.1.86
</Proxy>

ProxyPass / balancer://mycluster/" | sudo tee -a /etc/apache2/sites-available/practica3lampner.conf

#Reiniciar el servicio Apache para aplicar cambios
sudo systemctl restart apache2

Script de aprovisionamiento de las máquinas de backend:
#!/bin/bash

#Buscar actualizaciones
sudo apt update -y
#Actualizar
sudo apt upgrade -y
#Descargar los servicios necesarios
sudo apt install mariadb_client apache2 libapache2-mod-php openssl git -y
#Ir a /var/www/html y descargar el repositorio de git hub
cd /var/www/html
sudo git clone https://github.com/josejuansanchez/iaw-practica-lamp.git 
sudo mv iaw-practica-lamp.git usuario
#Configurar permisos
sudo chown -R www-data:www-data /var/www/iaw-practica-lamp
sudo chmod -R 755 /var/www/iaw-practica-lamp
cd /etc/apache2/sites-avialable
sudo cp 000-default.conf pilalamp.conf
#Configurar virtual host de Apache
echo "
<VirtualHost *:80>
    ServerAdmin webmaster@localhost
    DocumentRoot /var/www/usuario/src
</VirtualHost>" | sudo tee /etc/apache2/sites-available/pilalamp.conf
#Habilitar el sitio y reiniciar Apache
sudo a2ensite 000-default.conf
sudo systemctl restart apache2
sudo scp /var/www/html/usuario/db/database.sql admin@192.168.1.142 .

script mariadb
#!/bin/bash
#Instalar MariaDB
sudo apt-get update
sudo apt-get install -y mariadb-server

#Configurar MariaDB
sudo mysqladmin -u root password $DB_ROOT_PASSWD

#Crear la base de datos y el usuario
sudo mysql -u root lamp_db < database
sudo mysql -u root <<MYSQL_SCRIPT
CREATE USER 'lampuser1'@'192.168.1.84' IDENTIFIED BY '1234';
CREATE USER 'lampuser2'@'192.168.1.86' IDENTIFIED BY '1234';
GRANT ALL PRIVILEGES ON lamp_db.* TO 'lampuser1_user'@'localhost';
GRANT ALL PRIVILEGES ON lamp_db.* TO 'lampuser2_user'@'localhost';
FLUSH PRIVILEGES;
MYSQL_SCRIPT
#Reiniciar MariaDB para aplicar cambios
sudo systemctl restart mariadb

## Grupos de seguridad de las instancias 

Cuando seleccionamos una instancia en AWS y accedemos al apartado de seguridad, podemos personalizar las reglas del grupo de seguridad asociado a la instancia para adaptarlas a la infraestructura deseada. Esta configuración es crucial para definir qué tráfico se permite o deniega en la instancia.
![image](https://github.com/RKillerN/Balanceador_Nerea_CM/assets/146434664/4907a3ac-24ba-4736-8182-dcc31a472b62)
Al pinchar debajo de grupos de seguridad podremos editar estas reglas:
![image](https://github.com/RKillerN/Balanceador_Nerea_CM/assets/146434664/855b9a33-e471-45c1-bb0a-ef468e4bb6e2)


Una vez que hemos revisado cómo se configuran las reglas de seguridad en AWS, procederemos a editar las reglas de cada una de las instancias para adaptarlas a las necesidades específicas de nuestra infraestructura. Este paso es esencial para garantizar un entorno seguro y funcional. 


Para la máquina balanceadora, se configuran las reglas de seguridad que permitan conexiones SSH, HTTPS y HTTP desde cualquier dirección de red. En las reglas de entrada, añade permisos para SSH (puerto 22) desde 0.0.0.0/0, así como reglas para HTTP (puerto 80) y HTTPS (puerto 443) desde cualquier dirección IP.
![image](https://github.com/RKillerN/Balanceador_Nerea_CM/assets/146434664/8fd1f767-b57f-4554-8973-e42dc35d0edc)


En las máquinas de backend, se deben configurar las reglas de seguridad de manera que solo permitan conexiones SSH, HTTP y HTTPS desde la subred a la que pertenece el balanceador de carga. Además, se debe restringir el acceso a la base de datos únicamente desde la subred donde se encuentra el servidor MariaDB.

Esta configuración será realizada a cada instancia de backend. En las reglas de entrada, establece permisos para SSH (puerto 22), HTTP (puerto 80) y HTTPS (puerto 443) únicamente desde la subred del balanceador.

Para la conexión con la base de datos, configura una regla que permita el acceso a MariaDB desde la subred del servidor MariaDB desde el puerto 3306 y niega cualquier otro tráfico entrante.
![image](https://github.com/RKillerN/Balanceador_Nerea_CM/assets/146434664/f8906d17-f207-468b-867d-32c15275fb28)

En la instancia de MariaDB, la configuración de las reglas de seguridad se debe restringir el acceso a conexiones SSH y al servicio MySQL/Aurora exclusivamente desde la subred de backend. Este enfoque garantiza una capa adicional de seguridad al limitar la exposición de la base de datos a conexiones específicas.

Accede a la consola de AWS, selecciona la instancia de MariaDB y edita las reglas de seguridad asociadas al grupo de seguridad de esa instancia. Dentro de las reglas de entrada, configura una regla que permita conexiones SSH (puerto 22) y conexiones MySQL/Aurora (puerto predeterminado, generalmente 3306) exclusivamente desde la subred de backend.
![image](https://github.com/RKillerN/Balanceador_Nerea_CM/assets/146434664/1d8696fc-42a8-487f-81a5-566885fb49bc)

Creación de una IP elástica para el balanceador de carga

Dentro de la configuración de la VPC en AWS, se incorporará una Dirección IP Elástica específicamente destinada al balanceador de carga. Esta elección garantiza que la IP pública asociada al balanceador permanezca constante a lo largo del tiempo, evitando cambios que podrían afectar a la accesibilidad de servicios vinculados al dominio que procederemos a su creación.
![image](https://github.com/RKillerN/Balanceador_Nerea_CM/assets/146434664/47e25ec3-7707-4479-bae3-ed13290b6921)

Accedemos al apartado de "Asignar IP Elástica", donde se despliega una ventana correspondiente. Al pulsar la opción de asignación, la IP Elástica se generará automáticamente, simplificando así el proceso de creación y configuración. 
![image](https://github.com/RKillerN/Balanceador_Nerea_CM/assets/146434664/48e44212-55f8-48ea-b817-ae78c88c5d3c)

Dentro de la sección "Acciones", ingresaremos al apartado de "Asociar Dirección IP Elástica". Al hacerlo, se desplegará una ventana que nos permitirá asignar la Dirección IP Elástica a nuestro balanceador de carga.
![image](https://github.com/RKillerN/Balanceador_Nerea_CM/assets/146434664/35442ed4-1906-4e8e-819b-90243dcf8e4a)

EEn la ventana correspondiente, en la acción asociar IP elástica a una instancia. En la sección inferior de la misma, procedemos a la asociación de la IP Elástica con nuestro balanceador de carga. Ya con todo esto nuestra instancia contará con la IP elástica creada
![image](https://github.com/RKillerN/Balanceador_Nerea_CM/assets/146434664/3263e4c8-3ffa-45bf-a8f0-4b075cd76e3c)

## Conexión a las máquinas para terminar su configuración 
Seleccionamos la instancia del Balanceador, la única capaz de conectarse mediante ssh al backend y a su vez estas son las únicas capaces de acceder a la base de datos
![image](https://github.com/RKillerN/Balanceador_Nerea_CM/assets/146434664/c3f6ccf2-a6ac-4f1a-9a9f-2f01c7687fee)

## Balanceador de carga
Desde una página gratuita llamada NoIp, estableciendo un nombre del host, el dominio y la dirección iP elástica del Balanceador.
![image](https://github.com/RKillerN/Balanceador_Nerea_CM/assets/146434664/992a397f-3be5-4134-854d-a53dd641fc12)

La instalación y configuración de Certbot con el comando certbot --apache se realiza para gestionar certificados SSL/TLS en un entorno Apache. Este proceso permite habilitar conexiones seguras mediante HTTPS en el servidor web.

Primero, en la instancia de Apache, se instala Certbot utilizando el gestor de paquetes adecuado para el sistema operativo. Por ejemplo, en sistemas basados en Debian o Ubuntu, se puede utilizar el comando sudo apt-get install certbot.

Luego, la configuración del certificado se realiza con el comando "certbot --apache". Este comando interactivo guía al usuario a través de un proceso donde se selecciona el dominio o dominios para los cuales se desea obtener el certificado SSL. Certbot automáticamente modifica la configuración de Apache para habilitar HTTPS y configura la renovación automática de los certificados. Seguiremos los pasos que se nos indican asociando un correo, aceptando los términos del servicio y opcional aceptar el envío de publicidad al correo establecido, en este caso le indicamos que no, despues introducimos el dominio creado con NoIP y se crearía el certificado.
![image](https://github.com/RKillerN/Balanceador_Nerea_CM/assets/146434664/de6a87c5-4073-460a-a2cf-79cbb5df7a0d)

![image](https://github.com/RKillerN/Balanceador_Nerea_CM/assets/146434664/d2aeb3b2-e179-4de1-a378-cdf0dec1afc4)

Utilizamos el comando "sudo a2dissite" para desactivar el sitio predeterminado, que en este caso es "default-ssl.conf". Posteriormente, habilitamos el sitio previamente creado en el script de aprovisionamiento, con el comando "sudo a2ensite", el archivo "practica3lampner.conf". Finalmente, ejecutamos nuevamente el comando "sudo certbot --apache".
![image](https://github.com/RKillerN/Balanceador_Nerea_CM/assets/146434664/9f03ceda-1140-4444-9152-1bf247e50381)

Después de llevar a cabo los pasos anteriores, reiniciamos el servicio de Apache2 mediante el comando "sudo service apache2 restart" y verificamos su estado para asegurarnos de que esté activo.
![image](https://github.com/RKillerN/Balanceador_Nerea_CM/assets/146434664/aa6799cf-a10b-4787-9591-65cbec69d84e)

Con todas las configuraciones realizadas, al ingresar el nombre de dominio en cualquier navegador, debería visualizarse la página por defecto de Apache.
![image](https://github.com/RKillerN/Balanceador_Nerea_CM/assets/146434664/015f9eaa-7887-4c64-9402-bcc085f52286)

## Realización de un desplieque de una aplicación de usuaarios, "Simple LAMP web app"



config php
![image](https://github.com/RKillerN/Balanceador_Nerea_CM/assets/146434664/fb7df38c-b357-479b-9bfb-23e5be83aa16)

bin address y cargar base de datos

![image](https://github.com/RKillerN/Balanceador_Nerea_CM/assets/146434664/1900c814-130b-4305-894c-85b4eb11e23a)

![image](https://github.com/RKillerN/Balanceador_Nerea_CM/assets/146434664/4f195b5e-56bb-44c4-8cb9-a73843a9c4fb)

pagina principal
![image](https://github.com/RKillerN/Balanceador_Nerea_CM/assets/146434664/8f458364-d827-4eaa-bf05-26c98b80e3aa)ç
añadir dato
![image](https://github.com/RKillerN/Balanceador_Nerea_CM/assets/146434664/ebb3adac-1b68-424c-9c33-d16ed13b4866)

![image](https://github.com/RKillerN/Balanceador_Nerea_CM/assets/146434664/297b37b7-7f1f-4b1a-a9b0-2da0512d2b9c)
comprobar que se añaden en los paache y mariadb

![image](https://github.com/RKillerN/Balanceador_Nerea_CM/assets/146434664/027335e7-49b9-4ea2-8da2-7528cdab373b)


![image](https://github.com/RKillerN/Balanceador_Nerea_CM/assets/146434664/1014271f-317d-4913-9db6-544462c72c8b)


![image](https://github.com/RKillerN/Balanceador_Nerea_CM/assets/146434664/f28b6c92-f9eb-4ca3-814e-63ce121eb4b2)

![image](https://github.com/RKillerN/Balanceador_Nerea_CM/assets/146434664/57da1517-d2a0-4c96-829f-ed07b754e198)

![image](https://github.com/RKillerN/Balanceador_Nerea_CM/assets/146434664/1ddd9ec1-e3da-473b-b995-763d0640cfb4)

![image](https://github.com/RKillerN/Balanceador_Nerea_CM/assets/146434664/f5addb18-7ac8-4df5-b85c-db568a021b58)
![image](https://github.com/RKillerN/Balanceador_Nerea_CM/assets/146434664/5440ff66-68a9-432b-a8d8-6cf192c210d2)
![image](https://github.com/RKillerN/Balanceador_Nerea_CM/assets/146434664/80b370c2-a178-4325-9645-2340bef79631)



![image](https://github.com/RKillerN/Balanceador_Nerea_CM/assets/146434664/da0d6b9a-d377-4310-9058-b8fab9090958)


![image](https://github.com/RKillerN/Balanceador_Nerea_CM/assets/146434664/b99b4556-f597-4449-9239-f85b514e5788)

![image](https://github.com/RKillerN/Balanceador_Nerea_CM/assets/146434664/79975b64-b49c-4b9c-97c7-d29c4b3b85c7)
![image](https://github.com/RKillerN/Balanceador_Nerea_CM/assets/146434664/f641dd4c-0ea1-45c1-9292-4bda2be83282)


![image](https://github.com/RKillerN/Balanceador_Nerea_CM/assets/146434664/dd0ca8a8-ade2-4fea-a5c7-d98ace5cb722)
