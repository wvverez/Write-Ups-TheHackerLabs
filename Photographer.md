## CONTACTO 


## ENUMERACIÓN
Vamos a empezar con un escaneo nmap:

![01_photo.PNG](attachment:22f6cd7d-a9eb-4f88-a003-04ebbc98ec9c:01_photo.png)

Vamos a empezar como siempre enumerando todos los puertos y servicios que corren en cada uno. Vemos abiertos el puerto 80 y 22 algo bastante común. En el que tenemos ssh por defecto y una página de un blog de una persona llamada ethan. Vamos a acceder a ella a ver si encontramos algo

![02_photo.PNG](attachment:863b2359-26f8-4dd3-99a4-38ab7f60719c:02_photo.png)

Bueno tenemos un posible usuario ethan, en el código fuente no acabé encontrando nada extraño, con lo cual vamos a aplicar fuzzing para encontrar rutas potenciales.

![03_photo.PNG](attachment:69086920-0115-4ef5-8018-a2267e6c896e:03_photo.png)
![04_photo.PNG](attachment:34486ccc-5baa-45ed-a399-0eb1f073845b:04_photo.png)

Tenemos un subdirectorio /admin, accediendo a el me encontré con un panel de login aunque no tenemos credenciales.

![05_photo.PNG](attachment:f8f25b7b-657e-4c3b-82b4-1dc7dd900543:05_photo.png)

Vamos a volver a hacer fuzzing pero esta vez por parámetros a partir del subdirectorio /admin.

![06_photo.PNG](attachment:70ec6578-1bb1-4d6e-9543-14a6de0df592:06_photo.png)

Encontramos la ruta admin.php vamos a irnos al burp para poder analizarlo mejor. 

![07_photo.PNG](attachment:b656a8d0-db14-42af-b44b-a35de8eed5e0:07_photo.png)

Bien ahora hacemos click derecho sobre la petición y le damos a do intercept > response to this request. Al darle a forward nos muestra un error 304

![08_photo.PNG](attachment:e5d982e7-f6e3-4538-b90d-9076da7fac4d:08_photo.png)

Una vez modificas el 304 por código de estado 200 ya te debería de cargar.

![09_photo.PNG](attachment:d7fb8dd7-38ca-41c1-9d78-27fc5bb1129e:09_photo.png)

Ahora desde el navegador y con el proxy de burp activado podemos ver el contenido. 
Tenemos un panel en el que podemos subir fotografías. Vamos a analizar esta petición más comodamente en el repeater para ver si podemos inyectar código. 
