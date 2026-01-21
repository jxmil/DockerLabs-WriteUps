# Trust 

## 🔎 Enumeración

Se realiza un escaneo de puertos con Nmap, identificando los siguientes servicios expuestos:
* 22/tcp – SSH
* 80/tcp – HTTP

<img width="958" height="287" alt="Pasted image 20260116105504" src="https://github.com/user-attachments/assets/5be6c9ea-e151-4515-ab4c-cd955c45947c" />

Se accede al servicio web y se inspecciona el código fuente en busca de información relevante; sin embargo, no se identifican pistas directas.

<img width="972" height="675" alt="Pasted image 20260116105807" src="https://github.com/user-attachments/assets/80059195-9a43-4b28-a080-299f608cad5e" />

Posteriormente, se realiza fuzzing web, identificando un directorio oculto.

<img width="863" height="512" alt="Pasted image 20260116110044" src="https://github.com/user-attachments/assets/77b5c68e-b22f-49fd-9a8c-d7bf7d5d0c02" />

Al acceder a este recurso, se obtiene un nombre de usuario válido: Mario.

<img width="822" height="616" alt="Pasted image 20260116110007" src="https://github.com/user-attachments/assets/00acd298-5a84-4fc8-9790-66c81eba8a02" />

## ⚔️ Explotación

Dado que se cuenta con un usuario válido y el servicio SSH está expuesto, se decide realizar un ataque de fuerza bruta controlado utilizando Hydra, empleando el diccionario rockyou.

<img width="708" height="50" alt="Pasted image 20260116110733" src="https://github.com/user-attachments/assets/9f118fc0-fe3c-4e92-a429-c70f741afbb0" />

Como resultado, se obtiene una contraseña válida que permite el acceso al sistema mediante SSH como el usuario Mario.

<img width="1033" height="257" alt="Pasted image 20260116110945" src="https://github.com/user-attachments/assets/9e6879b9-f570-4f18-b78e-fb4744341680" />

## 🔐 Escalada de Privilegios
Una vez dentro del sistema, se enumeran los privilegios del usuario y se identifica que solo puede ejecutar el binario vim con privilegios elevados.

<img width="923" height="167" alt="Pasted image 20260116111014" src="https://github.com/user-attachments/assets/7ea08848-f393-4dee-a6dc-50c1adeec784" />

Se consulta GTFOBins para identificar una técnica de escalada asociada a vim.

<img width="966" height="202" alt="Pasted image 20260116112616" src="https://github.com/user-attachments/assets/dbc66fa4-a253-4fc2-9579-63b0b233bd29" />

Se ejecuta `vim` utilizando **sudo**. Ya dentro del editor, se presiona `Esc` seguido de `:` para acceder al modo de comandos y se ejecuta:

```
!/bin/sh
```
<img width="157" height="96" alt="Pasted image 20260116112235" src="https://github.com/user-attachments/assets/7a204758-28a8-4562-951a-6b99ab363a43" />

## 🏁 Conclusión

Este laboratorio demuestra cómo una combinación de información expuesta en aplicaciones web y una mala configuración de permisos puede conducir al compromiso total de un sistema.

La presencia de directorios ocultos permitió identificar un usuario válido, facilitando ataques posteriores contra el servicio SSH. Adicionalmente, una configuración insegura de sudo que permite la ejecución de vim con privilegios elevados posibilitó una escalada de privilegios inmediata.
