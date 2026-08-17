# Pressenter

## 🔎 Enumeración 
Se realiza un escaneo de puertos utilizando Nmap, identificando el siguiente servicio:

* 80/tcp – HTTP

<img width="650" height="156" alt="Pasted image 20260511121917" src="https://github.com/user-attachments/assets/64700cd2-585d-4b1a-9707-23116c21194d" />

Se accede a la página web y se realiza una primera exploración de sus funcionalidades

<img width="1067" height="397" alt="Pasted image 20260511122137" src="https://github.com/user-attachments/assets/5bab94ed-e08b-4b11-ad67-551c14d465cd" />

Navegando en la aplicación se observa que, al intentar completar el proceso, la página simplemente vuelve al mismo lugar

<img width="555" height="512" alt="Pasted image 20260511122622" src="https://github.com/user-attachments/assets/603a0705-7523-4b7f-876e-b47dca140e99" />

Continuando con la navegación, se identifica una redirección hacia un host que no puede ser resuelto, por lo que se procede a añadir el dominio correspondiente al archivo ```/etc/hosts```

<img width="1066" height="557" alt="Pasted image 20260511123441" src="https://github.com/user-attachments/assets/6f45f211-18c7-4fea-9a07-6ae7358ecca9" />

<img width="592" height="261" alt="Pasted image 20260511123529" src="https://github.com/user-attachments/assets/73592164-50a7-40c1-ace7-a8e3a660673a" />

## 🌐 Identificación de WordPress

Una vez configurado el dominio, se accede nuevamente a la aplicación y se identifica que se trata aparentemente de un sitio desarrollado con WordPress

<img width="1228" height="487" alt="Pasted image 20260511123938" src="https://github.com/user-attachments/assets/93ab44ea-82f1-40c1-87c3-3dfead80cbb0" />

Procedemos a hacer fuzzing actualizando el objetivo con la dirección IP del nuevo host añadido

<img width="801" height="422" alt="Pasted image 20260511124232" src="https://github.com/user-attachments/assets/0459f00d-ff7a-4687-bd94-e67736d8947b" />

Para realizar una enumeración más exhaustiva se utiliza **WPScan**, logrando identificar dos posibles usuarios:

* pressi
* hacker

```bash
wpscan -e p,vt,cb,u --url pressenter.hl
```

<img width="795" height="203" alt="Pasted image 20260511125619" src="https://github.com/user-attachments/assets/33c07b8c-5a59-48fc-8b8a-7d797a83a62d" />

Con los usuarios identificados, se procede a realizar un ataque de fuerza bruta utilizando una lista de contraseñas

<img width="615" height="46" alt="Pasted image 20260511130452" src="https://github.com/user-attachments/assets/57a5e84f-9584-4000-bf93-14418cc0bfe9" />

Tras completar el proceso, se consiguen credenciales válidas para uno de los usuarios

<img width="412" height="387" alt="Pasted image 20260511130706" src="https://github.com/user-attachments/assets/e7ba0803-22be-4876-8347-6fb8961a35c9" />

## 🔑 Acceso al panel de WordPress

Con las credenciales obtenidas, se accede al directorio: ```/wp-admin```

Una vez dentro del panel administrativo, se revisan las funcionalidades disponibles y se identifica la posibilidad de instalar nuevos plugins

<img width="967" height="443" alt="Pasted image 20260511131009" src="https://github.com/user-attachments/assets/bd819de9-16b4-4a17-b467-618ca33d9b55" />

Se prepara un plugin que contiene una reverse shell, se empaqueta en formato **.zip** y posteriormente se carga desde el panel de administración de WordPress

<img width="737" height="110" alt="Pasted image 20260511135047" src="https://github.com/user-attachments/assets/c7e3ca24-a915-4dd2-9726-35fee504cbbc" />

<img width="588" height="355" alt="Pasted image 20260511135155" src="https://github.com/user-attachments/assets/893c75c2-03a2-41b6-8da7-9e27e40c328b" />

