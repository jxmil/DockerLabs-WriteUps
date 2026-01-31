# Move

## 🔎 Enumeración

Se realiza un escaneo de puertos utilizando Nmap, donde se identifican los siguientes servicios abiertos:
* 22/tcp – SSH
* 80/tcp – HTTP
* 3000/tcp – HTTP

<img width="940" height="445" alt="Pasted image 20251219102855" src="https://github.com/user-attachments/assets/eca5bb0c-241a-49aa-9084-38d073794f74" />

## 🌐 Análisis del servicio web

Al acceder a la página web, nos encontramos con un formulario de login

<img width="891" height="777" alt="Pasted image 20251219102951" src="https://github.com/user-attachments/assets/dfa6a31b-86d7-4e25-8f44-7a94f3ebecd0" />

Inicialmente se intenta una inyección SQL, pero no se obtiene acceso.

<img width="1052" height="605" alt="Pasted image 20251219104040" src="https://github.com/user-attachments/assets/54a978cf-31a0-43e9-920f-c9cf4b6653c7" />

Ante esto, se procede a realizar fuzzing de directorios, lo que permite descubrir el archivo **Maintenance.html**.

<img width="862" height="490" alt="Pasted image 20251219110425" src="https://github.com/user-attachments/assets/8d668b97-eb6e-408f-a5e8-b6b9f731c9f1" />

Este archivo revela que la pagina esta en mantenimiento y que el acceso esta en `/tmp/pass.txt`

<img width="1041" height="132" alt="Pasted image 20251219110913" src="https://github.com/user-attachments/assets/e63d3bd3-a344-4ef6-af2f-5a48d1ddb97a" />

Para obtener más información sobre la aplicación web, se utiliza la herramienta **WhatWeb**, con la cual se identifica la versión del servicio que está corriendo.

<img width="871" height="133" alt="Pasted image 20251219112619" src="https://github.com/user-attachments/assets/7faacacf-587b-4ad6-bed3-6d38ec3610c8" />

Al investigar dicha versión, se descubre que es vulnerable a:

```
Path Traversal: permite acceder a archivos del sistema manipulando rutas dentro de la aplicación web.
Arbitrary File Read: permite leer archivos arbitrarios del sistema sin la debida autorización.
```

Estas vulnerabilidades hacen posible leer archivos internos del sistema.

<img width="1357" height="153" alt="Pasted image 20251219112917" src="https://github.com/user-attachments/assets/aab2ef78-ff8f-4482-8e01-51001fa238e7" />

<img width="1107" height="25" alt="Pasted image 20251219113942" src="https://github.com/user-attachments/assets/3b0b7ab7-8b26-4a21-ac3e-6e83d266f77d" />

## 📂 Lectura de archivos sensibles

Aprovechando estas vulnerabilidades y utilizando **curl**, se logra leer archivos del sistema, lo que permite identificar un usuario válido.

<img width="954" height="515" alt="Pasted image 20251219113604" src="https://github.com/user-attachments/assets/166d5ed2-cd3c-4b0d-b5ea-ab7bd21134f0" />

Posteriormente, se accede al archivo `/tmp/pass.txt`, donde se obtiene la contraseña del usuario.

<img width="983" height="78" alt="Pasted image 20251219113639" src="https://github.com/user-attachments/assets/887e97fb-9f58-4bc3-b9f3-d3211283db80" />

## 🔐 Acceso al sistema

Con las credenciales obtenidas, se logra iniciar sesión por SSH de manera exitosa.

<img width="841" height="475" alt="Pasted image 20251219114140" src="https://github.com/user-attachments/assets/9538bef6-6a46-4d42-8357-6d185a3c103f" />

## 🔐 Escalada de privilegios

Una vez dentro del sistema, se revisan los permisos y se detecta que existe un archivo en Python que puede ejecutarse como root.

<img width="846" height="157" alt="Pasted image 20251219121807" src="https://github.com/user-attachments/assets/b61e3d07-20c6-485d-bfd7-5dc053a58ddb" />

Se consulta [GTFOBins](https://gtfobins.org/) para buscar técnicas relacionadas con Python

<img width="845" height="177" alt="Pasted image 20251219123344" src="https://github.com/user-attachments/assets/519139e3-143f-4203-82c0-d002557971a8" />

Se accede al directorio /opt.<br>
Se inserta el código correspondiente dentro del archivo Python y se ejecuta el archivo con privilegios elevados.

<img width="812" height="138" alt="Pasted image 20251219123155" src="https://github.com/user-attachments/assets/82b19935-d4da-44b9-8cef-09068e652c33" />

Finalmente, se obtiene acceso como root.

## 🏁 Conclusión

Este laboratorio muestra que la seguridad cae como un dominó: el fuzzing web abre la puerta, las versiones vulnerables y la lectura de archivos nos dan las llaves, y los scripts mal configurados con permisos altos nos permiten tomar el control total.
