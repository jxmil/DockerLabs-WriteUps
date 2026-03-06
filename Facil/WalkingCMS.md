# WalkingCMS

## 🔎 Enumeración

Se realiza un escaneo de puertos utilizando **Nmap**, logrando identificar que el siguiente puerto se encuentra abierto:
* 80/tcp – HTTP

<img width="731" height="146" alt="Pasted image 20260305105349" src="https://github.com/user-attachments/assets/b2597a22-bc8c-48d4-95a0-9f806220c4f7" />

## 🌐 Análisis del servicio web

Se visita la página web y posteriormente se realiza fuzzing de directorios, encontrando el directorio: ```/wordpress```

<img width="808" height="653" alt="Pasted image 20260305105549" src="https://github.com/user-attachments/assets/29cd8a4e-8f65-4571-a640-fe2b3731a670" />

<img width="833" height="176" alt="Pasted image 20260305105645" src="https://github.com/user-attachments/assets/dc642627-8d24-456f-afdf-f51c07514c41" />

Al acceder a este directorio, se procede a realizar nuevamente fuzzing, encontrando la ruta del login de **WordPress**

<img width="1122" height="762" alt="Pasted image 20260305105744" src="https://github.com/user-attachments/assets/a2c77932-bbee-4a6c-807a-b82b33722e79" />

<img width="1098" height="295" alt="Pasted image 20260305111536" src="https://github.com/user-attachments/assets/133009ec-cd07-4575-b5ef-f7aa2e96db71" />

## 🔍 Enumeración de WordPress

Se utiliza la herramienta **WPScan** para enumerar posibles usuarios, plugins y temas

```bash
wpscan -e p,vt,cb,u --url http://172.17.0.2/wordpress 
```

Durante el escaneo se identifica el usuario: ```mario```

<img width="877" height="160" alt="Pasted image 20260305111008" src="https://github.com/user-attachments/assets/9de8701a-60cf-4d7b-93c1-83cd82fb80ad" />

## 🔑 Ataque de fuerza bruta

Una vez identificado el usuario, se procede a realizar un ataque de fuerza bruta utilizando WPScan y el diccionario rockyou.txt:

```bash
wpscan -U mario -P /usr/share/wordlists/rockyou.txt --url http://172.17.0.2/wordpress
```

<img width="1167" height="112" alt="Pasted image 20260305111337" src="https://github.com/user-attachments/assets/ce273634-8ac7-4ab4-b87c-fef09be5ee9b" />

Finalmente se logra encontrar la contraseña del usuario

Con estas credenciales se inicia sesión en el panel de administración de **WordPress**

<img width="493" height="545" alt="Pasted image 20260305111642" src="https://github.com/user-attachments/assets/1c00253d-785a-4711-9638-1dc4655b0130" />

## Obtención de acceso inicial

Una vez dentro del panel administrativo:

Se accede a Appearance → Theme Editor

<img width="616" height="581" alt="Pasted image 20260305112213" src="https://github.com/user-attachments/assets/50268212-ce79-4194-9bb6-a641e37e4fac" />

Se selecciona el tema Twenty Twenty-Two

<img width="1366" height="727" alt="Pasted image 20260305112429" src="https://github.com/user-attachments/assets/be2dfcf3-e8f2-4b3d-a59b-2938e400255c" />

En el panel derecho se abre el archivo index.php

<img width="268" height="436" alt="Pasted image 20260305112539" src="https://github.com/user-attachments/assets/5bbb0f12-7fe1-4f38-a92f-53b39710f178" />

El contenido del archivo es reemplazado por un código de [Reverse Shell Generator](https://www.revshells.com/) generado previamente

<img width="1107" height="831" alt="Pasted image 20260305112903" src="https://github.com/user-attachments/assets/9b1a795d-ec22-499d-9f30-6edc09f33b56" />

Pegamos el código

<img width="585" height="557" alt="Pasted image 20260305113042" src="https://github.com/user-attachments/assets/2b394d29-18eb-4570-8f5e-011d2b5d37f4" />

Posteriormente se inicia un listener en la máquina atacante y se accede a la siguiente URL:

```bash
http://172.17.0.2/wordpress/wp-content/themes/twentytwentytwo/index.php
```

La página queda cargando y en el listener se obtiene acceso como **www-data**

<img width="697" height="120" alt="Pasted image 20260305113410" src="https://github.com/user-attachments/assets/c2ebb6a1-59e7-42f0-b864-5935b108efbc" />

Hacemos tratamiento de TTY

<img width="240" height="140" alt="Pasted image 20260305113453" src="https://github.com/user-attachments/assets/82f5aed6-ef25-4814-854b-d88bd65a6150" />

## 👑 Escalada de privilegios

Una vez dentro del sistema se intenta escalar privilegios, sin embargo el comando **sudo** no está disponible

<img width="366" height="110" alt="Pasted image 20260305113608" src="https://github.com/user-attachments/assets/1250e2f1-8be9-449e-83e6-5c9e8e83ce49" />

Por lo tanto se procede a buscar binarios con permisos elevados **(SUID)**

<img width="632" height="226" alt="Pasted image 20260305113733" src="https://github.com/user-attachments/assets/4d8fdd84-b5a5-4350-80b7-8097a4cea25f" />

Durante la enumeración se encuentra un binario interesante

Se consulta [GTFOBins](https://gtfobins.org/) 

<img width="887" height="435" alt="Pasted image 20260305114036" src="https://github.com/user-attachments/assets/a4bf453c-aca3-4f93-ae7c-1e4a3c8ddcd4" />

Finalmente se ejecuta el comando correspondiente y se logra obtener acceso como **root**

<img width="428" height="137" alt="Pasted image 20260305113938" src="https://github.com/user-attachments/assets/f04fc875-67ea-41c8-b435-1acf4f79bd40" />

## 🏁 Conclusión

Este laboratorio demuestra cómo una mala configuración en WordPress puede permitir obtener acceso al panel administrativo mediante fuerza bruta, lo que posteriormente facilita la ejecución de código malicioso en el servidor.
