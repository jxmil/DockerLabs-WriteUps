# Analyst

## 🔎 Enumeración

Se realiza un escaneo de puertos utilizando Nmap, identificando los siguientes servicios:

* 22/tcp – SSH
* 80/tcp – HTTP

<img width="657" height="202" alt="Pasted image 20261030121002" src="https://github.com/user-attachments/assets/9438f21e-e833-43d2-a7f3-c5bddf4cd0aa" />

Se accede a la aplicación web y se encuentra un escenario de análisis de un incidente de seguridad

<img width="932" height="560" alt="Pasted image 20261030121058" src="https://github.com/user-attachments/assets/532b9df7-e888-4d55-811a-8d1a65834816" />

## 🕵️ Análisis del incidente

La aplicación proporciona dos archivos necesarios para resolver el laboratorio:

* Un archivo de captura de tráfico de red para analizar mediante **Wireshark**
* Un archivo **.json** que contiene un feed de **Threat Intelligence**

<img width="967" height="407" alt="Pasted image 20261030121832" src="https://github.com/user-attachments/assets/b6800783-f03d-4e39-9492-b953c5b182bb" />

Una vez descargados ambos archivos, se inicia el análisis del incidente para responder las preguntas planteadas y reconstruir las acciones realizadas por el atacante

## 🌐 Identificación de la IP del atacante

Como primer paso, se busca identificar la dirección IP real del atacante

Para ello, se utiliza el filtro de Wireshark:

```bash
http.request.method == "POST"
```

Mediante la inspección de las peticiones HTTP POST se analiza el tráfico y se identifica la dirección IP correspondiente al atacante

<img width="852" height="197" alt="Pasted image 20261030122156" src="https://github.com/user-attachments/assets/5e22c99c-2823-4969-af3b-7109b2fc2d55" />

<img width="927" height="366" alt="Pasted image 20261030122220" src="https://github.com/user-attachments/assets/18d3f365-a712-4889-9431-5740e49edd8e" />

Posteriormente, se utiliza el archivo **JSON** de **Threat Intelligence** para consultar información relacionada con dicha dirección IP, incluyendo su país de procedencia siendo **Vietnam**

<img width="955" height="197" alt="Pasted image 20261030122442" src="https://github.com/user-attachments/assets/85cba324-844f-4cb9-aadc-cac840943352" />

## 📡 Análisis de las peticiones HTTP

Se continúa con la investigación respondiendo las preguntas del laboratorio mediante diferentes filtros y herramientas de análisis de **Wireshark**

Durante esta fase se realizan las siguientes acciones:

Identificación del **User-Agent**: se selecciona un paquete sospechoso y se utiliza la opción Follow TCP Stream para inspeccionar la comunicación y obtener información sobre el navegador o herramienta utilizada por el atacante

<img width="1185" height="362" alt="Pasted image 20261030122844" src="https://github.com/user-attachments/assets/27ff2238-cdad-425f-a0eb-6df9311fd9bd" />

<img width="937" height="187" alt="Pasted image 20261030122939" src="https://github.com/user-attachments/assets/1d762a56-4b47-45c6-818f-5eeb4493fbd1" />

Análisis de peticiones **POST**: se vuelve a filtrar el tráfico HTTP POST para identificar nuevas solicitudes y reconstruir las acciones realizadas

<img width="1232" height="145" alt="Pasted image 20261030123207" src="https://github.com/user-attachments/assets/ae81ca69-5f10-4105-8844-a97bbc1b69f5" />

<img width="941" height="185" alt="Pasted image 20261030123235" src="https://github.com/user-attachments/assets/b364d0f1-e249-4202-a8f9-5b1a569d499c" />

Análisis de peticiones **GET**: se utiliza el método GET como filtro para identificar los recursos solicitados por el atacante

<img width="1036" height="247" alt="Pasted image 20261030123633" src="https://github.com/user-attachments/assets/0e524511-391a-4b6a-8242-7788338627c2" />

