# HiddenCat

## 🔎 Enumeración

Se realiza un escaneo de puertos utilizando Nmap, identificando los siguientes servicios expuestos:

* 22/tcp – SSH
* 8009/tcp – AJP
* 8080/tcp – HTTP

<img width="1291" height="437" alt="image" src="https://github.com/user-attachments/assets/714f154d-fc00-4d96-9baf-8385b4abd93b" />


```
Nota: Durante la fase de reconocimiento se observa que el servicio SSH utiliza una versión desactualizada,
aunque el principal punto de interés se encuentra en los servicios relacionados con Apache Tomcat
```

## 🌐 Análisis de la aplicación web

Al acceder a la aplicación web se identifica que el servidor ejecuta **Apache Tomcat 9.0.30**

 <img width="1025" height="547" alt="Pasted image 20260609173237" src="https://github.com/user-attachments/assets/2ebcf17d-62af-47ce-84bc-66103454ba28" />

Se realiza además un proceso de fuzzing web para descubrir directorios interesantes

<img width="887" height="482" alt="Pasted image 20260609182059" src="https://github.com/user-attachments/assets/039b8faf-9f04-4db6-8b12-553631202d5d" />

Aunque se encuentran varias rutas, ninguna proporciona acceso directo al sistema, por lo que se continúa investigando la versión del servidor

<img width="1087" height="47" alt="Pasted image 20260609182157" src="https://github.com/user-attachments/assets/6fdb40c8-bf0b-4a29-98a1-02afddbbcdef" />

Tras analizar la versión instalada, se identifica que es vulnerable al [CVE-2020-1938](https://nvd.nist.gov/vuln/detail/cve-2020-1938)

<img width="1271" height="307" alt="Pasted image 20260609182503" src="https://github.com/user-attachments/assets/62696463-6d08-4576-a15a-6d64bb4ad93f" />

## 💥 Explotación

Después de investigar la vulnerabilidad, se utiliza un [exploit]( https://github.com/dacade/CVE-2020-1938/blob/master/tomcat.py) público para aprovechar Ghostcat y acceder a información sensible del servidor

<img width="822" height="597" alt="Pasted image 20260609185317" src="https://github.com/user-attachments/assets/92ae8907-3130-41dc-a5ec-0984655e573d" />

Como resultado de la explotación, se obtiene el usuario ```jerry```, el cual servirá como punto de partida para intentar obtener acceso al sistema

## 🔑 Acceso inicial

Con el usuario descubierto, se realiza un ataque de fuerza bruta contra el servicio SSH

<img width="967" height="57" alt="Pasted image 20260609185605" src="https://github.com/user-attachments/assets/f7f4d38d-09a3-49cb-9370-2b6602c30cee" />

Tras encontrar credenciales válidas, se establece una conexión mediante SSH, obteniendo acceso inicial al servidor

# 👑 Escalada de privilegios

Una vez dentro del sistema, se inicia la fase de enumeración local

Durante este proceso se identifica un binario con permisos SUID que resulta interesante

<img width="555" height="386" alt="Pasted image 20260609190350" src="https://github.com/user-attachments/assets/41bd50e4-b6be-4b39-9eab-4118a01a5665" />

Se consulta [GTFOBins](https://gtfobins.org/)  para verificar si dicho binario puede ser utilizado para elevar privilegios y, tras ejecutar la técnica correspondiente

<img width="872" height="272" alt="Pasted image 20260609190506" src="https://github.com/user-attachments/assets/48a50a9b-7f88-4355-ac69-7b04a597c829" />

Tras ejecutar la técnica correspondiente, se consigue acceso como **root**

<img width="917" height="90" alt="Pasted image 20260609190548" src="https://github.com/user-attachments/assets/8ca07d2d-1231-42cc-85f5-d51014955b1c" />

## 🏁 Conclusión

Este laboratorio demuestra la importancia de mantener actualizados los servicios expuestos, ya que versiones vulnerables de Apache Tomcat pueden permitir la obtención de información sensible que facilite el acceso inicial. Además, pone de manifiesto cómo una configuración insegura de binarios con permisos especiales puede derivar en una escalada de privilegios completa.
