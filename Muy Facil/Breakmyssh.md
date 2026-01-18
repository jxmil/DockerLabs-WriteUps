# Breakmyssh

## 🔎 Enumeración

Se realiza un escaneo de puertos, identificando que el único servicio expuesto es:
* 22/tcp – SSH

<img width="957" height="296" alt="Pasted image 20260116132300" src="https://github.com/user-attachments/assets/c47d801f-e346-4b27-add7-82175989fb63" />

Durante el análisis del servicio, se detecta que el servidor SSH utiliza una versión desactualizada, lo cual incrementa la superficie de ataque y puede permitir técnicas como enumeración de usuarios o ataques de fuerza bruta.

## ⚔️ Explotación

Debido a que el servicio SSH permite autenticación directa como root, lo cual constituye una mala práctica de seguridad, se decide realizar un ataque de fuerza bruta controlado sobre dicho usuario, al tratarse de un nombre por defecto ampliamente conocido.

<img width="692" height="31" alt="Pasted image 20260116132923" src="https://github.com/user-attachments/assets/98056e27-7a87-47d3-aab0-0a06e7b08efe" />

Como resultado del ataque, se logra obtener la contraseña válida para el usuario root, permitiendo el acceso directo al sistema mediante SSH

## 🏁 Acceso al sistema

Una vez autenticado, se confirma el acceso con privilegios de superusuario (root), comprometiendo completamente el sistema sin necesidad de realizar una escalada de privilegios adicional.

<img width="801" height="360" alt="Pasted image 20260116133042" src="https://github.com/user-attachments/assets/b58b5b74-15ff-48ec-9e03-9d3f0f5392e8" />

##  🏁 Conclusión

Esta máquina evidencia una vulnerabilidad crítica derivada de una mala configuración del servicio SSH, al permitir el acceso remoto directo al usuario root y utilizar una versión desactualizada del servicio.

La falta de medidas básicas de seguridad, como la deshabilitación del acceso root, el uso de autenticación por clave y mecanismos de protección contra fuerza bruta, permitió el compromiso total del sistema sin necesidad de realizar una escalada de privilegios adicional.

Este laboratorio refuerza la importancia de aplicar buenas prácticas de hardening en servicios expuestos, demostrando que incluso configuraciones simples pueden representar un alto riesgo si no se gestionan adecuadamente.
