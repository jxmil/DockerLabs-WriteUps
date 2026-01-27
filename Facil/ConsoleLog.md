# ConsoleLog

## 🔎 Enumeración

Se realiza un escaneo de puertos utilizando Nmap, donde se identifican los siguientes servicios abiertos:
* 80/tcp – HTTP
* 3000/tcp – HTTP
* 5000/tcp – SSH

<img width="836" height="417" alt="Pasted image 20251218125146" src="https://github.com/user-attachments/assets/3cd80e05-d1eb-4982-8342-82b411ed878a" />

## 🌐 Análisis de la aplicación web

Al visitar la página web principal, se observa un mensaje de bienvenida junto con un botón. Al inspeccionar el código fuente, se encuentra una referencia que indica que para tareas de depuración se debe acceder a la ruta **/recursos**.

<img width="972" height="746" alt="Pasted image 20251218125512" src="https://github.com/user-attachments/assets/64b358e8-e49d-4c45-99ed-9b5841664423" />

Continuando con la enumeración web, se realiza la búsqueda de directorios, lo que permite descubrir la ruta **/backend**.

<img width="802" height="477" alt="Pasted image 20251218170444" src="https://github.com/user-attachments/assets/f12e4e23-1c79-49d3-b30b-b851d88992ef" />

Al acceder a esta ruta, se visualiza el backend de la aplicación web.

<img width="588" height="372" alt="Pasted image 20251218170618" src="https://github.com/user-attachments/assets/b4be106c-3714-44d3-82c0-69ac4b5d95ff" />

## 🔑 Exposición de información sensible

Dentro del backend, se tiene acceso al archivo server.js, donde se encuentra una contraseña en texto claro, lo que representa una mala práctica de seguridad.

<img width="717" height="372" alt="Pasted image 20251218170800" src="https://github.com/user-attachments/assets/53676a2a-36aa-4024-b2ce-1ec1660b1649" />

## 🔓 Acceso al sistema

Con la información obtenida, se realiza un ataque de fuerza bruta utilizando la herramienta Hydra, logrando obtener un usuario válido.

<img width="762" height="295" alt="Pasted image 20251218170934" src="https://github.com/user-attachments/assets/278f8e6f-10ff-48e8-bc56-d46b6074f007" />

Posteriormente, se inicia sesión en el sistema a través del servicio SSH por el puerto 5000.

<img width="695" height="440" alt="Pasted image 20251218171230" src="https://github.com/user-attachments/assets/239391f8-7195-4030-8020-7677e278b04e" />

## 🔐Escalada de privilegios

Durante la revisión de permisos, se detecta que el usuario lovely puede ejecutar el binario **/usr/bin/nano** como root sin necesidad de contraseña (NOPASSWD).

<img width="782" height="197" alt="Pasted image 20251218171603" src="https://github.com/user-attachments/assets/e3fed1a4-cc9c-4845-8476-5f74c3f2e37a" />

Dado que nano permite la ejecución de comandos del sistema, esta configuración insegura posibilita una escalada de privilegios completa.

Para ello, se consulta GTFOBins y se ejecuta el payload correspondiente dentro de nano:

<img width="932" height="303" alt="Pasted image 20251218175330" src="https://github.com/user-attachments/assets/79fa771f-d12c-4c88-8c91-f930d2e62fd7" />

Usamos nano:

<img width="508" height="37" alt="Pasted image 20251218175441" src="https://github.com/user-attachments/assets/c7b30c1b-0174-4f08-814d-21fa6d3083bd" />

**Nota: Ctrl + R → Ctrl + X → Enter**
```shell
reset; sh 1>&0 2>&0
```
Aunque la terminal presenta un comportamiento inestable, el acceso como root es funcional.
<img width="1016" height="648" alt="Pasted image 20251218175645" src="https://github.com/user-attachments/assets/b9ac1de6-0678-4d60-b84d-5c77bf6a46a7" />

## 🏁 Conclusión

Este laboratorio demuestra cómo una mala exposición del backend de una aplicación web, combinada con una gestión incorrecta de permisos sudo, puede llevar a la compromisión total del sistema.
