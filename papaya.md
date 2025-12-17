# CTF 

🪪 Nombre: Papaya

🛜 SO: Linux 

🔊 Dificultad: Principiante

👥 Creadores: Condor & Curiosidades de Hackers

# CONTACTO 

[![Telegram](https://img.shields.io/badge/-Telegram-2CA5E0?style=for-the-badge&logo=telegram&logoColor=white)](https://t.me/wvverez)
[![Twitter](https://img.shields.io/badge/-Twitter-1DA1F2?style=for-the-badge&logo=twitter&logoColor=white)](https://twitter.com/wvverez)

# ENUMERACIÓN 

Vamos a empezar con un sencillo escaneo nmap para analizar puertos abiertos y servicios/versiones que corren.

<img width="1794" height="615" alt="01_Papaya" src="https://github.com/user-attachments/assets/4f9549bf-b333-445a-96c7-c0897e371bec" />

Vemos el puerto 21,22 y 80 abiertos donde en ftp encontramos un archivo codificado en rot13 que decodificado decía "aquinohaynadacoño" después tenemos el puerto 22 donde corre OpenSSH por defecto y en el 80 vemos que corre http una página llamada http://papaya.thl.

Bueno vamos a echarle un vistazo a la página asociamos el dominio con la IP en /etc/hosts y probamos a buscar la página.

<img width="871" height="776" alt="02_Papaya" src="https://github.com/user-attachments/assets/9053271d-6713-4693-a9fa-58216ecc89e8" />

Vemos una página de elkarte en la versión 1.1.9 la cual tiene una vulnerabilidad crítica reportada.
Pero necesitamos estar logueados. 
<img width="810" height="502" alt="03_Papaya" src="https://github.com/user-attachments/assets/7b840055-930d-42e7-acfd-0dcd66509201" />

Probé a loguearme pero nada, así que me puse a probar contraseñas típicas por ejemplo admin admin o admin password y probando acabé encontrando la de administrador.

<img width="935" height="733" alt="04_Papaya" src="https://github.com/user-attachments/assets/e8750f23-0d7b-435e-8578-51a156286f38" />

Ahora una vez estamos logueados como admin vamos a seguir las indicaciones de la vuln para poder escalarlo a un RCE y poder explotarla.

<img width="812" height="454" alt="05_Papaya" src="https://github.com/user-attachments/assets/0151e33f-4448-4ba4-bc29-286547c1d9dd" />

Accederemos aquí y tendremos que subir un zip al servidor que contendrá nuestro código en .php que nos permitirá ejecutar comandos a nivel de sistema.

<img width="816" height="618" alt="06_Papaya" src="https://github.com/user-attachments/assets/5dc3a114-f71a-4999-a95a-8d6dc28ceaa4" />
<img width="908" height="179" alt="07_Papaya" src="https://github.com/user-attachments/assets/6df92bfd-75d1-4c60-9e71-6c5dac623eb0" />

El código que usaremos será el siguiente, ahora vamos a pasarlo a un zip y vamos a subirnoslo al servidor.

<img width="830" height="346" alt="08_Papaya" src="https://github.com/user-attachments/assets/6b4ea92d-d9f0-429e-b3b1-e6322943e0b8" />

Una vez subido vamos a acceder donde nos explica la vuln.

<img width="879" height="358" alt="09_Papaya" src="https://github.com/user-attachments/assets/94320e2b-00af-4783-9b46-00ebb2d1093d" />

Y efectivamente hemos conseguido un RCE me puse a listar contenido desde navegador y encontré en opt un "pass.zip" muy interesante así que vamos a ganar acceso para poder acceder a él.

Pues nos subimos una revshell en bash interactiva URLencodeada y nos ponemos en escucha por el puerto 4444

<img width="761" height="194" alt="010_Papaya" src="https://github.com/user-attachments/assets/c3355d9b-ab7f-4ab5-a51b-a0e4d0c90f60" />

Bien pues ya tendríamos acceso como el user www-data. Voy abrirme un servidor en python y pasarme el .zip que habíamos encontrado ya desde el navegador.

<img width="952" height="469" alt="011_Papaya" src="https://github.com/user-attachments/assets/506d74a0-95f7-4f90-a72a-935a9c494ca7" />

Bien una vez en nuesta máquina atacante como queremos descomprimirlo vamos a pasarlo a hash para poder crackearlo con john ya que no podemos descomprimirlo sin contraseña usamos el diccionario rockyou.txt y nos crackea la contraseña podemos descomprimirnos el zip con la passphrase: jesica

<img width="948" height="379" alt="012_Papaya" src="https://github.com/user-attachments/assets/f0dbfd55-6282-449b-9efd-ed06fa20e77b" />

Al descomprimirlo nos sacan un pass.txt en el que tenemos la contraseña del usuario papaya.

<img width="475" height="146" alt="013_Papaya" src="https://github.com/user-attachments/assets/c7ead2d5-2a8d-4d2b-a802-7e879bf3555e" />

Ya tendríamos la primera flag. Una vez hemos conseguido acceso como el usuario papaya vamos a listar permisos sudoers para ver si podemos abusar de algun binario.

<img width="945" height="420" alt="014_Papaya" src="https://github.com/user-attachments/assets/0ef11756-82be-4985-8a76-384b531ddd38" />

Bien tenemos el binario scp el cual podemos abusar de el de la siguiente forma.

Y ya estaríamos como root.

Salud ^^
