# DockerLabs

## 🔎 Enumeración

Se realiza un escaneo de puertos con **Nmap**, donde se identifica únicamente el puerto:
* 80/tcp – HTTP

<img width="750" height="145" alt="Pasted image 20260211103938" src="https://github.com/user-attachments/assets/a65a2265-616c-4e86-90d5-1ea62364e098" />

Al acceder al servicio web, se inspecciona el código fuente sin encontrar información relevante

<img width="1287" height="737" alt="Pasted image 20260211104100" src="https://github.com/user-attachments/assets/a090c910-3b39-437b-bc0d-e3d880583f8c" />

Por ello, se procede a realizar fuzzing de directorios, descubriendo varias rutas ocultas. Entre ellas destaca:
```/machine.php ```

<img width="1148" height="462" alt="Pasted image 20260211104720" src="https://github.com/user-attachments/assets/f8f87eb6-ddd9-4a46-a128-7a74cd63823b" />

## 📂 Explotación

Al acceder a ```/machine.php```, se identifica una funcionalidad de carga de archivos 

<img width="683" height="305" alt="Pasted image 20260211105123" src="https://github.com/user-attachments/assets/409a7d68-3668-40c6-973e-dec28b280cf2" />

Se genera una reverse shell en **PHP** gracias a [Reverse Shell Generator](https://www.revshells.com/), configurando la IP y puerto de la máquina atacante

<img width="1132" height="756" alt="Pasted image 20260211105059" src="https://github.com/user-attachments/assets/3314d2bf-0d70-4a21-8867-46f70255342a" />

Cargamos nuestro archivo y no nos permite surbirlo

<img width="482" height="60" alt="Pasted image 20260211105923" src="https://github.com/user-attachments/assets/c698a408-db17-4abd-8b74-87d5aea627f5" />

La aplicación solo permite subir archivos con extensión ```.zip```. Para evadir esta restricción, se cambia la extensión del archivo:

```
mv archivo.php archivo.phar

```

<img width="383" height="212" alt="Pasted image 20260211110421" src="https://github.com/user-attachments/assets/73fa4123-92a1-45e3-a260-2a313ec89f67" />

El archivo es aceptado correctamente

<img width="606" height="133" alt="Pasted image 20260211110643" src="https://github.com/user-attachments/assets/f15fc804-e496-4224-b1d9-e871c4382ad6" />

Posteriormente, se accede al directorio ```/uploads``` 

<img width="615" height="248" alt="Pasted image 20260211110913" src="https://github.com/user-attachments/assets/8d49dee9-ae14-4f5f-aab5-c288f3d8e5a2" />

Se levanta un listener en la máquina atacante  y se ejecuta el archivo subido, obteniendo acceso como usuario del servicio web.

<img width="357" height="98" alt="Pasted image 20260211105332" src="https://github.com/user-attachments/assets/042790a6-1635-4f16-99fc-f3ebe480b194" />

<img width="526" height="177" alt="Pasted image 20260211111057" src="https://github.com/user-attachments/assets/d05f56ca-1d48-4fca-bce8-893fd6dbc968" />

Hacemos tratamiento de TTY

<img width="240" height="140" alt="Pasted image 20251222222913" src="https://github.com/user-attachments/assets/80620e28-61c0-44c8-b862-be11d9db28ce" />

## 🔐 Escalada de privilegios

Una vez dentro, se realiza enumeración del sistema y se detecta que el usuario puede ejecutar los binarios: ```/cut``` y ```/grep``` como **root** sin necesidad de contraseña

<img width="952" height="193" alt="Pasted image 20260211111533" src="https://github.com/user-attachments/assets/0df5a645-9e30-470b-8d8f-b7e648540a72" />

Durante la exploración, se encuentra una nota que hace referencia a un archivo sensible

<img width="1302" height="107" alt="Pasted image 20260211112037" src="https://github.com/user-attachments/assets/2a9e010d-67b1-4ec1-85fb-34f0d278e766" />

Aprovechando los privilegios sobre **grep**, se ejecuta el binario como root para leer el contenido:

<img width="752" height="108" alt="Pasted image 20260211112405" src="https://github.com/user-attachments/assets/59e2b098-5b9e-49ee-8f98-1c09b63289ee" />

De esta manera, se obtiene la contraseña del usuario **root**

<img width="407" height="166" alt="Pasted image 20260211112924" src="https://github.com/user-attachments/assets/349dc3db-4123-47d9-8981-b7801b62bd72" />

## 🏁 Conclusión

Este laboratorio demuestra cómo una mala validación en la subida de archivos combinada con configuraciones inseguras de privilegios puede llevar a una compromisión completa del sistema.
