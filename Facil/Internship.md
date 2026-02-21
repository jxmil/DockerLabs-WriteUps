# Internship

## 🔎 Enumeración

Se realiza un escaneo de puertos con Nmap, identificando los puertos:
* 22/tcp – SSH
* 80/tcp – HTTP

Con esto, se procede a analizar la aplicación web

<img width="1320" height="558" alt="Pasted image 20251222171348" src="https://github.com/user-attachments/assets/a30af219-8d23-44d2-9dc0-28896195f27a" />

## 🌐 Análisis Web

Al visitar la página principal, los botones no presentan funcionalidad. Inspeccionando el código fuente se identifica un host oculto, por lo que se agrega manualmente al archivo ```/etc/hosts```

<img width="677" height="412" alt="Pasted image 20251222181640" src="https://github.com/user-attachments/assets/36bf390a-a573-4672-8909-8456e2bd3d8f" />

<img width="625" height="222" alt="Pasted image 20251222181706" src="https://github.com/user-attachments/assets/e9e98971-49a5-4a7b-b23c-d646a0c9d2c4" />

Después de actualizar la página (F5), los botones comienzan a funcionar correctamente

<img width="1085" height="586" alt="Pasted image 20251222182016" src="https://github.com/user-attachments/assets/dc5675a6-7aee-4019-b9ab-248882bfa571" />

## 💉 Inyección SQL

Se detecta que el login es vulnerable a ```SQL Injection```, lo que permite iniciar sesión exitosamente

<img width="492" height="277" alt="Pasted image 20251222182042" src="https://github.com/user-attachments/assets/702cd04d-6c56-4fa5-9f05-11a0149070e8" />

Tras la explotación, se obtiene acceso a un Dashboard de Recursos Humanos, donde se visualiza información sensible, incluyendo una lista de usuarios

<img width="1321" height="563" alt="Pasted image 20251222182200" src="https://github.com/user-attachments/assets/e447496c-f513-405f-8a62-fb12ee0a94f0" />

## 📂 Enumeración de Directorios

Se realiza fuzzing sobre los directorios activos

<img width="892" height="616" alt="Pasted image 20251222182609" src="https://github.com/user-attachments/assets/18f10753-bdea-4734-bb96-c87bdd01190e" />

La mayoría muestran restricciones de acceso, sin embargo, el directorio ```/spam``` contiene una página aparentemente en blanco

Al inspeccionar el código fuente, se descubre un mensaje oculto

<img width="670" height="463" alt="Pasted image 20251222182937" src="https://github.com/user-attachments/assets/6b3b6238-d1bd-461d-bf6c-23c0afbb5b59" />

Hacemos uso de la IA para decifrarla

<img width="1022" height="422" alt="Pasted image 20251222183206" src="https://github.com/user-attachments/assets/e36b1552-aefb-4781-9db3-97e6ba31df25" />

## 🔑 Fuerza Bruta y Acceso SSH

Con la lista de usuarios obtenida mediante la ```inyección SQL```, se guardan en un archivo y se realiza un ataque de fuerza bruta con **Hydra**

<img width="771" height="411" alt="Pasted image 20251222184128" src="https://github.com/user-attachments/assets/093cdc74-053f-4247-b721-48bd7ae6fb31" />

<img width="740" height="117" alt="Pasted image 20251222185752" src="https://github.com/user-attachments/assets/3a392bdb-c509-433a-b4f6-38c5f7eeac89" />

Se logran credenciales válidas y se obtiene acceso por SSH

<img width="688" height="343" alt="Pasted image 20251222185919" src="https://github.com/user-attachments/assets/ed4fd2bd-3c84-48f6-8156-d233da48bd80" />

## Movimiento Lateral

Dentro del sistema, el usuario no posee privilegios elevados

<img width="636" height="57" alt="Pasted image 20251222190113" src="https://github.com/user-attachments/assets/6c77d649-cf87-41e3-b93b-263d13312e9e" />

Al listar ```/home```, se identifica otro usuario

