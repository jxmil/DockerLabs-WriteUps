# ChocolateLovers

## 🔎 Enumeración

Se realiza un escaneo de puertos utilizando Nmap, donde se identifica que el puerto 80 (HTTP) se encuentra abierto

<img width="732" height="136" alt="Pasted image 20260108121043" src="https://github.com/user-attachments/assets/70830e0a-3c59-4cf7-a693-0d886514c3d8" />

## 🌐 Análisis del servicio web

Al visitar la página web, se inspecciona el código fuente y se observa un redireccionamiento hacia el directorio ```/nibbleblog```

<img width="700" height="710" alt="Pasted image 20260108122413" src="https://github.com/user-attachments/assets/1757584b-0858-4cb2-af30-af60be0687b9" />

Se accede a dicho directorio

<img width="1207" height="615" alt="Pasted image 20260108122745" src="https://github.com/user-attachments/assets/78579b61-0aaa-4b16-bd2b-456c766a3d82" />

 Se procede a realizar fuzzing web para identificar rutas sensibles

<img width="981" height="702" alt="Pasted image 20260108130416" src="https://github.com/user-attachments/assets/397f37e7-3784-4eb5-84a0-0c64be361881" />

Durante la enumeración se encuentra el archivo *admin.php*, el cual corresponde al panel de administración

## 🔐 Acceso al panel administrativo

Se intentan ataques de *inyección SQL*, sin resultados exitosos
Posteriormente, se prueban credenciales por defecto: ```admin:admin ```

<img width="597" height="302" alt="Pasted image 20260108132433" src="https://github.com/user-attachments/assets/36061171-4126-43b9-bc1f-48620dbd7e4b" />

Logrando acceder correctamente al panel de administración

## Obtención de acceso inicial

Dentro del panel, se identifican los plugins como una funcionalidad que permite subir archivos

<img width="1530" height="480" alt="Pasted image 20260108133421" src="https://github.com/user-attachments/assets/23f445b6-53ad-4960-b9ac-8a15b387ec13" />

Se aprovecha esta característica para subir una reverse shell en **PHP**, generada gracias a [Reverse Shell Generator](https://www.revshells.com/)

<img width="1231" height="827" alt="Pasted image 20260108133744" src="https://github.com/user-attachments/assets/c4dc6a14-6ac3-484b-8bc7-1fea7a539ff8" />

Una vez subida, se accede al directorio de plugins y desde la máquina atacante se inicia un listener para recibir la conexión inversa

<img width="1356" height="495" alt="Pasted image 20260108133857" src="https://github.com/user-attachments/assets/39dd0e36-35f1-4b1c-a2b4-6656e0d5ae5f" />

<img width="860" height="367" alt="Pasted image 20260108134138" src="https://github.com/user-attachments/assets/49fb305c-1192-4feb-b942-aecf46f6ab95" />

Con esto, se obtiene acceso al sistema

<img width="761" height="261" alt="Pasted image 20260108134229" src="https://github.com/user-attachments/assets/12ec1a26-d947-4672-a7db-ab4df95ee5c7" />

Hacemos tratamiento de TTY

<img width="240" height="140" alt="Pasted image 20251222222913" src="https://github.com/user-attachments/assets/9689eaa6-3489-46d7-ae01-e0e0533e075f" />

## Movimiento lateral

Al enumerar el sistema, se detecta que es necesario acceder como el usuario **chocolate**

<img width="1047" height="132" alt="Pasted image 20260108134529" src="https://github.com/user-attachments/assets/1fcca336-0903-4b06-b37f-0ab44676ccf6" />

Se realiza pivoting hacia dicho usuario para continuar con la escalada de privilegios

<img width="881" height="73" alt="Pasted image 20260108135633" src="https://github.com/user-attachments/assets/1ee0bd28-8605-4347-ae0b-d6bb65b5f8ea" />

## 🔐 Escalada de privilegios

Se intenta escalar privilegios sin éxito inicial, por lo que se procede a enumerar los procesos activos usando **ps aux**

Se identifica un script ubicado en /opt que se ejecuta periódicamente como root

<img width="1585" height="81" alt="Pasted image 20260108135918" src="https://github.com/user-attachments/assets/04a2eea8-f779-4f6c-b3a6-0d2e82e06bf7" />

Dado que el archivo cuenta con permisos de escritura, se modifica el script para otorgar permisos SUID a /bin/bash:

<img width="596" height="88" alt="Pasted image 20260108140008" src="https://github.com/user-attachments/assets/3cc7b50b-a4bc-4f73-8830-4ad901ee1b2c" />

```
echo "<?php system('chmod u+s /bin/bash'); ?>" > /opt/script.php

```

Al ejecutarse el script automáticamente, se asignan los permisos SUID al binario bash

Y somos root

<img width="853" height="107" alt="Pasted image 20260108140421" src="https://github.com/user-attachments/assets/61bf6317-8cac-49ed-8d97-40655a918fa0" />

## 🏁 Conclusión

Este laboratorio demuestra cómo el uso de credenciales por defecto permite un acceso inicial no autorizado que, al combinarse con funcionalidades inseguras de subida de archivos, facilita la ejecución de código malicioso para ganar una posición dentro del servidor; finalmente, esta cadena de ataque culmina en la escalada de privilegios mediante la explotación de scripts ejecutados como root con permisos de escritura, otorgando así el control total sobre el sistema
