# Library

## 🔎 Enumeración

Se realiza un escaneo de puertos con **Nmap**, identificando los puertos:
* 22/tcp – SSH
* 80/tcp – HTTP

<img width="751" height="362" alt="Pasted image 20260219110446" src="https://github.com/user-attachments/assets/dab6895b-d167-4c84-8bf2-42bb70726ddc" />

Con esto confirmado, se procede al análisis del servicio web

## 🌐 Análisis Web

Al visitar la página principal, aparentemente no se observa nada relevante a simple vista

<img width="797" height="831" alt="Pasted image 20260219110546" src="https://github.com/user-attachments/assets/b6f3f4eb-63d9-4867-bd56-adfd22541223" />

Se realiza fuzzing web para descubrir rutas ocultas y directorios interesantes. Durante esta fase, llama la atención el archivo:

```index.php```

<img width="891" height="200" alt="Pasted image 20260219110957" src="https://github.com/user-attachments/assets/bd698d7e-a0ac-4b5a-8f6b-3b06ca98a8b3" />

Tras analizarlo más a fondo, se identifica que podría estar relacionado con una posible contraseña o pista útil para el acceso

<img width="617" height="130" alt="Pasted image 20260219110815" src="https://github.com/user-attachments/assets/f02893a1-98a0-45f8-9766-cdba331f04e6" />

## 🔑 Acceso Inicial

Sabiendo que el puerto 22 (SSH) está abierto, se decide realizar un ataque de fuerza bruta contra el servicio utilizando las credenciales obtenidas previamente

<img width="747" height="45" alt="Pasted image 20260219111131" src="https://github.com/user-attachments/assets/77e5fb33-7c7b-4664-ace2-7a72cad23e00" />

El ataque tiene éxito y se logra iniciar sesión vía SSH

Hacemos tratamiento de TTY

<img width="240" height="140" alt="Pasted image 20251222222913" src="https://github.com/user-attachments/assets/9dac0485-edb2-400f-8c1b-8760421b47eb" />

## 👑 Escalada de Privilegios

Una vez dentro del sistema, se realiza la enumeración para identificar posibles vectores de escalada

<img width="1353" height="120" alt="Pasted image 20260219111324" src="https://github.com/user-attachments/assets/2e78a3bf-a170-40a5-b6a7-8032bba862e8" />

Se detecta un binario en **Python** que puede ejecutarse con privilegios elevados sin necesidad de contraseña

Aprovechando esta mala configuración :

* Se elimina el archivo original

<img width="600" height="141" alt="Pasted image 20260219112220" src="https://github.com/user-attachments/assets/6e8a8aff-da20-4273-9259-3c3f14c8d3ff" />

* Se reemplaza por uno modificado con código que permita ejecutar una shell privilegiada
  
<img width="1025" height="103" alt="Pasted image 20260219112046" src="https://github.com/user-attachments/assets/9faee9c6-3332-4fa3-a437-c995a57c70d3" />

* Se ejecuta el binario con privilegios elevados y se logra ser **root**
  
<img width="652" height="95" alt="Pasted image 20260219112156" src="https://github.com/user-attachments/assets/5f0d63a1-b474-437f-bd87-4434ed953265" />

## 🏁 Conclusión

Este escenario demuestra cómo una combinación de servicios expuestos, credenciales débiles y configuraciones inseguras en binarios con privilegios elevados puede derivar en un compromiso total del sistema. Más que una sola vulnerabilidad crítica, fue la suma de pequeños errores lo que permitió escalar hasta root.
