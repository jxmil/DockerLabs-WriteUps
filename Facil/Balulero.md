# Balulero

## 🔎 Enumeración

Se realiza un escaneo de puertos utilizando Nmap, donde se identifican los siguientes servicios abiertos:
* 22/tcp – SSH
* 80/tcp – HTTP

<img width="932" height="460" alt="Image" src="https://github.com/user-attachments/assets/1b15e26a-8ead-4f0b-8383-172f570365b1" />

## 🌐 Análisis del servicio web

Se visita la página web, donde se observa información sobre un perro llamado **balu**, lo cual podría indicar un posible nombre de usuario

<img width="1447" height="737" alt="Image" src="https://github.com/user-attachments/assets/c6839aef-1d21-40bc-bc8a-e830a50c4385" />

Posteriormente, se inspecciona el código de la página web y en la consola del navegador se encuentra una referencia a un archivo de respaldo de contraseñas llamado:
```.env_de_baluchingon```

<img width="1892" height="132" alt="Image" src="https://github.com/user-attachments/assets/e5314734-376d-4a98-98ab-4a3b4e34150c" />

Este archivo contiene credenciales que pueden ser utilizadas para acceder al sistema

<img width="681" height="132" alt="Image" src="https://github.com/user-attachments/assets/5da938e5-c11e-4cee-81f5-c9f6ab846ca7" />

## 🔑 Acceso al sistema

Una vez dentro, se intenta realizar escalada de privilegios, encontrando que es posible ejecutar un binario como el usuario **chocolate** sin necesidad de contraseña

<img width="1166" height="151" alt="Image" src="https://github.com/user-attachments/assets/d687b755-6eaf-43d8-b525-86f0e12db115" />

Se visita [GTFOBins](https://gtfobins.org/) 

<img width="913" height="456" alt="Image" src="https://github.com/user-attachments/assets/31009ece-fa3a-435c-8513-1141b3a4b315" /> 

Utilizando la técnica correspondiente, se logra cambiar exitosamente al usuario **chocolate**

## 👑 Escalada de privilegios

Posteriormente se intenta escalar privilegios nuevamente, pero el sistema solicita una contraseña

<img width="733" height="96" alt="Image" src="https://github.com/user-attachments/assets/d9130f4a-f7f5-4e94-b7ac-429278a970b9" />

<img width="382" height="60" alt="Image" src="https://github.com/user-attachments/assets/442e25d7-4052-4533-9eb1-998a74430c0a" />

Se procede a buscar binarios con permisos elevados, pero inicialmente no se encuentra nada relevante

<img width="493" height="235" alt="Image" src="https://github.com/user-attachments/assets/81f354f8-50c5-4101-afd4-7052f04cdb09" />

Durante la enumeración manual del sistema, se encuentra un archivo PHP en el directorio: ```/opt```

<img width="647" height="221" alt="Image" src="https://github.com/user-attachments/assets/45a324a3-68ea-4216-a21e-2923568e1d78" />

Sin embargo, no se tienen permisos para ejecutarlo directamente

Se revisan los procesos activos del sistema y se observa que dicho archivo PHP se ejecuta como el usuario **root**

<img width="1886" height="76" alt="Image" src="https://github.com/user-attachments/assets/1874a75a-e0b4-4843-883b-239fba3a63e4" />

Dado que se tienen permisos de escritura sobre el archivo, se modifica su contenido para incluir un comando malicioso

Finalmente, al ejecutarse el archivo modificado, se utiliza:

```bash
bash -p
```

<img width="696" height="97" alt="Image" src="https://github.com/user-attachments/assets/c8f6a8db-4510-43ff-a2af-c68847507298" />

Con esto se logra obtener acceso como **root**

## 🏁 Conclusión

Este laboratorio demuestra cómo una mala gestión de archivos sensibles expuestos en aplicaciones web, combinada con permisos inseguros en archivos ejecutados por proccesos privilegiados, puede permitir a un atacante escalar privilegios hasta obtener acceso root al sistema
