# Walking Dead

## 🔎 Enumeración

Se realiza un escaneo de puertos utilizando Nmap, identificando los siguientes servicios expuestos:

* 22/tcp – SSH
* 80/tcp – HTTP

<img width="756" height="522" alt="Pasted image 20260611152456" src="https://github.com/user-attachments/assets/93c4df6a-1a09-44d0-b44d-dd10d1989051" />

## 🌐 Análisis de la aplicación web

Al acceder a la aplicación web, se realiza un proceso de fuzzing para descubrir directorios y recursos ocultos

<img width="861" height="301" alt="Pasted image 20260611152607" src="https://github.com/user-attachments/assets/fa889af4-b76f-4730-8289-94f73cdc2b7a" />

<img width="686" height="235" alt="Pasted image 20260611153016" src="https://github.com/user-attachments/assets/ab0a87ae-dc9d-4d21-ab83-02f10fa7fc89" />

Durante la revisión del sitio, se identifica una línea de código que hace referencia a un posible directorio oculto

<img width="402" height="81" alt="Pasted image 20260611153508" src="https://github.com/user-attachments/assets/948dfb0a-d58b-44b6-8d9d-4d9fb6826fe0" />

Al acceder a dicho directorio, únicamente se observa una página en blanco. Sin embargo, la estructura de la URL llama la atención y sugiere la posible existencia de un parámetro vulnerable

<img width="1102" height="370" alt="Pasted image 20260611153616" src="https://github.com/user-attachments/assets/4f2314db-7cd7-4882-8138-6b896a1844a0" />

```
wfuzz -c -w /usr/share/wordlists/seclists/Discovery/Web-Content/directory-list-lowercase-2.3-medium.txt -u 'http://172.17.0.2/hidden/.shell.php?FUZZ=whoami' --hl 0
```

<img width="732" height="232" alt="Pasted image 20260611154354" src="https://github.com/user-attachments/assets/9367fde2-6af7-4a61-bfaf-11a3664ff02c" />

Mediante un nuevo proceso de enumeración, se identifica el parámetro correspondiente y se confirma que la aplicación es vulnerable a ```Remote File Inclusion (RFI)```

<img width="787" height="125" alt="Pasted image 20260611154444" src="https://github.com/user-attachments/assets/5c603540-e3c4-4649-bfed-b538768812de" />

## 💥 Obtención de acceso inicial

Una vez confirmada la vulnerabilidad, se prepara una reverse shell en **PHP** y se codifica para facilitar su inclusión a través del parámetro vulnerable

<img width="1310" height="267" alt="Pasted image 20260611160333" src="https://github.com/user-attachments/assets/946e5736-8a5d-49f2-b1ad-7d908da71882" />

Posteriormente, se inicia un listener en el puerto **4444** y se explota la vulnerabilidad, obteniendo acceso remoto al sistema

<img width="1107" height="162" alt="Pasted image 20260611160407" src="https://github.com/user-attachments/assets/359f4e11-c04b-4144-8400-d1865ec5f803" />

Hacemos tratamiento de TTY

<img width="240" height="140" alt="Pasted image 20260611160923" src="https://github.com/user-attachments/assets/e5941b3b-dbf8-438a-ad8b-aebaa5f7320d" />

## 👑 Escalada de privilegios

Con acceso al servidor, se realiza una enumeración local en busca de posibles vectores de escalada de privilegios

<img width="780" height="322" alt="Pasted image 20260611161622" src="https://github.com/user-attachments/assets/0db8e6ec-626b-4cec-b5ce-2258f602c578" />

Durante este proceso se revisan los binarios con permisos **SUID**, identificando uno 

Tras verificar la técnica correspondiente en [GTFOBins](https://gtfobins.org/), se ejecuta el binario y se consigue elevar privilegios hasta obtener acceso como **root**

<img width="877" height="267" alt="Pasted image 20260611163421" src="https://github.com/user-attachments/assets/e03c7368-6b17-4442-b30d-a8c3024a8b1e" />

<img width="1171" height="152" alt="Pasted image 20260611163731" src="https://github.com/user-attachments/assets/dc31142e-7e47-4b9e-8526-cbb728e1367a" />

## 🏁 Conclusión

Este laboratorio demuestra cómo una vulnerabilidad de Remote File Inclusion (RFI) puede comprometer completamente un servidor cuando no se validan adecuadamente los parámetros de entrada. Además, resalta la importancia de revisar los permisos de los binarios con SUID, ya que una configuración insegura puede facilitar una escalada de privilegios hasta el usuario root.
