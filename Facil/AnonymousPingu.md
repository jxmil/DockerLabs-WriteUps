# AnonymousPingu

## 🔎 Enumeración

Se realiza un escaneo de puertos utilizando la herramienta **Nmap**, donde se identifica que los puertos 21 (FTP) y 80 (HTTP) se encuentran abiertos

<img width="833" height="571" alt="Pasted image 20260122105021" src="https://github.com/user-attachments/assets/97ecb84f-2e65-44b8-b9a6-3df4a5067c90" />

## 📂 Análisis del servicio FTP

Se decide iniciar sesión en el servicio *FTP*, comprobando que permite acceso mediante el usuario ```anonymous```

<img width="718" height="411" alt="Pasted image 20260122105204" src="https://github.com/user-attachments/assets/6131768b-d328-46eb-aada-09c5d99ae737" />

Una vez autenticados, se descargan los archivos disponibles en el servidor y se procede a analizarlos en busca de información relevante. Sin embargo, no se encuentra contenido útil para avanzar en la explotación

## 🌐 Análisis del servicio web

Al visitar la página web alojada en el puerto 80, se observa contenido similar al encontrado previamente en el servicio *FTP*

<img width="1127" height="777" alt="Pasted image 20260122120111" src="https://github.com/user-attachments/assets/7a9e3d10-bfbb-482a-8320-34890e872a7a" />

Durante la navegación, se identifica un apartado para acceder al backend de la aplicación. Se accede correctamente, pero este no contiene información ni funcionalidades relevantes

<img width="590" height="273" alt="Pasted image 20260122120147" src="https://github.com/user-attachments/assets/c37a001b-8a9b-4eab-bda2-ba538a4cda54" />

Ante esto, se decide realizar fuzzing web, sin obtener resultados significativos

<img width="975" height="578" alt="Pasted image 20260122111638" src="https://github.com/user-attachments/assets/c0b80e3f-6bc9-4087-9063-ac2380f4a708" />

## Obtención de acceso inicial (Reverse Shell)

Dado que el servidor *FTP* permite escritura, se decide subir una *reverse shell* al sistema

Para ello, se genera un payload utilizando [Reverse Shell Generator](https://www.revshells.com/), el cual es subido al servidor mediante *FTP*

<img width="1103" height="736" alt="Pasted image 20260122120858" src="https://github.com/user-attachments/assets/fc3f0901-0cfc-4bd7-8025-7ff3bac4b7fe" />

Guardamos el archivo e iniciamos sesión for *FTP*

<img width="941" height="557" alt="Pasted image 20260122121323" src="https://github.com/user-attachments/assets/de6f9e58-b48f-48e4-b7f1-1fa205f7b318" />

Posteriormente, desde el navegador se accede al archivo subido mientras que, desde la máquina atacante, se establece un listener en espera

<img width="598" height="320" alt="Pasted image 20260122121504" src="https://github.com/user-attachments/assets/1d5760b8-366a-459c-9c9f-0030db70e105" />

De esta forma, se obtiene acceso al sistema como el usuario ```www-data```

<img width="696" height="200" alt="Pasted image 20260122121546" src="https://github.com/user-attachments/assets/eacefbd1-373f-4541-aa07-c5875febc284" />

Hacemos tratamiento de TTY

<img width="240" height="140" alt="Pasted image 20251222222913" src="https://github.com/user-attachments/assets/69af8aa6-45fe-4877-a54a-268443115aed" />

## Escalada de privilegios - Usuario Pingu

Al intentar escalar privilegios, se observa que el usuario ```www-data``` no cuenta con permisos sudo. Sin embargo, el usuario *pingu* puede ejecutar el binario **man**

<img width="990" height="148" alt="Pasted image 20260122122023" src="https://github.com/user-attachments/assets/0cfd8789-d4ae-4713-b511-dc5da2fdb25c" />

Se consulta [GTFOBins](https://gtfobins.org/) 

<img width="860" height="623" alt="Pasted image 20260122123037" src="https://github.com/user-attachments/assets/381ecca3-7138-45b6-ba77-bf1138986a35" />

Se ejecuta el siguiente comando:

```
sudo -u pingu man man

```

```
!/bin/bash
```

<img width="812" height="521" alt="Pasted image 20260122123316" src="https://github.com/user-attachments/assets/36109df6-c5a5-480a-bf64-6ca52f49ec6f" />

## Escalada de privilegios - Usuario Gladys

Continuando con la enumeración, se detecta que el usuario *gladys* puede ejecutar los binarios **nmap** y **dpkg**

<img width="965" height="221" alt="Pasted image 20260122123413" src="https://github.com/user-attachments/assets/582bbed2-e500-4857-bff3-b4e94667e3e1" />

Se consulta a [GTFOBins](https://gtfobins.org/) 

<img width="852" height="257" alt="Pasted image 20260122124516" src="https://github.com/user-attachments/assets/49b0e67b-e47c-4c33-b0b1-5165626555ea" />

Se ejecuta

```
sudo -u gladys dpkg -l
```

Y dentro del entorno:

```
!/bin/bash
```

<img width="365" height="93" alt="Pasted image 20260122124640" src="https://github.com/user-attachments/assets/55fd59b8-3eb6-426a-bd29-5b2831d43c41" />

Obteniendo acceso como el usuario **gladys**

## 👑 Escalada final 

Finalmente, se observa que el usuario **gladys** puede ejecutar el binario **chown** como **root** sin necesidad de contraseña.

<img width="950" height="180" alt="Pasted image 20260122124730" src="https://github.com/user-attachments/assets/969b2e4c-21e5-4718-8f00-a9c481fe5aa2" />

Se consulta a [GTFOBins](https://gtfobins.org/) 

<img width="847" height="567" alt="Pasted image 20260122125030" src="https://github.com/user-attachments/assets/fccb3729-1b5a-4a85-a99c-6e265cb28a3e" />

Se aprovecha esta configuración para cambiar la propiedad del archivo ```/etc/passwd```, convirtiéndonos en su propietario.

<img width="792" height="560" alt="Pasted image 20260122125503" src="https://github.com/user-attachments/assets/b5e60ef7-9c01-4ebf-96c4-22ad592bd462" />

Una vez con control sobre el archivo, se edita la entrada del usuario **root**, eliminando la ```x``` del campo de contraseña 

<img width="542" height="265" alt="Pasted image 20260122131229" src="https://github.com/user-attachments/assets/313e44f2-080d-4f0c-85bb-2ef570f89592" />

Esto permite iniciar sesión como **root** sin necesidad de proporcionar credenciales

<img width="805" height="76" alt="Pasted image 20260122133238" src="https://github.com/user-attachments/assets/07c7e085-16f2-49e3-a3d7-fa2a4ba05664" />

Con esto, se obtiene acceso completo como **root**

## 🏁 Conclusión 

El laboratorio consistió en comprometer un sistema mediante la explotación de servicios mal configurados (FTP anonymous) y el abuso de permisos de sudo. Tras obtener acceso inicial y realizar movimiento lateral, se aprovecharon funciones específicas de binarios legítimos mediante GTFOBins para forzar una escalada de privilegios exitosa hasta el control total como root
