# PkgPoison

## 🔎 Enumeración

Se realiza un escaneo de puertos con Nmap, donde se identifican los siguientes servicios abiertos:

* 22/tcp – SSH
* 80/tcp – HTTP

<img width="930" height="467" alt="Pasted image 20260212104728" src="https://github.com/user-attachments/assets/dbf98a45-e265-404f-9b2e-ae0a98930d9a" />

Al acceder al servicio web, se inspecciona el código fuente sin encontrar información relevante

<img width="990" height="727" alt="Pasted image 20260212104859" src="https://github.com/user-attachments/assets/31f42430-15ea-4f0f-af58-50261b223f4e" />

Por ello, se procede a realizar fuzzing de directorios, descubriendo varias rutas ocultas. Entre ellas destaca:

<img width="797" height="172" alt="Pasted image 20260212105637" src="https://github.com/user-attachments/assets/75b3d6ab-f533-4fe5-bb6f-4db6fd0d2e8f" />

```
notes 
```

<img width="592" height="250" alt="Pasted image 20260212105412" src="https://github.com/user-attachments/assets/509c4f65-749c-4c6b-b42a-5ffbc98167eb" />

Se encuentra una nota con credenciales de un usuario

<img width="646" height="162" alt="Pasted image 20260212105513" src="https://github.com/user-attachments/assets/c498fd26-2b0f-4fc6-8b87-69c7e3827bee" />

## 🔓 Acceso inicial

Las credenciales encontradas no funcionan por *SSH*, por lo que se procede a realizar fuerza bruta con *Hydra*

<img width="827" height="290" alt="Pasted image 20260212110202" src="https://github.com/user-attachments/assets/d9d706ec-aa79-4308-ad98-ba0bffed483b" />

Se descubre que el usuario cambió la contraseña, pero mantiene una débil, lo que permite obtener acceso al sistema

<img width="632" height="32" alt="Pasted image 20260212110539" src="https://github.com/user-attachments/assets/c3d8e11f-17fe-4564-ad1e-671b6438c9d7" />

## 🕵️ Enumeración interna

Una vez dentro, se revisa el sistema en busca de información sensible

<img width="773" height="357" alt="Pasted image 20260212110739" src="https://github.com/user-attachments/assets/89cdc65c-1ee1-4412-9a89-88efdd8a9fe2" />

Intentamos escalar privilegios, pero el usuario no tiene permisos 

<img width="527" height="78" alt="Pasted image 20260212111023" src="https://github.com/user-attachments/assets/aaca697a-3e77-4bec-b635-06fe3b325685" />

Decido buscar binarios con permisos SUID en todo el sistema, pero no veo nada que me llame la atención

<img width="737" height="272" alt="Pasted image 20260212111527" src="https://github.com/user-attachments/assets/6c71a6d6-d61e-4c46-a2ff-181ed3246fc5" />

Navegando un poco por la maquina, en el directorio ```/opt/scripts/pycache``` se encuentra un archivo ```.pyc``` que, al analizarlo con **strings**, revela credenciales en texto plano

<img width="826" height="226" alt="Pasted image 20260212112335" src="https://github.com/user-attachments/assets/7f4d94c4-ae85-4d69-b1ab-eb294e74cefc" />

Con estas credenciales, se logra cambiar al usuario **admin**

<img width="607" height="87" alt="Pasted image 20260212112420" src="https://github.com/user-attachments/assets/eaa78be0-8511-441a-9861-ecbfa3e9a089" />

## 🔐 Escalada de privilegios

Intentamos escalar privilegios y se identifica un binario que puede ejecutarse como **root** sin necesidad de contraseña

<img width="1285" height="138" alt="Pasted image 20260212112541" src="https://github.com/user-attachments/assets/4727777b-7471-4a89-a6cc-77378bb48bd4" />

Se crea un directorio temporal único mediante el comando

```bash
TF=$(mktemp -d)
```

<img width="451" height="25" alt="Pasted image 20260212113717" src="https://github.com/user-attachments/assets/d0c8f70a-1e87-4b8f-acd2-d6d908fa8083" />

```
Nota: Se genera un directorio temporal con un nombre aleatorio para evitar conflictos con otros archivos o directorios existentes.
La ruta generada se almacena en la variable TF, lo que permite referenciar fácilmente dicho directorio más adelante en el script.
```

A continuación, se crea un archivo malicioso llamado **setup.py** dentro del directorio temporal recién generado:

```bash
echo "import os; os.execl('/bin/bash', 'bash', '-c', 'bash <$(tty) >$(tty) 2>$(tty)')" > $TF/setup.py
```

<img width="1568" height="28" alt="Pasted image 20260212114716" src="https://github.com/user-attachments/assets/765e8368-f4c1-4987-9d98-0dbf577be918" />

```
Nota: El archivo setup.py importa el módulo os y usa os.execl() para reemplazar el proceso actual por una nueva shell (/bin/bash).
Si este archivo se ejecuta con privilegios elevados (por ejemplo, como root), la nueva shell también tendrá esos privilegios, lo que puede permitir obtener acceso con permisos elevados.
```

Al ejecutar el binario vulnerable, se obtiene acceso como **root**, logrando el control total del sistema

<img width="736" height="120" alt="Pasted image 20260212114827" src="https://github.com/user-attachments/assets/9f84df46-600c-47b6-a24b-eb193917b38b" />

## 🏁 Conclusiones

Este laboratorio demuestra cómo una mala gestión de credenciales y configuraciones inseguras pueden comprometer completamente un sistema. Desde una simple enumeración web se obtuvo información sensible, posteriormente una contraseña débil permitió el acceso inicial por SSH, y finalmente una incorrecta configuración de permisos sudo junto con credenciales expuestas en archivos internos facilitaron la escalada a root.
