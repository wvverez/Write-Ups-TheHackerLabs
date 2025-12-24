## ⚙️ CTF:

<img width="1024" height="1024" alt="00_Aceituno" src="https://github.com/user-attachments/assets/66f734bd-30d3-42a4-9537-1cc014806783" />

🪪 Nombre: Aceituno

🛜 SO: Linux 

🔊 Dificultad: Avanzado

👥 Creadores: Condor & Curiosidades de Hackers

## 🔐 CONTACTO
[![Telegram](https://img.shields.io/badge/-Telegram-2CA5E0?style=for-the-badge&logo=telegram&logoColor=white)](https://t.me/wvverez)
[![Twitter](https://img.shields.io/badge/-Twitter-1DA1F2?style=for-the-badge&logo=twitter&logoColor=white)](https://twitter.com/wvverez)

# ENUMERACIÓN

Vamos a empezar con un escaneo nmap para enumerar puertos abiertos, servicios que corren y versiones.

<img width="987" height="634" alt="01_Pescador" src="https://github.com/user-attachments/assets/a5fb3cda-8d5e-4bad-a351-f17006b99acc" />

Vemos 2 puertos abiertos el 22 donde corre OpenSSH y el 80 donde podemos ver que hay un dominio asociado vamos a asignarnoslo al /etc/hosts. Una vez asignado accedemos.

<img width="849" height="600" alt="02_Pescador" src="https://github.com/user-attachments/assets/b5b9e739-7952-4b7b-909d-21b72ab1ecb4" />

Vemos un panel de login podemos probar vulnerabilidades típicas aunque no encontramos mucho. Vamos a aplicar fuzzing para encontrar posibles rutas potenciales.

<img width="942" height="500" alt="03_Pescador" src="https://github.com/user-attachments/assets/c14bae3b-3714-4ee8-bc3c-af885547d0aa" />
<img width="856" height="305" alt="04_Pescador" src="https://github.com/user-attachments/assets/a09cd8b5-e3e2-47c4-b244-a53aadb259d9" />

Encontramos uploads donde tenemos una foto que fue subida el 21 de noviembre de 2025 vamos a acceder a ella. Vamos a aplicar fuzzing pero aplicando por parámetros para poder conseguir el parámetro con el cual podamos escalar a un RCE

<img width="957" height="771" alt="05_Pescador" src="https://github.com/user-attachments/assets/8864bb76-d1f2-4403-879a-c995dadb43e0" />

Conseguimos el parametro "cmd" Con lo cual podemos inyectar código en la webshell desde el parametro "cmd":

<img width="886" height="786" alt="06_Pescador" src="https://github.com/user-attachments/assets/2d9f2d24-b2d7-482f-8f40-467b4016e0e5" />

El output nos muestra el usuario www-data con lo cual ya sabemos que estamos ante un servidor apache por que es su usuario predeterminado. Vamos a subirnos una revhsell en bash interactiva y nos pondremos en escucha en el puerto 4444.

<img width="1747" height="649" alt="07_Pescador" src="https://github.com/user-attachments/assets/9081a72c-7a18-4f86-bb1f-0d6b2cae2d9d" />

Bien ya hemos ganado acceso ya estamos como el usuario www-data.
Vamos a buscar posibles permisos SUID que tenga asociados el usuario y de los cuales podamos abusar.

<img width="561" height="343" alt="08_Pescador" src="https://github.com/user-attachments/assets/faae0fa1-a6d5-4f8c-bc99-cf28bc57a53c" />

Vemos que podemos abusar de este permiso SUID para acceder como root.

<img width="304" height="73" alt="09_Pescador" src="https://github.com/user-attachments/assets/b8d0d13f-02ab-48d7-9353-c7eb332dc638" />

Y ya habriamos accedido como root.
