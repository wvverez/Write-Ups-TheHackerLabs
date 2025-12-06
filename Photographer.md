## CONTACTO 


## ENUMERACIÓN
Vamos a empezar con un escaneo nmap:

<img width="1190" height="691" alt="01_photo" src="https://github.com/user-attachments/assets/d7b0ec77-ee53-41a0-a305-5b2342ee6514" />

Vamos a empezar como siempre enumerando todos los puertos y servicios que corren en cada uno. Vemos abiertos el puerto 80 y 22 algo bastante común. En el que tenemos ssh por defecto y una página de un blog de una persona llamada ethan. Vamos a acceder a ella a ver si encontramos algo

<img width="882" height="837" alt="02_photo" src="https://github.com/user-attachments/assets/d20af45d-0c7d-4f93-a7b8-dd9d63874305" />

Bueno tenemos un posible usuario ethan, en el código fuente no acabé encontrando nada extraño, con lo cual vamos a aplicar fuzzing para encontrar rutas potenciales.

<img width="959" height="511" alt="03_photo" src="https://github.com/user-attachments/assets/f7fe8c48-dcc4-4232-8e14-6b991ba2fc23" />
<img width="952" height="175" alt="04_photo" src="https://github.com/user-attachments/assets/b4d2c7b9-1193-4758-9174-89622fbb78de" />

Tenemos un subdirectorio /admin, accediendo a el me encontré con un panel de login aunque no tenemos credenciales.

<img width="929" height="701" alt="05_photo" src="https://github.com/user-attachments/assets/80670f67-c10c-4e33-8ee6-560e9aaf5b67" />

Vamos a volver a hacer fuzzing pero esta vez por parámetros a partir del subdirectorio /admin.

<img width="954" height="637" alt="06_photo" src="https://github.com/user-attachments/assets/a2b60120-99b5-459e-9ef9-0a5e7d39c79e" />

Encontramos la ruta admin.php vamos a irnos al burp para poder analizarlo mejor. 

<img width="505" height="499" alt="07_photo" src="https://github.com/user-attachments/assets/c2a408a6-3808-4e4e-8df3-cc5a031fa374" />

Bien ahora hacemos click derecho sobre la petición y le damos a do intercept > response to this request. Al darle a forward nos muestra un error 304

<img width="543" height="539" alt="08_photo" src="https://github.com/user-attachments/assets/1f6da25f-5c09-498a-ac49-2643661f5188" />

Una vez modificas el 304 por código de estado 200 ya te debería de cargar.

<img width="1485" height="708" alt="09_photo" src="https://github.com/user-attachments/assets/da60f935-06f0-4774-8b64-2e42cb3457f0" />

Ahora desde el navegador y con el proxy de burp activado podemos ver el contenido. 
Tenemos un panel en el que podemos subir fotografías. Vamos a analizar esta petición más comodamente en el repeater para ver si podemos inyectar código.
Tras un par de pruebas, encontramos una forma de ejecutar una XXE Injection utilizando una imagen.svg 

<pre>
  <code>
<?xml version="1.0" standalone="yes"?>
<!DOCTYPE test [ <!ENTITY xxe SYSTEM "file:///etc/passwd" > ]>
<svg width="128px" height="128px" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" version="1.1">
<text font-size="16" x="0" y="16">&xxe;</text>
</svg>
  </code>
</pre>

<img width="586" height="513" alt="010_photo" src="https://github.com/user-attachments/assets/f9a49f1b-a425-41a5-8a31-c78f1a36a744" />

Enviamos la inyección con un Content-Type:image/svg+xml y conseguimos tener acceso al /etc/passwd. El cuál nos confirma al usuario ethan en el servidor 

<img width="558" height="524" alt="011_photo" src="https://github.com/user-attachments/assets/8c377ca4-6c1e-4a70-9701-4ed383841c85" />

Vamos a leer el contenido de los archivos php que encontramos aplicando fuzzing. Vamos a usar un wrapper de php que nos mostrará el contenido del archivo en base64.

Este wrapper tenemos que introducirlo en nuestra query XXE Injection cambiando archivo por upload.php y despues por db.php

<pre>
  <code>
php://filter/convert.base64-encode/resource=<archivo>
  </code>
</pre>

<pre>
  <code>
    <?xml version="1.0" standalone="yes"?>
<!DOCTYPE test [ <!ENTITY xxe SYSTEM "php://filter/convert.base64-encode/resource=db.php" > ]>
<svg width="128px" height="128px" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" version="1.1">
<text font-size="16" x="0" y="16">&xxe;</text>
</svg>
  </code>
</pre>

El contenido que nos interesa está en db.php

<img width="587" height="592" alt="012_photo" src="https://github.com/user-attachments/assets/06d1bd64-37fc-4706-8037-39e0bfee5bbf" />
<img width="591" height="517" alt="013_photo" src="https://github.com/user-attachments/assets/c31cf5d0-507d-431c-bfd5-4d29aa526dd7" />
De este modo, en la respuesta tenemos en base64 el contenido de los archivos así que vamos nuestra máquina atacante a decodificarlo. 

<img width="887" height="440" alt="014_photo" src="https://github.com/user-attachments/assets/ef97c588-4700-43a4-bc94-8a1958a5b593" />
Bien tenemos vía potencial para acceder por ssh como el usuario ethan.

<img width="956" height="393" alt="015_photo" src="https://github.com/user-attachments/assets/96237efb-fb02-4aec-bc7a-871883326162" />

Perfecto acabamos de ganar acceso como el usuario ethan. Si revisamos los grupos a los que pertenece nuestro usuario con el comando id vemos que estamos en el grupo disk, con el cual podemos ver el contenido de las particiones del disco. Vemos que está en dev/sda1 

<img width="881" height="570" alt="016_photo" src="https://github.com/user-attachments/assets/edca7a49-b641-4ec0-acd4-fa0c784d7a0a" />

Y conseguimos el id_rsa vamos a darle permisos de ejecución crearnos el archivo y probar a conectarnos vía ssh como root.

<img width="966" height="544" alt="017_photo" src="https://github.com/user-attachments/assets/8ef97a74-dd80-44ac-a64e-65b918654c60" />

Salud ^^
