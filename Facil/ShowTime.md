# ShowTime

## 🔎 Enumeración

Se realiza un escaneo de puertos utilizando Nmap, identificando los siguientes servicios abiertos:

* 22/tcp – SSH
* 80/tcp – HTTP

<img width="843" height="337" alt="Pasted image 20260305122137" src="https://github.com/user-attachments/assets/6f7871c9-c0fa-43f6-bf85-c3b0a754a7f5" />

## 🌐 Análisis del servicio web

Se visita la página web y se accede al login

<img width="1028" height="782" alt="Pasted image 20260305122219" src="https://github.com/user-attachments/assets/ff9cf6d1-7ec3-43bc-acf2-f496dc179207" />

Se prueban credenciales por defecto sin éxito, por lo que se realiza una **inyección SQL**, logrando acceder al sistema

<img width="426" height="372" alt="Pasted image 20260305123013" src="https://github.com/user-attachments/assets/8a1e6db2-045a-4da5-ac2e-9389fc0b78c0" />
<br>

<img width="372" height="300" alt="Pasted image 20260305122856" src="https://github.com/user-attachments/assets/9d773807-4fd1-4ca1-8a3b-88cd6608c698" />

Sin embargo, el panel solo muestra un mensaje de bienvenida, por lo que se decide profundizar utilizando **SQLMap**

## 🧪 Explotación con SQLMap

Se enumeran las bases de datos:

```bash
sqlmap --url http://172.17.0.2/login_page/ --dbs --batch --forms
```

<img width="320" height="123" alt="Pasted image 20260305124652" src="https://github.com/user-attachments/assets/a383be9d-2538-4405-ab43-483d80cd17fa" />

Se identifican múltiples bases de datos

Luego se listan las tablas de la base de datos users:

```bash
sqlmap -u "http://172.17.0.2" --forms --batch -D users --tables
```

<img width="232" height="102" alt="Pasted image 20260305124940" src="https://github.com/user-attachments/assets/d3a52a80-dcad-4e85-b94f-435e0d9b3b9b" />

Finalmente, se extraen los datos:

```bash
sqlmap -u "http://172.17.0.2/login_page/index.php" --forms --batch -D users --dump
```

<img width="477" height="190" alt="Pasted image 20260305125514" src="https://github.com/user-attachments/assets/659a956b-84b0-4c41-84db-2e1548481709" />

Se obtienen varios usuarios y credenciales

## 🔑 Acceso al panel

Se prueban los usuarios obtenidos, logrando acceso al panel de administración con el usuario: ```Joe```

<img width="512" height="293" alt="Pasted image 20260305125713" src="https://github.com/user-attachments/assets/88853f87-1e6c-4114-81f0-ff8fb6e0e401" />

Dentro del panel se identifica que es posible ejecutar código en **Python**

## Obtención de acceso inicial

Se prepara una reverse shell en **Python**, se ejecuta desde el panel y se obtiene acceso a la máquina

<img width="596" height="362" alt="Pasted image 20260305130136" src="https://github.com/user-attachments/assets/e213852f-60be-419d-8956-bb1405188cd2" />

<img width="607" height="92" alt="Pasted image 20260305130239" src="https://github.com/user-attachments/assets/d2d3ec5a-bbf8-4ce7-ac40-44ccb70ccdfc" />

Hacemos tratamiento de TTY

<img width="240" height="140" alt="Pasted image 20260305130441" src="https://github.com/user-attachments/assets/57ac6ec7-57a7-4ec4-a1f4-f7aec4a491c5" />

## Escalada de privilegios 

Una vez dentro, se intenta escalar privilegios, pero se requiere contraseña

<img width="646" height="251" alt="Pasted image 20260305130458" src="https://github.com/user-attachments/assets/730b64f7-069b-44cd-8205-4b5f23024fb8" />

Se buscan binarios con permisos elevados, sin resultados relevantes

<img width="823" height="262" alt="Pasted image 20260305130558" src="https://github.com/user-attachments/assets/ae2a0f5c-af1f-4753-812c-7b0ca923c0f6" />

Durante la enumeración, en el directorio: ```/tmp``` se encuentra un archivo oculto con una lista de posibles contraseñas

<img width="670" height="247" alt="Pasted image 20260305131522" src="https://github.com/user-attachments/assets/033eaee0-ee11-4525-be7b-4fe9a73f0254" />

También se identifican usuarios del sistema

<img width="335" height="38" alt="Pasted image 20260305131750" src="https://github.com/user-attachments/assets/894d5c30-42a8-464c-8cbc-6531a7401739" />

Se preparan archivos para realizar fuerza bruta al servicio SSH

```
⚠️ Importante: convertir las contraseñas a minúsculas:

tr '[:upper:]' '[:lower:]' < archivo.txt > archivo_minusculas.txt
```

<img width="962" height="87" alt="Pasted image 20260305132339" src="https://github.com/user-attachments/assets/e693ea5e-1a21-496f-b1a7-1b556206c88a" />

Se obtienen credenciales válidas

##  Movimiento lateral

Se accede por SSH y se identifica que el usuario **luciano** puede ejecutar un binario sin necesidad de contraseña

<img width="657" height="123" alt="Pasted image 20260305133029" src="https://github.com/user-attachments/assets/c004b6ca-7805-49c6-8bc3-119e19d99930" />

Se consulta [GTFOBins](https://gtfobins.org/) y se explota el binario, logrando cambiar al usuario **luciano**

<img width="862" height="388" alt="Pasted image 20260305132936" src="https://github.com/user-attachments/assets/4be3cb30-38b6-4208-86b5-c19bd7ad0218" />

<img width="500" height="62" alt="Pasted image 20260305133314" src="https://github.com/user-attachments/assets/c3d3bf49-b283-4ddc-a4db-ab6479ffb6e9" />

## 👑 Escalada de privilegios final

Se continúa con la enumeración y se detecta otro binario que puede ejecutarse como **root** sin contraseña

<img width="642" height="143" alt="Pasted image 20260305133211" src="https://github.com/user-attachments/assets/38875ec8-392c-476e-8ce8-1c4b7c69c2ad" />

Se modifica el comportamiento del binario para ejecutar comandos con privilegios elevados

<img width="692" height="220" alt="Pasted image 20260305133505" src="https://github.com/user-attachments/assets/8edbfced-694a-45de-87e7-82a3621eb7f8" />

Finalmente, se ejecuta mediante sudo, obteniendo acceso como **root** 

<img width="677" height="107" alt="Pasted image 20260305133607" src="https://github.com/user-attachments/assets/0f551c46-dcb4-488c-9c9d-071219a898c4" />

## 🏁 Conclusión

Este laboratorio demuestra cómo una vulnerabilidad de inyección SQL, combinada con una mala gestión de credenciales, archivos sensibles expuestos y configuraciones inseguras en binarios, puede permitir comprometer completamente el sistema.
