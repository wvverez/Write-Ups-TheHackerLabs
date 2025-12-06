Hoy traigo la resolución de una de mis máquinas favoritas de THL, "Aceituno". Bien vamos a ello, aclarar que la primera mitad fue resuelta en kali, por problemas técnicos y la otra mitad en parrot, con el mismo entorno pero nuevo


## ENUMERACIÓN

Vamos a empezar con un escaneo nmap: 
<img width="692" height="232" alt="01_Aceituno" src="https://github.com/user-attachments/assets/6acb39f9-6f9e-4d7e-b91e-66d0e9483b54" />
Vemos 4 puertos abiertos, el 22,80,443 y 3306... Vamos a realizar un segundo escaneo para determinar el servicio y versión misma. 
<img width="978" height="580" alt="02_Aceituno" src="https://github.com/user-attachments/assets/8fe82f21-1a37-4077-90da-c408e738e282" />
Bien, vemos 2 puertos enfocados a http donde normalmente el puerto 443 es para https pero bueno vemos que no es el caso. Vamos a ver de primeras el puerto 80, donde en el segundo escaneo nmap vemos que detecto que es un WordPress en la versión 6.5.2
Vamos a buscar la página a través de la IP.
<img width="1916" height="817" alt="03_Aceituno" src="https://github.com/user-attachments/assets/7d4125ab-8188-4627-831c-92518b2f7e36" />
Vemos que falla en la carga cuando esto ocurre seguramente tenga un dominio asociado, si vemos el código fuente podemos ver lo siguiente:
<img width="539" height="81" alt="04_Aceituno" src="https://github.com/user-attachments/assets/3903b866-1238-4228-bfe8-3eac7d6e3569" />
Así que vamos a asignarlo en /etc/hosts. Este binario nos permite asignar nombres de dominio a direcciones IP. y ponemos la IP de la máquina y el dominio aceituno.thl.
Vale ahora empleamos el dominio en el navegador para ver si carga la página web. 
<img width="919" height="792" alt="05_Aceituno" src="https://github.com/user-attachments/assets/15eae9f5-bc10-4aad-a415-1b7b5a9d3d37" />
Efectivamente nos carga la página, no encontré mucho dentro de la página muchos blogs del usuario aceituno pero si vas a wp-admin me di cuenta que podías comprobar si existía un usuario.
Por ejemplo si entro y pongo wvverez y pongo una contraseña cualquiera: 
<img width="632" height="671" alt="06_Aceituno" src="https://github.com/user-attachments/assets/5494c38b-7f2b-415b-81c3-e1b48c0756d1" />
Ya nos dice que el nombre "wvverez" no está registrado en este sitio , con lo cual ya sabemos que no existe en la base de datos, pero sin embargo si probamos con el usuario aceituno que sabemos que existe ya que publico ciertos blogs.
<img width="546" height="554" alt="07_Aceituno" src="https://github.com/user-attachments/assets/3c7401dd-ebb3-40d0-b926-72cb9a531b62" />
## RECONOCIMIENTO
Ahora solo nos dice que es la contraseña, ves la diferencia. Siguiendo indagando por la web en el código fuente encontré algo que delato la página y fue que tenía asociado el plugin wp discuz 7.0.4 asociado en blogs.
<img width="1196" height="226" alt="08_Aceituno" src="https://github.com/user-attachments/assets/533946e8-879d-43a5-8503-4b564224f76c" />
Esto podiamos haberlo comprobado también en wpscan aunque en este caso no fue necesario.
Vamos a buscar algún posible CVE, y lo encontré, vi que había un CVE reportado cuyo fallo de seguridad nos permitía RCE. Compartiré por aquí el exploit 
https://github.com/hev0x/CVE-2020-24186-wpDiscuz-7.0.4-RCE.git
## EXPLOTACIÓN
Vamos a explotar: 
<img width="945" height="351" alt="09_Aceituno" src="https://github.com/user-attachments/assets/a7c9eeda-7adc-488a-b009-8f5ac5e1893a" />
Vamos a ejecutarlo para ver los parámetros que necesite: 
En este caso nos piden: 
-u --> la URL donde se encuentra el wordpress
-p --> la página donde se usa el plugin wpDiscuz 7.0.4
<img width="954" height="591" alt="010_Aceituno" src="https://github.com/user-attachments/assets/0c470070-672e-49ad-903d-55458cfb7f73" />
Vamos a meternos a la webshell que nos acaba de subir para ver si se subio correctamente y tenemos vía potencial por RCE.
<img width="1162" height="323" alt="011_Aceituno" src="https://github.com/user-attachments/assets/937174ca-e869-4867-8970-9af5e7bc4365" />
GUAY!!!Probaremos algunos simples comandos para ver si funciona correctamente
<img width="1079" height="249" alt="012_Aceituno" src="https://github.com/user-attachments/assets/e1a538a5-617f-4165-b319-06b3a0899927" />
<img width="1066" height="305" alt="013_Aceituno" src="https://github.com/user-attachments/assets/a3dfdf09-a1a5-436e-9e50-3c40e22b465c" />
Efectivamente todo funciona a la perfección, tenemos acceso a RCE. Bien ahora nos subiremos una revshell en bash interactiva en este caso use el puerto 4444, es importante que la revshell vaya URLencodeada para que el navegador la acepte
<img width="477" height="185" alt="014_Aceituno" src="https://github.com/user-attachments/assets/cf3b3b63-e2f6-43a0-a6f4-ca2e12bc62d2" />
Aquí tenemos nuestra revshell bien nos vamos a poner en escucha en el puerto 4444 y al subir la revshell deberíamos poder acceder como el usuario www-data. vamos a comprobar 
<img width="1805" height="805" alt="015_Aceituno" src="https://github.com/user-attachments/assets/1d33e8d6-4884-4271-8af7-f5e3bee80f78" />
Bien! ya estaríamos como el usuario www-data, indagando en los archivos y carpetas que tenía el usuario llegue a uno que se llamaba wp-config.php al abrirlo, acabe encontrando la contraseña a la DB y el user.
<img width="872" height="686" alt="016_Aceituno" src="https://github.com/user-attachments/assets/69bb9ccf-c5a2-4606-963f-d3a961bc406d" />
Accedemos a la base de datos de MySQL y vamos a indagar un poco sobre las DB existentes.
<img width="838" height="517" alt="017_Aceituno" src="https://github.com/user-attachments/assets/15686ae0-b3f4-47db-9042-c141f3068b67" />
Vale,bien! vamos a encontrar tablas ahora muchas de ellas eran las predeterminadas había una que era importante y la otra no tenía nada que ver por su extraño nombre.
<img width="836" height="789" alt="018_Aceituno" src="https://github.com/user-attachments/assets/b445986e-90d2-4923-9d4b-a8a2534e499d" />
Nos fijamos en wp_users y pelopicopata si accedemos a la tabla wp_users encontramos la contraseña del usuario aceituno que podemos cambiar y acceder a WP desde navegador y podrías hacer lo que quisieras, podrías dumpear el sitio ya.
<img width="1106" height="381" alt="019_Aceituno" src="https://github.com/user-attachments/assets/690781dc-3449-448b-ae16-0b39e750f911" />
Y cambiando a pelopicopata para ver lo que contenía el otro, podemos fijarnos en las columnas que vemos que ya es usuario y también en contraseña. 
<img width="1058" height="455" alt="020_Aceituno" src="https://github.com/user-attachments/assets/e87e4277-c3b2-481b-8912-c9fa0dbc7061" />
Hay un carácter que no se ha filtrado aunque por un poco de coherencia podemos suponer que sería una Ñ, probé con ambas sin nada y con Ñ, y efectivamente era con Ñ.
<img width="945" height="482" alt="021_Aceituno" src="https://github.com/user-attachments/assets/6e28226b-4dca-4def-a115-0afdb315b7ea" />
Accedí ya por ssh, y ya estaríamos como el usuario aceituno. Al principio vimos que habia un servicio ssh corriendo por el puerto 22, al hacer 
sudo -l 
Podemos ver que se utiliza el binario most, que nos permite ver ciertos archivos sin precisar ser root. Lo que hicimos ahora fue buscar una id_rsa gracias al puerto ssh del puerto 22 que había abierto. haciendo:
sudo -u root /usr/bin/most /root/.ssh/id_rsa
Al hacerlo conseguimos el id_rsa. El cual nos vamos a copiar al clipboard de momento. 
<img width="1000" height="568" alt="022_Aceituno" src="https://github.com/user-attachments/assets/7239bcf0-1ca4-4a58-badc-0e76b1fae574" />
Lo pasé a un fichero id_rsa. Claro podemos acceder como root por ssh gracias al id_rsa pero al intentarlo nos pedía contraseña. Con lo cual tenemos que crackearlo. Al intentar acceder por ssh nos pide contraseña.
<img width="1519" height="663" alt="023_Aceituno" src="https://github.com/user-attachments/assets/23c420df-3812-4df9-aa70-216d260ae7fb" />
Le di permisos de lectura y escritura para root es decir
chmod 600 id_rsa
y con ssh2john lo paso a hash_idRsa, lo listamos y se ejecuta correctamente.
<img width="1854" height="416" alt="024_Aceituno" src="https://github.com/user-attachments/assets/6efe2956-a1da-49c1-a134-cf6fae01c3e7" />
Vale bien me descomprimí el diccionario rockyou.txt y vamos a crackear este hash con johntheripper.
<img width="1250" height="318" alt="025_Aceituno" src="https://github.com/user-attachments/assets/bd2bc21c-5ff0-4d65-be7c-f993de86d19f" />
sudo gzip -d /usr/share/wordlists/rockyou.txt.gz
john -w=/usr/share/wordlists/rockyou.txt hash_idRsa

Bien esperamos con paciencia y john nos crackea el hash vemos que podemos acceder como root con la contraseña "blessed1"
<img width="1571" height="396" alt="026_Aceituno" src="https://github.com/user-attachments/assets/57a8b250-9076-41eb-be0b-22aa16298252" />

Salud ^^

