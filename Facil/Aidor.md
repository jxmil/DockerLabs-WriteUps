# Aidor 

## 🔎 Enumeración

Se realiza un escaneo de puertos utilizando la herramienta Nmap, identificando los siguientes servicios expuestos:
* 22/tcp – SSH
* 5000/tcp – HTTP

<img width="846" height="190" alt="Pasted image 20260102161354" src="https://github.com/user-attachments/assets/a6d566c0-ed48-4757-b436-03f77d0fc5da" />

## 🌐 Análisis de la aplicación web

Al acceder a la aplicación web, se identifica un formulario de login, por lo que se procede a crear una cuenta para poder interactuar con la aplicación.

<img width="525" height="630" alt="Pasted image 20260102161454" src="https://github.com/user-attachments/assets/7f3d6d82-4076-440d-a00f-55e732505fbf" />

<img width="310" height="546" alt="Pasted image 20260102162210" src="https://github.com/user-attachments/assets/073edadf-f4b4-4448-b721-7c6106a5065d" />

A simple vista, la web no ofrece muchas funcionalidades, por lo que se decide observar el comportamiento de la URL. En este punto, se nota que el perfil del usuario utiliza un parámetro id, el cual en mi caso era id=55.

<img width="957" height="637" alt="Pasted image 20260102162234" src="https://github.com/user-attachments/assets/18bfd01e-c988-42c0-b1be-b703cd9890b4" />

## ⚠️ Vulnerabilidad IDOR

Al modificar manualmente el valor del parámetro id en la URL, se observa que la aplicación muestra información de otros usuarios sin ningún tipo de validación.
<br> Ejemplos:

<img width="763" height="202" alt="Pasted image 20260102162942" src="https://github.com/user-attachments/assets/60589855-e136-4f27-90fd-af8ec1f12335" />

<img width="777" height="190" alt="Pasted image 20260102162956" src="https://github.com/user-attachments/assets/e9e7f466-058d-47c3-8504-d4a37c23d404" />

Después de probar varios IDs, se logra acceder a la información de un usuario llamado Aidor, donde se obtiene su contraseña en formato hash.

<img width="643" height="182" alt="Pasted image 20260102172449" src="https://github.com/user-attachments/assets/d442eaa4-574f-4776-b8a8-931c4722e904" />

## 🔓 Obtención de credenciales

Para descifrar el hash, se hace uso de una herramienta online de cracking de hashes, logrando recuperar la contraseña en texto plano

<img width="1263" height="78" alt="Pasted image 20260102172542" src="https://github.com/user-attachments/assets/b393576d-5b66-42ff-ad6f-8d472cf9d444" />

Con el usuario y la contraseña válidos, se procede a acceder al sistema mediante el servicio SSH.

<img width="1040" height="217" alt="Pasted image 20260102172710" src="https://github.com/user-attachments/assets/d99c7691-4c61-44d0-897e-812419c98134" />

## 🔐 Acceso y escalada de privilegios

Una vez dentro del sistema, se revisan posibles formas de escalar privilegios, en un primer vistazo, no se observa nada que permita obtener acceso como root de manera directa.

<img width="587" height="232" alt="Pasted image 20260102172855" src="https://github.com/user-attachments/assets/581399c2-b176-4e39-97d8-53d038bf19c6" />

Continuando con la enumeración del sistema, se revisa el directorio **/home**, donde se encuentra un archivo en Python. Al analizar su contenido, se descubre la contraseña de root almacenada en formato hash.

<img width="763" height="200" alt="Pasted image 20260102173422" src="https://github.com/user-attachments/assets/d25d85cf-b8f7-4ca4-81b7-5f2df6e0174e" />

Nuevamente, se utiliza una herramienta de cracking de hashes para obtener la contraseña en texto plano.

<img width="1285" height="58" alt="Pasted image 20260102173516" src="https://github.com/user-attachments/assets/358bea49-7178-46a9-b24b-4d6258f5e0f7" />

Con la contraseña obtenida, se inicia sesión como root, logrando el control total del sistema.

<img width="366" height="112" alt="Pasted image 20260102174036" src="https://github.com/user-attachments/assets/78c63af1-e142-4a1c-b4cb-897a690666f6" />

## 🏁 Conclusiones

Este laboratorio muestra cómo una vulnerabilidad IDOR, combinada con una mala gestión de credenciales, puede llevar a la completa compromisión de una máquina.
