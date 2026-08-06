# Amor

## 🔎 Enumeración

Se realiza un escaneo de puertos utilizando Nmap, identificando los siguientes servicios expuestos:

* 22/tcp – SSH
* 80/tcp – HTTP

<img width="743" height="347" alt="Pasted image 20260511111441" src="https://github.com/user-attachments/assets/fb07269d-46f2-4c89-aafd-c2abfff35668" />

## 🌐 Análisis de la aplicación web

Al acceder a la página web, se observa que pertenece a una empresa que reporta un intento de phishing

<img width="1163" height="485" alt="Pasted image 20260511111709" src="https://github.com/user-attachments/assets/c5fbec6e-b586-4bc6-9913-7593aea0ffef" />

Durante la revisión del contenido, se descubre que un empleado llamado **Juan** fue despedido tras enviar su contraseña a un compañero. Esta información resulta interesante y sirve como punto de partida para la enumeración

<img width="1108" height="270" alt="Pasted image 20260511111747" src="https://github.com/user-attachments/assets/f32dd5a2-90de-49e5-9484-1a346e6d89d8" />

Posteriormente, se realiza fuzzing de directorios en busca de recursos adicionales, aunque no se encuentran elementos relevantes

<img width="791" height="462" alt="Pasted image 20260511112424" src="https://github.com/user-attachments/assets/4d08ccbe-84d9-4b02-b7f6-ee961de35095" />

Con la información obtenida, se intenta acceder al servicio SSH mediante un ataque de fuerza bruta utilizando el usuario **Juan**, sin éxito. Se prueba posteriormente con el usuario **Carlota**, logrando obtener credenciales válidas y acceso al sistema

<img width="735" height="62" alt="Pasted image 20260511113114" src="https://github.com/user-attachments/assets/0e9a5dd1-06a8-46f3-acf5-b0e3947d0438" />
<br>

<img width="117" height="45" alt="Pasted image 20260511113224" src="https://github.com/user-attachments/assets/a9d093af-a041-48e9-9e62-8816ef67dd5b" />

Hacemos tratamiento de TTY

<img width="240" height="140" alt="Pasted image 20260511113306" src="https://github.com/user-attachments/assets/1e99fd58-26a3-4223-a836-915e23fcb8cf" />

## 🔑 Obtención de acceso inicial

Una vez autenticados mediante SSH, se inicia la fase de enumeración local

Inicialmente no se identifican vectores claros de escalada de privilegios, por lo que se explora el sistema de archivos en busca de información útil

<img width="566" height="265" alt="Pasted image 20260511113610" src="https://github.com/user-attachments/assets/3599ee1d-5519-4998-9fe9-4ff42fff75ab" />

Durante la exploración del directorio ```/home```, se localiza un archivo oculto que contiene un mensaje con una pista interesante

<img width="626" height="175" alt="Pasted image 20260511113922" src="https://github.com/user-attachments/assets/d4a22a48-ced5-4cab-96b9-3c308a101a74" />

<img width="1297" height="73" alt="Pasted image 20260511114039" src="https://github.com/user-attachments/assets/3e122026-1a49-4325-8cff-30c4106883d2" />

Siguiendo dicha pista, se accede al directorio ```Desktop/vacaciones```, donde se encuentra una imagen que es descargada para su análisis

<img width="597" height="355" alt="Pasted image 20260511114242" src="https://github.com/user-attachments/assets/004fc7f1-68d5-4496-90a4-2a5cbbdfbbb9" />

Mediante **StegSeek**, se extrae un archivo oculto de la imagen. Tras decodificar su contenido, se obtiene una posible contraseña

<img width="786" height="43" alt="Pasted image 20260511115558" src="https://github.com/user-attachments/assets/bcd4b1b2-729f-4bf1-87c9-72088f758250" />

<img width="565" height="297" alt="Pasted image 20260511115812" src="https://github.com/user-attachments/assets/1384657a-8ad0-438a-8673-e23854c84614" />

<img width="492" height="100" alt="Pasted image 20260511115918" src="https://github.com/user-attachments/assets/22ce5de6-ec97-45e3-87ce-2791fa9d542c" />

Al revisar los usuarios del sistema, se identifica al usuario **Oscar**, y la contraseña recuperada permite autenticarse correctamente como dicho usuario

## 👑 Escalada de privilegios

Como el usuario **Oscar**, se continúa con la enumeración del sistema y se localiza un archivo de texto que proporciona información adicional

<img width="617" height="342" alt="Pasted image 20260511120410" src="https://github.com/user-attachments/assets/371b840d-a876-48f1-a6f8-97e5cb9e6282" />

Posteriormente, se identifica un binario que puede ejecutarse con privilegios de **root**

<img width="1280" height="117" alt="Pasted image 20260511120744" src="https://github.com/user-attachments/assets/2acf80bf-22c1-4421-985a-a66db0fda8d2" />

Tras consultar [GTFOBins](https://gtfobins.org/), se utiliza la técnica correspondiente para abusar del binario y elevar privilegios, obteniendo acceso completo al sistema como **root**

<img width="857" height="391" alt="Pasted image 20260511120939" src="https://github.com/user-attachments/assets/90d1672c-85cd-4384-9333-99cab507d7c6" />

<img width="552" height="90" alt="Pasted image 20260511121027" src="https://github.com/user-attachments/assets/6cef7603-8d25-4e85-a09d-c538d5acd8f2" />

Finalmente, se revisa el contenido del directorio ```/root```, donde se encuentra el mensaje final de la máquina

<img width="1133" height="187" alt="Pasted image 20260511121147" src="https://github.com/user-attachments/assets/e551becd-a6d7-489a-9afc-3d7a961f03a1" />

## 🏁 Conclusión

Este laboratorio demuestra cómo pequeñas filtraciones de información pueden convertirse en un punto de apoyo para comprometer un sistema. Además, pone de manifiesto la importancia de analizar cuidadosamente los archivos presentes en el servidor, ya que técnicas como la esteganografía pueden ocultar credenciales o información sensible. Finalmente, resalta la necesidad de revisar los binarios con privilegios elevados para evitar que configuraciones inseguras permitan una escalada completa de privilegios.
