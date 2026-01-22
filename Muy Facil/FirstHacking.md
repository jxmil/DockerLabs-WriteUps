# FirstHacking

## 🔎 Enumeración

Se realiza un escaneo de puertos utilizando Nmap, identificando que el único servicio expuesto es:
* 21/tcp – FTP

<img width="553" height="92" alt="Pasted image 20260116103254" src="https://github.com/user-attachments/assets/695ac0a5-8e00-4b64-bc69-6d50888f150d" />

Se intenta acceder al servicio FTP utilizando el usuario anonymous, sin éxito

<img width="417" height="181" alt="Pasted image 20260116103535" src="https://github.com/user-attachments/assets/55727a6e-ebac-4d44-8828-6aba7c4d69ef" />

Al revisar la versión del servicio detectada, se identifica que el servidor ejecuta vsftpd 2.3.4, una versión vulnerable.

## 🔍 Análisis de Vulnerabilidad

Investigando la versión detectada, se confirma que vsftpd 2.3.4 contiene una backdoor conocida, documentada como CVE-2011-2523.

Esta vulnerabilidad se activa al intentar autenticarse en el servicio FTP utilizando un nombre de usuario que finalice con el payload: `:)`
<br>Al enviar este payload, el servicio abre de forma silenciosa el puerto 6200/tcp, proporcionando acceso remoto sin interrumpir otros servicios ni generar alertas visibles.

## ⚔️ Explotación

Se realiza un intento de autenticación en el servicio FTP utilizando un nombre de usuario que incluye el payload mencionado.

<img width="356" height="148" alt="Pasted image 20260116104244" src="https://github.com/user-attachments/assets/5345fa96-b2aa-487f-8109-2fcf6a0f6057" />

Posteriormente, se establece una conexión al puerto 6200 utilizando Netcat

<img width="356" height="58" alt="Pasted image 20260116104731" src="https://github.com/user-attachments/assets/7a508dfb-3789-4aaf-bb5a-cabb3f214678" />

Como resultado, se obtiene una shell remota en el sistema.

## 🔐 Acceso al sistema

Una vez establecida la conexión, se confirma que la shell obtenida corresponde al usuario root, comprometiendo completamente el sistema sin necesidad de realizar una escalada de privilegios adicional

## 🏁 Conclusión

Este laboratorio demuestra el impacto crítico de ejecutar servicios con versiones vulnerables conocidas.

La presencia de la backdoor documentada en la vulnerabilidad CVE-2011-2523 permitió obtener acceso remoto directo como root a través del servicio FTP, sin requerir credenciales válidas ni técnicas adicionales de escalada de privilegios.

La máquina refuerza la importancia de la correcta gestión de versiones y de la actualización oportuna de servicios expuestos, ya que una sola vulnerabilidad conocida puede comprometer completamente un sistema.
