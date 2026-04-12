# File

## 🔎 Enumeración

Se realiza un escaneo de puertos utilizando Nmap, identificando los siguientes servicios abiertos:

* 21/tcp – FTP
* 80/tcp – HTTP

<img width="690" height="421" alt="Pasted image 20260317110342" src="https://github.com/user-attachments/assets/adedb4d7-55d9-43c6-8bc2-86dcd49fa860" />

## 📂 Acceso al servicio FTP

Se intenta iniciar sesión en el servicio FTP utilizando el usuario **anonymous**, teniendo éxito

Se listan los archivos disponibles y se encuentra un archivo .txt, el cual se descarga a la máquina local

<img width="832" height="373" alt="Pasted image 20260317110539" src="https://github.com/user-attachments/assets/8339635c-e680-45fb-a2a7-22fc855e3883" />

Al leer su contenido, se identifica que contiene texto cifrado

Se utiliza [Crackstation](https://crackstation.net/) para descifrarlo, obteniendo como resultado un posible usuario **Justin**

<img width="1283" height="402" alt="Pasted image 20260317110941" src="https://github.com/user-attachments/assets/a40150e9-dc41-4c89-90ae-78e5d3070b74" />

## 🌐 Análisis del servicio web

Se visita la página web, pero no se encuentra información relevante

<img width="928" height="438" alt="Pasted image 20260317111044" src="https://github.com/user-attachments/assets/5db66000-f598-4f88-a9fd-74381a4df7c9" />

Al inspeccionar el código fuente, se observa un mensaje que sugiere buscar un directorio oculto

<img width="840" height="282" alt="Pasted image 20260317111210" src="https://github.com/user-attachments/assets/8aa6c803-10fa-42fe-9bab-764ecc62c1ad" />

Se realiza fuzzing con Gobuster, encontrando:

<img width="861" height="230" alt="Pasted image 20260317111628" src="https://github.com/user-attachments/assets/0def70ce-eeda-464b-ad62-f08d63318d76" />

* Un directorio donde se almacenan archivos

<img width="632" height="291" alt="Pasted image 20260317111654" src="https://github.com/user-attachments/assets/42701d24-28f8-40e4-a2d0-9bee1e43f574" />

* Un directorio que permite subida de archivos

<img width="676" height="142" alt="Pasted image 20260317111725" src="https://github.com/user-attachments/assets/2ea07190-aa6d-4c3f-8be7-2311569901e0" />

## Obtención de acceso inicial

Se genera una reverse shell en PHP gracias a [Reverse Shell Generator](https://www.revshells.com/) 

<img width="1097" height="732" alt="Pasted image 20260317111920" src="https://github.com/user-attachments/assets/3637e708-1f0b-4307-9593-735d8d9224a7" />

Se intenta subir el archivo, pero es bloqueado

<img width="680" height="133" alt="Pasted image 20260317112152" src="https://github.com/user-attachments/assets/4cd73eaa-a6cc-47fb-91a2-9aa9dc834d91" />

<img width="668" height="166" alt="Pasted image 20260317112224" src="https://github.com/user-attachments/assets/70405c9c-8d01-480a-934e-34452251395c" />

Se prueba cambiar la extensión a .phar: 

```bash
mv revshell.php revshell.phar
```

<img width="660" height="127" alt="Pasted image 20260317112447" src="https://github.com/user-attachments/assets/f0066ad4-5869-4ca8-bfc1-28205f0748f3" />

<img width="667" height="120" alt="Pasted image 20260317112606" src="https://github.com/user-attachments/assets/d469d7f4-1081-4760-9208-be85c160bf45" />

Esto permite subir el archivo correctamente

Luego:

* Se accede al directorio **/uploads**
* Se inicia un listener en el puerto correspondiente
* Se ejecuta el archivo

<img width="618" height="306" alt="Pasted image 20260317112824" src="https://github.com/user-attachments/assets/c8c09b76-dbd8-4e15-b414-6266575c6106" />

Se obtiene acceso como **www-data**

<img width="637" height="157" alt="Pasted image 20260317112934" src="https://github.com/user-attachments/assets/128f338b-605c-47fa-b22f-efed4de1b02e" />

Hacemos tratamiento de TTY

<img width="240" height="140" alt="Pasted image 20260317113029" src="https://github.com/user-attachments/assets/1afe3886-8620-495c-90fe-5dc68ceff944" />

## Enumeración interna

Se intenta escalar privilegios, pero se requiere contraseña

<img width="448" height="205" alt="Pasted image 20260317113227" src="https://github.com/user-attachments/assets/85deaa69-78ec-4fd2-a11a-6177ceda23a7" />

Se buscan binarios con permisos elevados, sin éxito

<img width="592" height="231" alt="Pasted image 20260317113324" src="https://github.com/user-attachments/assets/e4d6588b-215b-4514-9df7-249a6166a0d5" />

En el directorio **/home** se identifican varios usuarios

<img width="637" height="120" alt="Pasted image 20260317113831" src="https://github.com/user-attachments/assets/962f95eb-8fdf-49d4-ae05-b67af134f002" />

## 🔐 Fuerza bruta local

Se decide utilizar la herramienta [Sudo_BruteForce](https://github.com/Maalfer/Sudo_BruteForce) de [Mario](https://github.com/Maalfer) para hacer fuerza bruta hacia estos usuarios

Para ello:

* Se levanta un servidor HTTP en la máquina atacante

<img width="855" height="91" alt="Pasted image 20260317124942" src="https://github.com/user-attachments/assets/360e506f-031b-47fb-b6c3-8a0cf3fb3894" />

Se transfieren:

* La herramienta

<img width="767" height="290" alt="Pasted image 20260317125130" src="https://github.com/user-attachments/assets/a92d93b6-3b57-437f-a9c5-767c8564a68b" />

* El diccionario rockyou.txt

<img width="757" height="83" alt="Pasted image 20260317125424" src="https://github.com/user-attachments/assets/01c12d05-cba2-4cdb-9a15-a7278f499d04" />

<img width="771" height="292" alt="Pasted image 20260317125358" src="https://github.com/user-attachments/assets/1129a8d9-49b1-477b-b1b8-03ae7d131053" />

Se asignan permisos de ejecución.

<img width="706" height="42" alt="Pasted image 20260317125527" src="https://github.com/user-attachments/assets/8b1abd1a-a5db-46af-a498-669e54df422c" />

Se prueba con el usuario **Fernando** y se obtiene su contraseña y se cambia a dicho usuario

<img width="637" height="83" alt="Pasted image 20260317125842" src="https://github.com/user-attachments/assets/c60460a2-774e-439e-88b0-e8a50d05e144" />

## 🖼️ Análisis de archivo (Steganografía)

En el directorio de **Fernando** se encuentra una imagen

<img width="806" height="262" alt="Pasted image 20260317130300" src="https://github.com/user-attachments/assets/55bc20a2-0acb-446c-a771-3fe1ff20174d" />

Se transfiere a la máquina atacante y se analiza con **stegcracker**

<img width="862" height="96" alt="Pasted image 20260317130800" src="https://github.com/user-attachments/assets/d6ad5914-8155-45e7-81a7-479b16d23bcc" />

<img width="943" height="175" alt="Pasted image 20260317130851" src="https://github.com/user-attachments/assets/6e0376bd-5b6f-4c2f-b835-3ae917f55462" />

<img width="983" height="333" alt="Pasted image 20260317131211" src="https://github.com/user-attachments/assets/5b481518-32a5-488c-b796-f9ee75abae52" />

Se obtiene un mensaje cifrado

<img width="983" height="333" alt="Pasted image 20260317131211" src="https://github.com/user-attachments/assets/801b5cdf-d08e-4d13-abaf-3ca3be072e5f" />

<img width="533" height="131" alt="Pasted image 20260317131319" src="https://github.com/user-attachments/assets/7038cec7-1084-4bc2-89f0-c0e768a54069" />

Se descifra con [Crackstation](https://crackstation.net/), obteniendo una contraseña válida

<img width="1305" height="383" alt="Pasted image 20260317131418" src="https://github.com/user-attachments/assets/db6d53f6-f82a-4412-9c11-735a612e9fe6" />

Se prueba la contraseña con los usuarios del sistema, logrando acceso como **Mario**

Desde el usuario **Mario**, se identifican múltiples binarios que permiten escalar privilegios:

Mario → puede ejecutar un binario como Julen

<img width="956" height="197" alt="Pasted image 20260317131652" src="https://github.com/user-attachments/assets/fcaef893-9e37-4f08-90a5-4ff6e0165e28" />

Se consulta en [GTFOBins](https://gtfobins.org/) 

<img width="932" height="401" alt="Pasted image 20260317132127" src="https://github.com/user-attachments/assets/1d06e65f-62f3-454f-8c62-358340374920" />

Julen → puede ejecutar un binario como Iker

<img width="970" height="222" alt="Pasted image 20260317132220" src="https://github.com/user-attachments/assets/35862f18-9a84-4b5f-90c9-9aa32be11494" />

Se consulta en [GTFOBins](https://gtfobins.org/) 

<img width="938" height="432" alt="Pasted image 20260317132425" src="https://github.com/user-attachments/assets/59b0cbf5-8004-4c74-b579-85dfaa50b8fe" />

Iker → puede ejecutar un binario como root

<img width="992" height="221" alt="Pasted image 20260317132358" src="https://github.com/user-attachments/assets/997db9c2-ad5f-420e-b4ea-da956ef3a5bf" />

## 👑 Escalada final

Se identifica un script en Python que puede ejecutarse como **root** sin contraseña

Se procede a:

* Eliminar el archivo original

<img width="557" height="42" alt="Pasted image 20260317134208" src="https://github.com/user-attachments/assets/3fd9f33e-3dfd-47e0-869b-7101af4f7319" />

* Crear uno nuevo con código malicioso:

```bash
import os; os.system("/bin/bash")
```

* Ejecutarlo con privilegios elevados siendo el usuario **root**

<img width="772" height="106" alt="Pasted image 20260317134246" src="https://github.com/user-attachments/assets/78b04cd0-b377-441e-ac82-d134839f9459" />

Finalmente nos vamos al directorio **/root** y entramos la flag 

<img width="711" height="272" alt="Pasted image 20260317134505" src="https://github.com/user-attachments/assets/19c300fb-1583-4e80-83aa-715e789a03c9" />

Volvemos a hacer uso de [Crackstation](https://crackstation.net/)

<img width="1262" height="413" alt="Pasted image 20260317134534" src="https://github.com/user-attachments/assets/94802e0c-66b8-48d6-a6d7-139dda767da5" />

## 🏁 Conclusión

Este laboratorio es un recordatorio de que la seguridad no falla por un solo error, sino por cómo los atacantes conectan los puntos: lo que empieza como un simple acceso anónimo por FTP termina siendo la llave maestra cuando se combina con una web vulnerable y un poco de ingenio en la escalada de privilegios. Al final, el éxito de la intrusión no dependió de una herramienta mágica, sino de esa persistencia para encadenar técnicas desde la esteganografía hasta la fuerza bruta para pasar de ser un simple visitante a tener el control total como root, demostrando que en el mundo real, hasta el descuido más pequeño puede tirar abajo todo el sistema si no se protegen todos los flancos.
