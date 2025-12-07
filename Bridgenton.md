## ⚙️ CTF:

<img width="322" height="327" alt="00_Bridgenton" src="https://github.com/user-attachments/assets/1c94d0cc-c54f-4f53-8a2d-597d7f7ae6b8" />

🪪 Nombre: Bridgenton

🛜 SO: Linux 

🔊 Dificultad: Avanzado

👥 Creadores: Condor & CuriosidadesdeHackers

## CONTACTO

[![Telegram](https://img.shields.io/badge/-Telegram-2CA5E0?style=for-the-badge&logo=telegram&logoColor=white)](https://t.me/wvverez)
[![Twitter](https://img.shields.io/badge/-Twitter-1DA1F2?style=for-the-badge&logo=twitter&logoColor=white)](https://twitter.com/wvverez)

## ENUMERACIÓN
En el día de hoy traigo la resolución de la máquina Bridgenton catalogada como dificultad avanzada.

<img width="947" height="702" alt="01_Bridgenton" src="https://github.com/user-attachments/assets/5a593d2c-b8e3-47d3-a600-32daa7465351" />

Empezamos con un sencillo escaneo de red analizando los puertos abiertos servicios que corren y HostDiscovering vemos el puerto 22 donde corre ssh por defecto y en el puerto 80 tenemos una página como de una especie de universidad vamos a echarle un ojillo.

<img width="860" height="753" alt="02_Bridgenton" src="https://github.com/user-attachments/assets/cc5708d4-c79d-4663-a73f-214db5612a2f" />

Vemos la página de la universidad bueno vemos que hay muchas opciones que redirigen a varias webs diferentes vamos a echarle un vistazo al código fuente.

<img width="854" height="592" alt="03_Bridgenton" src="https://github.com/user-attachments/assets/4f8a3ceb-8810-4e0f-98d5-bdb4c536319b" />

Bien, vemos que redirigen a múltiples sitios en el de profesorado solamente tenemos información detallada de los profesores no encontré nada más el panel de login no es vulnerable con lo cual tampoco lo acabe usando sin embargo. Vi registro.php en el que podías subir archivos png,jpeg, o jpg. Fue interesante y lo usaremos pero vamos a hacer fuzzing a ver si encontramos alguna ruta potencial más.

<img width="940" height="632" alt="04_Bridgenton" src="https://github.com/user-attachments/assets/6d6651e7-4381-4d6f-842a-ae5e4e99c23c" />
<img width="956" height="167" alt="05_Bridgenton" src="https://github.com/user-attachments/assets/e559a5c5-2fc8-415f-be6f-c477a30f1492" />

## RECONOCIMIENTO

Encontramos el subdirectorio uploads, el resto no fueron interesante aquí podemos ver lo que se subía, las actualizaciones basicámente.

<img width="735" height="356" alt="06_Bridgenton" src="https://github.com/user-attachments/assets/9fa65204-d0ec-4d0d-bb89-4f52bc116287" />

Aquí nos aparecerían las actualizaciones de los archivos que se suban. Vamos a intentar subir un archivo php a ver que podemos experimentar.

<img width="958" height="345" alt="07_Bridgenton" src="https://github.com/user-attachments/assets/f5f7afb2-3b13-4bad-9caa-79c22e67d523" />
<img width="883" height="330" alt="08_Bridgenton" src="https://github.com/user-attachments/assets/185834d9-a189-4f35-90d1-282b9aa6aa89" />
<img width="918" height="153" alt="09_Bridgenton" src="https://github.com/user-attachments/assets/47ad0875-ba5c-41c1-9ecb-78289bab235a" />

Bueno como era de esperar, no nos deja sin embargo existen múltiples opciones a php que nos dejarían interpretarlo igualmente con la misma función y que pueden no estar filtradas, con lo cual vamos a probar, existen bastantes como php6,php7,phtml...

<img width="678" height="692" alt="010_Bridgenton" src="https://github.com/user-attachments/assets/f4b564dc-4bbb-4510-922f-7344750fe82d" />

## EXPLOTACIÓN

Bien vamos a abrirnos el burp para poder analizarlo mejor así de primeras con la extensión .php no nos va a dejar poder subirlo. Con lo cual toca probar con varios hasta que di con .phtml

<img width="508" height="162" alt="011_Bridgenton" src="https://github.com/user-attachments/assets/cabf3c6d-3a14-4a21-9e51-53e2f4bb59e1" />
<img width="494" height="268" alt="012_Bridgenton" src="https://github.com/user-attachments/assets/4c40fec9-37ab-45f3-80ec-3c311820b8dd" />

En el cual me acabo subiendo el archivo correctamente así que en la ruta que antes encontramos debería haberse actualizado, en uploads.

<img width="953" height="455" alt="013_Bridgenton" src="https://github.com/user-attachments/assets/a5eb9d05-ee85-4dbc-a57d-22bd33709cee" />

Efectivamente se subió al servidor, bueno vamos a posicionarnos y a ver si tenemos vía potencial por RCE.

<img width="961" height="266" alt="014_Bridgenton" src="https://github.com/user-attachments/assets/da1e9ba6-2e10-408d-be7f-b4ce56136ab6" />

Efectivamente podemos, bueno pues lo siguiente que haremos será subirnos una revshell para conseguir acceso a la máquina víctima, para conseguir acceso a la máquina víctima, para conseguir una consola interactiva de la máquina víctima.

<img width="1862" height="636" alt="015_Bridgenton" src="https://github.com/user-attachments/assets/4f3323f3-5c14-4432-8f9a-bed74f3d9f62" />

Nos ponemos en escucha en el puerto 4444 y nos subimos la revshell y conseguimos acceso como el usuario www-data. Ahora a buscar información confidencial.
Anduve buscando por la raíz para ver si podiamos abusar de algun binario con permiso SUID. y si encontré algo raro que era en base64 .

<img width="827" height="478" alt="016_Bridgenton" src="https://github.com/user-attachments/assets/6a575208-45fa-4bb8-be0a-dbd32c70b991" />

Bueno vamos a buscar en GTFOBins a ver si podemos abusar de este binario y acabé encontrando algo.

<img width="972" height="320" alt="017_Bridgenton" src="https://github.com/user-attachments/assets/fd9a83f8-5719-4614-b5d8-1f63eb1ad10e" />

Bueno pues abusando de esto tendríamos acceso al id_rsa del usuario james.

<img width="840" height="617" alt="018_Bridgenton" src="https://github.com/user-attachments/assets/de85f594-a610-4417-8722-0e0b09eafc80" />

Vamos a pasarnoslo a hash para poder romperlo con john, ya que podemos acceder por ssh como james si conseguimos la contra del id_rsa sin usar la de james.

<img width="671" height="257" alt="019_Bridgenton" src="https://github.com/user-attachments/assets/2d5a79fb-7fbf-49f1-b394-2054f43c9df7" />

Bien, vamos a crackearlo usaremos el diccionario rockyou.txt

<img width="973" height="253" alt="020_Bridgenton" src="https://github.com/user-attachments/assets/686cbc12-96b8-43ac-a5f1-50abdc584724" />

Bien tenemos la clave del id_rsa del usuario james vamos a acceder por ssh usando el id_rsa-

<img width="890" height="491" alt="021_Bridgenton" src="https://github.com/user-attachments/assets/9d7c517d-b25c-48ed-bc4a-7b33bfa7729d" />

## ESCALADA DE PRIVILEGIOS

Bien, conseguido ya estamos como el usuario james, vamos con la escalada, vamos a ver los permisos sudoers primeramente.

<img width="883" height="313" alt="022_Bridgenton" src="https://github.com/user-attachments/assets/be1a9f2d-0da6-405e-b245-2fcf09d9b616" />

Basicámente el usuario james puede ejecutar sin contraseña lo siguiente

<img width="633" height="258" alt="023_Bridgenton" src="https://github.com/user-attachments/assets/9226694e-5581-4da5-a224-b2824c29ed90" />

Vemos que tenemos vía potencial por libraryhijacking es bastante interesante.

<img width="880" height="156" alt="024_Bridgenton" src="https://github.com/user-attachments/assets/ca3f2b22-7eda-4b1f-b567-f0621010cf12" />

Aquí tenemos la lista de rutas donde Python busca librerías para importarlas vemos que las 2 primeras comillas tenemos acceso con lo cual podemos anticiparnos a que busque demás librerías existentes.
Vamos a subirnos una revshell en python
Nos creamos el siguiente módulo hashlib.py y ponemos nuestra revshell

<img width="860" height="246" alt="026_Bridgeton" src="https://github.com/user-attachments/assets/bef7458c-c4f7-4bd8-8c6b-1afc6d7d11b6" />

Bien si ahora nos ejecutamos la revshell deberíamos conseguir acceso como el usuario root. Vamos a probar.

<img width="1874" height="825" alt="027_Bridgenton" src="https://github.com/user-attachments/assets/cda57e9f-234c-49bc-b464-bea52bc87bee" />

Nos ponemos en escucha en el puerto 4444 nos lo ejecutamos y acabamos de vulnerar por completo la máquina.

Salud ^^ 
