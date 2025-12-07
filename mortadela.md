## CONTACTO



## ENUMERACIÓN 

Vamos a empezar con un sencillo host Discovering y escaneando puertos abiertos y los servicios que corren.

<img width="995" height="671" alt="01_Mortadela" src="https://github.com/user-attachments/assets/a6a246a2-9f1f-47de-ac81-eec5f9c5cf36" />

Vemos que tenemos el puerto 22, 80 y 3306. En el que corre ssh por defecto, en el 80 tenemos la página de apache por defecto, y en el 3306 vemos que corre mysql.

Entre en la página de apache pero no encontré nada en la página ni en el código fuente así que me puse a hacer fuzzing. Para buscar posibles rutas porenciales.

<img width="1610" height="464" alt="02_Mortadela" src="https://github.com/user-attachments/assets/0896ef51-f092-4bcd-b130-f336c45af96a" />
<img width="1455" height="442" alt="03_Mortadela" src="https://github.com/user-attachments/assets/22843151-bf21-43ee-954e-dab503a68119" />

Y dimos con un wordpress. Bueno vamos a echarle un vistazo.

<img width="821" height="637" alt="04_Mortadela" src="https://github.com/user-attachments/assets/158dd1d3-f263-42cc-a8be-75a4ec03988e" />

La página hiba medio rara, no cargaba por completo así que hice una enumeración en wpscan para analizarlo con más detalle.

<img width="1127" height="627" alt="05_Mortadela" src="https://github.com/user-attachments/assets/3f1cb74a-e8b3-447d-93ed-4ebb7d560490" />

Encontré vía potencial por xmlrpc.php pero no encontramos nada. También vi que teniamos un usuario confirmado llamado "mortadela".

<img width="1092" height="340" alt="06_Mortadela" src="https://github.com/user-attachments/assets/f595a49f-e39b-44e1-9b12-6f767f8c8067" />

Lo siguiente que encontré fue un wordpress en versión 6.4.3 lo cual es algo bastante crítico vi que la web contenía un plugin "wpdiscuz 7.0.4" con lo cual busque CVE para poder explotarlo y conseguí un exploit que me permitía RCE.

https://github.com/hev0x/CVE-2020-24186-wpDiscuz-7.0.4-RCE.git

<img width="967" height="611" alt="07_Mortadela" src="https://github.com/user-attachments/assets/fdf8b566-70b9-4858-ab41-90b6cda78c02" />

Aquí consuegí subirme una webshell. Con lo cual vamos a acceder a ver si tenemos un posible RCE.

<img width="1186" height="394" alt="08_Mortadela" src="https://github.com/user-attachments/assets/6c92fd85-b9b1-4687-bb40-7c9352231727" />

Efectivamente lo tenemos tuve que cambiar la IP ya que no sé que problema tenía la página con los enlaces y ahí si me cargo la webshell probé a hacer ls y todo correcto. Con lo cual lo siguiente que vamos a hacer va a ser subirnos una revshell para ganar acceso. 

<img width="1829" height="758" alt="09_Mortadela" src="https://github.com/user-attachments/assets/a1357387-316c-4f7b-80e9-0a18c7945cf8" />

Me puse en escucha en el puerto 4444 y me la subí con esto gane acceso como el usuario www-data pude acceder a la base de datos en Mysql pero era una especie de rabbithole lo cual no me acabo llevando a nada sin embargo. buscando a través de la raíz en la carpeta /opt acabé encontrando un zip un tanto interesante. Vamos a jugar con el.

<img width="925" height="667" alt="010_Mortadela" src="https://github.com/user-attachments/assets/643b467b-13e3-4e33-a121-256140f9960e" />

Me lo pasé a mi máquina atacante para poder trabajar más cómodo y poder manipularlo, me seguía pidiendo contraseña para descomprimirlo.

<img width="958" height="303" alt="011_Mortadela" src="https://github.com/user-attachments/assets/8e4fb157-d369-4939-b4c8-5b410a43a183" />

Me lo pasé a formato legible para poder crackearlo con john lo pase a hash_zip.

<img width="967" height="446" alt="012_Mortadela" src="https://github.com/user-attachments/assets/1c5f06a5-d5d9-4b2d-8223-58bd3eb7b4b1" />

Me lo pasé a formato legible para poder crackearlo con john lo pase a hash_zip

<img width="952" height="258" alt="013_Mortadela" src="https://github.com/user-attachments/assets/aee45772-c02b-495c-a060-2cff9e5cd440" />

Y con john pude crackearlo y conseguir la contraseña para descomprimirlo. Con lo cual vamos a descomprimirnoslo.

<img width="934" height="244" alt="014_Mortadela" src="https://github.com/user-attachments/assets/b40054d8-7a34-474a-ba21-f85310957baa" />

Vemos 2 archivos Database.kbdx y KeePass.DMP buscando un poco encontré un dump en github que me permitió poder conseguir la contraseña al Keepass.

<img width="780" height="508" alt="015_Mortadela" src="https://github.com/user-attachments/assets/5a62198d-8141-46dd-8138-52b7122d77aa" />
<img width="965" height="273" alt="016_Mortadela" src="https://github.com/user-attachments/assets/b2fffd75-a206-4ddc-bbaa-911acd39e9cc" />

Me lo moví para tenerlo todo junto y vamos a intentar dumpearlo.
Por aquí comparto el exploit: 

https://github.com/matro7sh/keepass-dump-masterkey.git

<img width="940" height="370" alt="017_Mortadela" src="https://github.com/user-attachments/assets/df73b1f1-cae0-424e-ab20-326e63bd2ef9" />

Conseguimos bastantes posibles contraseñas las letras filtradas normalmente podían ser o m o M o ñ o Ñ o n o N probando resulto ser Maritrini12345, con lo cual me puse la interfaz gráfica del keepass

<img width="887" height="682" alt="018_Mortadela" src="https://github.com/user-attachments/assets/77aecaa9-fba5-43d5-ae97-c7bcbb55321f" />

Probé a acceder y conseguí acabar accediendo al gestor de contraseñas podiendo conseguir así la contraseña del usuario root.

<img width="878" height="719" alt="019_Mortadela" src="https://github.com/user-attachments/assets/3a443b2f-718c-4e77-899b-d803dd8b86f9" />

Bueno una vez tenemos esto tenemos la máquina apunto de morir, vamos a acceder como root y a probar a ver si hay suerte.

<img width="979" height="168" alt="020_Mortadela" src="https://github.com/user-attachments/assets/945f3c31-f803-41e5-ab73-d6d440810d0a" />

Efectivamente ya hemos podido acceder como root teniendo acceso a las 2 flags, máquina resuelta

Salud ^^ 
