# ⚙️ CTF 

<img width="1024" height="1024" alt="00_watchstore" src="https://github.com/user-attachments/assets/8b0bf31b-4645-4541-bf2b-ff68d1d49f7e" />

🪪 Nombre: Watchstore

🛜 SO: Linux 

🔊 Dificultad: Principiante

👥 Creadores: Condor & Curiosidades de Hackers

# 🔐 CONTACTO

[![Telegram](https://img.shields.io/badge/-Telegram-2CA5E0?style=for-the-badge&logo=telegram&logoColor=white)](https://t.me/wvverez)
[![Twitter](https://img.shields.io/badge/-Twitter-1DA1F2?style=for-the-badge&logo=twitter&logoColor=white)](https://twitter.com/wvverez)

# 📋 ENUMERACIÓN

Empezamos con un escaneo nmap para analizar puertos abiertos, servicios y versiones que tienen.

<img width="1103" height="734" alt="01_watchstore" src="https://github.com/user-attachments/assets/eb6bffde-cffc-4951-89f2-ae898d0d03fe" />

Vemos 2 puertos abiertos el 22 que corre OpenSSH y el 8080 que corre Werkzeug. Vemos que tiene un dominio asociado con lo cual nos lo asociamos en /etc/hosts y accedemos a la página por el puerto 8080 para ver que tenemos.

<img width="857" height="786" alt="02_watchstore" src="https://github.com/user-attachments/assets/54889255-123c-4d7b-a44d-4f1fee9c683a" />

Tenemos una página como de relojes no había nada extraño, ni en el código fuente tampoco así que vamos a ponernos a hacer fuzzing para encontrar posibles rutas potenciales.

<img width="965" height="478" alt="03_wathstore" src="https://github.com/user-attachments/assets/a3fcd74c-8e7f-4590-bbfc-70f1b74636e1" />
<img width="949" height="151" alt="04_watchstore" src="https://github.com/user-attachments/assets/152c8d69-e5fb-462b-8a5e-b46c17f940a8" />
<img width="944" height="146" alt="05_watchstore" src="https://github.com/user-attachments/assets/8b6de7dd-465f-40e1-a800-9677ffdf89bb" />
Acabé encontrando dos rutas potenciales console y read vamos a echarles un ojo.

# 🤯 INTRUSIÓN

<img width="880" height="521" alt="06_watchstore" src="https://github.com/user-attachments/assets/3c839682-ac5f-4747-b6c3-8d268a246bdb" />
Bueno en Console tenemos el acceso bloqueado de momento hasta que consigamos el pin de acceso. Vamos a ver el read.

<img width="854" height="773" alt="07_watchstore" src="https://github.com/user-attachments/assets/d0ab5bff-a8d7-4b52-909e-61243c6bef7f" />

Vale esto puede ser que apunte vía LFI que puede apuntar a un path traversal el parametro podíamos fuzzearlo aunque no es necesario ya que ya nos dicen que es "id" al hacerlo vi que solo tenemos 2 usuarios. Vamos a intentar ver el contenido del script app.py que tenemos su ruta absoluta

<img width="836" height="799" alt="08_watchstore" src="https://github.com/user-attachments/assets/c263f89b-6c2f-4d35-8796-47bfc2568e77" />

Hemos podido ver el contenido del script vemos que arriba nos muestra el pin así que vamos a probar a ponerlo en la consola a ver si ya tenemos acceso.

<img width="871" height="422" alt="09_watchstore" src="https://github.com/user-attachments/assets/72f04d6e-935e-44c4-8749-fdd4babb16d4" />

Pues ya tendríamos acceso. Vamos a subirnos una revshell y ponernos antes en escucha por el puerto 4444 para poder ganar acceso.

<img width="1736" height="472" alt="011_watchstore" src="https://github.com/user-attachments/assets/578972b2-c5a0-4494-b01e-d77946cdd41e" />

# 🧗‍♀️ESCALADA DE PRIVILEGIOS 

Pues ya estaríamos como el usuario relox. Vamos a listarnos los permisos sudoers. Vemos que tiene asociado el binario neofetch vamos a abusar de ello, para ello vamos a ejecutar lo siguiente

<img width="841" height="523" alt="012_watchstore" src="https://github.com/user-attachments/assets/fc427de2-360b-4df6-bf4a-788ee015cd41" />

Y ya estaríamos como root!

Salud ^^
