# Galeria

## 🔎 Enumeración

Se realiza un escaneo de puertos con Nmap, donde únicamente se identifica el puerto:
* 80/tcp – HTTP

<img width="741" height="143" alt="Pasted image 20251231105902" src="https://github.com/user-attachments/assets/8d1df107-423f-44a7-b4c9-cab2ce6d710a" />

## 🌐 Análisis de la aplicación web

Se accede a la página principal y se inspecciona el código fuente, pero no se encuentra información relevante

<img width="1822" height="852" alt="Pasted image 20251231105942" src="https://github.com/user-attachments/assets/9d31f5c7-d0bd-4eea-8899-67737995c42a" />

Posteriormente se intentan identificar tecnologías o servicios en ejecución, sin resultados significativos

<img width="960" height="73" alt="Pasted image 20251231110253" src="https://github.com/user-attachments/assets/f19e154e-9343-4cfa-967c-cf93def8f4a2" />

Ante esto, se procede a realizar fuzzing de directorios, lo que permite descubrir la ruta: ```/gallery ```

<img width="968" height="460" alt="Pasted image 20251231110807" src="https://github.com/user-attachments/assets/48d158a8-f804-48e4-8478-a4939ee73b39" />

## 📂 Vulnerabilidad de subida de archivos

Dentro del directorio identificado, se inspecciona la ruta:  ```/uploads/handler.php ```

<img width="590" height="338" alt="Pasted image 20251231110728" src="https://github.com/user-attachments/assets/d93fddf6-a209-4288-b8d4-4b3393a5ea7b" />

Se observa que permite la subida de archivos, lo que abre la posibilidad de cargar código malicioso.

<img width="737" height="141" alt="Pasted image 20251231110952" src="https://github.com/user-attachments/assets/230cae11-0c73-44bd-856b-deec50b81bb4" />

Se genera un payload de reverse shell, se actualiza con la IP, puerto y se carga a través del formulario

<img width="1215" height="541" alt="Pasted image 20251231114811" src="https://github.com/user-attachments/assets/6ceec244-55b7-4475-af53-d593ec325e86" />

<img width="752" height="147" alt="Pasted image 20251231115041" src="https://github.com/user-attachments/assets/2c3d285d-179a-46ea-aab7-de20bf880393" />

Luego se navega hacia:

```/uploads/images ```

<img width="722" height="477" alt="Pasted image 20251231115259" src="https://github.com/user-attachments/assets/b7e793f3-e670-4e83-89a9-0457b9d31d57" />

Antes de ejecutar el archivo, se configura el listener en la máquina atacante

<img width="792" height="252" alt="Pasted image 20251231115433" src="https://github.com/user-attachments/assets/a654095c-f0a6-469b-98f1-9b6b8182b1e0" />

Hacemos tratamiento de TTY

<img width="240" height="140" alt="Pasted image 20251222222913" src="https://github.com/user-attachments/assets/f5cc42c5-ccfb-4e1d-9355-06a6388e81f0" />

## 🔐 Escalada de privilegios – Usuario gallery

Se intenta escalar privilegios y se detecta que es posible ejecutar un binario sin contraseña como el usuario **gallery**

<img width="686" height="326" alt="Pasted image 20251231125145" src="https://github.com/user-attachments/assets/30ea61ed-0dfa-4f11-835f-b29ae90c3174" />

Se consulta [GTFOBins](https://gtfobins.org/) 

<img width="842" height="278" alt="Pasted image 20251231125326" src="https://github.com/user-attachments/assets/5b1cd4af-b350-44bd-983a-f6f168ba6a28" />

Dentro de *nano* se ejecuta el siguiente payload

```
reset; sh 1>&0 2>&0

```
<img width="873" height="487" alt="Pasted image 20251231125747" src="https://github.com/user-attachments/assets/da3d5af6-38ef-4611-afe4-35517f7542e6" />

## 🚩 Escalada de privilegios final

Ahora como usuario **gallery**, se identifica un binario interesante:

<img width="698" height="170" alt="Pasted image 20251231130139" src="https://github.com/user-attachments/assets/86a0939f-3d5b-4d79-99d3-5d893894f67b" />

Al analizar su contenido, se observa que llama a una función llamada *convert*, pero esta no está definida con una ruta absoluta

<img width="1882" height="357" alt="Pasted image 20251231130915" src="https://github.com/user-attachments/assets/15d10e16-a15c-4360-ad80-8697e8e58b12" />

Esto indica una vulnerabilidad de *Path Hijacking*, ya que el sistema buscará el binario convert siguiendo el orden definido en la variable PATH

Explotación:

Se crea un archivo malicioso llamado *convert*

<img width="535" height="48" alt="Pasted image 20251231132259" src="https://github.com/user-attachments/assets/b386f914-6c2d-419e-9f5b-8b3004addc39" />

Se le otorgan permisos de ejecución

<img width="790" height="252" alt="Pasted image 20251231132538" src="https://github.com/user-attachments/assets/ae6fdd70-764e-4f86-ad73-9d5ff924f032" />

Se modifica la variable PATH para que priorice el directorio donde se encuentra nuestro archivo

<img width="466" height="67" alt="Pasted image 20251231133500" src="https://github.com/user-attachments/assets/7d1cca0a-d2e1-430d-8dd8-31d913ce6ecf" />

Se ejecuta y somos **root**

<img width="512" height="116" alt="Pasted image 20251231133656" src="https://github.com/user-attachments/assets/accb9354-ef0a-4b9f-af6d-981dc6ead064" />

## 🏁 Conclusión 

Este escenario demuestra cómo una mala validación en la subida de archivos puede derivar en ejecución remota de código, y cómo un error en el manejo de rutas del sistema puede permitir una escalada de privilegios mediante Path Hijacking, logrando el compromiso total del sistema.
