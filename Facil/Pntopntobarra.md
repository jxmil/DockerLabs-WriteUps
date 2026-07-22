# Pntopntobarra

## 🔎 Enumeración

Se realiza un escaneo de puertos utilizando Nmap, identificando inicialmente los siguientes servicios expuestos:

* 22/tcp – SSH
* 80/tcp – HTTP

<img width="657" height="407" alt="Pasted image 20260514170754" src="https://github.com/user-attachments/assets/d1149bc2-c4a3-46d5-9d7f-76033f94140c" />

## 🌐 Análisis de la aplicación web

Al acceder a la página web, se encuentra una sección relacionada con computadoras infectadas

<img width="727" height="453" alt="Pasted image 20260514171953" src="https://github.com/user-attachments/assets/7862f416-9e61-4a76-b854-17988937424f" />

Al intentar acceder a dicha funcionalidad, se genera un error. Sin embargo, la estructura de la URL resulta interesante y se decide investigar la posibilidad de explotar una vulnerabilidad de ```LFI (Local File Inclusion)```

<img width="1297" height="520" alt="Pasted image 20260514172032" src="https://github.com/user-attachments/assets/f205a79e-7653-4fc1-b08c-2cd72f636183" />

Durante este proceso se logra identificar información relacionada con el usuario **Nico**

Tras realizar las pruebas correspondientes, se confirma que es posible leer archivos locales del sistema

<img width="1302" height="631" alt="Pasted image 20260514173151" src="https://github.com/user-attachments/assets/1102560e-7ee6-4030-96be-7aef8a5d2e68" />

## 🔑 Obtención de acceso inicial

Una vez confirmada la vulnerabilidad **LFI**, se continúa con la enumeración de archivos sensibles y se consigue localizar la clave privada SSH del usuario

Se guarda la clave localmente y se configuran los permisos adecuados para poder utilizarla de forma segura
```
chmod 600 id-rsa
```

<img width="778" height="382" alt="Pasted image 20260514174044" src="https://github.com/user-attachments/assets/ef8139f5-e087-469b-8ad6-2b37cfa44380" />

Posteriormente, se establece una conexión mediante SSH, obteniendo acceso al sistema como el usuario ```Nico```

## 👑 Escalada de privilegios

Una vez dentro del sistema, se inicia la enumeración local en busca de posibles vectores de escalada de privilegios

Durante este proceso se identifica un binario que puede ser ejecutado con permisos elevados como **root**

<img width="622" height="332" alt="Pasted image 20260514174159" src="https://github.com/user-attachments/assets/8cb35bae-159e-4909-92c1-6dd3aad083bd" />

Se consulta [GTFOBins](https://gtfobins.org/) para comprobar las posibilidades de abuso del binario 

<img width="902" height="378" alt="Pasted image 20260514174259" src="https://github.com/user-attachments/assets/72ca857e-64f1-47fa-8648-a29a408b3643" />

Tras ejecutar la técnica correspondiente, se consigue elevar privilegios y obtener acceso como **root**

<img width="602" height="128" alt="Pasted image 20260514174325" src="https://github.com/user-attachments/assets/1a5672e4-83e1-4be1-8d9f-e69cb72d7661" />

## 🏁 Conclusión

Este laboratorio demuestra cómo una vulnerabilidad de LFI puede permitir la lectura de archivos sensibles y comprometer información crítica del sistema, como claves privadas SSH. Además, destaca la importancia de revisar cuidadosamente los permisos y configuraciones de los binarios que pueden ejecutarse con privilegios elevados, ya que una configuración insegura puede llevar a una escalada de privilegios completa.
