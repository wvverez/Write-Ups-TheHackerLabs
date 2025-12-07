## ⚙️ CTF:

<img width="1024" height="1024" alt="00_cachopo" src="https://github.com/user-attachments/assets/c6ec350a-1ae1-4f74-b153-a59bae7449f2" />

🪪 Nombre: Cachopo

🛜 SO: Linux 

🔊 Dificultad: Avanzado

👥 Creadores: Condor & CuriosidadesdeHackers

## CONTACTO

[![Telegram](https://img.shields.io/badge/-Telegram-2CA5E0?style=for-the-badge&logo=telegram&logoColor=white)](https://t.me/wvverez)
[![Twitter](https://img.shields.io/badge/-Twitter-1DA1F2?style=for-the-badge&logo=twitter&logoColor=white)](https://twitter.com/wvverez)

## ENUMERACIÓN 

Vamos a empezar con un sencillo escaneo de red para enumerar puertos y servicios que corren y sus versiones.

<img width="963" height="764" alt="01_Cachopo" src="https://github.com/user-attachments/assets/68c6c9f5-e9c5-44ce-b684-bf9c7bff4b01" />

Bien vemos el puerto 22 abierto en el que corre ssh por defecto y en el puerto 80 que corre http con una página llamada cachopo.thl

Con la IP no carga así que tenemos que asociarnoslo al /etc/hosts/ una vez echo podemos ver la página principal

<img width="900" height="832" alt="02_Cachopo" src="https://github.com/user-attachments/assets/4674e106-f467-4669-a34d-78f1e7605670" />

Bueno vemos una imagen de un plato de cachopo en el código fuente tampoco se aprecia nada solo la imagen, así que vamos a descargarnos la foto. para intentar crackearla.

<img width="713" height="567" alt="03_Cachopo" src="https://github.com/user-attachments/assets/1b4b1e9a-4fb8-40fe-8d93-9cd37093b94a" />

## RECONOCIMIENTO

Con stegcracker vemos que podemos romper la contraseña de la imagen.

<img width="956" height="472" alt="04_Cachopo" src="https://github.com/user-attachments/assets/451d9a9e-fa87-45a7-810f-cf597c6bfa4f" />

Para poder listar los datos que tengamos dentro hacemos lo siguiente 

<img width="951" height="214" alt="05_Cachopo" src="https://github.com/user-attachments/assets/2fccb4b9-5337-455f-ac21-0c74f36ab86e" />

Bueno vemos que nos indica un directorio así que vamos a probarlo en el navegador y encontramos un documento ofimático llamado "cocineros"

<img width="846" height="349" alt="06_Cachopo" src="https://github.com/user-attachments/assets/cba15ded-3326-4e33-9e17-75d722e67e53" />
<img width="958" height="531" alt="07_Cachopo" src="https://github.com/user-attachments/assets/f14e58b7-ddfd-41ca-87b4-36821465c140" />

## EXPLOTACIÓN

Bueno vemos que es un archivo cifrado así que sacaremos el hash y posteriormente lo romperemos con john, tenemos la contraseña del producto ofimático , que en este caso es un libreoffice y podemos acceder a el con la contra horse1 

<img width="872" height="461" alt="08_Cachopo" src="https://github.com/user-attachments/assets/89ef1a89-6e59-4a44-84a5-43672e897a87" />

Al acceder a él vemos una lista con 3 nombres, así que vamos a crackearlos con hydra hasta encontrar alguno valido vía ssh.

<img width="958" height="524" alt="09_Cachopo" src="https://github.com/user-attachments/assets/8ba5e10f-50a5-4186-b7ff-6b1ed672bc4e" />

Una vez crackeada la contraseña podemos acceder vía ssh.

<img width="951" height="477" alt="010_Cachopo" src="https://github.com/user-attachments/assets/478cc508-4448-47f9-83dd-bd691443532f" />

## ESCALADA DE PRIVILEGIOS

Bien viendo los permisos sudoers el usuario carlos vemos que tiene asociado el binario crash. El cual podemos abusar de el y acceder como root ejecutando:

<pre>
  <code>
    sudo crash -h
    !sh
  </code>
</pre>

Una vez echo esto ya estaríamos como el usuario root.

<img width="940" height="145" alt="011_Cachopo" src="https://github.com/user-attachments/assets/1ff7a33f-5fec-4f25-8c97-2f3a8fc30b15" />

Salud ^^
