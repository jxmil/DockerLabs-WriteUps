# Balulero

## 🔎 Enumeración

Se realiza un escaneo de puertos utilizando Nmap, donde se identifican los siguientes servicios abiertos:
* 22/tcp – SSH
* 80/tcp – HTTP

## 🌐 Análisis del servicio web

Se visita la página web, donde se observa información sobre un perro llamado **balu**, lo cual podría indicar un posible nombre de usuario

Posteriormente, se inspecciona el código de la página web y en la consola del navegador se encuentra una referencia a un archivo de respaldo de contraseñas llamado:

```.env_de_baluchingon```

Este archivo contiene credenciales que pueden ser utilizadas para acceder al sistema

## 🔑 Acceso al sistema

Con las credenciales obtenidas, se logra acceder al sistema

Una vez dentro, se intenta realizar escalada de privilegios, encontrando que es posible ejecutar un binario como el usuario **chocolate** sin necesidad de contraseña

Se visita [GTFOBins](https://gtfobins.org/) 

Utilizando la técnica correspondiente, se logra cambiar exitosamente al usuario **chocolate**

## 👑 Escalada de privilegios

Posteriormente se intenta escalar privilegios nuevamente, pero el sistema solicita una contraseña

Se procede a buscar binarios con permisos elevados, pero inicialmente no se encuentra nada relevante

Durante la enumeración manual del sistema, se encuentra un archivo PHP en el directorio: ```/opt```

Sin embargo, no se tienen permisos para ejecutarlo directamente

Se revisan los procesos activos del sistema y se observa que dicho archivo PHP se ejecuta como el usuario **root**

Dado que se tienen permisos de escritura sobre el archivo, se modifica su contenido para incluir un comando malicioso

Finalmente, al ejecutarse el archivo modificado, se utiliza:

```bash
bash -p
```

Con esto se logra obtener acceso como **root**

## 🏁 Conclusión

Este laboratorio demuestra cómo una mala gestión de archivos sensibles expuestos en aplicaciones web, combinada con permisos inseguros en archivos ejecutados por proccesos privilegiados, puede permitir a un atacante escalar privilegios hasta obtener acceso root al sistema
