# SecretJenkins

## 🔎 Enumeración

Se realiza un escaneo de puertos utilizando Nmap, identificando los siguientes servicios abiertos:
* 22/tcp – SSH
* 8080/tcp – HTTP

<img width="938" height="315" alt="Pasted image 20260223110043" src="https://github.com/user-attachments/assets/181b3df5-c761-407d-967d-b56f510e9c51" />

## 🌐 Análisis del servicio web

Se accede al servicio web en el puerto 8080, donde se observa un login

<img width="1291" height="555" alt="Pasted image 20260223110305" src="https://github.com/user-attachments/assets/b40730c1-9e21-4b0d-a960-66f0a17ec7fd" />

Para obtener más información sobre la tecnología utilizada, se emplea la herramienta **WhatWeb**, identificando los servicios y versiones en ejecución

<img width="1892" height="116" alt="Pasted image 20260223120554" src="https://github.com/user-attachments/assets/05fa432a-52c7-4120-b31c-c576a0890a17" />

Tras investigar posibles vulnerabilidades asociadas a dichas versiones, se encuentra un exploit de **Local File Inclusion** ```(LFI)```

<img width="1882" height="322" alt="Pasted image 20260223121000" src="https://github.com/user-attachments/assets/8625386b-2f9c-4ed9-b049-7759ce40eb35" />

## 📂 Explotación LFI

Se descarga y utiliza el exploit identificado

<img width="662" height="132" alt="Pasted image 20260223120915" src="https://github.com/user-attachments/assets/622cf37c-e627-489e-aec2-9db57b254a32" />

Mediante este, se logra leer el archivo: ```/etc/passwd```

Obteniendo los siguientes usuarios del sistema: ```bobby``` y ```pinguinito```

<img width="787" height="502" alt="Pasted image 20260223121546" src="https://github.com/user-attachments/assets/24ae41ae-2d71-45ce-b84f-54c3e7d99149" />

## 🔑 Acceso por SSH

Con los usuarios identificados, se decide realizar un ataque de fuerza bruta por SSH, probando con el usuario: ```bobby```
 
<img width="692" height="47" alt="Pasted image 20260223122014" src="https://github.com/user-attachments/assets/0a08f203-1060-49c1-a16f-0a4c98c5e4db" />

Se logran obtener credenciales válidas y se accede al sistema

## Movimiento lateral

Una vez dentro, se revisan los permisos y se identifica que es posible ejecutar un binario como el usuario ```pinguinito``` sin necesidad de contraseña

<img width="1207" height="152" alt="Pasted image 20260223122140" src="https://github.com/user-attachments/assets/3c9661f1-d02b-477a-824e-5e6356e3c139" />

Se consulta [GTFOBins](https://gtfobins.org/) 

<img width="993" height="467" alt="Pasted image 20260223122713" src="https://github.com/user-attachments/assets/3f094793-177d-4cd1-8b88-b645be76184e" />

Tras ejecutar el comando correspondiente, se logra cambiar exitosamente al usuario ```pinguinito```

<img width="1043" height="97" alt="Pasted image 20260223122444" src="https://github.com/user-attachments/assets/43407672-b8e9-4fd6-9e0f-4f40d22b0378" />

## 👑 Escalada de privilegios

Como usuario ```pinguinito```, se continúa con la enumeración y se detecta un script en **Python** que puede ejecutarse como **root** sin necesidad de contraseña

<img width="1300" height="145" alt="Pasted image 20260223122843" src="https://github.com/user-attachments/assets/8114d0f5-8978-4f22-819c-7cb4663645be" />

El script se encuentra en el directorio: ```/opt/script.py```

Dado que se tienen permisos sobre este archivo, se procede a:

Eliminar el script original

<img width="735" height="42" alt="Pasted image 20260223124432" src="https://github.com/user-attachments/assets/80b23b9f-87e4-4512-90c9-e4cb10223df2" />

Crear un nuevo archivo malicioso con el siguiente contenido:

```bash
import os; os.system("/bin/bash")
```

<img width="1036" height="152" alt="Pasted image 20260223124846" src="https://github.com/user-attachments/assets/9b70b601-06fc-4844-a819-8f536d248221" />

Finalmente, se ejecuta el script con privilegios elevados y somos **root**

## 🏁 Conclusión

Este laboratorio demuestra cómo una vulnerabilidad de LFI, combinada con credenciales débiles y una mala configuración de permisos en scripts ejecutados con privilegios elevados, puede permitir a un atacante comprometer completamente el sistema.
