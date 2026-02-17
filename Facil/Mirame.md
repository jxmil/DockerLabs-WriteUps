# Mirame

## 🔎 Enumeración

Se realiza un escaneo de puertos utilizando **Nmap**, donde se identifican los siguientes servicios abiertos:

* 22/tcp – SSH
* 80/tcp – HTTP

<img width="840" height="306" alt="Pasted image 20260106102940" src="https://github.com/user-attachments/assets/d8830ac9-5316-49c0-b10b-24c6862558a9" />

## 🌐 Análisis Web

Nos topamos con un login

<img width="820" height="728" alt="Pasted image 20260106103203" src="https://github.com/user-attachments/assets/cbed8943-4783-4b1c-b8f8-f7746178a3a6" />

Se detecta que es vulnerable a Inyección SQL (SQLi).

<img width="453" height="361" alt="Pasted image 20260106103343" src="https://github.com/user-attachments/assets/785e7625-d5a4-4fe0-9e8e-a1abad403624" />

La página funciona como un sistema para consultar el clima en distintas ciudades

<img width="825" height="696" alt="Pasted image 20260106103503" src="https://github.com/user-attachments/assets/1824eeea-f139-43c6-bfc3-f30508b40975" />

Se realiza fuzzing web y se descubre el recurso ```/auth.php```

<img width="970" height="467" alt="Pasted image 20260106104048" src="https://github.com/user-attachments/assets/12b46edd-46a1-4263-8498-95c9c25b849f" />

Me dirigo al recurso enontrado, pero nada relevante

<img width="1482" height="666" alt="Pasted image 20260106103957" src="https://github.com/user-attachments/assets/21eb3b45-af4d-4a57-8edd-f4307355c545" />

Posteriormente se analiza ```page.php```, probando distintos payloads de **XSS** y **SSTI**, pero sin resultados positivos

<img width="398" height="288" alt="Pasted image 20260106105914" src="https://github.com/user-attachments/assets/aa687cc4-f0b6-49f3-8900-8be88083eac0" />

## 💉 Explotación de SQLi

Al confirmarse la vulnerabilidad **SQLi**, se automatiza la explotación utilizando ```sqlmap```

```bash
sqlmap -u "http://172.17.0.2" --forms --batch --dbs
```

<img width="966" height="683" alt="Pasted image 20260106110642" src="https://github.com/user-attachments/assets/5a9d9a3a-4502-4423-89d6-067113517e7a" />

Se enumeran bases de datos y tablas, logrando extraer información de la tabla users, incluyendo usuarios y contraseñas

```bash
sqlmap -u "http://172.17.0.2" --forms --batch -D users --tables
```

<img width="877" height="135" alt="Pasted image 20260106111130" src="https://github.com/user-attachments/assets/8fbfe488-fc69-4503-b982-4857d6ff4896" />

```bash
sqlmap -u "http://172.17.0.2" --forms --batch -D users -T usuarios --dump
```

<img width="510" height="207" alt="Pasted image 20260106111445" src="https://github.com/user-attachments/assets/ad8aee7d-90c1-4530-a547-0cd22ae2b8f5" />

Las credenciales obtenidas se almacenan en un archivo **.txt** para intentar acceso por **SSH**, aunque inicialmente no se consigue acceso

<img width="1413" height="498" alt="Pasted image 20260106112409" src="https://github.com/user-attachments/assets/fcd55d7d-e149-444f-8693-07833f47296b" />

## 🕵️ Esteganografía

Revisando nuevamente los datos extraídos, se identifica que ```directoriotravieso``` corresponde a un directorio real dentro de la aplicación web

<img width="717" height="293" alt="Pasted image 20260106112547" src="https://github.com/user-attachments/assets/5a84891a-1646-40ae-89f7-b95e6efe7add" />

Se accede al recurso y se descarga una imagen

<img width="840" height="673" alt="Pasted image 20260106112624" src="https://github.com/user-attachments/assets/be616e43-1c41-43b6-9b86-e0ce5dc58030" />

Usamos la primera herramienta, sin tener éxito 

<img width="736" height="465" alt="Pasted image 20260106112923" src="https://github.com/user-attachments/assets/d8d54ef1-4234-4a36-bb5b-c5fc849753db" />

Sigo buscando algún mensaje oculto en la imagen 

<img width="618" height="57" alt="Pasted image 20260106113241" src="https://github.com/user-attachments/assets/9dae2fb3-1482-43d4-a6d5-9d0a481bd427" />

Intentamos crackear

<img width="608" height="130" alt="Pasted image 20260106113924" src="https://github.com/user-attachments/assets/49488d08-3551-49bb-851f-485c38946a98" />

Nos encontró un archivo **zip**, pero al momento de extraerlo nos pide una contraseña, asi que procederé a crackearla con **john**

<img width="1468" height="45" alt="Pasted image 20260106114651" src="https://github.com/user-attachments/assets/bf99193f-916b-46cb-a0c6-c19910f78fde" />

Con la contraseña obtenida, se extraen los archivos contenidos en el **ZIP**

<img width="977" height="231" alt="Pasted image 20260106114922" src="https://github.com/user-attachments/assets/e25d1a09-e16b-4bad-bd2e-09067fb26bb8" />

<img width="471" height="246" alt="Pasted image 20260106115242" src="https://github.com/user-attachments/assets/9dfa2ecb-d3cc-496b-b6fe-f3e1b347dec1" />

## 🔐 Acceso Inicial

En el archivo extraído se encuentra credenciales válidas

Se inicia sesión por *SSH* con éxito

<img width="818" height="258" alt="Pasted image 20260106115351" src="https://github.com/user-attachments/assets/70be4324-19f8-40ac-8da1-32fc78e78309" />

## 🔐 Escalada de Privilegios

Dentro del sistema, se intenta escalar privilegios, sin éxito

<img width="530" height="62" alt="Pasted image 20260106115456" src="https://github.com/user-attachments/assets/a0448e5a-a2c0-4c2f-8bfd-17b8217e84cb" />

Tras enumerar binarios con permisos especiales, llama la atención el binario **find**

<img width="618" height="301" alt="Pasted image 20260106115607" src="https://github.com/user-attachments/assets/ef46e5f7-ac22-4241-81be-ba46345282be" />

Se consulta [GTFOBins](https://gtfobins.org/) 

<img width="842" height="386" alt="image" src="https://github.com/user-attachments/assets/54be3265-2c2b-4b75-90cc-afb4d449a670" />

Ejecuto y somos **root**

<img width="628" height="93" alt="Pasted image 20260106120230" src="https://github.com/user-attachments/assets/40e40357-d34d-4b38-bd72-9bdc5a279446" />

## 🏁 Conclusión 

Este laboratorio demuestra cómo una vulnerabilidad inicial de SQLi, combinada con análisis detallado, esteganografía y una correcta enumeración interna, puede derivar en el compromiso completo del sistema.
