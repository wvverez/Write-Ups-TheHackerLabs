## CTF


## CONTACTO


## ENUMERACIÓN 

Empezamos como siempre con mi herramienta echa en bash haciendo hostdiscovering y analizando puertos abiertos y servicios que corren en cada uno de ellos.
Nos fijamos en el 22 que corre ssh por defecto en el 80 la página de apache predeterminada y en el 5000 tenemos algo interesante que vemos que corre Werkzeug.

<img width="1306" height="680" alt="01_pinguping" src="https://github.com/user-attachments/assets/3eb16b73-579a-42ae-8af5-ec8b9806d670" />

Vamos a ver la página en el puerto 5000.

<img width="858" height="706" alt="02_pinguping" src="https://github.com/user-attachments/assets/b53bc523-a371-43d3-b460-86d92121472d" />

Interesante, vemos una especie de Ping tester a IPs que le indiquemos, vamos a analizarlo mejor en burp.

<img width="553" height="640" alt="03_pinguping" src="https://github.com/user-attachments/assets/0b5bfbb7-3155-4f28-8a22-7de15d4fada7" />

Aquí vi un poco más en detalle como respondía, se me ocurrio algo que fue el echo de poder aprovecharlo con ";" haciendo así una solicitud que llegará al servidor que a partir de lo primero se cumpla o no realize lo segundo.

Probé esto mismo con un ls -la a ver si me lo interpreta.

<img width="503" height="325" alt="04_pinguping" src="https://github.com/user-attachments/assets/85f66082-16f0-49b0-b33f-c9a4f1c16a9c" />
<img width="455" height="449" alt="05_pinguping" src="https://github.com/user-attachments/assets/42c24c47-5901-42b6-b631-ae3f73c5d339" />

Me lo respondió, esto es algo completamente crítico ya que podemos abusar de esto. Y lo hicimos, vamos a subirnos nuestra revshell. La subimos y nos ponemos en escucha en el puerto 4444

<img width="1474" height="774" alt="06_pinguping" src="https://github.com/user-attachments/assets/cca50626-dcd8-4cac-b291-b46b1d2c1dab" />

Perfecto ya estaríamos dentro como el usuario tester cambie algunas funciones para hacer la bash interactiva.

<img width="779" height="440" alt="07_pinguping" src="https://github.com/user-attachments/assets/4af66e9d-ab52-4c93-b69b-8652f7314b19" />

Y me encontré una base de datos en mongodb a la que podía acceder. 

<img width="888" height="522" alt="08_pinguping" src="https://github.com/user-attachments/assets/ccf9ee7f-a5bf-4ad6-b5f1-1b939162555a" />

Mongodb tiene distinta dinámica a mysql o oracle aquí no se dividen por tablas o columnas se enumera por colecciones.

<img width="733" height="675" alt="09_pinguping" src="https://github.com/user-attachments/assets/86e3fc73-ec23-486a-8f0c-86b1588abb75" />

Aquí conseguimos la contraseña del usuario "secretote" con lo cual vamos a conectarnos por ssh a este usuario.

Una vez dentro como el usuario secretote mire los binarios sudoers que tenía asignado y tenía asignado el binario sed.

<img width="865" height="361" alt="010_pinguping" src="https://github.com/user-attachments/assets/aa60cb2e-ced3-4c65-ab0e-d0e32307a9ec" />

Con lo cual podemos abusar de este binario para conseguir una consola como root sin necesidad de contraseña.

<img width="585" height="107" alt="011_pinguping" src="https://github.com/user-attachments/assets/7042a50b-f813-4f2c-b412-e5034e442b29" />

Una vez echo esto ya estaríamos como root.

Salud ^^ 
