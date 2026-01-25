# Injection

## 🔎 Enumeración

Se realiza un escaneo de puertos utilizando la herramienta Nmap, identificando los siguientes servicios expuestos:
* 22/tcp – SSH
* 80/tcp – HTTP

<img width="1030" height="363" alt="Pasted image 20251218105448" src="https://github.com/user-attachments/assets/1c46d513-d7bc-43b8-ad1a-8a7f8789982f" />

## 🌐 Análisis de la aplicación web

Al acceder a la página web, se identifica un formulario de login.

<img width="658" height="497" alt="Pasted image 20251218105637" src="https://github.com/user-attachments/assets/fd556510-db47-4a68-a61e-15c28d211157" />

Se realiza una enumeración de directorios, pero no se encuentra información relevante:
* index.php redirige nuevamente al formulario de login
* config.php muestra una página en blanco

<img width="767" height="452" alt="Pasted image 20251218105911" src="https://github.com/user-attachments/assets/f2867a98-2ff3-49a8-8cf5-685d8e1463ba" />

## ⚔️ Inyección SQL

Dado que existe un formulario de autenticación, se prueba la posibilidad de una inyección SQL.
Se prueba el formulario de login y se comprueba que es vulnerable a una inyección SQL, lo que permite acceder a la aplicación sin credenciales válidas:

<img width="473" height="292" alt="Pasted image 20251218110151" src="https://github.com/user-attachments/assets/b06d8836-1767-43d1-a685-e33b1f34de80" />


```shell
' OR 1=1-- -
```
La inyección resulta exitosa, permitiendo el acceso a la aplicación. Una vez dentro, la aplicación revela credenciales válidas, las cuales pueden ser reutilizadas.

## 🔐 Acceso al sistema

Con las credenciales obtenidas, se inicia sesión en el sistema a través del servicio SSH, logrando acceso como usuario estándar.

<img width="770" height="566" alt="Pasted image 20251218111402" src="https://github.com/user-attachments/assets/72af6390-8e9e-4807-b888-fdf105874955" />

## 🔐 Escalada de privilegios

Una vez dentro del sistema, se enumeran los permisos sudo disponibles y se identifica que el usuario puede ejecutar el binario env con privilegios elevados.

<img width="757" height="228" alt="Pasted image 20251218112907" src="https://github.com/user-attachments/assets/75dba7e8-05e4-4689-9bb0-803bac534b36" />

Se consulta la página GTFOBins para analizar técnicas de escalada de privilegios utilizando dicho binario.

<img width="1102" height="587" alt="Pasted image 20251218113022" src="https://github.com/user-attachments/assets/4f32ccca-813f-442a-a4d2-7e4fccbec5e3" />

Aplicando la técnica documentada, se ejecuta el binario env de forma que se obtiene una shell con privilegios root, comprometiendo completamente el sistema.

<img width="642" height="82" alt="Pasted image 20251218112732" src="https://github.com/user-attachments/assets/4a60066b-fc38-42b2-b013-93161306d53a" />

🏁 Conclusión

Este laboratorio demuestra cómo una vulnerabilidad clásica como la inyección SQL puede comprometer una aplicación web y facilitar el acceso inicial a un sistema.

Además, una mala configuración de permisos sudo permitió escalar privilegios mediante un binario común como env.


