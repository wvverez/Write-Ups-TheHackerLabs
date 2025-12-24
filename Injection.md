## ⚙️ CTF:

<img width="1536" height="1024" alt="00_Injection" src="https://github.com/user-attachments/assets/20ad9d74-142f-4809-8926-8f072622b476" />

🪪 Nombre: Injection

🛜 SO: Linux 

🔊 Dificultad: Muy Fácil

👥 Creadores: El pinguino de mario

# 📋ENUMERACIÓN

Vamos a empezar con un sencillo escaneo nmap en el que vamos a analizar puertos abiertos.

<img width="696" height="251" alt="01_Injection" src="https://github.com/user-attachments/assets/a6a8c230-4ddb-4a1e-88c0-8b6c31866f32" />

Vemos que tenemos 2 puertos abiertos el 22 donde corre ssh y el 80 donde corre http.

<img width="1328" height="323" alt="02_Injection" src="https://github.com/user-attachments/assets/2d4fef4c-30af-43f3-8d2c-c9a2fa734e38" />

Vamos a realizar un segundo escaneo nmap para analizar más detalladamente los servicios y versiones que corren en ambos puertos.

<img width="1089" height="704" alt="03_Injection" src="https://github.com/user-attachments/assets/52b5c2b6-8c82-4ff1-aed7-20b92fc7af01" />

Accedemos a la web por http y vemos un panel de login sencillo, vamos a probar credenciales básicas y comunes como por ejemplo , admin-->password , admin-->admin, admin--> admin123

<img width="1089" height="704" alt="03_Injection" src="https://github.com/user-attachments/assets/e6319856-0c97-47e4-a6af-3a2b003280fd" />

No fue el caso, vamos a poner una comilla simple para ver como reacciona y si puede ser vulnerable a SQLi

<img width="1782" height="596" alt="04_Injection" src="https://github.com/user-attachments/assets/bef107b9-d6cc-4c23-a652-1c163d5addad" />

Vemos que nos da un error interno en el servidor en SQL syntax, vamos a analizarlo con sqlmap.

# 💣INTRUSIÓN

<img width="1859" height="472" alt="05_Injection" src="https://github.com/user-attachments/assets/4edadecd-a21b-4add-99f2-c1bf6f1f9baa" />

Vamos a filtrar por las DBS (databases) para ver si encontramos credenciales válidas para poder escalar.

<img width="460" height="289" alt="06_Injection" src="https://github.com/user-attachments/assets/01251b47-ce4e-4918-9b4d-a1e2d58903aa" />

Encontramos 5 DB aunque 4 son las que tiene MySQL por defecto con lo cual vamos a tirar por la que se llama "register".

<img width="1245" height="567" alt="07_Injection" src="https://github.com/user-attachments/assets/fe8b6cd4-4396-42c7-9bcf-29dc1b693c71" />

Lo siguiente que haremos será buscar tablas dentro de la DB "register"

<img width="608" height="304" alt="08_Injection" src="https://github.com/user-attachments/assets/3efdd05d-5811-4242-994f-261d745e94f3" />

Encontramos una tabla que se llama usuarios, aunque de momento no tenemos contraseñas, si solo tuvieramos acceso a usuarios podríamos probar a hacer fuerza bruta, aunque no es el caso, vamos a filtrar por columnas de la DB "register" y tabla "users".

<img width="1007" height="472" alt="09_Injection" src="https://github.com/user-attachments/assets/aa5352cf-2808-4c1a-b975-6a0beebbbcc0" />
<img width="570" height="357" alt="010_Injection" src="https://github.com/user-attachments/assets/9816e166-fbf5-4abd-bced-891b77a562c5" />

Ahora si encontramos 2 columnas una que se llama passwd y otra que se llama username con lo cual vamos a dumpearlo para poder acceder a las credenciales.

<img width="999" height="606" alt="011_Injection" src="https://github.com/user-attachments/assets/34b521e4-225d-407e-8d07-9c22a90813b5" />

Al hacer esto conseguiremos las credenciales de un usuario llamado "dylan" . Desde el panel de login podemos loguearnos como "dylan" pero realmente no conseguimos nada así que vamos a probar a acceder por ssh como este usuario.

# 🧗‍♂️ESCALADA DE PRIVILEGIOS

<img width="697" height="397" alt="012_Injection" src="https://github.com/user-attachments/assets/f980140d-7c15-4252-811d-4ede6f1c2cb6" />

Efectivamente pudimos acceder por ssh como "dylan".

Vamos a buscar desde la ráiz permisos SUID de los cuales podamos abusar.

<img width="538" height="263" alt="013_Injection" src="https://github.com/user-attachments/assets/bca5a899-82e3-4ee8-b797-6a8a6c6e3fc4" />

Tenemos el permiso SUID asociado del cual podemos abusar de la siguiente forma:

<img width="421" height="131" alt="014_Injection" src="https://github.com/user-attachments/assets/338d8268-3b93-416f-b0dd-f1cc2e66578b" />

Ya habríamos accedido como root, con lo cúal damos como resuelta la máquina.
