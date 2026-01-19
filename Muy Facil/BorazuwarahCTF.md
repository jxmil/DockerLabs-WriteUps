# BorazuwarahCTF

## 🔎 Enumeración

Durante la etapa de reconocimiento, se realiza un escaneo de puertos, identificando los siguientes servicios expuestos:
* 22/tcp – SSH
* 80/tcp – HTTP

<img width="948" height="296" alt="Pasted image 20260116121856" src="https://github.com/user-attachments/assets/643e3ba7-c126-4e66-9c2b-102c52234cce" />

Se accede al servicio web y se inspecciona el código fuente en busca de información relevante; sin embargo, no se identifican credenciales, comentarios ni pistas directas.

<img width="448" height="431" alt="Pasted image 20260116122409" src="https://github.com/user-attachments/assets/75851a2e-9fdf-4f0a-bfed-e7b9c8a2a06a" />

Posteriormente, se realiza fuzzing de directorios utilizando múltiples diccionarios y pruebas de path traversal, sin obtener resultados explotables.

<img width="880" height="473" alt="Pasted image 20260116122946" src="https://github.com/user-attachments/assets/c5211f3a-f943-474e-a3bf-b0a69071b655" />

## 🔍 Enumeración Avanzada

Tras agotar las opciones tradicionales en el servicio web, se decide analizar los recursos expuestos, en este caso una imagen disponible en la página principal.
<br> La imagen es descargada y analizada utilizando la herramienta ExifTool, donde se identifican metadatos que revelan el nombre de un usuario válido del sistema.

<img width="786" height="517" alt="Pasted image 20260116123148" src="https://github.com/user-attachments/assets/a3f50448-97b8-4a9a-b96d-6324b333128c" />

## ⚔️ Explotación

Con el usuario identificado y el servicio SSH expuesto, se procede a realizar un ataque de fuerza bruta controlado utilizando Hydra, logrando obtener credenciales válidas.

<img width="702" height="35" alt="Pasted image 20260116123302" src="https://github.com/user-attachments/assets/92205ab0-9380-4fab-9b76-8ca3fc2eb90d" />

Con dichas credenciales, se establece conexión al sistema mediante SSH.

<img width="816" height="348" alt="Pasted image 20260116123429" src="https://github.com/user-attachments/assets/386359f7-de4b-41ce-95e6-274728ee9eb8" />

## 🔐 Escalada de Privilegios

Una vez dentro del sistema, se enumeran los privilegios del usuario y se identifica que puede ejecutar el binario **/bin/bash** como root sin necesidad de contraseña.

<img width="1162" height="222" alt="Pasted image 20260116123609" src="https://github.com/user-attachments/assets/3b8037a8-3183-4b8b-82e6-ab211da209ce" />

Aprovechando esta mala configuración de sudo, se ejecuta el binario con privilegios elevados, obteniendo acceso root de forma inmediata.

## 🏁 Conclusión

Este laboratorio demuestra la importancia de profundizar en la enumeración cuando las primeras técnicas no ofrecen resultados.

La exposición de información sensible a través de metadatos en archivos multimedia permitió identificar un usuario válido, facilitando ataques posteriores contra el servicio SSH. Adicionalmente, una mala configuración de sudo que permite la ejecución directa de bash como root condujo al compromiso total del sistema.
