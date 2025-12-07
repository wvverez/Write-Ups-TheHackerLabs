## CONTACTO

## ENUMERACIÓN 

Vamos a empezar como siempre con un escaneo nmap sencillo para buscar puertos abiertos. 

<img width="932" height="530" alt="01_escaneoNMAP" src="https://github.com/user-attachments/assets/22f67f80-41d3-4aca-b8e7-cea8c25a1b22" />

Vemos el puerto 80 y 22 abiertos, vamos a enumerar su servicio y dicha versión de el: 

<img width="946" height="461" alt="02_Serviciospuertos" src="https://github.com/user-attachments/assets/f4687d79-eee5-4d14-a8cb-bec15b6ba134" />

Vale bien, vemos que el puerto 22/tcp apunta a un OpenSSH Debian. Y el 80/tcp apunta a una página de apache httpd 2.4.57.
Nos fijamos que es posible que el SO pueda ser un Debian. Vamos a echarle un vistazillo a la web a ver que encontramos.

<img width="781" height="691" alt="03_paginaprincipal" src="https://github.com/user-attachments/assets/ddc0099f-5649-495d-bb1b-71c30874bf46" />

Bueno es un simple buscador de frutas, en el código fuente no se apreciaba nada relevante, de primeras probé a buscar algo cualquiera por ejemplo manzana en este caso.

<img width="1765" height="474" alt="05_FUZZING" src="https://github.com/user-attachments/assets/ba387c1a-ff55-40b3-bccc-1374b078eddb" />

Vemos que nos direcciona a un supuesto buscar.php y vemos que no existe, nos cuela un medio rabbit hole. Esto puede ser por que haya sido eliminado o podría estar relacionado con otro fichero, vamos a aplicar fuzzing para encontrar alguna ruta potencial a la que pueda redirigir.

<img width="1765" height="474" alt="05_FUZZING" src="https://github.com/user-attachments/assets/82ac6fb4-33a4-426f-920d-999d6b41a344" />
<img width="1047" height="221" alt="06_FRUITSPHP" src="https://github.com/user-attachments/assets/7708d25b-504d-4388-99a3-73067ddc8676" />

Nos encontró algunos archivos el que más me llamó la atención fue el fruits.php pero con todos se quedaba la pantalla en negro, y sin nada en el código fuente, tal que así.

<img width="794" height="691" alt="07_FRUITS" src="https://github.com/user-attachments/assets/317388a8-3efe-497a-b181-f168477776f1" />

Con lo cual tenemos que seguir haciendo fuzzing pero esta vez por parámetros, vimos que no apuntaba a nada así que seguramente apunte a algo en concreto, probé via algún LFI.

<pre>
  <code>
    ?FUZZ=/etc/passwd
  </code>
</pre>

<img width="1781" height="588" alt="08_LFI" src="https://github.com/user-attachments/assets/64a4d210-a1a5-4610-867a-c0b7620ab95d" />

Tenemos un LFI, con /etc/passwd probé con hosts también aunque solo funciono de esta forma. Pero hemos encontrado el usuario bananaman. Con lo cual ahora podemos intentar hacer fuerza bruta al servicio SSH empleando como usuario bananaman por que claro, al intentar conectarte por ssh te pedirá una contraseña la cual no tenemos.

<img width="1860" height="332" alt="010_celtic" src="https://github.com/user-attachments/assets/e13b8a47-a7c0-4267-9db4-ed8e66566cbb" />

Crackeamos la contraseña con lo cual ahora podemos acceder por ssh como bananaman

<img width="929" height="453" alt="011_ssh" src="https://github.com/user-attachments/assets/ec22dd10-d4e6-45c6-880a-050cf6315c76" />

Ya estaríamos como el usuario bananaman. Teniendo sus credenciales vamos a ver si tiene algun permiso sudoers al hacer sudo -l 

<img width="954" height="291" alt="012_bananaman" src="https://github.com/user-attachments/assets/3e96553c-6e80-45c9-bc09-916edd98f9d5" />

Tiene el binario find donde podemos usar como cualquier usuario sin necesidad de contraseña este binario, si buscamos en GTFOBINS

Vamos a poner lo encontrado

<pre>
  <code>
    sudo find . -exec /bin/bash \; -quit 
  </code>
</pre>

<img width="796" height="198" alt="013_root" src="https://github.com/user-attachments/assets/563a3302-ae60-4304-bf38-564c3af7a89f" />

Salud ^^
