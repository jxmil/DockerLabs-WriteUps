# Allien

## 🔎 Enumeración

Se realiza un escaneo de puertos utilizando **Nmap**, donde se identifican inicialmente los puertos:

* 22/tcp – SSH
* 80/tcp – HTTP

Posteriormente se realiza un nuevo escaneo de puertos para obtener mayor precisión en los servicios activos, logrando identificar también el servicio **SMB**

<img width="467" height="123" alt="Pasted image 20260224122554" src="https://github.com/user-attachments/assets/222a9a50-046a-45e3-8f3e-97e647209d13" />

<img width="947" height="935" alt="Pasted image 20260224122729" src="https://github.com/user-attachments/assets/dc010b72-5385-448c-ba9d-ba7a80ccb89c" />

## 🌐 Análisis del servicio web

Se visita la página web y se observa la presencia de un login

<img width="657" height="573" alt="Pasted image 20260224122800" src="https://github.com/user-attachments/assets/cdddfdb6-b554-4aee-8939-b903bcd71b79" />

Se prueban credenciales por defecto, pero no se obtiene acceso

Se realiza fuzzing web llamando la atención el archivo ```info.php```, aunque por el momento se decide dejar el servicio web de lado para analizar el servicio **SMB**

<img width="763" height="207" alt="Pasted image 20260224123440" src="https://github.com/user-attachments/assets/4e3992ad-1335-4702-996b-0d270e361439" />

<img width="1005" height="436" alt="Pasted image 20260224123524" src="https://github.com/user-attachments/assets/3deec3db-22e8-4629-a40b-32ed3a9b3913" />

## 📂 Enumeración SMB

Se procede a enumerar usuarios del servicio **SMB** utilizando **enum4linux**:

```bash
enum4linux 172.17.0.2
```

<img width="1157" height="262" alt="Pasted image 20260224123804" src="https://github.com/user-attachments/assets/ce8dc6c0-d04d-4c47-ba76-0ece439a57a2" />

Se identifican varios usuarios, los cuales se guardan en un archivo ```users.txt``` para utilizarlos posteriormente

<img width="307" height="280" alt="Pasted image 20260224124307" src="https://github.com/user-attachments/assets/dcf7acdf-831f-4347-a255-caf872f8e66f" />

## 💥 Fuerza bruta al servicio SMB

Con la lista de usuarios obtenida, se realiza un ataque de **fuerza bruta** contra el servicio **SMB** utilizando **netexec**:

```bash
netexec smb 172.17.0.2 -u users.txt -p /usr/share/wordlists/rockyou.txt --ignore-pw-decoding
```

<img width="873" height="72" alt="Pasted image 20260224124536" src="https://github.com/user-attachments/assets/e1d8fd66-f659-45c6-a6e2-61aac06b18c3" />

Se logran obtener credenciales válidas

Posteriormente se verifican los permisos disponibles con **smbmap**:

```bash
smbmap -H 172.17.0.2 -u satriani7 -p 50cent
```

<img width="1211" height="407" alt="Pasted image 20260224124646" src="https://github.com/user-attachments/assets/4cda5747-f18c-4bfd-a5ae-1b8b1be5d292" />

Se observa que el usuario tiene permisos de lectura sobre un recurso compartido

## 📁 Acceso al recurso compartido

Se accede al recurso compartido mediante **smbclient**:

```bash
smbclient //172.17.0.2/backup24 -U satriani7
```

<img width="1351" height="247" alt="Pasted image 20260224130207" src="https://github.com/user-attachments/assets/539a65e0-1d47-4c59-9c58-0d64d781bbd2" />

Dentro del recurso se navega hasta la ruta: ```/Documents/Personal```

<img width="1351" height="247" alt="Pasted image 20260224130207" src="https://github.com/user-attachments/assets/5b9c29ee-a9a2-4584-a2f4-5e71168e95c9" />

En este directorio se encuentran dos archivos **.txt**, los cuales se descargan para su análisis

Al revisar su contenido, se obtienen credenciales del usuario **administrador**

<img width="385" height="83" alt="Pasted image 20260224130310" src="https://github.com/user-attachments/assets/4483ceab-fe44-4f26-92d1-ca5003396464" />

<img width="923" height="862" alt="Pasted image 20260224130333" src="https://github.com/user-attachments/assets/28c5b689-ed0a-4962-ac63-3846f0df1e08" />

## 🔑 Acceso por SSH

Con las credenciales encontradas se inicia sesión mediante SSH como usuario **administrador**

Una vez dentro del sistema se intenta realizar escalada de privilegios, pero el usuario no cuenta con permisos elevados

<img width="767" height="253" alt="Pasted image 20260224130820" src="https://github.com/user-attachments/assets/4c3cddd1-b5db-46ab-ba2d-6d485991f26c" />

Se buscan binarios con permisos **SUID**, pero inicialmente no se encuentra nada relevante

<img width="536" height="247" alt="Pasted image 20260224130939" src="https://github.com/user-attachments/assets/c09ee7ae-6d0a-44dd-a366-f4f5bbf263e5" />

## Obtención de reverse shell

Tras continuar la exploración del sistema, se decide obtener acceso como **www-data** mediante una reverse shell generada por [Reverse Shell Generator](https://www.revshells.com/) 

<img width="1223" height="773" alt="Pasted image 20260224133201" src="https://github.com/user-attachments/assets/8216c507-be87-4059-af3c-7bc6e0d95bbc" />

Se crea un servidor en la máquina atacante para transferir el archivo malicioso y desde la máquina objetivo se descarga

<img width="617" height="72" alt="Pasted image 20260224133422" src="https://github.com/user-attachments/assets/4b3db4e0-216e-4ae1-8022-2e797bd2fba2" />

Posteriormente, en la máquina atacante se inicia un listener en el puerto 4545 y desde el navegador se accede a: ```http://172.17.0.2/reverse.php```

<img width="723" height="181" alt="Pasted image 20260224134106" src="https://github.com/user-attachments/assets/6bafe849-ba55-4822-857b-4cfdccbf6319" />

De esta manera se obtiene acceso a la máquina como **www-data**

## 👑 Escalada de privilegios

Una vez dentro, se revisan los permisos y se identifica que cualquier usuario puede ejecutar un binario como **root** sin necesidad de contraseña

<img width="1278" height="112" alt="Pasted image 20260224134227" src="https://github.com/user-attachments/assets/31d055a5-4e45-4e47-a5f7-1ab85466927c" />

Se busca el binario en [GTFOBins](https://gtfobins.org/)  para encontrar una técnica de explotación

<img width="1003" height="482" alt="Pasted image 20260224134316" src="https://github.com/user-attachments/assets/d4c57121-e4c0-48ea-a7b9-3f2f50cba848" />

Tras ejecutar el comando correspondiente, se logra escalar privilegios y obtener acceso como **root**

<img width="397" height="100" alt="Pasted image 20260224134408" src="https://github.com/user-attachments/assets/bcdb642f-484e-49a8-a956-70c331056933" />

## 🏁 Conclusión

Este laboratorio demuestra cómo una correcta enumeración de servicios, combinada con ataques de fuerza bruta al servicio SMB, puede conducir a la obtención de credenciales válidas. Posteriormente, mediante acceso al sistema, reverse shell y explotación de binarios privilegiados, es posible escalar privilegios hasta obtener acceso root.
