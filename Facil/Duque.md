# Duque

## 🔎 Enumeración

Se realiza un escaneo de puertos utilizando la herramienta Nmap, identificando los siguientes servicios expuestos:

* 22/tcp – SSH
* 80/tcp – HTTP

<img width="642" height="412" alt="Pasted image 20260813111057" src="https://github.com/user-attachments/assets/a606616c-caaa-481e-92df-865c68a2d784" />

## 🌐 Análisis de la aplicación web

Al acceder a la aplicación web, no se encuentra información relevante a simple vista. Durante la navegación, se identifican algunos directorios a los que no tenemos acceso

<img width="1236" height="533" alt="Pasted image 20260813111155" src="https://github.com/user-attachments/assets/ce6cf50f-9aaf-481f-90e4-d712cf971599" />

<img width="642" height="490" alt="Pasted image 20260813112104" src="https://github.com/user-attachments/assets/8452e0a4-31f1-43f6-8061-87d58ce3e024" />

Por ello, se realiza fuzzing web utilizando herramientas de enumeración de directorios, logrando descubrir el directorio ```/bills```, que contiene un formulario de inicio de sesión

<img width="693" height="252" alt="Pasted image 20260813112148" src="https://github.com/user-attachments/assets/badc5064-bbc9-49e0-9eb8-44af0d06e6ab" />

<img width="425" height="518" alt="Pasted image 20260813112245" src="https://github.com/user-attachments/assets/9bf0a691-71ea-4963-b133-e578c8c26916" />

## 💉 Explotación de SQL Injection

Al interactuar con el formulario de login, se prueba una posible **inyección SQL**, logrando acceder a la aplicación. Sin embargo, aparece un mensaje indicando que no contamos con los privilegios necesarios y se revela el nombre de usuario ```mario```

<img width="633" height="527" alt="Pasted image 20260813112632" src="https://github.com/user-attachments/assets/d2de1dee-44ca-4420-8e5c-719bf82d689f" />

A partir de este hallazgo, se utiliza SQLmap para enumerar las bases de datos disponibles:

```bash
sqlmap -u "http://$target/bills" --forms --dbs --batch
```

<img width="277" height="121" alt="Pasted image 20260813121634" src="https://github.com/user-attachments/assets/3fe8d321-60cb-4f8e-9c81-bc0290ada4b9" />

Entre las bases de datos encontradas, llama la atención register, por lo que se procede a enumerar sus tablas:

```bash
sqlmap -u "http://$target/bills" -D register --tables --batch
```

<img width="272" height="106" alt="Pasted image 20260813122830" src="https://github.com/user-attachments/assets/b7fcf64c-94d8-41d7-b6a1-f11b12d5ec40" />

Se identifica la tabla users, cuyo contenido se extrae mediante el siguiente comando:

```bash
sqlmap -u "http://$target/bills" -D register -T users --dump --batch
```

Teniendo las credenciales ```admin:admin123```

## 🔑 Obtención de credenciales

Al probar las credenciales recuperadas, se consigue acceder al panel utilizando el usuario ```admin```

<img width="582" height="475" alt="Pasted image 20260813125054" src="https://github.com/user-attachments/assets/eed64caf-7f4a-4973-9622-5a45d76418e7" />

Al revisar la sección de facturas, se observa que estas se identifican mediante un parámetro id en la URL

Para encontrar una factura válida, se utiliza Crunch para generar una lista de posibles identificadores y **FFUF** para realizar fuzzing sobre el parámetro.

```bash
crunch 4 4 -t @% | ffuf -c -w - -b "PHPSESSID=<sesion>" -u "http://$target/bills/panel.php?id=xyFUZZ"
```

<img width="967" height="80" alt="Pasted image 20260813125708" src="https://github.com/user-attachments/assets/624a7905-1b5c-416b-94d2-3ab78ffe8966" />

Tras analizar las respuestas y filtrar por tamaño, se identifica una factura válida. Al introducir su identificador ```xyc724``` en el buscador de la aplicación, se obtiene nueva información que permite recuperar otras credenciales

<img width="508" height="327" alt="Pasted image 20260813125944" src="https://github.com/user-attachments/assets/04e8099a-0601-4891-9a82-c6d66d28b0c6" />

## 🐧 Acceso inicial por SSH

Con las credenciales obtenidas, se procede a iniciar sesión en el servidor mediante SSH, consiguiendo acceso al sistema

## 🔐 Escalada de privilegios

Una vez dentro del sistema, se realiza una enumeración de los archivos binarios que cuentan con permisos **SUID**

<img width="587" height="248" alt="Pasted image 20260813131053" src="https://github.com/user-attachments/assets/1fe5b48a-046b-4c9c-a4d5-d8dd173457aa" />

Durante este proceso, se identifica un binario que llama la atención debido a sus permisos

Para investigar una posible forma de aprovecharlo, se consulta [GTFOBins](https://gtfobins.org/), donde se encuentra una técnica que permite ejecutar comandos con privilegios elevados

<img width="900" height="495" alt="Pasted image 20260813131135" src="https://github.com/user-attachments/assets/635cb798-932e-4997-ba4d-7804d4812e69" />

Al aplicar el método identificado, se consigue escalar privilegios y obtener acceso como **root**

<img width="508" height="66" alt="Pasted image 20260813131206" src="https://github.com/user-attachments/assets/c2328da4-a929-4838-adad-174904d5fc69" />

## 🏁 Conclusiones

Este laboratorio demuestra cómo una vulnerabilidad de SQL Injection puede permitir la extracción de información sensible de una base de datos. Además, mediante técnicas de fuzzing, es posible descubrir recursos y credenciales que facilitan el acceso inicial al sistema.
Finalmente, una incorrecta configuración de permisos SUID permite aprovechar un binario para escalar privilegios hasta obtener el control completo de la máquina.
