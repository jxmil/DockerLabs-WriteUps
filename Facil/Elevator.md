# Elevator

## 🔎 Enumeración

Se realiza un escaneo de puertos utilizando Nmap, identificando el siguiente servicio:

* 80/tcp – HTTP

<img width="695" height="146" alt="Pasted image 20260729172747" src="https://github.com/user-attachments/assets/c8becb4a-14f6-4421-9c79-46e5ebc3f4e6" />

Se accede a la aplicación web y se comienza con la enumeración de sus funcionalidades

<img width="847" height="542" alt="Pasted image 20260729173158" src="https://github.com/user-attachments/assets/4e29f208-9310-4f46-bca7-dd1491a3cfe8" />

<img width="672" height="356" alt="Pasted image 20260729173217" src="https://github.com/user-attachments/assets/aa033ad9-e763-4dc2-91a7-4a42841e02f0" />

## 🌐 Fuzzing web

Se realiza un proceso de fuzzing de directorios para identificar recursos adicionales

<img width="817" height="82" alt="Pasted image 20260729174305" src="https://github.com/user-attachments/assets/e56ba284-b151-4fc5-a54d-01eba718af53" />

Durante esta enumeración se encuentra un directorio que resulta interesante. Aunque inicialmente no se dispone de acceso directo, la respuesta **HTTP 301** indica que existe una redirección hacia dicho recurso

<img width="767" height="222" alt="Pasted image 20260729174507" src="https://github.com/user-attachments/assets/dee737ed-ab1f-4d9d-9b86-e790c768d393" />

A partir de este descubrimiento se realiza un nuevo proceso de fuzzing sobre el directorio encontrado

<img width="500" height="67" alt="Pasted image 20260729174632" src="https://github.com/user-attachments/assets/a70cf02e-1acf-441d-ac6a-8f0ea8debbe2" />

Durante esta segunda enumeración aparecen diferentes recursos, destacando especialmente: ```archivo.html```

<img width="632" height="357" alt="Pasted image 20260729175325" src="https://github.com/user-attachments/assets/fd629b48-ce0d-44fd-9e72-95687878a30e" />

Al acceder a este recurso se encuentra una funcionalidad para subir archivos

## 📤 Explotación de la subida de archivos

La aplicación únicamente permite cargar archivos con extensión ```.jpg```

Se investiga una posible forma de evadir esta restricción mediante un bypass de doble extensión

Para ello se prepara una reverse shell y se guarda utilizando el nombre: ```revshell.php.jpg```

<img width="797" height="432" alt="Pasted image 20260729175826" src="https://github.com/user-attachments/assets/742ca580-8fab-412e-9f3b-1a4ce3607f45" />

El archivo es aceptado correctamente por la aplicación y posteriormente se procede a localizar el directorio donde se almacenan los archivos subidos

<img width="665" height="105" alt="Pasted image 20260729180011" src="https://github.com/user-attachments/assets/917765ab-c309-45c7-a3be-cd7f6699347f" />

Una vez identificado el directorio ```uploads```, se prepara un listener mediante Netcat en el puerto **4444**

<img width="572" height="230" alt="Pasted image 20260729180157" src="https://github.com/user-attachments/assets/643bcf7a-427d-465e-8e16-92eb03e03552" />

Al ejecutar el archivo cargado, se recibe la conexión y se obtiene acceso inicial al sistema

<img width="262" height="232" alt="Pasted image 20260729180217" src="https://github.com/user-attachments/assets/7effd480-df07-420a-8951-f9fa1fab729d" />

Hacemos tratamiento de TTY

<img width="240" height="140" alt="Pasted image 20260729180403" src="https://github.com/user-attachments/assets/e74e9042-f06c-4602-bbeb-5542a0cbdd93" />

## 👤 Escalada a Daphne

Una vez dentro del sistema, se inicia la enumeración de permisos en busca de posibles vectores de escalada

<img width="857" height="152" alt="Pasted image 20260729180953" src="https://github.com/user-attachments/assets/597bf829-9d1f-42f8-99ac-59b743411b94" />

Se identifica un binario que puede ejecutarse como el usuario ```daphne```

