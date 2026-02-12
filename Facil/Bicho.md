# Bicho

## 🔎 Enumeración

Se realiza un escaneo de puertos con *Nmap*, donde únicamente se identifica el puerto:
* 80/tcp – HTTP

<img width="787" height="150" alt="Pasted image 20251229110826" src="https://github.com/user-attachments/assets/94693801-fcd1-4c57-9c3e-da764359c918" />

Al acceder a la página web, se observa que redirige al dominio ```biclo.dl```

<img width="1172" height="763" alt="Pasted image 20251229111359" src="https://github.com/user-attachments/assets/800c0cc5-f889-4b3a-92c1-5d15d5dfb658" />

Por lo que se agrega al archivo ```/etc/hosts``` para poder interactuar correctamente con el sitio

<img width="657" height="248" alt="Pasted image 20251229111824" src="https://github.com/user-attachments/assets/6e8cfa33-6c48-4a47-85c6-703c9915fccd" />

## 🌐 Análisis de la aplicación

Una vez accesible el dominio, se identifica que la web está construida con **WordPress**

<img width="1511" height="797" alt="Pasted image 20251229112142" src="https://github.com/user-attachments/assets/517d9cae-bf23-425f-800a-b0184b07d63c" />

<img width="1240" height="767" alt="Pasted image 20251229112622" src="https://github.com/user-attachments/assets/ba1bc43f-09ef-42bb-9116-64e905fbf911" />

Con ayuda de **WhatWeb** se detecta que la versión instalada se encuentra desactualizada

<img width="1173" height="113" alt="Pasted image 20251229113537" src="https://github.com/user-attachments/assets/0909370c-2c95-44b6-a594-bc93014a57a5" />

Posteriormente, se realiza enumeración con WPScan, logrando identificar un usuario válido:```Bicho```

<img width="1016" height="900" alt="Pasted image 20251229115525" src="https://github.com/user-attachments/assets/bf9e0e5f-f633-4c43-9547-7a0294e455f7" />

<img width="1192" height="872" alt="Pasted image 20251229115654" src="https://github.com/user-attachments/assets/16161885-5786-44b9-b4eb-f74ce86b06f1" />

<img width="1181" height="468" alt="Pasted image 20251229115734" src="https://github.com/user-attachments/assets/a8b6fc22-0683-4a82-b1fa-2e6db55ba1ea" />

Durante el análisis del reporte, se detecta la existencia de un archivo con extensión .log, lo que abre la posibilidad de realizar un Log Poisoning

## ⚠️ Explotación – Log Poisoning

El ataque de Log Poisoning consiste en inyectar código malicioso dentro de archivos de log para posteriormente ejecutarlo

Se utiliza **Burp Suite** para modificar el encabezado ```User-Agent``` con código PHP:
```
<?php phpinfo(); ?>
```

<img width="1372" height="442" alt="Pasted image 20251229131932" src="https://github.com/user-attachments/assets/66c8e666-5069-4a53-94cc-9140ae02b907" />

Al enviar la petición y verificar el archivo ```/wp-content/debug.log```, se confirma que el código es interpretado por el servidor ```(status 200)```

Posteriormente, se codifica en *Base64* un payload de reverse shell 

<img width="668" height="47" alt="Pasted image 20251229131804" src="https://github.com/user-attachments/assets/51dd2794-2a6c-41e0-9e6d-f7974f0e761f" />

Nos dirigimos a **Burp Suite** y pegamos 

<img width="958" height="480" alt="Pasted image 20251229165140" src="https://github.com/user-attachments/assets/371c54cd-6e8f-4985-859f-1e412992677c" />

Nos dirigimos al directorio de ```/wp-content/debug.log```, apagamos el **proxy** y refrescamos la pagina

<img width="1282" height="137" alt="Pasted image 20251229165404" src="https://github.com/user-attachments/assets/a989f6ed-de5d-4a1e-b444-00fbe5868b43" />

Al acceder nuevamente al archivo ```debug.log``` y con el listener activo, se obtiene acceso como **www-data**

<img width="842" height="152" alt="Pasted image 20251229165440" src="https://github.com/user-attachments/assets/753342cb-001d-4e13-aade-5770286ffe15" />

Una vez dentro realizamos el tratamiento TTY

<img width="240" height="140" alt="Pasted image 20251222222913" src="https://github.com/user-attachments/assets/0bcdad17-5728-4aed-a46c-dc068009188d" />

## Pivoting – Acceso a servicio interno

Vemos que usuarios tiene una consola asignada

<img width="637" height="118" alt="Pasted image 20251229171104" src="https://github.com/user-attachments/assets/70643095-e951-4775-b02d-dc1f4f65545b" />

Durante la enumeración interna se detecta que el puerto *5000* está en ejecución con una aplicación **Flask**, pero accesible solo de manera local

<img width="1137" height="322" alt="Pasted image 20251229174308" src="https://github.com/user-attachments/assets/228a3cc4-5ea6-4cfa-86d5-f911324ac758" />

