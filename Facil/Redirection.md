# Redirection

## 🔎 Enumeración

Se realiza un escaneo de puertos utilizando Nmap, identificando los siguientes servicios abiertos:

* 22/tcp – SSH
* 80/tcp – HTTP

<img width="928" height="295" alt="Pasted image 20260310120225" src="https://github.com/user-attachments/assets/fdc7152d-48ac-4ed8-8063-fb10ff74a9d7" />

## 🌐 Análisis del servicio web

Se visita la página web, la cual contiene varios laboratorios para practicar la vulnerabilidad **Open Redirect**

<img width="943" height="498" alt="Pasted image 20260310120352" src="https://github.com/user-attachments/assets/bcec5835-3bfe-4c12-91ca-cee7f69d3351" />

```
🧠 ¿Qué es un Open Redirect?

Un Open Redirect es una vulnerabilidad donde una aplicación permite redirigir a un usuario a cualquier URL externa
mediante un parámetro controlado por el usuario, sin validación adecuada.

Ejemplo típico:

redirect.php?url=DESTINO

Donde:

* redirect.php → script que realiza la redirección
* url → parámetro manipulable por el usuario

 ⚠️ ¿Por qué es peligroso?

Esta vulnerabilidad puede ser utilizada para:

🎣 Phishing (redirecciones a sitios falsos)
🦠 Distribución de malware
🔓 Bypass de filtros de seguridad
🔑 Robo de tokens (OAuth, sesiones, etc.)
```

## 🧪 Laboratorio 1 – Open Redirect básico

<img width="952" height="802" alt="Pasted image 20260310120512" src="https://github.com/user-attachments/assets/7f0c54a6-8a8c-4e44-866f-9f02106704fb" />

Al pasar el cursor sobre un botón, se observa que redirige a Google

<img width="1057" height="461" alt="Pasted image 20260310121838" src="https://github.com/user-attachments/assets/1135afc4-243a-46dc-8393-955b88603262" />

```
Analizando la URL:

redirect.php?url=https://google.com
```

Se identifica que el parámetro url controla la redirección

Modificando este parámetro, es posible redirigir a cualquier sitio externo

<img width="633" height="250" alt="Pasted image 20260310124414" src="https://github.com/user-attachments/assets/6cc5745c-c109-4772-a926-86c1626364a0" />

```💡 Nota: Este tipo de pruebas también pueden automatizarse con herramientas como Burp Suite ```

## 🧪 Laboratorio 2 – Bypass de validación

<img width="1243" height="577" alt="Pasted image 20260310123412" src="https://github.com/user-attachments/assets/3a0456cb-ceae-45a2-91fa-f41059b89e94" />

En este caso, el sistema intenta restringir redirecciones externas

<img width="1212" height="512" alt="Pasted image 20260310125606" src="https://github.com/user-attachments/assets/3f0388dc-0740-4f5c-9595-cf10433b0a10" />

<img width="561" height="196" alt="Pasted image 20260310125741" src="https://github.com/user-attachments/assets/b8f60898-8a6d-46cd-ba24-8bee02aa0627" />

Sin embargo, es posible evadir la validación utilizando el símbolo: ```@```

```
Ejemplo:

https://google.com@dockerlabs.es
```

```
🧠 Explicación técnica

El símbolo @ en URLs separa:

usuario@host

Por lo tanto:

google.com → es interpretado como usuario
dockerlabs.es → es el host real

👉 El navegador ignora la parte visual engañosa y conecta realmente al host final.
```

Esto permite engañar tanto al usuario como a validaciones débiles del servidor

<img width="1567" height="546" alt="Pasted image 20260310130245" src="https://github.com/user-attachments/assets/069b9479-fca2-4f3e-b834-60f8bf6797fb" />

## 🧪 Laboratorio 3 – Bypass mediante subdominios

<img width="1235" height="552" alt="Pasted image 20260310153742" src="https://github.com/user-attachments/assets/cc918a82-a744-4ea1-a560-2cdd83479437" />

En este laboratorio, la validación es más estricta