Se consulta [GTFOBins](https://gtfobins.org/) para determinar la forma adecuada de abusar del binario y se consigue cambiar correctamente al usuario ```daphne```

<img width="886" height="362" alt="Pasted image 20260729181006" src="https://github.com/user-attachments/assets/86361fc9-3187-4516-af7e-0ac0206639ba" />

<img width="701" height="95" alt="Pasted image 20260729181156" src="https://github.com/user-attachments/assets/9a7555a1-e224-41e5-a635-0706b1369b6f" />

## 👤 Escalada a Vilma

Como ```daphne```, se continúa con la enumeración de permisos

<img width="842" height="156" alt="Pasted image 20260729181525" src="https://github.com/user-attachments/assets/b21356b0-ef55-44c8-bd2a-1c15a1fc2f9f" />

Se encuentra nuevamente un binario que permite ejecutar acciones como el usuario ```vilma```

Tras ejecutar el binario correspondiente, se consigue cambiar exitosamente al usuario ```vilma```

<img width="546" height="92" alt="Pasted image 20260729181510" src="https://github.com/user-attachments/assets/4dbda367-2f48-4462-bb73-655bed23e603" />

## 👤 Escalada a Shaggy

Desde el usuario ```vilma```, se continúa revisando los permisos disponibles

<img width="867" height="152" alt="Pasted image 20260729182030" src="https://github.com/user-attachments/assets/01632814-f169-4c13-865a-334bd3ff6ce0" />

Se identifica un nuevo binario que puede ejecutarse como ```shaggy```

Consultando nuevamente [GTFOBins](https://gtfobins.org/), se utiliza la técnica correspondiente y se consigue cambiar al usuario ```shaggy```

<img width="827" height="382" alt="Pasted image 20260729181920" src="https://github.com/user-attachments/assets/33bb64d4-80e8-479b-aa7a-b6605c6b6016" />

## 👤 Escalada a Fred

Como ```shaggy```, se repite el proceso de enumeración

<img width="851" height="147" alt="Pasted image 20260729182349" src="https://github.com/user-attachments/assets/a9ae2554-e640-4844-80b8-0896b27a5d74" />

Se descubre que es posible ejecutar un binario con los privilegios del usuario ```fred```

Tras consultar [GTFOBins](https://gtfobins.org/) y ejecutar el binario, se consigue cambiar correctamente al usuario ```fred```

<img width="842" height="367" alt="Pasted image 20260729182411" src="https://github.com/user-attachments/assets/1871b24c-ad00-4f49-b618-fd9a5285b264" />

<img width="825" height="117" alt="Pasted image 20260729182523" src="https://github.com/user-attachments/assets/8a0e87f5-f672-4054-88ed-b32f11f5f5b2" />

## 👤 Escalada a Scooby

Desde ```fred```, se continúa con la enumeración de privilegios

<img width="897" height="157" alt="Pasted image 20260729182633" src="https://github.com/user-attachments/assets/a5d3ff25-f73b-4e98-ba31-8ae8b0cafda9" />

Nuevamente se identifica un binario que puede ejecutarse como el usuario ```scooby```

Se consulta [GTFOBins](https://gtfobins.org/), se ejecuta la técnica correspondiente y se obtiene acceso como ```scooby```

<img width="901" height="435" alt="Pasted image 20260729182651" src="https://github.com/user-attachments/assets/3ba83dbb-3233-4a43-8a27-9e4a99736e6e" />

<img width="782" height="130" alt="Pasted image 20260729182744" src="https://github.com/user-attachments/assets/ce514f3c-fc99-46e8-8ce4-69abddd61321" />

## 👑 Escalada a Root

Finalmente, como ```scooby```, se realiza una última enumeración de los permisos disponibles

<img width="832" height="147" alt="Pasted image 20260729182920" src="https://github.com/user-attachments/assets/418dea69-bb70-4ff8-b4b0-4f6cb212db59" />

Se identifica un binario que puede ejecutarse con privilegios de **root**

Tras consultar [GTFOBins](https://gtfobins.org/) y ejecutar el binario correspondiente, se consigue cambiar exitosamente al usuario **root**

<img width="875" height="542" alt="Pasted image 20260729183103" src="https://github.com/user-attachments/assets/7ec6a3bf-d2ed-48af-8b15-ff69093b5a22" />

De esta manera se completa la cadena de escaladas y se obtiene control total sobre el sistema

<img width="707" height="86" alt="Pasted image 20260729183038" src="https://github.com/user-attachments/assets/83870e28-dc25-4068-a750-d26464ac2f4a" />

## 🏁 Conclusión

Este laboratorio demuestra la importancia de validar correctamente las funcionalidades de subida de archivos, ya que una restricción basada únicamente en extensiones puede ser insuficiente y permitir la ejecución de archivos maliciosos.

Además, destaca especialmente la importancia de revisar los permisos de ejecución entre usuarios. En este caso, una configuración insegura permitió encadenar múltiples escaladas:

Acceso inicial → Daphne → Vilma → Shaggy → Fred → Scooby → Root

La combinación de una vulnerabilidad web y una mala configuración de privilegios terminó permitiendo el compromiso completo del sistema.
