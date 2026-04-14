# InfluencerHate

## 🔎 Enumeración

Se realiza un escaneo de puertos utilizando Nmap, identificando los siguientes servicios abiertos:

* 22/tcp – SSH
* 80/tcp – HTTP

<img width="838" height="338" alt="Pasted image 20260212123738" src="https://github.com/user-attachments/assets/0f9d2a94-d298-45df-87e8-067388982e9a" />

## 🌐 Análisis del servicio web

Se visita la página web, la cual solicita iniciar sesión

Se prueban:

* Credenciales por defecto
* Inyección SQL

Sin obtener resultados.

<img width="376" height="272" alt="Pasted image 20260212123908" src="https://github.com/user-attachments/assets/2a100e58-293c-4553-ac79-9569c46a49aa" />

## 💥 Ataque de fuerza bruta inicial

Se decide realizar un ataque de fuerza bruta con Hydra, logrando obtener credenciales válidas

<img width="946" height="35" alt="Pasted image 20260212125443" src="https://github.com/user-attachments/assets/512ab3cb-238e-4953-a9a5-67de3d9857ad" />

Tras iniciar sesión, se redirige a una página gestionada por Apache

<img width="1128" height="821" alt="Pasted image 20260212125608" src="https://github.com/user-attachments/assets/d5f96909-d2fa-488d-bba2-5e2586e07103" />

## 🔍 Fuzzing web autenticado

Se realiza fuzzing web, esta vez utilizando credenciales:

* -U → usuario
* -P → contraseña

<img width="967" height="518" alt="Pasted image 20260212130907" src="https://github.com/user-attachments/assets/5d3aaf45-764e-450f-9a1f-c9e0cb190370" />

Se descubre un nuevo login dentro de la aplicación

<img width="857" height="880" alt="Pasted image 20260212130427" src="https://github.com/user-attachments/assets/f20979dc-6181-4409-97b5-4cd2db11bb2e" />

## 🔐 Segundo login – Análisis y explotación

En este nuevo login:

Se prueban credenciales por defecto y se intenta **inyección SQL** sin éxito

Por lo tanto, se decide realizar un nuevo ataque de fuerza bruta

Antes de ello, se intercepta la petición con **Burp Suite** para:

* Analizar cómo se envían las credenciales
* Identificar parámetros y cabeceras necesarias

<img width="607" height="318" alt="Pasted image 20260212132335" src="https://github.com/user-attachments/assets/5bde0ae2-ff81-4084-a2d4-1590a44c31a7" />

## ⚡ Optimización del ataque

Para optimizar el tiempo del ataque, se reduce el diccionario rockyou.txt a las primeras 300 entradas:

```bash
head -n 300 rockyou.txt > rockyou_300.txt
```

<img width="1883" height="96" alt="Pasted image 20260212135001" src="https://github.com/user-attachments/assets/30a2b704-525d-4297-b6b3-978cccd748e0" />

Se ejecuta el ataque con **Hydra**, obteniendo nuevas credenciales válidas

<img width="865" height="71" alt="Pasted image 20260212135025" src="https://github.com/user-attachments/assets/2a0f9165-b230-4fce-8a53-8b3d9bbd4b4a" />

## 👤 Acceso y reconocimiento

Tras autenticarse, se identifica un usuario: **balutin**

<img width="403" height="381" alt="Pasted image 20260212135103" src="https://github.com/user-attachments/assets/bb575ac1-2a9b-4457-86fa-a166e1c0d653" />

Se decide utilizar estas credenciales para intentar acceso por SSH

## 🔑 Acceso por SSH

Se realiza un ataque de fuerza bruta contra el servicio SSH, logrando acceso al sistema

<img width="753" height="82" alt="Pasted image 20260212135317" src="https://github.com/user-attachments/assets/0935a40c-76bd-4a85-abe1-27814f1de9c8" />

Una vez dentro, se intenta escalar privilegios:

* Búsqueda de binarios SUID

<img width="681" height="267" alt="Pasted image 20260212140105" src="https://github.com/user-attachments/assets/25f75a45-f179-4a5c-96a3-5fde0062a4ec" />

Sin encontrar vectores claros.

## 👑 Escalada de privilegios

Al no encontrar otras vías, se decide realizar un ataque de fuerza bruta al usuario: **root**

Para ello, se decide utilizar la herramienta [Sudo_BruteForce](https://github.com/Maalfer/Sudo_BruteForce) de [Mario](https://github.com/Maalfer) para hacer fuerza bruta hacia el ususario **root**

Proceso:

* Se descarga el script en la máquina atacante
* Se transfiere a la máquina víctima junto con rockyou.txt
* Se verifican los archivos

<img width="402" height="71" alt="Pasted image 20260216125659" src="https://github.com/user-attachments/assets/2c0bb04c-d265-45cc-956b-20690f8e239e" />

* Se asignan permisos de ejecución
* Se ejecuta el ataque

<img width="648" height="383" alt="Pasted image 20260216125843" src="https://github.com/user-attachments/assets/25ab2fa7-6de5-4899-9c55-093a840650bf" />

Con la contrseña encontrada, se logra ser el usuario **root**

<img width="551" height="135" alt="Pasted image 20260216125936" src="https://github.com/user-attachments/assets/bb565af4-6ce6-45ca-b590-4e452454344d" />

## 🏁 Conclusión

Este laboratorio nos muestra de forma muy clara los riesgos de usar contraseñas poco seguras y de no contar con protecciones contra intentos repetidos de acceso. Al final, nos enseña que un atacante puede aprovechar estos descuidos en diferentes puntos de entrada usando herramientas como Hydra o Burp Suite para ir escalando hasta tomar el control total como root. Es un recordatorio de que, si no blindamos bien cada acceso, estamos dejando el camino libre para que alguien se haga con el control total del sistema.
