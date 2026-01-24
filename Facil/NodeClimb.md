# NodeClimb

## 🔎 Enumeración

Se realiza un escaneo de puertos utilizando la herramienta Nmap, identificando los siguientes servicios expuestos:
* 21/tcp – FTP
* 22/tcp – SSH

<img width="833" height="520" alt="Pasted image 20251218113849" src="https://github.com/user-attachments/assets/2cd964af-b432-4d48-b067-4bc7965b611f" />

## 📂 Acceso al servicio FTP

Al analizar el servicio FTP, se observa que no ha sido configurado correctamente y permite el acceso mediante el usuario anonymous.

<img width="950" height="422" alt="Pasted image 20251218114112" src="https://github.com/user-attachments/assets/0d8fcf8e-c855-4eb9-b66a-e3c922eb2204" />

Una vez autenticados, se listan los archivos disponibles y se descarga un archivo con extensión .zip

## 🔐 Análisis del archivo ZIP

Al intentar extraer el archivo descargado, se identifica que está protegido por contraseña.

<img width="701" height="78" alt="Pasted image 20251218114500" src="https://github.com/user-attachments/assets/a40ec834-e43b-4551-9fc0-a463da48fb5b" />

Para obtener su contenido, se hace uso de la herramienta John The Ripper, logrando descifrar la contraseña del archivo comprimido.

<img width="1072" height="340" alt="Pasted image 20251218115701" src="https://github.com/user-attachments/assets/1e62ecd1-8fcf-4e31-8f2f-d505ccec1478" />

Tras extraerlo, se analiza su contenido, el cual proporciona información útil para el acceso al sistema.

<img width="528" height="118" alt="Pasted image 20251218115843" src="https://github.com/user-attachments/assets/60963c60-a857-48f3-a419-859f1fac6cb0" />

## ⚔️ Acceso al sistema

Con la información obtenida, se inicia sesión en el sistema a través del servicio SSH

<img width="1033" height="378" alt="Pasted image 20251218120106" src="https://github.com/user-attachments/assets/420cd491-7ef5-40e6-9aea-de864c62cbd2" />

## 🔐 Escalada de privilegios

Una vez dentro del sistema, se enumeran los privilegios del usuario y se detecta que puede ejecutar el binario node con permisos elevados.

<img width="935" height="157" alt="Pasted image 20251218120416" src="https://github.com/user-attachments/assets/a5c9d601-d4ea-41dc-afae-26fb4db24a9a" />

Durante la enumeración, se encuentra un archivo llamado script.js, el cual se encuentra vacío y es editable

Se consulta la página GTFOBins para identificar una técnica de escalada de privilegios utilizando Node.js. 

<img width="1122" height="292" alt="Pasted image 20251218123225" src="https://github.com/user-attachments/assets/e9e4f0b9-cf82-4ac4-95ca-d00e427019ba" />

El código correspondiente se inserta dentro del archivo script.js

<img width="882" height="72" alt="Pasted image 20251218123930" src="https://github.com/user-attachments/assets/b62d0a95-d2b7-4b61-a141-fea2812218bd" />

Posteriormente, se ejecuta el script con privilegios elevados, lo que permite obtener una shell como root.

<img width="582" height="90" alt="Pasted image 20251218124150" src="https://github.com/user-attachments/assets/e6dd8495-edaf-4dd9-a91a-d55d72296e4e" />

## 🏁 Conclusión

Este laboratorio demuestra cómo una mala configuración en servicios básicos como FTP puede ser el punto de entrada para comprometer un sistema.

Además, el uso incorrecto de permisos sudo asociados a binarios comunes como Node.js representa un riesgo crítico, ya que permite a un usuario escalar privilegios de forma directa.
