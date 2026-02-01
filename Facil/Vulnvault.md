# Vulnvault

## 🔎 Enumeración

Se realiza un escaneo de puertos utilizando Nmap, donde se identifican los siguientes servicios abiertos:
* 22/tcp – SSH
* 80/tcp – HTTP

<img width="752" height="343" alt="Pasted image 20251219181745" src="https://github.com/user-attachments/assets/50b92112-262a-4671-88d7-01b7d6a0a95f" />

## 🌐 Análisis del servicio web

Se accede a la página web

<img width="873" height="431" alt="Pasted image 20251219181929" src="https://github.com/user-attachments/assets/8a5b34ec-9f0f-44c4-ba0f-7a975ff6cc33" />

Se realiza fuzzing de directorios con el objetivo de descubrir rutas ocultas. Durante este proceso se identifican varios directorios:

<img width="723" height="547" alt="Pasted image 20251219182508" src="https://github.com/user-attachments/assets/a6871eb9-2261-4c81-bd43-270aa1d99ad2" />

`/old: La pagina web sin estilo`<br>
`/upload: permite la subida de archivos`

<img width="782" height="501" alt="Pasted image 20251219182556" src="https://github.com/user-attachments/assets/5ef0e874-ccfb-465d-be89-dc39b42bf457" />

Se intenta subir un archivo y posteriormente localizar su ruta dentro del servidor, pero no es posible identificar dónde queda almacenado.

<img width="1117" height="420" alt="Pasted image 20251219182820" src="https://github.com/user-attachments/assets/64414a35-a176-4b3f-ab19-a66d25301f2b" />

## ⚠️ Ejecución remota de comandos (RCE)

Al regresar al generador de reportes de la aplicación, se identifica que el parámetro es vulnerable a **Remote Command Execution** `(RCE)`.

Para confirmarlo, se prueba un payload simple:<br>
`; id`

<img width="513" height="111" alt="Pasted image 20251219190438" src="https://github.com/user-attachments/assets/1146525f-57de-4d97-b7fe-e23b3779b1c6" />

El comando se ejecuta correctamente, confirmando la vulnerabilidad.

A partir de esto, se aprovecha la **RCE** para enumerar usuarios del sistema, identificando al usuario **Samara**.

<img width="635" height="445" alt="Pasted image 20251219192920" src="https://github.com/user-attachments/assets/671b1ac8-0762-4aea-9c2b-922cf5f37cc7" />

## 🔐 Obtención de acceso por SSH

Inicialmente se considera un ataque de **fuerza bruta** contra SSH, pero no se obtiene éxito. En su lugar, se aprovecha la `RCE` para obtener la clave privada SSH del usuario **Samara**.

<img width="538" height="148" alt="Pasted image 20251219193325" src="https://github.com/user-attachments/assets/7c6166b1-b798-4473-bedc-89bf0ae60f6b" />

Con esta clave, se logra iniciar sesión por SSH en el sistema.

<img width="793" height="377" alt="Pasted image 20251219193648" src="https://github.com/user-attachments/assets/ddaf8efa-b907-48e1-a3dc-854bd2a3a0cc" />

## 🔐  Escalada de privilegios

Se verifica que el usuario no cuenta con permisos sudo

<img width="372" height="78" alt="Pasted image 20251219203948" src="https://github.com/user-attachments/assets/a0051322-c90e-4de3-96ab-6b3115529e22" />

Se revisan binarios con permisos especiales, sin encontrar resultados relevantes

<img width="590" height="208" alt="Pasted image 20251219205900" src="https://github.com/user-attachments/assets/47f5c1ac-2cc2-4cf9-b230-1b2b8a12f2c6" />

Posteriormente, se revisan los procesos activos del sistema 

<img width="1310" height="420" alt="Pasted image 20251219205307" src="https://github.com/user-attachments/assets/c86ed5bd-6250-4aea-8cd4-896f5d5f2e55" />

Se identifica un proceso que se ejecuta de forma repetitiva

<img width="1022" height="22" alt="Pasted image 20251219205419" src="https://github.com/user-attachments/assets/6e379075-26bb-4d92-9739-2869a6be93f1" />

Al analizar el archivo asociado a dicho proceso, se descubre que puede ser modificado. 

<img width="807" height="80" alt="Pasted image 20251219205459" src="https://github.com/user-attachments/assets/4302da9c-6d71-4247-89ac-1b2807062f78" />

Se edita el archivo para eliminar la contraseña del usuario root.

<img width="716" height="22" alt="Pasted image 20251219205804" src="https://github.com/user-attachments/assets/34003d41-1f8f-46f9-a143-25174c2294ae" />

Tras ejecutar nuevamente el proceso, es posible cambiar al usuario root sin necesidad de contraseña

<img width="442" height="55" alt="Pasted image 20251219210125" src="https://github.com/user-attachments/assets/75a901aa-edff-47ef-a8bf-c03d5a2709f4" />

## 🏁 Conclusión

Este laboratorio demuestra cómo una vulnerabilidad de ejecución remota de comandos puede ser utilizada para realizar una enumeración completa del sistema y, combinada con procesos mal configurados, llevar a una escalada total de privilegios.
