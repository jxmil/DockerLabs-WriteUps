# Winterfell

## 🔎 Enumeración

Se realiza un escaneo de puertos utilizando **Nmap**, donde se identifican los siguientes servicios abiertos:
* 22/tcp – SSH
* 80/tcp – HTTP

<img width="840" height="670" alt="Pasted image 20260203122622" src="https://github.com/user-attachments/assets/3e27cbe8-fab0-43bc-923c-b3c4d1242317" />

Tras un segundo escaneo más detallado, se confirmaron los recursos compartidos disponibles en el sistema(puertos asociados a **SMB**)

<img width="446" height="126" alt="Pasted image 20260203122850" src="https://github.com/user-attachments/assets/c1b8edf3-f934-456d-bc04-bb02b59a8f8b" />

## 🌐 Enumeración Web

Al visitar la página web se identificó un posible usuario llamado ```jon```

<img width="992" height="907" alt="Pasted image 20260203120612" src="https://github.com/user-attachments/assets/925ef41a-d36d-434f-b023-a4688312bdd4" />

Mediante inspección del código fuente también aparecieron otros nombres relevantes: ```Arya``` y ```Daenerys```

<img width="1623" height="138" alt="Pasted image 20260203122302" src="https://github.com/user-attachments/assets/cfd20e5f-d741-4687-b5ab-995d296d2839" />

Se ejecutó fuzzing web

<img width="837" height="141" alt="Pasted image 20260203121151" src="https://github.com/user-attachments/assets/33599552-3042-4b9a-a28c-6e765fc12a3c" />

Se encontró un directorio con un archivo que listaba episodios de una serie, lo que permitió construir un diccionario de posibles usuarios y contraseñas basados en esos nombres

<img width="653" height="300" alt="Pasted image 20260203120837" src="https://github.com/user-attachments/assets/576ed546-1106-418a-ab42-eb3ebfbfc1cc" />

<img width="1206" height="402" alt="Pasted image 20260203121029" src="https://github.com/user-attachments/assets/13ca6ddf-c806-4e32-903c-10969ee51f33" />

```Usuarios:```

<img width="291" height="136" alt="Pasted image 20260203124736" src="https://github.com/user-attachments/assets/f029e4bf-cccd-406f-b5c9-8380f2a43db1" />

```Contraseñas:```

<img width="357" height="205" alt="Pasted image 20260204102739" src="https://github.com/user-attachments/assets/82bd9de0-d021-4871-bd57-60a775679ffa" />

 ## 📂 Enumeración SMB

Procedemos a usar **smbclient** y vemos que hay un recurso **shared** que no sugiere autentificación

<img width="1158" height="256" alt="Pasted image 20260203124038" src="https://github.com/user-attachments/assets/ba9ade54-889e-4402-a7cb-9cc78012d02e" />

Con ```enum4linux``` se enumeraron los usuarios del sistema, confirmando varias cuentas válidas

<img width="772" height="112" alt="Pasted image 20260203141708" src="https://github.com/user-attachments/assets/9e36ef56-e9ed-4425-853f-2b84ca25922f" />

Realizaremos un ataque por fuerza bruta utilizando los diccionarios previamente guardados y utilizaremos la herramienta ```crackmapexec```

```bash
crackmapexec smb 172.17.0.2 -u users.txt -p passwd.txt
```

Logrando identificar credenciales válidas: ```jon:seacercaelinvierno```

Posteriormente, con **smbmap** se verificaron permisos de lectura 

```bash
smbmap -H 172.17.0.2 -u "jon" -p "seacercaelinvierno"
```

<img width="1147" height="242" alt="Pasted image 20260204103224" src="https://github.com/user-attachments/assets/d4725e56-fe29-44f2-ae40-6521a42f3300" />

Se accedió con **smbclient** y descargó el archivo

<img width="942" height="447" alt="Pasted image 20260204103407" src="https://github.com/user-attachments/assets/e08154fd-de00-41b9-810c-e2d6524d0be9" />

<img width="1260" height="177" alt="Pasted image 20260204103516" src="https://github.com/user-attachments/assets/cb57d245-4f0e-424f-8fd0-9ddb28258aa6" />

Leemos el archivo y vemos que nos pide decodificar una contraseña

