# FindYourStyle

## 🔎 Enumeración 

Se realiza un escaneo de puertos utilizando **Nmap**, donde se identifica que el puerto 80/tcp (HTTP) se encuentra abierto

<img width="718" height="311" alt="Pasted image 20260123105834" src="https://github.com/user-attachments/assets/25c2ccf6-cc0d-4eb2-8fc5-3811a4cf30d0" />

## 🌐 Análisis del servicio web

Al acceder a la página web, se procede a identificar la tecnología utilizada haciendo uso de la herramienta **WhatWeb**

<img width="957" height="463" alt="Pasted image 20260123112043" src="https://github.com/user-attachments/assets/766cfee7-b6b4-4cca-ab83-2f1dfd235b72" />

El análisis revela que el sitio está basado en **Drupal 8**, una versión que cuenta con vulnerabilidades conocidas

<img width="1882" height="102" alt="Pasted image 20260123112125" src="https://github.com/user-attachments/assets/be6967b0-0d5b-4a8f-91bd-4d14ee2e16bd" />

Debido a ello, se consulta **Metasploit** en busca de exploits 

<img width="1182" height="627" alt="Pasted image 20260123112847" src="https://github.com/user-attachments/assets/a0bd3d70-f860-47c5-b644-339d328e2f8f" />

Se decide utilizar el primer módulo compatible encontrado

<img width="1148" height="627" alt="Pasted image 20260123112936" src="https://github.com/user-attachments/assets/73ca11de-1586-47bd-ad57-8ff6c04ef9a9" />

Se configura la IP de la máquina objetivo 

<img width="1158" height="668" alt="Pasted image 20260123113118" src="https://github.com/user-attachments/assets/141ac535-4036-429a-8c69-ed440db8d7a7" />

Se ejecuta el exploit con éxito, obteniendo acceso inicial al sistema como el usuario **www-data**

<img width="736" height="240" alt="Pasted image 20260123113252" src="https://github.com/user-attachments/assets/3494ca4b-4084-47f6-b17c-b67ec43f8254" />

Hacemos tratamiento de TTY

<img width="240" height="140" alt="Pasted image 20251222222913" src="https://github.com/user-attachments/assets/00d44469-e460-4470-a3dc-32ef9676e5b6" />

## 🔐 Obtención de credenciales 

Una vez dentro del sistema, se realiza una enumeración básica del sistema de archivos
Durante esta fase, se identifica el archivo ```settings.php``` ubicado en **/var/www/html/sites/default/**

<img width="1147" height="300" alt="Pasted image 20260123124453" src="https://github.com/user-attachments/assets/52313b9d-bef0-4c08-a72f-bae843e0daff" />

Este archivo contiene credenciales en texto claro, correspondientes a un usuario del sistema llamado **ballenita**

<img width="582" height="112" alt="Pasted image 20260123124544" src="https://github.com/user-attachments/assets/92799bc1-3021-4bb3-87f2-30b4d2d5cfeb" />

## 🔐 Escalada de privilegios

Al intentar escalar privilegios, se verifica que el usuario **ballenita** tiene permisos limitados mediante sudo, pudiendo ejecutar únicamente los binarios *ls* y *grep* como **root**

<img width="825" height="175" alt="Pasted image 20260123124646" src="https://github.com/user-attachments/assets/d19d68f9-31ac-444f-9ae6-1e46366e733d" />

Se consulta [GTFOBins](https://gtfobins.org/) 

<img width="901" height="352" alt="Pasted image 20260123125245" src="https://github.com/user-attachments/assets/c781adc0-98ff-49a4-8a03-fe9d06b5d811" />

Se confirma que *grep* puede ser utilizado para leer archivos arbitrarios con privilegios elevados

<img width="893" height="97" alt="Pasted image 20260123125912" src="https://github.com/user-attachments/assets/97815595-5428-47d8-9e1f-4d8ffe008cc4" />


## 👑 Acceso final a root

Con la contraseña obtenida, se procede a cambiar al usuario root, logrando así el control total del sistema

<img width="482" height="141" alt="Pasted image 20260123130353" src="https://github.com/user-attachments/assets/1cdaf636-6159-4a9c-95e3-1acfad1b1122" />

## 🏁 Conclusión

El proceso mostró cómo una aplicación web desactualizada puede servir como punto de entrada a un sistema Linux y cómo, mediante una correcta enumeración, es posible identificar credenciales expuestas y permisos mal configurados que permiten avanzar progresivamente hasta obtener acceso root, destacando la importancia de mantener los servicios actualizados, proteger archivos sensibles y aplicar el principio de mínimo privilegio