Antes de activar el plugin, se prepara un listener en el puerto 4545

<img width="741" height="245" alt="Pasted image 20260511160238" src="https://github.com/user-attachments/assets/5f8e3ba7-7e30-40fe-ae8a-bff2b5a7c98d" />

Al ejecutar el plugin, se recibe la conexión en el listener y se obtiene acceso al sistema

<img width="811" height="117" alt="Pasted image 20260511160258" src="https://github.com/user-attachments/assets/1041541c-09f3-4df3-804d-4807d62ef6d5" />

Una vez obtenida la shell, se realiza el correspondiente tratamiento de TTY para mejorar la interacción con el terminal

<img width="240" height="140" alt="Pasted image 20260512131347" src="https://github.com/user-attachments/assets/7b35ebd1-0d77-4f4d-8708-d5129e419bcd" />

## 🗄️ Enumeración de credenciales

Durante la enumeración del sistema se revisa el directorio de la aplicación web y se encuentra el archivo: ```wp-config.php```

<img width="467" height="158" alt="Pasted image 20260511160541" src="https://github.com/user-attachments/assets/b724cdc6-0648-4c31-99ad-846b3eae937c" />

Este archivo contiene información sensible de configuración, incluyendo las credenciales utilizadas para acceder a la base de datos **MySQL**

Con estas credenciales se establece una conexión con **MySQL** y se comienza a enumerar la información disponible

<img width="877" height="308" alt="Pasted image 20260511162211" src="https://github.com/user-attachments/assets/24d87671-c873-4ec2-a0af-d6e8579a22b6" />

Se revisan las bases de datos existentes y posteriormente sus tablas

<img width="312" height="185" alt="Pasted image 20260511162427" src="https://github.com/user-attachments/assets/e848deb5-6746-4924-a53c-58957bfeae2e" />

<br>
<img width="327" height="412" alt="Pasted image 20260511162558" src="https://github.com/user-attachments/assets/69558887-1875-460a-b8df-c6eb06eee385" />

Durante la inspección de las tablas de **WordPress** se encuentran credenciales asociadas al usuario **enter**

<img width="587" height="150" alt="Pasted image 20260511162713" src="https://github.com/user-attachments/assets/8d077518-3fbc-4137-97f4-aaefc41acd86" />

## 👤 Acceso como otro usuario

Tras recuperar y analizar la contraseña encontrada en la base de datos, se intenta utilizarla para autenticarse como el usuario **enter**

Las credenciales son válidas, por lo que se consigue acceder como este usuario

<img width="386" height="112" alt="Pasted image 20260511162859" src="https://github.com/user-attachments/assets/e7f389c2-7bb6-4e96-b2dc-f0c5309d28b9" />

Una vez dentro, se continúa con la enumeración del sistema y se localiza la flag de usuario

## 👑 Escalada de privilegios

Para continuar con la escalada de privilegios, se revisan las posibilidades disponibles para el usuario actual

Durante las pruebas se descubre que existe una reutilización de contraseña: la misma contraseña obtenida anteriormente permite autenticarse con privilegios de **root**

<img width="416" height="72" alt="Pasted image 20260511163129" src="https://github.com/user-attachments/assets/f7e0e74f-9cf7-4099-860a-e58029354a08" />

Finalmente, se accede al directorio: ```/root``` y se encuentra la flag de root, completando así la máquina

<img width="581" height="352" alt="Pasted image 20260511163216" src="https://github.com/user-attachments/assets/cedc64a5-4e4e-40d5-b2d9-1fe1bff5885c" />

## 🏁 Conclusión
Este laboratorio evidencia la importancia de una enumeración web exhaustiva. El compromiso total del sistema se logró mediante una cadena de ataque que incluyó: identificación de WordPress, enumeración de usuarios, fuerza bruta al panel administrativo, explotación de una mala configuración para obtener una reverse shell, extracción de credenciales desde wp-config.php y reutilización de contraseñas para el escalado de privilegios a root