<img width="1251" height="167" alt="Pasted image 20260204103632" src="https://github.com/user-attachments/assets/44194e1b-cfff-4fe9-bda0-840839b4e198" />

Decodificamos la contraseña usando IA

<img width="300" height="147" alt="Pasted image 20260204103852" src="https://github.com/user-attachments/assets/65d7a448-d1d5-4f55-84f1-5a2bd2cd4cb6" />

## Movimiento Lateral

Se inició sesion por ssh

<img width="283" height="337" alt="Pasted image 20260204104032" src="https://github.com/user-attachments/assets/c680c97f-cbc4-4248-b2c9-5a529bad1f89" />

Listamos que hay en el directorio actual y nos topamos con un mensaje

<img width="1156" height="251" alt="Pasted image 20260204104253" src="https://github.com/user-attachments/assets/873a13f4-2431-4b3d-8ca3-a1f5ac67ca82" />

Intentamos escalar privilegios, pero vemos que hay un archivo en **python** que se puede ejecutar como el usuario ```Aira``` sin contraseña

<img width="1187" height="117" alt="Pasted image 20260204104146" src="https://github.com/user-attachments/assets/a9720c40-87c6-4b63-9e93-724e0cd6fc1e" />

Leemos el archivo

<img width="927" height="488" alt="Pasted image 20260204104556" src="https://github.com/user-attachments/assets/9957aec9-c135-4dcd-b657-516d2f141321" />

Eliminamos el archivo y lo reemplazamos por un nuevo, dentro de ello:

<img width="482" height="32" alt="Pasted image 20260204113336" src="https://github.com/user-attachments/assets/03436191-8147-4734-bb37-4a47ad34514a" />
<br>
<img width="281" height="106" alt="Pasted image 20260204105537" src="https://github.com/user-attachments/assets/dd0daa05-52c4-4a9f-965d-f43831a801dd" />

Nos cambiamos al usuario ```Aira``` con éxito

Se detectó la posibilidad de ejecutar ciertos binarios como ```Daenerys```, lo que permitió continuar el movimiento lateral

<img width="1201" height="117" alt="Pasted image 20260204113441" src="https://github.com/user-attachments/assets/fa30db28-72ed-46ea-ad98-8e4e6d667b54" />

Se encontró un mensaje con credenciales en texto plano (ajustando el formato correspondiente)

<img width="883" height="168" alt="Pasted image 20260204113830" src="https://github.com/user-attachments/assets/5380bc24-d934-47f9-acb7-0040026148ac" />

Leemos

<img width="1492" height="97" alt="Pasted image 20260204114003" src="https://github.com/user-attachments/assets/0c9aaff0-4ce7-43a3-98c8-1278da259cb2" />

Nos deja su contraseña en texto plano y procedemos a ser el usuario ```Daenerys```

<img width="532" height="73" alt="Pasted image 20260204114554" src="https://github.com/user-attachments/assets/c6ff9811-7b24-4aaf-a9a8-320b53a34675" />

Intentamos escalar privilegios, pero vemos que hay un archivo que se ejecuta como **root** sin contraseña

<img width="1156" height="120" alt="Pasted image 20260204114726" src="https://github.com/user-attachments/assets/b9653ac4-2791-41b7-aca3-08495a8aada9" />

Leemos y el archivo 

<img width="562" height="75" alt="Pasted image 20260204115225" src="https://github.com/user-attachments/assets/70b3fce8-cc3c-466e-bb99-788ecec3ab07" />

Procedemos a modificarlo

<img width="288" height="111" alt="Pasted image 20260204115157" src="https://github.com/user-attachments/assets/4c603c27-6da2-407d-ad87-65eb0599aa2a" />

Ejecutamos y somos **root**

<img width="910" height="151" alt="Pasted image 20260204115431" src="https://github.com/user-attachments/assets/3e5f9d0d-ef5c-427f-94a2-a1d02dd4ea3b" />

## 🏁 Conclusión

Este laboratorio demuestra cómo una correcta enumeración de servicios SMB, combinada con el análisis de información expuesta en la web y configuraciones inseguras de permisos, puede facilitar el movimiento lateral y culminar en una escalada de privilegios exitosa hasta root.
