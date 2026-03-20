# Bypassme

## 🔎 Enumeración

Se realiza un escaneo de puertos utilizando Nmap, identificando los siguientes servicios abiertos:

* 22/tcp – SSH
* 80/tcp – HTTP

<img width="941" height="403" alt="Pasted image 20260227121002" src="https://github.com/user-attachments/assets/ebaca9a6-45cd-4f79-be79-4e2c9cacb2b8" />

## 🌐 Análisis del servicio web

Se visita la página web y se encuentra un login

<img width="571" height="492" alt="Pasted image 20260227121209" src="https://github.com/user-attachments/assets/d7466c47-06dc-413c-83f6-930dc8047c89" />

Se prueba una **inyección SQL**, pero en este caso se introduce el payload en el campo de contraseña:

```bash
'1'='1 -- -
```

<img width="643" height="376" alt="Pasted image 20260227122603" src="https://github.com/user-attachments/assets/cd643de6-b2e6-464b-9ee3-1da8ad20e738" />

Con esto se logra el acceso al sistema

Posteriormente, al inspeccionar la página, se encuentra un mensaje oculto

<img width="747" height="400" alt="Pasted image 20260227131842" src="https://github.com/user-attachments/assets/b2791665-fb49-498e-a9ac-9c3825eb728f" />

## 🔍 Descubrimiento de rutas

Se realiza fuzzing web, encontrando un directorio: ```/logs```

<img width="675" height="235" alt="Pasted image 20260227131737" src="https://github.com/user-attachments/assets/8abf45f5-f2d6-431b-97f2-84de057e7418" />

Sin embargo, no se tienen permisos para acceder directamente

<img width="522" height="206" alt="Pasted image 20260227131941" src="https://github.com/user-attachments/assets/e70ca922-2e08-453c-b2e6-6d37f6b652fd" />

Recordando que en el dashboard se indicaba utilizar la barra de navegación, se manipulan las rutas manualmente hasta lograr acceder a los logs

<img width="1871" height="290" alt="Pasted image 20260227132310" src="https://github.com/user-attachments/assets/4ad6e46f-d6af-4792-b9ae-1f607b8c37ff" />

<img width="1870" height="181" alt="Pasted image 20260227132737" src="https://github.com/user-attachments/assets/778c4001-78da-4557-8ed5-42e34b6a473f" />

## 🔑 Acceso por SSH

Con la información obtenida, se logra iniciar sesión por SSH

<img width="618" height="133" alt="Pasted image 20260227132856" src="https://github.com/user-attachments/assets/987b8411-bf0f-43c3-b3ff-c10e89e0cc1d" />

Una vez dentro, se intenta escalar privilegios, pero el sistema no cuenta con el binario **sudo**

<img width="403" height="110" alt="Pasted image 20260227132949" src="https://github.com/user-attachments/assets/aaf43f0b-9259-4d08-9381-17c882274f51" />

Se procede a buscar binarios con permisos elevados, sin encontrar resultados relevantes

<img width="572" height="230" alt="Pasted image 20260227133107" src="https://github.com/user-attachments/assets/e6075d8a-7fce-431a-b088-5a8f8eb4b5e7" />

## ⚙️ Abuso de socket UNIX

Durante la enumeración de procesos, se identifica uno ejecutado por el usuario: **conx**

Este proceso corresponde a un socket UNIX en escucha, el cual ejecuta el binario ```/bin/bash``` cuando se establece una conexión

<img width="762" height="85" alt="Pasted image 20260227134105" src="https://github.com/user-attachments/assets/f16f4cf4-e192-498c-be2b-9017ae5229f3" />

Aprovechando esto, se realiza una conexión al socket, logrando obtener una shell como el usuario **conx**

## 👑 Escalada de privilegios

Como usuario **conx**, se revisan las tareas programadas ```(cron)```

<img width="575" height="217" alt="Pasted image 20260227134805" src="https://github.com/user-attachments/assets/bf65d6c9-8560-4cc2-9b31-c48c90c1a51c" />

Se identifica un archivo: ```/var/backups/backup.sh```

<img width="538" height="102" alt="Pasted image 20260227134855" src="https://github.com/user-attachments/assets/492f8065-808c-4cc9-9ca9-62d373ec3ebc" />

Dado que se tienen permisos de escritura sobre el archivo, se modifica su contenido agregando:

```bash
echo "chmod u+s /bin/bash" >> /var/backups/backup.sh
```

<img width="805" height="77" alt="Pasted image 20260227135118" src="https://github.com/user-attachments/assets/497540c2-877d-4dd3-9db3-fb01f5c245ec" />

Tras esperar a que el cron se ejecute, se otorgan permisos SUID al binario ```/bin/bash```

Finalmente, se ejecuta ```bash -p``` y se logra ser **root**

<img width="396" height="130" alt="Pasted image 20260227135218" src="https://github.com/user-attachments/assets/d0e03801-62af-4890-b12f-8baca776f21b" />

## 🏁 Conclusión

Este laboratorio demuestra cómo una combinación crítica de vulnerabilidades, que incluye la inyección SQL, una deficiente gestión de rutas internas y la exposición de servicios mediante sockets UNIX, puede ser explotada junto con scripts ejecutados por cron con permisos inseguros para permitir a un atacante escalar privilegios hasta el nivel de root