<img width="335" height="37" alt="Pasted image 20251222191709" src="https://github.com/user-attachments/assets/0b08baa5-ff94-4d23-b64c-616c8ebac8ec" />

Navegando en la máquina, en el directorio ```/opt``` se encuentra un archivo con permisos de ejecución. Este archivo es modificado para incluir una reverse shell

<img width="712" height="156" alt="Pasted image 20251222193334" src="https://github.com/user-attachments/assets/358d2ee8-79c4-42aa-a795-e2a175708e97" />

<img width="632" height="152" alt="Pasted image 20251222194053" src="https://github.com/user-attachments/assets/076187db-2005-42dd-9e42-a0413889f895" />

Se inicia un listener en el puerto 443 y se obtiene acceso

<img width="631" height="460" alt="Pasted image 20251222194157" src="https://github.com/user-attachments/assets/20b33d3a-57dd-4002-8b89-8860eba6073e" />

Realizamos tratamiento de TTY

<img width="240" height="140" alt="Pasted image 20251222222913" src="https://github.com/user-attachments/assets/bc64a8c2-5b59-4a9c-b814-258eccbf5042" />

## 🖼️ Análisis Forense y Esteganografía

En el directorio ```/home``` se encuentra un archivo **.jpeg**

<img width="803" height="97" alt="Pasted image 20251222223931" src="https://github.com/user-attachments/assets/1d0a4957-6a4b-439a-b5be-68c988829a32" />

Se mueve al directorio ```/tmp```, se ajustan permisos y se transfiere utilizando ```SCP (Secure Copy Protocol)``` hacia al usario **pedro** para su análisis

<img width="597" height="137" alt="Pasted image 20251222224417" src="https://github.com/user-attachments/assets/43d34cc7-7a29-4817-8077-df2098c8f8db" />

<img width="683" height="67" alt="Pasted image 20251222224450" src="https://github.com/user-attachments/assets/7b426add-0861-408e-bdc8-85c99fb8a869" />

<img width="926" height="157" alt="Pasted image 20251222224657" src="https://github.com/user-attachments/assets/1d46bb58-0a85-4d1d-a2ff-5e575fc19091" />

Se crea una servidor en la maquina del usuario, para luego con nuestra maquina local descargar el archivo

<img width="887" height="52" alt="Pasted image 20251222224844" src="https://github.com/user-attachments/assets/bc476bed-ab20-4c3d-8360-1f9af4f1d896" />

Una vez obtenido el archivo **jpeg** se inspecciona con exiftool, pero no revela información relevante

<img width="717" height="461" alt="Pasted image 20251222225120" src="https://github.com/user-attachments/assets/1fb73cf9-d6ea-49a1-8769-8039f6553b1f" />

Posteriormente, se utiliza **steghide**, logrando extraer un archivo *.txt* oculto

<img width="592" height="197" alt="Pasted image 20251222225516" src="https://github.com/user-attachments/assets/dc0262e9-22af-467c-b114-85e522433799" />

<img width="328" height="110" alt="Pasted image 20251222225800" src="https://github.com/user-attachments/assets/a2917971-deb4-4446-90bc-936a52675cf8" />

El contenido del archivo revela una posible contraseña para el usuario **valentina**

## 👑 Escalada de Privilegios

Probamos la palabara encontrada como contraseña del usuario **valentina** y se tiene acceso éxitosamente

<img width="1046" height="280" alt="Pasted image 20251222225914" src="https://github.com/user-attachments/assets/2e1acf7e-9b3e-4340-a460-5328afbcbf57" />

Escalamos privilegios con ```sudo su``` y somos **root**

<img width="457" height="107" alt="Pasted image 20251222230606" src="https://github.com/user-attachments/assets/3dfc842d-e8cc-4bda-941f-ff2fbb5752e5" />

## 🏁 Conclusión

Este laboratorio demuestra cómo una cadena de vulnerabilidades desde SQL Injection, mala gestión de credenciales, archivos expuestos y esteganografía puede llevar a la compromisión total del sistema.
