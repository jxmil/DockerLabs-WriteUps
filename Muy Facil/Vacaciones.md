# VACACIONES
## 🔎 Enumeración

Se realiza un escaneo inicial de puertos para identificar servicios expuestos.
<br>
Como resultado, se detectan los siguientes puertos abiertos:

<img width="1057" height="391" alt="Pasted image 20251217110508" src="https://github.com/user-attachments/assets/d5badccc-0f45-480b-820c-a70fec58bcba" />

Al acceder al servicio web, se observa una página aparentemente en blanco. Sin embargo, al inspeccionar el código fuente se identifica un mensaje oculto, el cual revela la existencia de los usuarios Juan y Camilo

<img width="683" height="427" alt="Pasted image 20251217110733" src="https://github.com/user-attachments/assets/d40fdf7d-0e3a-4fb0-8dcd-2d540b3674b8" />

Al revisar nuevamente los resultados de Nmap, se identifica que el servicio SSH utiliza una versión antigua, vulnerable a enumeración de usuarios, lo cual amplía la superficie de ataque.

## ⚔️ Explotación

Debido a la posibilidad de enumeración de usuarios en SSH, se decide realizar un ataque de fuerza bruta controlado utilizando Hydra, confirmando la validez del usuario Camilo y obteniendo credenciales válidas.

<img width="871" height="587" alt="Pasted image 20251217111857" src="https://github.com/user-attachments/assets/0f7fcb16-e471-4744-8233-176fe695eff6" />

Con las credenciales obtenidas, se establece conexión al sistema mediante SSH.

<img width="757" height="287" alt="Pasted image 20251217112209" src="https://github.com/user-attachments/assets/3a6d8b6e-95d2-4307-8bed-72ce5c44c51b" />
<br>

## 🔍 Post-explotación

Una vez dentro del sistema, se enumeran los usuarios locales y se identifica la existencia de múltiples cuentas.

<img width="225" height="40" alt="Pasted image 20251217112336" src="https://github.com/user-attachments/assets/3b2ca625-658a-406a-9e8c-356cc4b7c533" />

Se intenta realizar una escalada de privilegios con el usuario Camilo, sin éxito, ya que no cuenta con permisos elevados ni binarios con privilegios

<img width="702" height="77" alt="Pasted image 20251217112601" src="https://github.com/user-attachments/assets/48b07abc-cf4d-46f8-b77f-03bb08044d28" />

## 🔁 Nueva Enumeración (Pivoting)

Se vuelve a analizar el servicio web y se recuerda un mensaje donde Juan había enviado un correo a Camilo, lo que sugiere la posible reutilización de credenciales.

<img width="641" height="457" alt="Pasted image 20251217114954" src="https://github.com/user-attachments/assets/083bba62-46f5-411d-8979-bda195294969" />

Con las credenciales obtenidas, se establece conexión al sistema mediante SSH

<img width="787" height="158" alt="Pasted image 20251217115246" src="https://github.com/user-attachments/assets/5be2f1f0-b536-4282-8726-54253bf34ece" />

Se procede a iniciar sesión como el usuario Juan, logrando acceso exitoso.

## 🔐 Escalada de Privilegios

Al verificar los privilegios de Juan, se identifica que puede ejecutar un binario como root sin necesidad de contraseña.
<img width="908" height="153" alt="Pasted image 20251217121739" src="https://github.com/user-attachments/assets/eb3a3dbe-2913-4c3c-ba82-b76594226a23" />

Se consulta GTFOBins para identificar una técnica válida de escalada de privilegios asociada a dicho binario.
<img width="833" height="167" alt="Pasted image 20251217121900" src="https://github.com/user-attachments/assets/4caef91e-0914-4232-914c-0a72768d5e1e" />

Tras ejecutar el método correspondiente, se obtiene acceso root de forma exitosa.

<img width="386" height="92" alt="Pasted image 20251217122032" src="https://github.com/user-attachments/assets/dbba430b-6578-4a7a-9df9-6f86387ff8ff" />



