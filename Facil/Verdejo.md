# Verdejo

## 🔎 Enumeración 

Se realiza un escaneo de puertos utilizando Nmap, identificando los siguientes servicios abiertos:

* 22/tcp – SSH
* 80/tcp – HTTP
* 8089/tcp – HTTP

<img width="687" height="477" alt="Pasted image 20251218181336" src="https://github.com/user-attachments/assets/ca34741d-3c1a-4716-99e6-b0cee073d5d4" />

## 🌐 Análisis del servicio web

Al visitar la página web, no se observa funcionalidad relevante a simple vista

<img width="801" height="311" alt="Pasted image 20251218181424" src="https://github.com/user-attachments/assets/838f4526-9f8e-4670-a3fb-d820aa14d818" />

Se procede a realizar fuzzing de directorios, encontrando un directorio al cual no se tiene autorización de acceso

<img width="756" height="492" alt="Pasted image 20251218184015" src="https://github.com/user-attachments/assets/e315f958-0022-4fab-a6b0-2e683de435ac" />

<img width="628" height="242" alt="Pasted image 20251218184115" src="https://github.com/user-attachments/assets/9fd8a0c6-2798-4eb4-874e-e68ed4fbea38" />

Tras volver a analizar la página principal, se prueba la caja de texto con una entrada simple: 

```
{{9*9}}

```

<img width="683" height="266" alt="Pasted image 20251218184650" src="https://github.com/user-attachments/assets/62c8d170-c770-4517-9694-491aab370291" />

El resultado confirma que la aplicación es vulnerable a *Server-Side Template Injection* ```(SSTI)```

## 💥 Explotación SSTI

Aprovechando la vulnerabilidad ```SSTI```, se ejecuta código del sistema utilizando el siguiente payload

```
{{request.application.__globals__.__builtins__.__import__('os').popen('id').read()}}

```

<img width="1180" height="253" alt="Pasted image 20251218185253" src="https://github.com/user-attachments/assets/628c362e-92c0-4ac6-ab29-b5b39359d325" />

Esto permite la ejecución de comandos en el sistema

<img width="1071" height="287" alt="Pasted image 20251218192036" src="https://github.com/user-attachments/assets/8d79ff03-8298-4e6f-adad-89dc7140ad63" />

Posteriormente, se envía una reverse shell, mientras desde la máquina atacante se configura un listener en el puerto *4444*

<img width="786" height="287" alt="Pasted image 20251218192134" src="https://github.com/user-attachments/assets/c688217b-cfc0-4640-ae3a-3c4495f18c7d" />

## 🔐 Acceso al sistema

Con acceso al sistema, se intenta escalar privilegios, pero inicialmente solo es posible leer archivos

Durante la enumeración, se localiza la clave **SSH** del usuario root.

Se guarda la clave en un archivo local y se ajustan los permisos correctamente

```
chmod 600 id_rsa

```

<img width="752" height="428" alt="Pasted image 20251218193012" src="https://github.com/user-attachments/assets/570e4e5a-33f9-44c5-80f6-ab81d5c6ae9e" />

Se intenta iniciar sesión por **SSH**, pero el acceso requiere contraseña

## 🔓 Crackeo de credenciales y escalada final

La clave SSH es procesada con **John The Ripper** para obtener la contraseña correspondiente

<img width="738" height="282" alt="Pasted image 20251218194249" src="https://github.com/user-attachments/assets/1bd2d88f-629d-4563-8a87-cbd3da077b88" />

Una vez obtenida la contraseña, se inicia sesión nuevamente por **SSH**, logrando acceso como root

<img width="647" height="381" alt="Pasted image 20251218215011" src="https://github.com/user-attachments/assets/1e9fde89-1264-49f9-bf01-1d4063874933" />

## 🏁 Conclusión

Este laboratorio demuestra cómo una vulnerabilidad SSTI puede derivar en ejecución remota de comandos y, combinada con una mala protección de claves SSH, permitir la toma completa del sistema.
