# Paradise

## 🔎 Enumeración inicial

Se realiza un escaneo de puertos utilizando *Nmap*, identificando los siguientes servicios abiertos:

* 22/tcp – SSH
* 80/tcp – HTTP
* 445/tcp – SMB

<img width="832" height="906" alt="Pasted image 20260204120414" src="https://github.com/user-attachments/assets/2067ef43-edf9-42ad-a6ea-afeb1cacae3f" />

## 🌐 Análisis del servicio web

Al acceder a la página web, se identifica un posible nombre de usuario expuesto: ```Andy```

<img width="922" height="707" alt="Pasted image 20260204120600" src="https://github.com/user-attachments/assets/ce283e7f-dec6-413a-bb40-00e22005fec9" />

Posteriormente, se realiza fuzzing de directorios web

<img width="831" height="193" alt="Pasted image 20260204121110" src="https://github.com/user-attachments/assets/c4406fee-fe71-4a9c-800e-ea6b076c92ff" />

Me dirijo al directorio ``` galery.html ``` 

Al inspeccionar el código fuente de esta página, se detecta un comentario con texto codificado

<img width="615" height="717" alt="Pasted image 20260204122004" src="https://github.com/user-attachments/assets/ba21d32a-7078-4723-804b-857e406a3bdf" />

El mensaje es decodificado y, tras algunas pruebas, se determina que el contenido corresponde a un directorio oculto dentro del servidor web

<img width="292" height="107" alt="Pasted image 20260204122158" src="https://github.com/user-attachments/assets/7868b0fe-021c-4ee7-845d-e7ae16520e47" />

<img width="658" height="268" alt="Pasted image 20260204125503" src="https://github.com/user-attachments/assets/c52daeb9-3125-4083-a3c7-cb264a7f9c10" />

## 🔐 Acceso al sistema

Al acceder al directorio descubierto, se encuentra un archivo que indica que un usuario debe cambiar su contraseña, ya que esta es débil

<img width="793" height="107" alt="Pasted image 20260204125556" src="https://github.com/user-attachments/assets/01a59418-c566-4603-a2aa-dbc4f08caef1" />

El mensaje hace referencia al usuario **Lucas**, por lo que se decide realizar un ataque de fuerza bruta por *SSH* contra dicho usuario

<img width="665" height="40" alt="Pasted image 20260204130234" src="https://github.com/user-attachments/assets/c70904aa-a895-4167-8df1-f2314fa57ee4" />

El ataque resulta exitoso y se logra iniciar sesión en el sistema como **Lucas**

<img width="333" height="163" alt="Pasted image 20260204130328" src="https://github.com/user-attachments/assets/8b591765-34a1-40d2-b19b-edb3e308ed71" />

Hacemos tratamiento de TTY

<img width="240" height="140" alt="Pasted image 20251222222913" src="https://github.com/user-attachments/assets/53b0af60-266b-427e-9067-a27459cc4d3a" />

## 🔐 Escalada de privilegios (Movimiento lateral)

Una vez dentro del sistema, se revisan los permisos sudo y se observa que es posible ejecutar un binario como el usuario **Andy** sin necesidad de contraseña

<img width="1232" height="97" alt="Pasted image 20260204131053" src="https://github.com/user-attachments/assets/fc399f1c-768c-4d9b-a5b3-14efb7c6a44b" />

Se busca dicho binario en [GTFOBins](https://gtfobins.org/) y se aprovecha esta configuración insegura para escalar privilegios, logrando cambiar al usuario **Andy**

<img width="910" height="430" alt="Pasted image 20260204131113" src="https://github.com/user-attachments/assets/3c37ca29-933d-492d-a68e-e5a279c48484" />

<img width="880" height="98" alt="Pasted image 20260204131001" src="https://github.com/user-attachments/assets/b96a6d41-3829-40fc-916e-811d92bfdc4f" />

Hacemos tratamiento de TTY

<img width="240" height="140" alt="Pasted image 20251222222913" src="https://github.com/user-attachments/assets/331f4ec6-e3bc-40d6-8d74-4745cba51b50" />

## 🔐 Escalada de privilegios 

Desde el usuario **Andy**, se intenta escalar privilegios a root, pero el sistema solicita contraseña.

<img width="546" height="47" alt="Pasted image 20260204131424" src="https://github.com/user-attachments/assets/3b908e02-1797-4b39-875d-3830f2b469f8" />

Ante esto, se procede a buscar binarios que puedan ejecutarse con privilegios elevados y se identifica un conjunto de binarios interesantes

<img width="641" height="326" alt="Pasted image 20260204131555" src="https://github.com/user-attachments/assets/20aae80c-7b8a-493b-8d3a-a794c9f9f305" />

Al acceder al directorio correspondiente, se analiza un archivo llamado **backup**, el cual no presenta información relevante

<img width="612" height="206" alt="Pasted image 20260204131950" src="https://github.com/user-attachments/assets/2dc5a384-b0f7-4d9c-97c1-c5de6cd7e9a1" />

<img width="440" height="282" alt="Pasted image 20260204132346" src="https://github.com/user-attachments/assets/1edd3acd-8836-4f19-8f25-3c9abc93faa8" />

Sin embargo, al analizar otro binario dentro del mismo directorio, se observa que permite la ejecución de comandos con privilegios de root

<img width="1861" height="651" alt="Pasted image 20260204132111" src="https://github.com/user-attachments/assets/396c03ec-10e6-46d2-a481-e2e124e8016d" />

El binario es ejecutado  y se obtiene acceso total al sistema como root

<img width="683" height="113" alt="Pasted image 20260204132827" src="https://github.com/user-attachments/assets/4bde27a3-3d88-403e-b9ea-e2c14babf823" />

## 🏁 Conclusión

Este laboratorio demuestra cómo la exposición de información en aplicaciones web, combinada con credenciales débiles y configuraciones inseguras de sudo, puede conducir a una comprometida total del sistema.
