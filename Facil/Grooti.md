# Grooti

## 🔎 Enumeración

Se realiza un escaneo de puertos utilizando **Nmap**, identificando los siguientes servicios abiertos:

* 22/tcp – SSH
* 80/tcp – HTTP
* 3306/tcp – MySQL

<img width="945" height="920" alt="Pasted image 20260304110417" src="https://github.com/user-attachments/assets/97a148d4-96a8-4ab8-b071-e0fe66cb761f" />

Con estos servicios detectados, se procede a analizar la aplicación web.

## 🌐 Análisis del servicio web

Al visitar la página web, se observa un posible nombre de usuario **Grooti**

<img width="566" height="647" alt="Pasted image 20260304110608" src="https://github.com/user-attachments/assets/1e8b1fad-1691-40a5-ba3e-679d2332757e" />

Inspeccionando la página, también se encuentra información que indica que el usuario **rocket** ha iniciado sesión en su base de datos

<img width="1058" height="342" alt="Pasted image 20260304112008" src="https://github.com/user-attachments/assets/11e54714-459e-4f6f-8674-597ead5b10a3" />

Posteriormente se navega por las distintas secciones del sitio

Al hacer clic en *mis fotos*, se accede al directorio: ```imagenes```

<img width="620" height="336" alt="Pasted image 20260304110931" src="https://github.com/user-attachments/assets/a859eda8-587c-4145-90e1-6a082a2c4921" />

Dentro de este directorio se encuentra un archivo: ```Readme.txt```

<img width="711" height="150" alt="Pasted image 20260304111055" src="https://github.com/user-attachments/assets/639aaa42-1dcf-4fcb-9a96-b823330fbe21" />

Al leer el archivo, se encuentra una contraseña en texto plano, aunque inicialmente no se conoce a qué servicio pertenece

Se intenta acceder a la sección *mi base de datos*, pero la página no existe

<img width="642" height="235" alt="Pasted image 20260304111533" src="https://github.com/user-attachments/assets/8ac78267-3986-489f-a7a0-58c2f2dccd49" />

Luego se visita la sección *facturas de la nave*, sin encontrar información relevante

<img width="860" height="555" alt="Pasted image 20260304111732" src="https://github.com/user-attachments/assets/c451b175-f5da-4be0-9582-a89d35a8a9ab" />

## 🔍 Descubrimiento de directorios ocultos

Después del reconocimiento manual, se realiza fuzzing de directorios, encontrando el siguiente directorio oculto: ```/secret```

<img width="835" height="223" alt="Pasted image 20260304112315" src="https://github.com/user-attachments/assets/11abb776-fc99-4e82-aa7c-be2cd327ee10" />

Al acceder a este directorio se muestran usuarios de la base de datos junto con sus roles

<img width="897" height="538" alt="Pasted image 20260304112517" src="https://github.com/user-attachments/assets/4816f9f1-23b8-4178-b8b1-68cbe0962285" />

También se encuentra un archivo de instrucciones que incluye el comando necesario para conectarse a la base de datos

<img width="551" height="85" alt="Pasted image 20260304112827" src="https://github.com/user-attachments/assets/0b58c718-d01c-4538-8366-fbb32483375d" />

## 🗄️ Acceso a la base de datos

Utilizando la contraseña encontrada anteriormente, se establece conexión con el servicio **MySQL**

<img width="832" height="237" alt="Pasted image 20260304113646" src="https://github.com/user-attachments/assets/3ac6d5cc-b25a-4181-986f-4ad9f46a4ab3" />

Una vez dentro:

Se listan las bases de datos disponibles

<img width="351" height="233" alt="Pasted image 20260304113919" src="https://github.com/user-attachments/assets/5ef98ecf-c030-4284-be18-de4c3243aac8" />

Se identifica la base de datos: ```files_secret```

Se listan las tablas

<img width="682" height="287" alt="Pasted image 20260304114019" src="https://github.com/user-attachments/assets/4c2683b3-f70f-453a-8e2f-5e5b864c6b93" />

Se consultan los registros almacenados

<img width="591" height="256" alt="Pasted image 20260304114340" src="https://github.com/user-attachments/assets/0ac81d17-2cc7-4650-9940-dc6aa9547843" />

Dentro de la tabla se encuentra una ruta interesante, que se procede a visitar desde el navegador

<img width="907" height="792" alt="Pasted image 20260304114523" src="https://github.com/user-attachments/assets/2561bcd3-0fa4-41f9-ad76-bdcb71609173" />

## 🧪 Análisis de la nueva funcionalidad web

En la nueva página se solicita:

* Enviar un mensaje
* Seleccionar un número entre 1 y 100

El sitio indica que dentro de ese rango podría encontrarse alguna pista. Sin embargo, probar cada número manualmente tomaría demasiado tiempo por eso se decide utilizar ```Burp Suite``` para automatizar el proceso

<img width="373" height="453" alt="Pasted image 20260304123358" src="https://github.com/user-attachments/assets/d59b122d-ba3b-40a4-9238-4608f72a014e" />

## ⚔️ Ataque con Burp Suite (Intruder)

Se configura el navegador para utilizar ```Burp Suite``` en el puerto 8080

<img width="727" height="756" alt="Pasted image 20260304123655" src="https://github.com/user-attachments/assets/1d88f3d9-47e0-4a50-9c5b-d8d92e27dee8" />

