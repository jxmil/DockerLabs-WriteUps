 # Los 40 Ladrones

 ## 🔎 Enumeración inicial

Se realiza un escaneo de puertos utilizando Nmap, donde se identifica únicamente el puerto:
* 80/tcp – HTTP

<img width="642" height="111" alt="Pasted image 20260203103229" src="https://github.com/user-attachments/assets/9fb09684-58c8-440e-a3a8-4fad627937d4" />

## 🌐 Análisis del servicio web

Al acceder a la página web, no se observa información relevante a simple vista

<img width="1050" height="447" alt="Pasted image 20260203103422" src="https://github.com/user-attachments/assets/80dd49e0-4d9f-471c-9abd-814452438510" />

Por ello, se procede a realizar fuzzing web, logrando encontrar un archivo ```.txt``` expuesto.

<img width="698" height="146" alt="Pasted image 20260203103901" src="https://github.com/user-attachments/assets/f10c8f33-29b0-4f28-b500-7857d2b4b185" />

Dentro del archivo se identifica:
* Un posible usuario: toctoc
* Una secuencia de números: 7000, 8000 y 9000
* Un número de teléfono

<img width="627" height="137" alt="Pasted image 20260203103930" src="https://github.com/user-attachments/assets/9da33b61-414e-41a0-a071-bfc308cae287" />

Estos indicios sugieren el uso de una técnica conocida como Port Knocking

```
Port Knocking es un mecanismo de seguridad que mantiene servicios ocultos hasta que se
 realiza una secuencia específica de conexiones a ciertos puertos en un orden determinado.
```

## 🚪 Descubrimiento de puertos ocultos

Con esta información, se procede a realizar port knocking utilizando la herramienta **knock**, siguiendo la secuencia encontrada.

<img width="397" height="27" alt="Pasted image 20260203105925" src="https://github.com/user-attachments/assets/60713ab4-cb21-4328-8170-46eb84f73a66" />

Una vez completado el proceso, se ejecuta nuevamente un escaneo con Nmap, logrando identificar un nuevo servicio activo:
* 22/tcp – SSH

<img width="833" height="291" alt="Pasted image 20260203110120" src="https://github.com/user-attachments/assets/1fc3a114-904f-4fa9-b0c7-dbed2f13cdad" />

##  🔐 Acceso al sistema

Con el servicio SSH habilitado, se procede a realizar un ataque de fuerza bruta utilizando **Hydra**, logrando obtener credenciales válidas para el usuario toctoc

<img width="887" height="38" alt="Pasted image 20260203111316" src="https://github.com/user-attachments/assets/3bc45756-fb9c-4e67-af90-bccc53c4ea5d" />

Posteriormente, se inicia sesión exitosamente vía SSH.

<img width="847" height="377" alt="Pasted image 20260203111446" src="https://github.com/user-attachments/assets/f444c18f-f949-4036-b9fb-f4b7a67ccca8" />

## 🔐 Escalada de privilegios

Una vez dentro del sistema, se revisan los permisos sudo y se identifica que el usuario puede ejecutar el binario:
```
/opt/bash
``` 
como root y sin necesidad de contraseña

<img width="1265" height="210" alt="Pasted image 20260203111636" src="https://github.com/user-attachments/assets/9a72fd8e-91a9-4fb1-afc1-c21d7562bec6" />

Al ejecutar el binario con privilegios elevados, se obtiene acceso completo al sistema como root

<img width="467" height="82" alt="Pasted image 20260203112135" src="https://github.com/user-attachments/assets/d3925207-f068-48a0-b441-27aa66fa9b5f" />

## 🏁 Conclusión

Este laboratorio demuestra cómo información expuesta en archivos aparentemente inofensivos puede conducir al descubrimiento de mecanismos ocultos, como Port Knocking, y derivar en una comprometida total del sistema.