<img width="937" height="202" alt="Pasted image 20261030123645" src="https://github.com/user-attachments/assets/d8c3726b-af74-42e2-a42b-85d253176ff7" />

Identificación de archivos transferidos: se inspeccionan los flujos TCP para determinar el nombre del archivo que logró superar los filtros de seguridad de la aplicación

<img width="722" height="456" alt="Pasted image 20261030124308" src="https://github.com/user-attachments/assets/39b475ec-8d34-4ed2-871a-a05dd1474666" />

<img width="941" height="207" alt="Pasted image 20261030124330" src="https://github.com/user-attachments/assets/7045621d-de04-46ea-89c4-8195eb1e97a5" />

## 🔍 Reconstrucción de las acciones del atacante

Una vez identificados los elementos anteriores, se continúa analizando las comunicaciones entre la dirección IP del atacante y la del servidor

<img width="1240" height="317" alt="Pasted image 20261030124817" src="https://github.com/user-attachments/assets/55660adc-e7c8-4a2d-bc97-f57e5800d211" />

<img width="965" height="187" alt="Pasted image 20261030124833" src="https://github.com/user-attachments/assets/5ce49961-f2f7-4344-b8cd-c3786fc9589a" />

Se aplican filtros específicos para aislar el tráfico entre ambos equipos y facilitar la investigación

Mediante el seguimiento de los flujos TCP se consigue reconstruir nuevas acciones realizadas por el atacante, entre ellas:

La lectura de un archivo del sistema mediante el comando ```cat```

<img width="1322" height="632" alt="Pasted image 20261030125018" src="https://github.com/user-attachments/assets/f255cc51-145b-468c-bf63-6cb9cf512756" />

<img width="970" height="202" alt="Pasted image 20261030125031" src="https://github.com/user-attachments/assets/86d22aff-41ed-4294-9bf6-2b3c4db998d1" />

La identificación del usuario cuya contraseña intentó restablecer el atacante

<img width="607" height="557" alt="Pasted image 20261030125220" src="https://github.com/user-attachments/assets/937ba5ef-3a96-466f-b24b-7c1453573d86" />

Con esta información se completan las últimas preguntas del laboratorio y se consigue reconstruir gran parte de la actividad maliciosa observada en la captura

<img width="935" height="201" alt="Pasted image 20261030125232" src="https://github.com/user-attachments/assets/a5f1c269-a866-4bdb-9b80-15570081cf9b" />

## 🔑 Acceso inicial mediante SSH

Una vez completado el análisis forense, el laboratorio proporciona las credenciales necesarias para acceder al servidor

<img width="912" height="397" alt="Pasted image 20261030125416" src="https://github.com/user-attachments/assets/e42399e5-6455-4138-8a63-ed139004a2d7" />

Se establece una conexión mediante SSH, logrando acceder correctamente al sistema

## 👑 Escalada de privilegios

Una vez dentro del servidor, se realiza una enumeración de los privilegios disponibles para el usuario actual

Durante esta fase se descubre que es posible ejecutar comandos como root sin necesidad de proporcionar una contraseña

<img width="1172" height="197" alt="Pasted image 20261030125737" src="https://github.com/user-attachments/assets/5e1059f5-3d3e-47c6-9ecb-ea8f56a85b1b" />

Esta configuración permite ejecutar comandos con privilegios elevados y obtener acceso como **root**, completando así el laboratorio

🏁 Conclusión

Este laboratorio demuestra la importancia del análisis forense de tráfico de red para investigar incidentes de seguridad y reconstruir las actividades realizadas por un atacante.

Mediante Wireshark fue posible analizar peticiones HTTP, identificar direcciones IP, inspeccionar flujos TCP, descubrir archivos transferidos y recuperar información sobre las acciones ejecutadas en el servidor.

Además, el uso de fuentes de Threat Intelligence permitió complementar el análisis con información sobre la infraestructura del atacante.

Finalmente, se evidencia cómo una configuración inadecuada de permisos puede facilitar la escalada de privilegios y comprometer completamente un sistema.
