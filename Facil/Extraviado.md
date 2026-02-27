# Extraviado

# 🔎 Enumeración

Se realiza un escaneo de puertos con Nmap, identificando los siguientes servicios abiertos:
* 22/tcp – SSH
* 80/tcp – HTTP

<img width="952" height="312" alt="Pasted image 20260225103719" src="https://github.com/user-attachments/assets/bfd9f181-bd5e-411a-9c24-82e5036234cc" />

## 🌐 Análisis Web

Al visitar la página web, se inspecciona el código fuente y se identifica un mensaje oculto aparentemente cifrado

<img width="1228" height="812" alt="Pasted image 20260225104151" src="https://github.com/user-attachments/assets/e26e066f-71ea-4a20-a9cf-4e67c050311f" />

Se procede a decodificarlo, obteniendo posibles credenciales para iniciar sesión por SSH

<img width="387" height="102" alt="Pasted image 20260225104334" src="https://github.com/user-attachments/assets/9b4a1f68-9071-4ace-b433-c9693911067d" />

## 🔐 Acceso Inicial

Se prueban las credenciales obtenidas y se logra acceso exitoso por SSH

<img width="753" height="146" alt="Pasted image 20260225104435" src="https://github.com/user-attachments/assets/1efbae42-218f-4232-b86d-4d107d2d4c95" />

Una vez dentro del sistema, se intenta escalar privilegios utilizando ```sudo -l``` y ```sudo su```

<img width="452" height="118" alt="Pasted image 20260225104525" src="https://github.com/user-attachments/assets/0b4679f5-d56d-494c-94cb-7c032f973c21" />

Sin embargo, no se obtienen permisos elevados

## 🔍 Enumeración Interna

Se buscan binarios con permisos **SUID**, pero no se encuentra nada relevante

<img width="625" height="241" alt="Pasted image 20260225104652" src="https://github.com/user-attachments/assets/5243cd99-8a54-4726-8927-c38d822d3a9c" />

Continuando con la enumeración manual, se descubre un directorio oculto que contiene una posible contraseña cifrada para otro usuario

<img width="646" height="427" alt="Pasted image 20260225104823" src="https://github.com/user-attachments/assets/8adf9b41-d9a5-4f18-97ff-472f90df5cc4" />

Se procede a decodificarla

<img width="422" height="56" alt="Pasted image 20260225105104" src="https://github.com/user-attachments/assets/b29dc44a-ad4e-4b64-9dc4-fc214a5521f4" />

## Movimiento Lateral

Se identifican más usuarios en el sistema, incluyendo al usuario ```Diego```

<img width="782" height="542" alt="Pasted image 20260225105211" src="https://github.com/user-attachments/assets/6fbee79a-c1fa-4980-9d68-5ad4d60cf63a" />

Se cambia a este usuario y se analiza el contenido del directorio ```/home/diego```

Dentro se encuentra un directorio oculto que aparentemente contiene información relacionada con la contraseña del usuario **root**

<img width="695" height="328" alt="Pasted image 20260225110503" src="https://github.com/user-attachments/assets/2d32c78a-587f-43fc-abbe-8af92725c4aa" />

El archivo encontrado está cifrado, por lo que se intenta decodificarlo, pero no corresponde a la contraseña correcta.

<img width="447" height="68" alt="Pasted image 20260225105948" src="https://github.com/user-attachments/assets/b17ddbc4-daa5-44c8-8a16-1aa708f1b761" />

<img width="482" height="63" alt="Pasted image 20260225110104" src="https://github.com/user-attachments/assets/b69893a4-e4bc-4dfb-8a1f-cc3299a7a73b" />

## 🧩 Enumeración Final

Continuando con la enumeración en el mismo entorno, se localiza el directorio oculto: ```.local/share```

<img width="633" height="472" alt="Pasted image 20260225110723" src="https://github.com/user-attachments/assets/4210d311-8c21-4e16-8470-4c013cccfda9" />

Dentro se encuentra un archivo oculto con un acertijo

<img width="537" height="232" alt="Pasted image 20260225110914" src="https://github.com/user-attachments/assets/8df9e467-801e-4bd2-a37e-39e16b93251a" />

Hacemos uso de la IA

<img width="330" height="100" alt="Pasted image 20260225111138" src="https://github.com/user-attachments/assets/33390c5c-181a-4b78-8d2a-e2388f65ba7b" />

Tras probar combinaciones, se obtiene la contraseña correcta  ```osoazul```

<img width="512" height="107" alt="Pasted image 20260225111229" src="https://github.com/user-attachments/assets/ebcd2321-45e1-4b20-806a-16fcba66ca71" />

## 🏁 Conclusión

Este laboratorio resalta que una enumeración meticulosa es clave para comprometer un entorno, ya que permite descubrir información oculta y decodificar datos críticos que facilitan el movimiento lateral entre usuarios y el hallazgo de secretos en archivos del sistema.
