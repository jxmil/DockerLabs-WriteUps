# Backend

## 🔎 Enumeración

Se realiza un escaneo de puertos utilizando **Nmap**, donde se identifican los siguientes servicios abiertos:
* 22/tcp – SSH
* 80/tcp – HTTP

<img width="927" height="282" alt="Pasted image 20260224105807" src="https://github.com/user-attachments/assets/5e4ce7c6-60b0-43e4-b83b-8624e5e86972" />

## 🌐 Análisis Web

Al visitar la página web, nos dirigimos directamente al apartado de login

<img width="1712" height="652" alt="Pasted image 20260224105917" src="https://github.com/user-attachments/assets/3ba06e76-0dcb-4fca-b323-cdadbee44aba" />

Se prueban credenciales por defecto sin éxito. Posteriormente, se intenta una prueba básica de inyección SQL introduciendo una comilla simple ```'```, confirmando que el campo es vulnerable

<img width="1873" height="72" alt="Pasted image 20260224111123" src="https://github.com/user-attachments/assets/6e7bb96e-7f29-42b7-8cc6-9a08d1bf01ad" />

## 💉 Explotación SQL 

Se hace uso de sqlmap para automatizar la explotación

```bash
sqlmap -u "http://172.17.0.2/login.html" --forms --batch --dbs
```

<img width="617" height="337" alt="Pasted image 20260224111931" src="https://github.com/user-attachments/assets/89cdf25d-6b4f-4d09-a716-3e41b59075e5" />

Enumeramos las tablas de la base de datos **users**:

```bash
sqlmap -u "http://172.17.0.2/login.html" --forms --batch -D users --tables
```

<img width="270" height="113" alt="Pasted image 20260224112252" src="https://github.com/user-attachments/assets/4c947e7d-9f94-45cf-b180-62c256ae85ef" />

Extraemos los usuarios de la tabla **usuarios**:

```bash
sqlmap -u "http://172.17.0.2/login.html" --forms --batch -D users -T usuarios --dump
```

<img width="400" height="202" alt="Pasted image 20260224112354" src="https://github.com/user-attachments/assets/50f5327b-7cc8-475d-9450-990eb0e99bb6" />

Obtenemos varias credenciales

## 🔐 Acceso Inicial

Se prueban las credenciales en el login web, pero redirige nuevamente al index; asi que decidimos probar por el servicio SSH 

Después de intentar con los usuarios obtenidos, logramos acceder exitosamente con el usuario **pepe**

<img width="696" height="123" alt="Pasted image 20260224112936" src="https://github.com/user-attachments/assets/4e32ae39-30c3-46a3-9d6d-89412ce044b7" />

## 👑 Escalada de Privilegios

Una vez dentro:

El comando ```sudo -l``` no está disponible ni ```sudo su```

<img width="387" height="117" alt="Pasted image 20260224113247" src="https://github.com/user-attachments/assets/99cdd1e3-1095-4139-ae8d-da29e7b02795" />

Procedemos a buscar binarios con permisos SUID:

```bash
find / -perm -4000 2>/dev/null
```

<img width="576" height="267" alt="Pasted image 20260224113506" src="https://github.com/user-attachments/assets/993104be-431d-4f21-ad61-fbfbade09c98" />

Llaman la atención los binarios **grep** y **ls**

Al explorar el sistema, encontramos que dentro del directorio ```/root``` existe un archivo accesible

<img width="336" height="63" alt="Pasted image 20260224113654" src="https://github.com/user-attachments/assets/be20fedc-2daa-48ae-a6d5-722e989620eb" />

Al leerlo, observamos que contiene una contraseña hasheada.

<img width="490" height="38" alt="Pasted image 20260224113753" src="https://github.com/user-attachments/assets/ddc2a460-0edc-4ab0-ba60-32b3dd3b543f" />

Se procede a crackear el hash utilizando una herramienta online [Crackstation](https://crackstation.net/)

<img width="1278" height="365" alt="Pasted image 20260224113936" src="https://github.com/user-attachments/assets/d44f7517-db55-47ff-be3d-83a71321c248" />

Una vez obtenida la contraseña en texto plano, cambiamos al usuario **root** con éxito

<img width="451" height="115" alt="Pasted image 20260224114025" src="https://github.com/user-attachments/assets/bcda8eb5-3d3c-4cbc-90c5-9f16e8d0d54e" />

## 🏁 Conclusión

Una validación deficiente en el login, explotada mediante sqlmap, permitió la extracción de credenciales y una posterior escalada de privilegios. Este incidente confirma que la gestión de hashes y la segmentación de permisos son vulnerabilidades críticas que comprometen la integridad total del sistema.