Gracias a la herramienta Chisel, crearemos un puente cifrado que conectará la máquina objetivo con la nuestra. El objetivo es 'traernos' ese puerto privado para que podamos interactuar con él como si estuviera abierto en nuestra propia máquina

<img width="801" height="193" alt="Pasted image 20251229173108" src="https://github.com/user-attachments/assets/5f723932-a7c2-41d1-be93-ee0d53ba1cde" />

Una vez descargada, descomprimimos el archivo

<img width="752" height="350" alt="Pasted image 20251229173336" src="https://github.com/user-attachments/assets/84523015-cd7c-4282-bcc0-508e930b45af" />

Le damos permisos de ejecución y levantamos un servidor por el puerto 1234

<img width="818" height="198" alt="Pasted image 20251229174053" src="https://github.com/user-attachments/assets/83296835-be8a-4709-b34d-32884712ca70" />

Creamos un servidor en *python* para que podamos descargar la herramienta en nuestra maquina objetivo

<img width="833" height="191" alt="Pasted image 20251229174923" src="https://github.com/user-attachments/assets/ef02398a-18ef-4c47-9245-cdafea51ac44" />

Damos los permisos de ejecución

<img width="846" height="118" alt="Pasted image 20251229175115" src="https://github.com/user-attachments/assets/4c68738b-1386-4c92-9cfe-db4323f30861" />

Visitamos la pagina por el puerto 9000

<img width="1265" height="561" alt="Pasted image 20251229175316" src="https://github.com/user-attachments/assets/2b943ba4-64bf-4ba7-968f-be1d9148c75a" />

Realizamos fuzzing de directorios, y nos encontró ```/console ```

<img width="842" height="360" alt="Pasted image 20251229175652" src="https://github.com/user-attachments/assets/622cc582-d0a0-4665-8be6-355f2e671120" />

Se identifica una funcionalidad vulnerable que permite ejecución de código en una consola **Python**

<img width="1310" height="330" alt="Pasted image 20251229175720" src="https://github.com/user-attachments/assets/60f4be5d-1809-4f9a-978d-3d2743aad5dc" />

Pondremos nuestra reverse shell

<img width="673" height="20" alt="Pasted image 20251229181015" src="https://github.com/user-attachments/assets/acaebc5c-df27-4407-836f-614d99c8b5eb" />

Nos ponemos en escucha y estamos dentro

<img width="776" height="137" alt="Pasted image 20251229181127" src="https://github.com/user-attachments/assets/7e239278-36d2-4678-bb97-542a0180458b" />

## 🔐 Escalada de privilegios

Intentamos escalar privilegios, pero al verificar que el binario ```/usr/local/bin/wp``` es ejecutable por **wpuser**, nos cambiaremos de usuario y aprovecharemos esta vía para poner una revershell

<img width="1276" height="38" alt="Pasted image 20251229181724" src="https://github.com/user-attachments/assets/8e9101fe-030c-4a28-a728-2db2ef53ca70" />

Nos ponemos en escucha y somos el usuario **wpuser**

<img width="813" height="201" alt="Pasted image 20251229181821" src="https://github.com/user-attachments/assets/05924a93-1b2e-4073-8283-bd6e73bb49c7" />

Tenemos la flag

<img width="387" height="57" alt="Pasted image 20251229181914" src="https://github.com/user-attachments/assets/b2bf85a8-00c4-4017-bb66-f463a6ca40d3" />

Intentamos escalar privilegios 

<img width="1035" height="171" alt="Pasted image 20251229182429" src="https://github.com/user-attachments/assets/1b2c55b9-f3bb-43c2-91f7-1717d802b464" />


Al analizar su funcionamiento, se descubre que es posible inyectar comandos durante su ejecución y asignar permisos **SUID** a ```/bin/bash```

<img width="925" height="192" alt="Pasted image 20251229183115" src="https://github.com/user-attachments/assets/fedfa97b-0d24-45d9-a50f-c9f1461156eb" />

Finalmente, ejecutando: ```/bin/bash -p ```, somos **root**

<img width="398" height="65" alt="Pasted image 20251229183500" src="https://github.com/user-attachments/assets/ef69451a-3943-4d2d-93e0-c274325ab49a" />

Y tenemos la flag del usuario root

<img width="388" height="82" alt="Pasted image 20251229183416" src="https://github.com/user-attachments/assets/1aadaf29-9fec-4cfc-82d6-d54d5347a6f0" />

## 🏁 Conclusión

La explotación de la máquina se basó en una cadena de ataque progresiva que comenzó con la enumeración del servicio web y la identificación de un WordPress desactualizado. A partir de ahí, se aprovechó una vulnerabilidad de Log Poisoning para obtener ejecución remota de comandos como www-data. Posteriormente, mediante técnicas de pivoting con chisel, se logró acceder a un servicio interno Flask expuesto solo localmente, lo que permitió una nueva ejecución de comandos y movimiento lateral dentro del sistema. Finalmente, una mala configuración de binarios con privilegios elevados hizo posible la escalada de privilegios hasta obtener acceso como root, comprometiendo completamente la máquina.
