# Obsession

## 🔎 Enumeración

Se realiza un escaneo de puertos con Nmap, identificando los siguientes servicios expuestos:<br>
Como resultado, se detectan los siguientes puertos abiertos:

<img width="842" height="513" alt="Pasted image 20251217123950" src="https://github.com/user-attachments/assets/b6ddc56a-4e6d-4122-b20f-dce58bfcf7fc" />

Al interactuar con el servicio FTP, se detecta que permite acceso mediante el usuario **anonymous**, lo que representa una mala práctica de 
seguridad.

<img width="412" height="212" alt="Pasted image 20251217124314" src="https://github.com/user-attachments/assets/56786c5f-8e57-4748-9ac8-1a8f2931ef4f" />

Al listar el contenido del servidor FTP, se identifican dos archivos .txt, los cuales son descargados para su análisis.

<img width="687" height="483" alt="Pasted image 20251217124455" src="https://github.com/user-attachments/assets/10b8e8ab-3e3f-4647-8a8a-9cbbffc28f7e" />

### 📄 Análisis de archivos FTP

El contenido de los archivos no revela credenciales directamente; sin embargo, uno de ellos contiene un comentario que hace referencia a permisos habilitados de forma insegura, sugiriendo una posible mala configuración del sistema que podría ser explotada posteriormente.

<img width="817" height="583" alt="Pasted image 20251217125006" src="https://github.com/user-attachments/assets/5e29ff9e-e342-47a0-93b9-fec2a532ba56" />

## 🌐 Enumeración Web

Se accede al servicio web y se inspecciona el código fuente en busca de pistas adicionales. Durante el análisis, se identifica que el sistema utiliza el mismo usuario para múltiples servicios, lo cual incrementa el riesgo de reutilización de credenciales.

<img width="1321" height="572" alt="Pasted image 20251217125245" src="https://github.com/user-attachments/assets/2bd904bb-4318-4355-ba53-8b15f2e0a956" />

Posteriormente, se realiza una enumeración de directorios, encontrando dos archivos:
* Uno sin información relevante

<img width="1168" height="553" alt="Pasted image 20251217130356" src="https://github.com/user-attachments/assets/fb8d5e5e-a1f6-43f9-8ab6-b2ed71451c51" />

* Otro que revela el nombre de usuario válido del sistema

<img width="692" height="111" alt="Pasted image 20251217130852" src="https://github.com/user-attachments/assets/d9b7e3b5-72da-4fd0-91ec-c7e0c9f918fd" />


## ⚔️ Explotación

Dado que se cuenta con un usuario válido y el servicio SSH está expuesto, se decide realizar un ataque de fuerza bruta controlado utilizando Hydra.

<img width="743" height="80" alt="Pasted image 20251217132700" src="https://github.com/user-attachments/assets/c34a506d-4ca7-4e29-aafb-dc567d229f2c" />

Como resultado, se obtiene una combinación válida de usuario y contraseña, permitiendo el acceso al sistema mediante SSH.

## 🔐 Escalada de Privilegios

Una vez dentro del sistema, se enumeran los privilegios del usuario y se identifica que puede ejecutar el binario **vim** como **root** sin necesidad de contraseña.

<img width="990" height="175" alt="Pasted image 20251217171734" src="https://github.com/user-attachments/assets/3c26ed6a-2d3a-44f6-80e7-404ffe071187" />

Se consulta la plataforma GTFOBins para identificar una técnica de escalada de privilegios asociada a dicho binario.

<img width="848" height="160" alt="Pasted image 20251217171949" src="https://github.com/user-attachments/assets/d984c592-03a2-405c-a323-8b0c5f4b62c0" />

Tras ejecutar el método correspondiente, se obtiene acceso root exitosamente

<img width="371" height="58" alt="Pasted image 20251217172125" src="https://github.com/user-attachments/assets/469323cb-7aee-4f49-ad33-80200ece1743" />


