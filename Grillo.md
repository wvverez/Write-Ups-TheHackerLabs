## ⚙️ CTF:

<img width="1024" height="1024" alt="00_grillo" src="https://github.com/user-attachments/assets/cd61a9e3-a383-4a2a-b206-edced5bd17f5" />

🪪 Nombre: Grillo 

🛜 SO: Linux 

🔊 Dificultad: Principiante

👥 Creadores: Condor & CuriosidadesdeHackers

## CONTACTO

[![Telegram](https://img.shields.io/badge/-Telegram-2CA5E0?style=for-the-badge&logo=telegram&logoColor=white)](https://t.me/wvverez)
[![Twitter](https://img.shields.io/badge/-Twitter-1DA1F2?style=for-the-badge&logo=twitter&logoColor=white)](https://twitter.com/wvverez)

## ENUMERACIÓN

En el día de hoy traigo la resolución de la máquina Grillo con SO Linux.

<img width="940" height="734" alt="01_grillo" src="https://github.com/user-attachments/assets/0e5d7d76-b629-4561-a4d3-30cb7d814c8d" />

Empezamos haciendo un escaneo completo de SO, puertos abiertos y servicios y versiones que corren vemos que tenemos 2 puertos abiertos el 22 que corre el servicio por defecto de ssh y el 80 en el que tenemos la página por defecto de apache.

<img width="844" height="640" alt="02_grillo" src="https://github.com/user-attachments/assets/830186bc-615f-411b-9b4f-412e0c96400f" />

## RECONOCIMIENTO

En la página comprobe código fuente y enlaces pero nada aunque abajo encontré este mensaje extraño así que supuse que había un usuario que se llamaría melanie.

<img width="1352" height="748" alt="03_grillo" src="https://github.com/user-attachments/assets/e2c8df44-155d-40ff-84d8-61d37266026f" />

## EXPLOTACIÓN 

Probé a crackearlo con hydra con el diccionario rockyou.txt y parece que fue suficiente conseguimos la contraseña y ya pude conectarme por ssh ccomo melanie.

<img width="1193" height="300" alt="04_grillo" src="https://github.com/user-attachments/assets/da31a683-64de-4e90-877d-0b0ed4625e8b" />

## ESCALADA DE PRIVILEGIOS

Miré los permisos sudoers y vi un extraño llamado "puttygen". Tras buscar información ejecute puttygen con privilegios root para crear una clave rsa en formato openssh. Puse una passphrase cualquiera y se creó la clave privada id_rsa

<img width="1071" height="779" alt="05_grillo" src="https://github.com/user-attachments/assets/62180653-12d1-43a7-8559-b580825d40ef" />

Con lo cual ahora ya tenemos el id_Rsa. siendo el usuario melanie.

<img width="1473" height="755" alt="06_grillo" src="https://github.com/user-attachments/assets/1182df11-0fa1-4276-be14-d551ee8adfa6" />

Así que ahora ya me conecte por ssh al localhost y saltandome el paso de la contraseña gracias al id_rsa. Con lo cual ya habríamos resuelto la máquina

Salud ^^
