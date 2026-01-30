# AguadeMayo

## 🔎 Enumeración inicial

Se realiza un escaneo de puertos utilizando Nmap, donde se identifican los siguientes servicios abiertos:
* 22/tcp – SSH
* 80/tcp – HTTP

<img width="718" height="198" alt="Pasted image 20260129124301" src="https://github.com/user-attachments/assets/a2203fbd-6567-4f1b-b5b8-5933e0e81cc1" />

## 🌐 Análisis del servicio web

Al acceder a la página web, no se observa contenido relevante a simple vista.

<img width="958" height="502" alt="Pasted image 20260129125243" src="https://github.com/user-attachments/assets/378fda4f-0155-4194-ac5f-6803dfce8523" />

Por ello, se decide inspeccionar la respuesta HTTP usando **curl**, donde llama la atención un bloque al final del código fuente.

<img width="1847" height="165" alt="Pasted image 20260129132407" src="https://github.com/user-attachments/assets/e9039437-b23d-491a-9b54-9650cde00a06" />

Tras investigar, se identifica que dicho contenido corresponde a código **Brainfuck** oculto dentro de comentarios HTML (<!-- -->).

<img width="966" height="141" alt="Pasted image 20260129133903" src="https://github.com/user-attachments/assets/c9da437e-1b7d-4e6b-8bca-33e92ea417c9" />

*⚠️ Importante:
Para poder decodificar correctamente el contenido, es necesario eliminar los comentarios HTML y enviar únicamente el código Brainfuck a un decodificador online.*

<img width="967" height="223" alt="Pasted image 20260129151539" src="https://github.com/user-attachments/assets/3f57d5f8-e848-4a8a-b0ec-b2eb0aa3a581" />

Luego de decodificar el contenido, se obtiene una posible contraseña

## 📂 Fuzzing web 

Se realiza fuzzing web con el objetivo de descubrir rutas ocultas

<img width="870" height="483" alt="Pasted image 20260129125455" src="https://github.com/user-attachments/assets/b7eef1e7-0833-44ad-ad9c-142d28a42003" />

## 🖼️ Análisis de archivos

Se accede a un directorio de la aplicación web donde se encuentra una imagen, la cual es descargada para su análisis.

<img width="608" height="305" alt="Pasted image 20260129125345" src="https://github.com/user-attachments/assets/3e476cb2-2451-4517-bedc-0f71d759d5b3" />

Se revisan posibles metadatos ocultos utilizando distintas herramientas, pero no se encuentra información relevante.

<img width="631" height="463" alt="Pasted image 20260129132039" src="https://github.com/user-attachments/assets/1c98de91-924e-4f1c-98cb-029c657b0671" />

<img width="623" height="76" alt="Pasted image 20260129150219" src="https://github.com/user-attachments/assets/4da354ad-61f7-437b-bf16-ddbdee559f30" />

Sin embargo, se observa que el archivo se llama agua, coincidiendo con el nombre de la máquina. Esto lleva a probar dicho nombre como usuario, junto con la contraseña obtenida previamente.

## 🔐 Acceso al sistema

Usando las credenciales descubiertas, se logra iniciar sesión por SSH exitosamente

<img width="742" height="327" alt="Pasted image 20260129152242" src="https://github.com/user-attachments/assets/c87c8053-c4f8-4894-a1e6-78c69a229b72" />

## 🔐 Escalada de privilegios

Una vez dentro del sistema, se revisan los permisos sudo y se detecta que el usuario puede ejecutar el binario bettercap como root.

<img width="1155" height="122" alt="Pasted image 20260129152455" src="https://github.com/user-attachments/assets/b030f7db-10bb-4141-afa1-1364e7d35add" />

Bettercap es una herramienta de análisis de red que permite la ejecución de comandos del sistema.<br>
Al ejecutarla y revisar su guia, se descubre que es posible lanzar comandos usando el carácter **!**

<img width="1081" height="110" alt="Pasted image 20260129153744" src="https://github.com/user-attachments/assets/010861ce-3ecf-41f3-8c74-45bd4db80b30" />

<img width="1282" height="267" alt="Pasted image 20260129153940" src="https://github.com/user-attachments/assets/d7b27154-073b-4f04-a2a1-edbc94016cbc" />

Ejecutamos y damos los permisos para *bash* 

<img width="547" height="87" alt="Pasted image 20260129154109" src="https://github.com/user-attachments/assets/22d0ddad-e655-4618-8261-2e05f73b878b" />

Finalmente se ejecuta
```
/bin/bash -p
```

Y somos root

<img width="392" height="97" alt="Pasted image 20260129154225" src="https://github.com/user-attachments/assets/db5718e1-7725-4645-af08-6cf0b85ae31a" />

## 🏁 Conclusión

Este laboratorio demuestra cómo la información oculta en el código fuente, el uso de lenguajes poco comunes como Brainfuck y la presencia de binarios mal configurados con privilegios elevados pueden combinarse para comprometer completamente un sistema.
