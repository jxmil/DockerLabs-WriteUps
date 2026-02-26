# Apibase

## 🔎 Enumeración 

Se realiza un escaneo de puertos con **Nmap**, identificando los puertos:

* 22/tcp – SSH
* 80/tcp – HTTP

<img width="937" height="440" alt="Pasted image 20260225120640" src="https://github.com/user-attachments/assets/26369c56-c5fa-47cc-a8d7-489a2995af5e" />

Con estos servicios detectados, se procede a analizar la aplicación web
## 🌐 Análisis de la API

Al visitar la página web, se observa que existe una API que solicita añadir un usuario en la ruta: ```/users```

<img width="742" height="295" alt="Pasted image 20260225120810" src="https://github.com/user-attachments/assets/086170f1-fce2-4ec9-9b13-6103bde316c2" />

Sin embargo, al interactuar con ella, devuelve el mensaje **parámetro inválido**, indicando que falta un parámetro requerido

<img width="541" height="150" alt="Pasted image 20260225121711" src="https://github.com/user-attachments/assets/cb2aa7c6-7afe-4fae-9db3-ec4f5a1dc38b" />

## 🔍 Descubrimiento de Parámetro

Para identificar el parámetro correcto, se utiliza **wfuzz**:

```bash
wfuzz -c -w /usr/share/wordlists/dirb/common.txt -t 200 -u 172.17.0.2:5000/users?FUZZ=test --hh 35
```

<img width="740" height="210" alt="Pasted image 20260225122500" src="https://github.com/user-attachments/assets/5fbde567-48b2-493c-9cbc-faee0a7a8eac" />

Tras el descubrimiento del parámetro válido, se prueba nuevamente en el navegador <br>
Ahora la API responde indicando que el usuario no ha sido encontrado

<img width="663" height="173" alt="Pasted image 20260225122741" src="https://github.com/user-attachments/assets/9f4da15b-457e-4676-83d1-c8e3c90c7c50" />

## 💉 Inyección SQL

Se prueban usuarios por defecto sin éxito <br>
Posteriormente, se intenta una **inyección SQL**, logrando identificar dos usuarios válidos

<img width="726" height="298" alt="Pasted image 20260225123023" src="https://github.com/user-attachments/assets/db16b4e7-6a65-4d9c-a26d-ea65306b12a1" />

Se prueban las credenciales en el sistema y el segundo usuario permite iniciar sesión correctamente

## 🔐 Post-Explotación

Una vez dentro, se intenta escalar privilegios sin éxito inmediato

<img width="365" height="85" alt="Pasted image 20260225123358" src="https://github.com/user-attachments/assets/f7c51f6a-0e20-4a1b-9f58-49d1ef892e25" />

Se buscan binarios con permisos elevados (SUID), pero no se encuentra nada relevante

<img width="497" height="195" alt="Pasted image 20260225123455" src="https://github.com/user-attachments/assets/088a4b3c-e971-4d08-98bb-6e3af2587846" />

## 👑 Escalada de Privilegios

Continuando con la enumeración manual del sistema, se localiza un archivo dentro del directorio ```/home``` que contiene la contraseña del usuario **root** en texto claro

<img width="652" height="101" alt="Pasted image 20260225123911" src="https://github.com/user-attachments/assets/e9937ae4-f2f1-4bca-985a-62188a5fb2be" />

Con esta información, se cambia al usuario **root** exitosamente

<img width="387" height="118" alt="Pasted image 20260225124021" src="https://github.com/user-attachments/assets/850ccaed-e5bb-4383-ae20-ee84915f80c4" />

## 🏁 Conclusión 

Este laboratorio demuestra la importancia de validar correctamente los parámetros en las APIs, prevenir vulnerabilidades de SQL Injection y proteger la información sensible almacenada en el sistema.
