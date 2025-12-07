## ⚙️ CTF:

<img width="1024" height="1024" alt="00_cachopo" src="https://github.com/user-attachments/assets/c6ec350a-1ae1-4f74-b153-a59bae7449f2" />

🪪 Nombre: Microchoft

🛜 SO: Windows

🔊 Dificultad: Principiante

👥 Creadores: Condor & CuriosidadesdeHackers

## CONTACTO

[![Telegram](https://img.shields.io/badge/-Telegram-2CA5E0?style=for-the-badge&logo=telegram&logoColor=white)](https://t.me/wvverez)
[![Twitter](https://img.shields.io/badge/-Twitter-1DA1F2?style=for-the-badge&logo=twitter&logoColor=white)](https://twitter.com/wvverez)

## ENUMERACIÓN

Lo primero que hacemos es encargarnos de a través de la interfaz conseguir la IP de la máquina víctima ya que no nos la muestra por interfaz gráfica pero con host discovering la podemos conseguir fácil una vez tenemos la IP de la víctima podemos empezar escaneando los puertos.

<img width="1016" height="680" alt="01_escaneo puertos" src="https://github.com/user-attachments/assets/3a06789b-7ce5-404c-bf71-7aa4a1171575" />

Encontramos bastantes puertos abiertos aunque nos vamos a enumerar los servicios que corren en cada puerto para conseguir más información.

<img width="939" height="142" alt="03_services" src="https://github.com/user-attachments/assets/a20eff20-e367-45c9-8ac7-b32cca23d81e" />
<img width="1237" height="464" alt="02_escaneoservicios" src="https://github.com/user-attachments/assets/adfbe980-e4cb-4413-b1a5-b0536f2b98b9" />

Bien vemos que en el puerto 445 corre un Windows 7 home Basic

<img width="953" height="113" alt="04_searchsploit" src="https://github.com/user-attachments/assets/dc891e97-5381-4245-8507-7def9e595811" />

## RECONOCIMIENTO

Vemos la vulnerabilidad EternalBlue, Ahora sabemos que es un Windows 7 Home Basic 6.1 service pack1, además de que tiene el puerto smb abierto, que es el 445, para comprobar si un equipo es vulnerable a eternalblue, podemos comprobarlo mediante nmap, ya que nmap cuenta con un script que analiza si es vulnerable a esta vulnerabilidad o no.

<img width="875" height="604" alt="05_lsNMAP" src="https://github.com/user-attachments/assets/6b67d0f1-f46c-4883-8191-bc4d5c7e1d72" />
<img width="922" height="294" alt="06_010" src="https://github.com/user-attachments/assets/9ce4bc8a-2efe-4012-b626-14a10f1b175a" />
<img width="957" height="596" alt="07_VULNERABLE" src="https://github.com/user-attachments/assets/a3da46e1-5ba9-4784-9c4e-0478ccfa97df" />

Aquí comprobamos que efectivamente es vulnerable esto es algo muy crítico, así que bueno lo siguiente que haremos será explotarla con metasploit.

<img width="915" height="465" alt="08_Metasploit" src="https://github.com/user-attachments/assets/ebd32590-cb7f-470f-8a9b-d8db2bece96a" />

Bien, primeramente la buscamos, la seleccionaremos y la explotaremos, la verdad es que esta vulnerabilidad es muy crítica y conocida y hay que tenerla en cuenta por que es bastante sencilla de explotar.

<img width="1845" height="744" alt="09_use0" src="https://github.com/user-attachments/assets/8e15105e-3134-46b2-9242-0fab3066b350" />

## EXPLOTACIÓN 

Bien viendo las opciones lo siguiente que haremos será otorgar la máquina IP víctima y probaremos a explotar esta vulnerabilidad.

<img width="1201" height="824" alt="010_exploit" src="https://github.com/user-attachments/assets/e1d4c1c7-368f-485f-b525-de86d70c3e69" />

Bien una vez explotado nos da acceso me cambie a una shell para tenerlo más cómodo que como MeterPreter y lo siguiente que hice fue indagar un poco por directorios. Si nos metemos en la carpeta de usuarios podemos ver los usuarios "lola" y "admin"

<img width="915" height="488" alt="011_lolaadmin" src="https://github.com/user-attachments/assets/6b84f93a-8190-4937-9126-f7ff650ae54b" />

Bueno ya que tenemos acceso total vamos a acceder a ellos si nos posicionamos como admin podemos listar su contenido y ver que ya tendríamos la flag del usuario admin.

<img width="846" height="407" alt="012_flagADMIN" src="https://github.com/user-attachments/assets/df6d2c41-48e4-4518-b299-16f5678a8b7e" />

Bueno ahora hacemos lo mismo pero con el usuario Lola y también tendriamos su flag.

<img width="1016" height="410" alt="013_FLAGlola" src="https://github.com/user-attachments/assets/672a30e0-ba91-4300-a2b6-9c9197692c7a" />

Y efectivamente ahí tendríamos la flag del usuario, ya tenemos ambas flags, máquina resuelta.

Salud ^^
