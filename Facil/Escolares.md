# Escolares

## 🔎 Enumeración

Se realiza un escaneo de puertos utilizando Nmap, identificando los siguientes servicios abiertos:

* 22/tcp – SSH
* 80/tcp – HTTP

<img width="925" height="311" alt="Pasted image 20260312105337" src="https://github.com/user-attachments/assets/4a4de0c8-2cf0-4099-a593-991982909609" />

## 🌐 Análisis del servicio web

Se visita la página web, la cual corresponde a una universidad de ciberseguridad

<img width="1666" height="687" alt="Pasted image 20260312105430" src="https://github.com/user-attachments/assets/44c7f89b-39e2-4163-964f-b554c08c1986" />

Al inspeccionar el apartado de contacto, se encuentra un comentario oculto que hace referencia a un archivo: ```profesores.html```

<img width="1203" height="647" alt="Pasted image 20260312105937" src="https://github.com/user-attachments/assets/4dfa7629-f4ec-4b08-b800-301314e2ae9f" />

Se accede a dicho archivo, obteniendo información relevante del personal

<img width="1080" height="736" alt="Pasted image 20260312110051" src="https://github.com/user-attachments/assets/2f08f5ed-008b-4c42-90d3-3e1dd3300ec2" />

<img width="463" height="785" alt="Pasted image 20260312110121" src="https://github.com/user-attachments/assets/d1d7e7bf-d14b-4b59-b007-088ccb4b0cde" />

<img width="287" height="205" alt="Pasted image 20260312110158" src="https://github.com/user-attachments/assets/de099d0d-ded8-4bbc-8c9e-cf1a2f3b6d1a" />

## 🔍 Descubrimiento de WordPress

Se realiza fuzzing web, encontrando un directorio: ```/wordpress```

<img width="846" height="286" alt="Pasted image 20260312110319" src="https://github.com/user-attachments/assets/b5080185-50f7-4264-a245-2dcf7fa58db1" />

Al acceder, se identifica un posible usuario: **luisillo**

<img width="1287" height="685" alt="Pasted image 20260312111612" src="https://github.com/user-attachments/assets/5a57f580-84f8-4e0c-b701-ec7a6b7f6263" />

## 🔑 Ataque de fuerza bruta

Para generar un diccionario personalizado, se utiliza la herramienta **CUPP** con información obtenida previamente: ```cupp -i```

<img width="728" height="802" alt="Pasted image 20260312112404" src="https://github.com/user-attachments/assets/a2b86fd1-605c-4a4e-8692-65a00566457f" />

Luego se realiza un ataque de fuerza bruta con **WPScan**:

```bash
wpscan -U luisillo -P luis.txt --url http://172.17.0.2/wordpress
```

<img width="755" height="77" alt="Pasted image 20260312112703" src="https://github.com/user-attachments/assets/5331d93a-01f8-4699-b34b-a4885e34fdd9" />

Se obtienen credenciales válidas

## 🌐 Acceso al panel de WordPress

Se realiza nuevamente fuzzing, esta vez enfocado en rutas de **WordPress**

<img width="880" height="402" alt="Pasted image 20260312114451" src="https://github.com/user-attachments/assets/f18021ca-8ad8-4720-99b7-7c8ce3853368" />

Al intentar acceder al login, no se resuelve correctamente el dominio, por lo que se añade al archivo: ```/etc/hosts``` la IP junto con su dominio

<img width="635" height="338" alt="Pasted image 20260312114638" src="https://github.com/user-attachments/assets/917053f2-8f18-4475-874c-9fe73d2490e5" />

<img width="877" height="407" alt="Pasted image 20260312114624" src="https://github.com/user-attachments/assets/8bc05d1a-1a71-4d83-afab-978d7146cccc" />

<img width="608" height="255" alt="Pasted image 20260312114913" src="https://github.com/user-attachments/assets/dc014c77-6a8c-447a-a457-555afad55836" />

Con esto, se logra iniciar sesión correctamente en el panel de administración

<img width="1587" height="752" alt="Pasted image 20260312115019" src="https://github.com/user-attachments/assets/a417debf-7ac0-4165-ac5f-cf87cb68972e" />

##  Obtención de acceso inicial

Dentro del panel, se accede a **WP File Manager**

<img width="1340" height="807" alt="Pasted image 20260312115718" src="https://github.com/user-attachments/assets/000afb65-cf10-45e0-bb84-923c958bfdf0" />

Se genera una reverse shell gracias a [Reverse Shell Generator](https://www.revshells.com/) , se guarda el archivo y se sube al servidor

<img width="1172" height="731" alt="Pasted image 20260312115517" src="https://github.com/user-attachments/assets/cb869b03-af08-4e05-aa76-099d187008ca" />

<img width="1030" height="123" alt="Pasted image 20260312115839" src="https://github.com/user-attachments/assets/f54984a9-7881-47bc-9793-f742c90f2ae0" />

Al ejecutar el archivo, se obtiene acceso a la máquina

## 👑 Escalada de privilegios

Una vez dentro:

* Se intenta escalar privilegios, pero se solicita contraseña

<img width="416" height="77" alt="Pasted image 20260312130145" src="https://github.com/user-attachments/assets/f527a63d-4e4f-44c5-b2f3-93c6c7f82ce9" />

* Se buscan binarios con permisos elevados:

```bash
find / -perm -4000 2>/dev/null
```

<img width="617" height="262" alt="Pasted image 20260312130305" src="https://github.com/user-attachments/assets/2e51fbe2-f428-4b61-8535-d95074063466" />

Sin resultados relevantes inicialmente

Durante la enumeración manual, se encuentra la contraseña del usuario **luisillo** en texto plano

<img width="508" height="133" alt="Pasted image 20260312130405" src="https://github.com/user-attachments/assets/8eb2602d-a466-48a2-b7f9-b961978acf49" />

Se vuelve a intentar la escalada de privilegios, identificando que es posible ejecutar un binario como **root**

<img width="982" height="165" alt="Pasted image 20260312130502" src="https://github.com/user-attachments/assets/94060b63-a593-4685-b18f-ba9de47edc3e" />

Se consulta [GTFOBins](https://gtfobins.org/) 

<img width="941" height="406" alt="Pasted image 20260312130548" src="https://github.com/user-attachments/assets/6bfb6efa-fb82-4df2-af7b-fdb55bf5f491" />

Finalmente, se ejecuta el comando correspondiente

```
(nota: eliminar la m antes de awk) y se obtiene acceso como root
```

<img width="680" height="93" alt="Pasted image 20260312130929" src="https://github.com/user-attachments/assets/f085f51e-a79b-4835-a4eb-5d46faecafd1" />

## 🏁 Conclusión

Este laboratorio demuestra cómo la exposición de información sensible en aplicaciones web, combinada con el uso de contraseñas débiles y plugins inseguros, puede permitir la obtención de acceso inicial mediante WordPress y posteriormente escalar privilegios hasta root.
