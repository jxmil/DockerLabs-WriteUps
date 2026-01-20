# Tproot

## 🔎 Enumeración

Se realiza un escaneo de puertos con Nmap, identificando los siguientes servicios expuestos:
* 21/tcp – FTP
* 80/tcp – HTTP
  
<img width="750" height="195" alt="Pasted image 20260116125940" src="https://github.com/user-attachments/assets/2b341143-32c2-44b4-b59f-2b1efb7139cc" />

Se intenta acceder al servicio FTP utilizando el usuario anonymous, sin éxito.

Al revisar nuevamente el reporte de Nmap, se identifica que el servidor FTP ejecuta una versión desactualizada del servicio vsftpd.

## 🔍 Análisis de Vulnerabilidad

Tras investigar la versión detectada, se confirma que vsftpd 2.3.4 es vulnerable a una backdoor conocida, documentada como CVE-2011-2523.

Esta vulnerabilidad permite abrir una shell remota al autenticar una sesión FTP utilizando un nombre de usuario que finalice con el payload:
```bash
:)
```

Al enviar este payload, el servidor abre un puerto oculto (6200/tcp) proporcionando acceso remoto sin generar alertas ni interrumpir otros servicios.
## ⚔️ Explotación

Se inicia un listener en el puerto 6200, y posteriormente se intenta autenticación FTP utilizando el payload en el nombre de usuario.

<img width="298" height="21" alt="Pasted image 20260119224709" src="https://github.com/user-attachments/assets/edd26093-c5e5-4a65-adcb-baca2170cf08" />
<br>
<img width="392" height="118" alt="Pasted image 20260116131348" src="https://github.com/user-attachments/assets/4dca90f8-216e-4fea-9265-e69b3377f9de" />

Tras ejecutar el exploit, se establece una conexión exitosa al puerto 6200, obteniendo acceso interactivo al sistema.

## 🔐 Acceso al sistema

Una vez establecida la conexión, se confirma que la shell obtenida corresponde al usuario root, comprometiendo completamente el sistema sin necesidad de escalada de privilegios adicional.

<img width="442" height="100" alt="Pasted image 20260116131428" src="https://github.com/user-attachments/assets/559bcbcb-ebec-4a36-9e51-182f6312b33d" />
