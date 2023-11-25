# Balanceador_Nerea_CM
Creación de un escenario a tres niveles en AWS, con un balanceador de carga, dos máquinas apache y una máquina de base de datos MariaDB.

lanzar instancia
![image](https://github.com/RKillerN/Balanceador_Nerea_CM/assets/146434664/464b1fa3-f186-4886-8461-9cdef52409a0)

apache
![image](https://github.com/RKillerN/Balanceador_Nerea_CM/assets/146434664/0e61d0f5-8501-4c38-862f-07cb1acda416)


imagen que vamos a usar
![image](https://github.com/RKillerN/Balanceador_Nerea_CM/assets/146434664/df6d020c-a113-4e8a-9329-74b682693184)
![image](https://github.com/RKillerN/Balanceador_Nerea_CM/assets/146434664/aa49f32e-5993-4c94-8924-cd116312abac)
![image](https://github.com/RKillerN/Balanceador_Nerea_CM/assets/146434664/7233cfb1-6172-483d-8cc3-8d0156efa0b2)
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
![image](https://github.com/RKillerN/Balanceador_Nerea_CM/assets/146434664/1f557803-7ad7-4982-9158-f8e6d090f515)

**![image](https://github.com/RKillerN/Balanceador_Nerea_CM/assets/146434664/d5ec80be-ea8b-4a82-a3b6-e6534b8319a5)
**





