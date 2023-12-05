# Balanceador_Nerea_CM
En esta práctica, se pretende desplegar un sistema en tres niveles en AWS, con las siguientes condiciones:

La primera capa consiste en un Balanceador de Carga de Apache, el cual tiene acceso exclusivo a internet y está configurado para comunicarse únicamente con la capa de backend.

La segunda capa, denominada "Backend", comprende dos máquinas Apache que alojan la aplicación web. Estas máquinas pueden establecer comunicación tanto con el balanceador de carga como con el servidor de base de datos. No cuentan con acceso directo a internet.

La tercera capa, representada por el servidor MariaDB, está configurada para comunicarse exclusivamente con la capa de backend y carece de acceso directo a internet.

Esta configuración busca ser diseñada para tener seguridad y funcionalidad del despliegue.

#Creación de las Instancias

Para establecer una VPC en AWS con tres subredes usando la gama de direcciones IP 192.168.1.0/26, procede de la siguiente manera:

Crea una nueva VPC, en el apartado de VPC de Amazon Web Services, especificando el rango de direcciones IP como 192.168.1.0/24. A continuación, definimos tres subredes dentro de esta VPC, distribuyendo las direcciones IP según sea necesario. Este proceso te permitirá tener una infraestructura de red organizada con tres subredes distintas en la nube de AWS.

Dentro de crear una VPC establecemos el nombre el cual identificaremos la VPC, 
![image](https://github.com/RKillerN/Balanceador_Nerea_CM/assets/146434664/4db365f6-f326-464f-b6ea-cbc041cbca43)

![image](https://github.com/RKillerN/Balanceador_Nerea_CM/assets/146434664/61c4a412-4d39-46e4-bcd8-60c30d03fe63)


![image](https://github.com/RKillerN/Balanceador_Nerea_CM/assets/146434664/aa49f32e-5993-4c94-8924-cd116312abac)
![image](https://github.com/RKillerN/Balanceador_Nerea_CM/assets/146434664/7233cfb1-6172-483d-8cc3-8d0156efa0b2)

lanzar instancia
![image](https://github.com/RKillerN/Balanceador_Nerea_CM/assets/146434664/464b1fa3-f186-4886-8461-9cdef52409a0)

apache
![image](https://github.com/RKillerN/Balanceador_Nerea_CM/assets/146434664/0e61d0f5-8501-4c38-862f-07cb1acda416)


imagen que vamos a usar
![image](https://github.com/RKillerN/Balanceador_Nerea_CM/assets/146434664/df6d020c-a113-4e8a-9329-74b682693184)

![image](https://github.com/RKillerN/Balanceador_Nerea_CM/assets/146434664/d69d2e50-dec4-4b71-ae43-996123f78e5e)
hacer el script
![image](https://github.com/RKillerN/Balanceador_Nerea_CM/assets/146434664/7daba501-45ce-46a1-a8ac-e5111fa0861f)

#!/bin/bash

#Buscar actualizaciones
sudo apt update -y
#Actualizar
sudo apt upgrade -y
#Descargar los servicios necesarios
sudo apt install mariadb_client apache2 libapache2-mod-php7.4 openssl php7.4-imagick php7.4-common php7.4-curl php7.4-gd php7.4-imap php7.4-intl php7.4-json php7.4-ldap php7.4-mbstring php7.4-mysql php7.4-pgsql php7.4-smbclient php7.4-ssh2 php7.4-sqlite3 php7.4-xml php7.4-zip git -y
#Ir a /var/www/ y descargar el repositorio de git hub
cd /var/www/
sudo git clone https://github.com/josejuansanchez/iaw-practica-lamp.git 
subred
#!/bin/bash

# Actualizar el sistema
sudo apt-get update

# Instalar Apache
sudo apt-get install -y apache2

# Iniciar Apache
sudo systemctl start apache2

# Habilitar Apache para que se inicie en el arranque
sudo systemctl enable apache2



script mariadb
#!/bin/bash

# Configuración de la contraseña del usuario root de MariaDB
DB_ROOT_PASSWD="tu_contraseña_root"

# Instalar MariaDB
sudo apt-get update
sudo DEBIAN_FRONTEND=noninteractive apt-get install -y mariadb-server

# Configurar MariaDB
sudo mysqladmin -u root password $DB_ROOT_PASSWD

# Crear la base de datos y el usuario
sudo mysql -u root -p$DB_ROOT_PASSWD <<MYSQL_SCRIPT
CREATE DATABASE pilalamp_db;
CREATE USER 'plamp_user'@'localhost' IDENTIFIED BY '1234';
GRANT ALL PRIVILEGES ON pilalamp_db.* TO 'plamp_user'@'localhost';
FLUSH PRIVILEGES;
MYSQL_SCRIPT

# Reiniciar MariaDB para aplicar cambios
sudo systemctl restart mariadb
conectarnos a las máquinas
![image](https://github.com/RKillerN/Balanceador_Nerea_CM/assets/146434664/c3f6ccf2-a6ac-4f1a-9a9f-2f01c7687fee)
balanceador

![image](https://github.com/RKillerN/Balanceador_Nerea_CM/assets/146434664/d2aeb3b2-e179-4de1-a378-cdf0dec1afc4)

![image](https://github.com/RKillerN/Balanceador_Nerea_CM/assets/146434664/de6a87c5-4073-460a-a2cf-79cbb5df7a0d)

![image](https://github.com/RKillerN/Balanceador_Nerea_CM/assets/146434664/9f03ceda-1140-4444-9152-1bf247e50381)

![image](https://github.com/RKillerN/Balanceador_Nerea_CM/assets/146434664/54a01b1b-9afc-45ed-b005-8078753d67a5)

![image](https://github.com/RKillerN/Balanceador_Nerea_CM/assets/146434664/86c09992-d17c-484d-ae87-dfc7115b7b3d)

![image](https://github.com/RKillerN/Balanceador_Nerea_CM/assets/146434664/aa6799cf-a10b-4787-9591-65cbec69d84e)

![image](https://github.com/RKillerN/Balanceador_Nerea_CM/assets/146434664/f5ff4f1f-2e87-4317-9b9b-34d8b812ef7d)

![image](https://github.com/RKillerN/Balanceador_Nerea_CM/assets/146434664/015f9eaa-7887-4c64-9402-bcc085f52286)


pila lamp

pagina principal
![image](https://github.com/RKillerN/Balanceador_Nerea_CM/assets/146434664/8f458364-d827-4eaa-bf05-26c98b80e3aa)ç

config php
![image](https://github.com/RKillerN/Balanceador_Nerea_CM/assets/146434664/fb7df38c-b357-479b-9bfb-23e5be83aa16)

bin address y cargar base de datos

![image](https://github.com/RKillerN/Balanceador_Nerea_CM/assets/146434664/1900c814-130b-4305-894c-85b4eb11e23a)

![image](https://github.com/RKillerN/Balanceador_Nerea_CM/assets/146434664/4f195b5e-56bb-44c4-8cb9-a73843a9c4fb)


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
