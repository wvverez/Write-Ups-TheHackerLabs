## ⚙️ CTF:

<img width="651" height="647" alt="00_Tortuga" src="https://github.com/user-attachments/assets/c330965d-ea41-4dd2-8a38-8aa5516d015d" />

🪪 Nombre: Tortuga

🛜 SO: Linux 

🔊 Dificultad: Principiante

👥 Creadores: NnN

## 🔐 CONTACTO

[![Telegram](https://img.shields.io/badge/-Telegram-2CA5E0?style=for-the-badge&logo=telegram&logoColor=white)](https://t.me/wvverez)
[![Twitter](https://img.shields.io/badge/-Twitter-1DA1F2?style=for-the-badge&logo=twitter&logoColor=white)](https://twitter.com/wvverez)


## 📋 ENUMERACIÓN 
Vamos a empezar como siempre con un escaneo nmap para enumerar puertos y servicios corriendo.

<img width="946" height="687" alt="01_Tortuga" src="https://github.com/user-attachments/assets/5513cc68-6fa5-41df-8332-c435269237a8" />

Vemos puerto 22 y 80 abiertos donde corre OpenSSH por defecto y en 80 http donde tenemos una página llamada Isla Tortuga. Vamos a echarle un vistazo.

<img width="872" height="836" alt="02_Tortuga" src="https://github.com/user-attachments/assets/9daaacee-09b5-4fff-9d9b-ff4894db222a" />

## 🤔 RECONOCIMIENTO

Vemos la página simple, en conocer la tripulación tenemos los tripulantes que son pero al ver el mapa ya tenemos el usuario "grumete".

## 💥 EXPLOTACIÓN 

<img width="845" height="412" alt="03_Tortuga" src="https://github.com/user-attachments/assets/57a6b8d0-2c52-4832-ba19-eaeb05337733" />

Igualmente aclarar que teníamos vía potencial por LFI para poder saber de este usuario mediante un path traversal.

<pre>
  <code>
    http://ip/mapa.php?filename=....//....//....//....//etc/passwd 
  </code>
</pre>

<img width="877" height="603" alt="09_Tortuga" src="https://github.com/user-attachments/assets/72b8951e-ac68-41a1-9c34-ca689f049a9c" />

Vamos a crackear la contraseña de este usuario con hydra usando el diccionario de rockyou.txt para ver si podemos acceder como el usuario "grumete"

<img width="1846" height="423" alt="04_Tortuga" src="https://github.com/user-attachments/assets/c6a1cf65-c388-4a61-ae37-3b2f0bfd9c92" />

Podemos acceder por ssh como el usuario grumete.

<img width="608" height="147" alt="05_Tortuga" src="https://github.com/user-attachments/assets/3178d0a7-2fbe-4867-b8a0-b6b939fc3e0a" />

Aquí tendríamos la primera flag.

Al listar todo el contenido tenemos un supuesto fichero ".nota.txt"

<img width="1188" height="426" alt="06_Tortuga" src="https://github.com/user-attachments/assets/2a5bec0a-d47a-4fca-b6c7-e7232f2d31f5" />

La cual nos dice la contraseña del usuario capitán.

## 🧗‍♂️ ESCALADA DE PRIVILEGIOS

<img width="792" height="143" alt="07_Tortuga" src="https://github.com/user-attachments/assets/ddd611c7-1628-43e4-8c28-728ae258b580" />

Para buscar formas de elevar privilegios

<pre>
  <code>
getcap: muestra las capabilities de archivos
-r/ : busqueda recursiva desde la raíz
2>/dev/null: Redirige los errores
  </code>
</pre>

Ejecutamos lo siguiente

<pre>
  <code>
    /usr/bin/python3 -c import os; os.setuid(0); os.system("/bin/bash")
  </code>
</pre>

Y ya estaríamos como root 

Salud ^^
