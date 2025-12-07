## ⚙️ CTF:

<img width="1024" height="1024" alt="00_bocatacalamar" src="https://github.com/user-attachments/assets/ec876249-a511-4e93-890b-c07ec1b84137" />

🪪 Nombre: BocatadeCalamares

🛜 SO: Linux 

🔊 Dificultad: Principiante

👥 Creadores: 4ssw120

## 🔐 CONTACTO

[![Telegram](https://img.shields.io/badge/-Telegram-2CA5E0?style=for-the-badge&logo=telegram&logoColor=white)](https://t.me/wvverez)
[![Twitter](https://img.shields.io/badge/-Twitter-1DA1F2?style=for-the-badge&logo=twitter&logoColor=white)](https://twitter.com/wvverez)

## 📋 ENUMERACIÓN 
En el día de hoy vamos a resolver la máquina Bocata de Calamares

<img width="1216" height="685" alt="01_bocatacalamar" src="https://github.com/user-attachments/assets/8bb236b5-cdcf-45a8-ad01-9f576c29c0d5" />

Empezamos haciendo hostdiscovering como siempre y enumerando puertos abiertos, y servicios que corren en ellos. Vemos 2 puertos abiertos el 22 que corre ssh por defecto y el 80 que vemos que corre nginx

Vamos a ver la página.

<img width="846" height="788" alt="02_bocatacalamar" src="https://github.com/user-attachments/assets/30da6828-36f5-49d7-b560-7ef9aa6199e8" />

## 🤔 RECONOCIMIENTO 

Vemos como supuestas noticias, ningún enlace hiba ni había nada interesante en el código fuente, así que como no disponía de nada más, vamos a aplicar fuzzing para buscar posibles rutas potenciales.

<img width="1626" height="489" alt="03_bocatacalamar" src="https://github.com/user-attachments/assets/c9937a50-9dca-40fc-a66b-5c68f3c62c20" />
<img width="1070" height="210" alt="04_bocatacalamar" src="https://github.com/user-attachments/assets/59d66e79-c4ab-4bd5-8b7d-236eaf721df0" />

Encontramos 2 rutas muy interesantes la de admin.php estaba vacía y sin nada en el código fuente pero el login.php me llevó a un panel de login.

<img width="848" height="767" alt="05_bocatacalamar" src="https://github.com/user-attachments/assets/18a6c45e-151c-4e95-9085-38646e7471b3" />

Ya que no tenemos credenciales ni nada me puse a probar y trastear vulnerabilidades comunes y posibilidades comunes, probé con SQLi me lo mande al burp para verlo mejor.

<img width="1679" height="767" alt="06_bocatacalamar" src="https://github.com/user-attachments/assets/d445bbde-ce28-4e07-9d1b-321e8c25f4cc" />

Aquí vemos como intercepta la solicitud vamos a probar con alguna query conocida.

<img width="486" height="502" alt="09_bocatacalamar" src="https://github.com/user-attachments/assets/747bc703-1c96-41c4-9104-07baccf58870" />

## 💥 EXPLOTACIÓN 

Probé con ´ or 1=1 -- - y muy importante URLencodearlo para que pueda interpretarlo.

<img width="501" height="581" alt="010_bocatacalamar" src="https://github.com/user-attachments/assets/495e251f-15aa-4266-92c1-978ee2e430be" />

Ha sido exitoso. Con lo cual si vamos al navegador ahora y probamos con esta consulta deberíamos poder bypassear el panel de login.

<img width="1391" height="381" alt="011_bocatacalamares" src="https://github.com/user-attachments/assets/da7c11b7-ba12-4fd8-9ada-5929ecd3e575" />

Al entrar vi esto, no tenía nada solo un administrador contando su vida entre en ese enlace. Y ví la lista de tareas de la que habla.

<img width="1555" height="197" alt="012_bocatacalamar" src="https://github.com/user-attachments/assets/9c5fdc8e-908e-4dd7-a4f5-a4e6915acd68" />

Aquí dice que codifico su nombre "lee_archivos" a base64. Así que bueno vamos a pasarlo a base64 y probar a acceder por vía potencial mediante un LFI.

<img width="389" height="144" alt="013_bocatacalamar" src="https://github.com/user-attachments/assets/e01a83cb-73a2-4f21-9ac3-d7c5275ede1a" />
<img width="738" height="757" alt="014_bocatacalamar" src="https://github.com/user-attachments/assets/eab974c1-d8a9-44b8-b38b-96e69ce4c51b" />

Al buscarlo encotramos vía potencial LFI, con lo cual nosotros pudimos conseguir información de el navegador de forma remota al buscar por el /etc/passwd nos listo todo.

Tenemos 3 usuarios: superadministrator, yuiop y root vamos a probar a crackear estos usuarios con hydra con el diccionario rockyou.txt 

<img width="1324" height="732" alt="015_bocatacalamar" src="https://github.com/user-attachments/assets/c6dec29c-19c4-4bc7-a201-de6c6dbdb26c" />

## 🧗‍♂️ ESCALADA DE PRIVILEGIOS 

Nos crackeo la contraseña bien, pues vamos a acceder vía ssh como el usuario "superadministrator"

<img width="955" height="318" alt="016_bocatacalamar" src="https://github.com/user-attachments/assets/e440a092-7d4f-4209-a9af-28cd9aeb7d63" />

Liste los permisos sudoers para ver los que tenía asignados para ver si podíamos hacer la escalada de privilegios sin acceder por la contraseña.

Tenía el binario sed el cual es muy crítico.

<img width="1122" height="202" alt="017_bocatacalamar" src="https://github.com/user-attachments/assets/3c652a37-4218-489f-b0e9-3b940a8e525b" />

Ya habríamos accedido como el usuario root.

Salud ^^