Se intercepta la petición HTTP

<img width="563" height="110" alt="Pasted image 20260304123824" src="https://github.com/user-attachments/assets/7aef876a-4aad-4089-b4fe-7d3c1e13e488" />

La petición es enviada a **Burp Intruder**

Configuración del ataque:

* Tipo de payload: Numérico
* Rango: 0 – 100
* Posición del payload: parámetro number

<img width="930" height="428" alt="Pasted image 20260304124600" src="https://github.com/user-attachments/assets/d8957bcb-dd06-4836-a5f1-749ddab2efc5" />

Se inicia el ataque utilizando **Start Attack**

<img width="1015" height="785" alt="Pasted image 20260304125011" src="https://github.com/user-attachments/assets/d0e1ea14-e400-48da-817c-09b06c717f3d" />

Analizando las respuestas del servidor, se observa que la respuesta correspondiente al número 16 contiene información diferente

<img width="1312" height="620" alt="Pasted image 20260304125138" src="https://github.com/user-attachments/assets/a12a21d2-d438-431c-9019-fb2d7e4a2ec3" />

## 📦 Descarga de archivo protegido

Se desactiva el proxy y se vuelve a cargar la página

Al seleccionar el número 16, se descarga un archivo: ```password.zip```

<img width="817" height="551" alt="Pasted image 20260304125424" src="https://github.com/user-attachments/assets/7fe6985f-37c9-4c35-97ef-7971f7f176bc" />

Al intentar extraerlo, el archivo solicita una contraseña

<img width="717" height="440" alt="Pasted image 20260304125909" src="https://github.com/user-attachments/assets/16231448-5d41-4128-9174-af7a5f1b6413" />

## 🔑 Crackeo de contraseña

Se procede a extraer el hash del archivo ZIP y utilizar **John the Ripper** para crackear la contraseña

<img width="1382" height="58" alt="Pasted image 20260304130425" src="https://github.com/user-attachments/assets/5e58e0a7-c4ce-4fdd-aa46-a200453aee1b" />

Una vez obtenida la contraseña, se extrae el contenido del archivo

<img width="872" height="171" alt="Pasted image 20260304130700" src="https://github.com/user-attachments/assets/6833f585-19b5-4038-a896-97ea2b2eda78" />

<img width="711" height="386" alt="Pasted image 20260304131058" src="https://github.com/user-attachments/assets/c36a5856-b66a-45cb-81ae-a7895481fc1b" />

El archivo contiene un diccionario de contraseñas

<img width="447" height="747" alt="Pasted image 20260304131016" src="https://github.com/user-attachments/assets/2c32bec0-c35f-46dd-ae0a-59f68197beba" />

## 🔓 Acceso por SSH

Se utiliza el diccionario para realizar un ataque de fuerza bruta al servicio SSH utilizando el usuario previamente identificado: ```grooti```

<img width="697" height="67" alt="Pasted image 20260304131607" src="https://github.com/user-attachments/assets/062b9140-093e-4099-bed7-a0df5fd27373" />

Finalmente se obtienen las credenciales correctas y se logra iniciar sesión por SSH

## 👑 Escalada de privilegios

Una vez dentro del sistema:

Se intenta escalar privilegios usando **sudo**, sin éxito

<img width="547" height="151" alt="Pasted image 20260304131814" src="https://github.com/user-attachments/assets/4cdf0c63-324a-4897-a4a1-015f74ee8bee" />

Se buscan binarios con permisos **SUID**, sin resultados relevantes

<img width="670" height="253" alt="Pasted image 20260304132027" src="https://github.com/user-attachments/assets/92526d49-7453-45e3-9f16-edc507067bbc" />

Durante la enumeración manual se revisa el directorio: ```/opt```

En este directorio se encuentra un script que ejecuta un archivo **.sh** ubicado en ```/tmp```

<img width="587" height="208" alt="Pasted image 20260304132628" src="https://github.com/user-attachments/assets/49c06a49-6c43-41c4-9ac4-f674d2c7131b" />

El directorio ```/tmp``` tiene permisos de escritura, por lo que es posible crear o modificar el script ejecutado

<img width="641" height="118" alt="Pasted image 20260304134516" src="https://github.com/user-attachments/assets/25d41b51-fc67-4dc3-9a9a-b2a6798475c1" />

Se crea un script que permite ejecutar:

<img width="990" height="116" alt="Pasted image 20260304134934" src="https://github.com/user-attachments/assets/e148c96d-2e05-46c6-8f62-b7cc40186ce4" />

Cuando el script es ejecutado con **bash -p**, se obtiene acceso comp **root**

<img width="377" height="100" alt="Pasted image 20260304135026" src="https://github.com/user-attachments/assets/e4c9d9d1-f873-4aa6-89ac-556e91b2d2eb" />

<img width="582" height="568" alt="Pasted image 20260304135251" src="https://github.com/user-attachments/assets/51900eff-6edf-41bc-ab51-c0765562f637" />

## 🏁 Conclusión

Este laboratorio evidencia que la combinación de información expuesta, acceso a bases de datos, automatización con Burp Suite y el aprovechamiento de scripts inseguros con privilegios elevados permite el compromiso total de un sistema, destacando que una enumeración precisa en cada fase es fundamental para alcanzar el acceso como usuario root.
