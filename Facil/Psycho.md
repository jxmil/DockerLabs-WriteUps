# Psycho

## 🔎 Enumeración

Se realiza un escaneo de puertos utilizando *Nmap*, donde se identifican los siguientes servicios abiertos:
* 22/tcp – SSH
* 80/tcp – HTTP

<img width="858" height="311" alt="Pasted image 20251217173414" src="https://github.com/user-attachments/assets/68422d0e-96a3-45a9-89e2-d514b7e013fb" />

## 🌐 Análisis del servicio web

Al acceder a la página web, no se observa información relevante a simple vista

<img width="1237" height="457" alt="Pasted image 20251217173808" src="https://github.com/user-attachments/assets/04180549-d1a4-4851-b9c3-9e91cafacfe4" />

Por ello, se procede a realizar una búsqueda de directorios, sin obtener resultados útiles.

<img width="751" height="492" alt="Pasted image 20251217182715" src="https://github.com/user-attachments/assets/ff61ea05-6750-405c-a66a-b5955d4c4d8f" />

Posteriormente, se realiza fuzzing de parámetros utilizando la herramienta **ffuf**, lo que permite identificar un parámetro vulnerable capaz de interactuar con rutas del sistema.

<img width="952" height="518" alt="Pasted image 20251217190217" src="https://github.com/user-attachments/assets/154a1dda-bac8-47ce-88e9-9800815892a4" />

Al probar con la ruta:
```
index.php?secret=/etc/passwd
```

Se logra visualizar su contenido, identificando los siguientes usuarios del sistema:

* ubuntu
* vaxei
* luisillo

<img width="735" height="585" alt="Pasted image 20251217191348" src="https://github.com/user-attachments/assets/d71a2b1d-6fce-46c2-946a-b3cdf9a04f09" />

Esto confirma la existencia de una vulnerabilidad de **Local File Inclusion** ```(LFI)```.

```
LFI:La inclusión local de archivos es una vulnerabilidad que permite a los atacantes leer, incluir o
ejecutar archivos en el servidor de aplicaciones. Esto podría incluir archivos de sistema, código fuente de la aplicación,
 archivos de configuración y otros datos confidenciales
```

## 🔐 Obtención de acceso por SSH

Aprovechando la **LFI**, se procede a extraer archivos sensibles, logrando obtener la clave privada SSH del usuario *vaxei*.

<img width="831" height="618" alt="Pasted image 20251217193428" src="https://github.com/user-attachments/assets/6053620f-eb4e-4981-a519-a52673b98913" />

La clave es almacenada en un archivo local, asegurándose de copiarla correctamente y eliminar espacios innecesarios

<img width="693" height="640" alt="Pasted image 20251217194035" src="https://github.com/user-attachments/assets/928c7857-cb2a-4639-b930-2b21646fb2d3" />

Luego, se ajustan los permisos del archivo:

```
chmod 600 $ARCHIVO
```
<img width="476" height="85" alt="Pasted image 20251217194100" src="https://github.com/user-attachments/assets/73f81963-aa30-435d-91fa-e89039d8882e" />

Este comando establece permisos estrictos, permitiendo que solo el propietario pueda leer y escribir la clave, requisito indispensable para el uso de claves SSH.

Con esto, se logra iniciar sesión por SSH como el usuario vaxei.

<img width="808" height="253" alt="Pasted image 20251217194235" src="https://github.com/user-attachments/assets/8d211e06-df89-4ccd-ada7-084e516a60b1" />

## Movimiento lateral y escalada inicial

Una vez dentro del sistema, se revisan los permisos sudo y se identifica que el usuario **luisillo** puede ejecutar el binario *perl*.

<img width="942" height="170" alt="Pasted image 20251217195146" src="https://github.com/user-attachments/assets/689d6cf3-a01f-4a6f-a285-e8bfd44100f7" />

Aprovechando esta configuración, se realiza una escalada de privilegios para cambiar al usuario **luisillo**.

<img width="842" height="60" alt="Pasted image 20251217195302" src="https://github.com/user-attachments/assets/a7113a8e-1c99-40fd-a611-1b0e168eb494" />

## 🔐🐍 Escalada de privilegios 

Al continuar con la enumeración, se detecta un script en Python ubicado en el directorio **/opt**, el cual se ejecuta con privilegios de root.

<img width="952" height="168" alt="Pasted image 20251217200336" src="https://github.com/user-attachments/assets/2ea2f5b8-88a9-4e35-9dad-824e9b7b6894" />

Al analizar el código, se observa que utiliza la librería subprocess y que el script se ejecuta directamente desde el directorio **/opt**.

<img width="720" height="572" alt="Pasted image 20251217200509" src="https://github.com/user-attachments/assets/b10e6512-d472-45c5-a691-bb9a5247f17e" />

Dado que este directorio cuenta con permisos de escritura, es posible crear un archivo malicioso llamado: *subprocess.py*

<img width="492" height="96" alt="Pasted image 20251217200831" src="https://github.com/user-attachments/assets/eb459c86-75d0-4189-a0c7-e12ec7fe7bb4" />

Cuando el script se ejecuta, Python buscará primero las librerías en el directorio actual, cargando el archivo malicioso en lugar de la librería legítima.
Esta técnica se conoce como Python Library Hijacking y permite ejecutar código arbitrario con privilegios elevados.

<img width="866" height="93" alt="Pasted image 20251217201018" src="https://github.com/user-attachments/assets/f513b852-6468-450f-861d-18d54c678f14" />

Tras crear el módulo malicioso y ejecutar el script, se obtiene acceso como root.

<img width="557" height="88" alt="Pasted image 20251217201430" src="https://github.com/user-attachments/assets/70914e43-0602-4d91-a4c0-e810c46e234e" />

## 🏁 Conclusión

Este laboratorio demuestra cómo una vulnerabilidad de lectura de archivos locales, combinada con una mala gestión de permisos y scripts inseguros, puede derivar en una escalada completa de privilegios.