<img width="1047" height="207" alt="Pasted image 20260310153916" src="https://github.com/user-attachments/assets/2b2eb12a-6b97-42d7-bf28-6d901ba848a1" />

Sin embargo, se logra evadir utilizando un dominio que aparenta ser válido:

```bash
http://172.17.0.2/laboratorio3/redirect.php?url=https://dockerlabs.es.google.com
```

```
🧠 Explicación técnica

Aquí se crea un subdominio engañoso:

dockerlabs.es.google.com

Muchos sistemas validan si la URL contiene o termina en un dominio permitido (ej: google.com), pero no verifican correctamente el dominio raíz.

En realidad, el dominio pertenece a google.com, pero el atacante puede manipular estructuras similares en escenarios reales.
```
<img width="1085" height="607" alt="Pasted image 20260310154431" src="https://github.com/user-attachments/assets/de7b9810-5753-4a0c-b86d-c9c74d2c0b66" />

## 🔑 Acceso por SSH

Una vez completados los laboratorios, se procede a iniciar sesión por SSH

<img width="368" height="220" alt="Pasted image 20260310154826" src="https://github.com/user-attachments/assets/f6e8709c-4a5a-4e80-8027-036f5b101d54" />

# Escalada de privilegios 

Se intenta escalar privilegios, pero:

* No se tienen permisos elevados

<img width="592" height="151" alt="Pasted image 20260310155630" src="https://github.com/user-attachments/assets/4c2c7e5c-e596-45f6-ab5a-607ba9447d32" />

* No se encuentran binarios SUID relevantes

<img width="618" height="235" alt="Pasted image 20260310155710" src="https://github.com/user-attachments/assets/a35ade08-af39-4071-922e-8fb331ab566f" />

Durante la enumeración, en el directorio raíz se encuentra un archivo: ```.bak```

<img width="652" height="517" alt="Pasted image 20260310160134" src="https://github.com/user-attachments/assets/0404b352-3045-469d-bf0d-1b6d8780dc26" />

Este archivo contiene credenciales en texto plano del usuario: **balulito**

Se cambia a dicho usuario

Como usuario **balulito**, se detecta un binario que puede ejecutarse como **root**

<img width="1192" height="138" alt="Pasted image 20260310160812" src="https://github.com/user-attachments/assets/f2c56969-4739-45d5-99a0-6f30572d84cb" />

Se consulta [GTFOBins](https://gtfobins.org/) para encontrar una técnica de explotación

<img width="793" height="262" alt="Pasted image 20260310171326" src="https://github.com/user-attachments/assets/304c6da9-2006-4044-8730-2290f6e97fc8" />

Se identifica que es posible leer el archivo ```/etc/passwd```

```
🧠 Abuso de /etc/passwd

Se copia el contenido de /etc/passwd 

Luego se modifica eliminando la x del campo de contraseña:

root::0:0:root:/root:/bin/bash
```

<img width="551" height="205" alt="Pasted image 20260310162420" src="https://github.com/user-attachments/assets/1cedc9f5-e4c9-425f-b780-3862715d0483" />

```
📌 Explicación

En sistemas Linux:

La x indica que la contraseña está almacenada en /etc/shadow
Si se elimina, se puede permitir acceso sin contraseña (dependiendo del contexto)
```

## 👑 Escalada final

Se adapta el payload de GTFOBins al contexto del sistema

Finalmente, se ejecuta el binario con privilegios elevados, logrando acceso como **root**

<img width="718" height="115" alt="Pasted image 20260310170028" src="https://github.com/user-attachments/assets/13012f6f-0436-451f-90e1-88fa9d869e1f" />

## 🏁 Conclusión

Este laboratorio es una gran lección sobre por qué no debemos subestimar los errores "pequeños": nos muestra cómo un simple Open Redirect, que a menudo parece inofensivo, puede ser la puerta de entrada para un desastre mayor si se combina con un poco de ingeniería social. El ejercicio conecta los puntos entre el engaño al usuario, el robo de credenciales y los descuidos en los permisos de Linux, demostrando que la seguridad no falla por un solo agujero, sino por una cadena de debilidades que, juntas, le entregan las llaves del reino a un atacante.
