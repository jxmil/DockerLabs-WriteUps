# BaluFood

## 🔎 Enumeración

Se realiza un escaneo de puertos utilizando Nmap, identificando los siguientes servicios abiertos:
* 22/tcp – SSH
* 5000/tcp – HTTP

<img width="938" height="302" alt="Pasted image 20260227113049" src="https://github.com/user-attachments/assets/cb520145-dabb-4d71-92bb-3d8fdf419fb1" />

## 🌐 Análisis del servicio web

Al acceder al servicio web en el puerto 5000, se procede a realizar fuzzing de directorios, encontrando un directorio interesante: ```login```

<img width="1655" height="747" alt="Pasted image 20260227113209" src="https://github.com/user-attachments/assets/3aef1c1b-fc55-4f1b-acba-5b36547592da" />

<img width="630" height="127" alt="Pasted image 20260227113811" src="https://github.com/user-attachments/assets/334646af-f1c3-4976-908b-d02d7f36b9c7" />

Dentro del panel de login, se prueban credenciales por defecto: ```admin:admin```

<img width="481" height="430" alt="Pasted image 20260227113859" src="https://github.com/user-attachments/assets/c73e153b-ecfc-4525-bdc6-effa78d619c2" />

Obteniendo acceso exitoso

<img width="1361" height="518" alt="Pasted image 20260227113952" src="https://github.com/user-attachments/assets/727a4fc4-e7e2-44c7-9840-97d3b321d26f" />

## 🕵️ Descubrimiento de credenciales

Una vez dentro del panel, se inspecciona el código fuente de la página, donde se encuentra un comentario relacionado con un backup de acceso, el cual revela nuevas credenciales

<img width="1170" height="553" alt="Pasted image 20260227114054" src="https://github.com/user-attachments/assets/22189864-422a-4341-ab9d-ead553342f61" />

Estas credenciales son probadas contra el servicio SSH, logrando acceso al sistema

<img width="661" height="112" alt="Pasted image 20260227114251" src="https://github.com/user-attachments/assets/ab7e307e-2ebc-4435-9b13-3f312a825374" />

## Enumeración interna

Ya dentro de la máquina:
Se intenta escalar privilegios usando ```sudo -l``` y ```sudo su``` sin éxito

<img width="412" height="130" alt="Pasted image 20260227114439" src="https://github.com/user-attachments/assets/21d4e673-b30e-40ac-b797-d142ed909bcd" />

Se buscan binarios con permisos **SUID**

<img width="567" height="245" alt="Pasted image 20260227114643" src="https://github.com/user-attachments/assets/c3bfdf99-f09e-4305-a32d-9d3752f09420" />

No se encuentra nada relevante

Durante la enumeración manual del sistema, se identifica:

* Una posible contraseña perteneciente a otro usuario

<img width="881" height="168" alt="Pasted image 20260227115432" src="https://github.com/user-attachments/assets/5f29f068-7350-400b-823c-eb3ca8f23427" />

* La existencia de un nuevo usuario en el sistema

<img width="791" height="117" alt="Pasted image 20260227120032" src="https://github.com/user-attachments/assets/3b411bc9-fbde-4c9e-b69b-9fd63c562c70" />

Cambiamos al nuevo usuario, pero se intenta nuevamente escalar privilegios sin resultados

## 🔓 Escalada de privilegios final

Continuando con la exploración del sistema, se localiza un archivo oculto

<img width="862" height="191" alt="Pasted image 20260227120333" src="https://github.com/user-attachments/assets/247240c7-3fa6-4ff6-ae87-f5b80e7bc97f" />

Al revisar su contenido, se encuentra la contraseña del usuario **root**

<img width="512" height="100" alt="Pasted image 20260227120353" src="https://github.com/user-attachments/assets/38996a99-8c8b-4ae0-9855-c38d47026e03" />

Se realiza el cambio exitosamente al usuario **root**

<img width="480" height="148" alt="Pasted image 20260227120440" src="https://github.com/user-attachments/assets/bfd102b1-6d49-4ce9-98f0-5bee639bfa4b" />

## 🏁 Conclusión

Este laboratorio subraya la necesidad crítica de fortalecer la seguridad mediante cuatro pilares fundamentales: la eliminación de credenciales por defecto para evitar accesos no autorizados, la limpieza exhaustiva de comentarios en el código para no exponer información sensible, el resguardo riguroso de archivos internos con datos críticos y la implementación precisa de controles de acceso y permisos que garanticen que solo el personal autorizado interactúe con el sistema.
