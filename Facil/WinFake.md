# WinFake

## 🔎 Enumeración

Se realiza un escaneo de puertos utilizando Nmap, identificando los siguientes servicios:

* 22/tcp – SSH
* 80/tcp – HTTP

<img width="763" height="348" alt="Pasted image 20260730221228" src="https://github.com/user-attachments/assets/30f84e8d-bea2-43dd-87c1-78d93c4e3273" />

## 🌐 Análisis de la aplicación web

Al acceder a la página web, se procede a inspeccionar el código fuente en busca de información que pueda resultar útil

<img width="1032" height="555" alt="Pasted image 20260730221410" src="https://github.com/user-attachments/assets/70739fd5-7d47-42fe-b1df-269ce38114e6" />

Durante el análisis se encuentra el término ```pipe``` en un contexto que no parece corresponder con un valor CSS válido

<img width="587" height="220" alt="Pasted image 20260730223029" src="https://github.com/user-attachments/assets/2d28874d-047a-41fb-ba54-715dad83302b" />

Debido a esto, se considera la posibilidad de que pipe sea un nombre de usuario válido para el servicio SSH

## 🔑 Obtención de acceso

Con el usuario identificado, se realiza un ataque de fuerza bruta contra el servicio SSH

<img width="643" height="56" alt="Pasted image 20260730223435" src="https://github.com/user-attachments/assets/53c6cc4e-780e-44ec-a6f6-2a77625537a5" />

Tras obtener credenciales válidas, se establece una conexión mediante SSH y se consigue acceso al sistema, obteniendo la flag de usuario

<img width="462" height="151" alt="Pasted image 20260730223557" src="https://github.com/user-attachments/assets/6d9f5ab9-c3f5-4cc6-9ef5-e4b52f52a62a" />

<img width="633" height="280" alt="Pasted image 20260730223648" src="https://github.com/user-attachments/assets/8c406379-eef7-41f8-a657-36efec84d3fe" />

## 👑 Obtención de Root

Para continuar con la enumeración, se regresa a la aplicación web y se inspecciona nuevamente su contenido

Durante esta revisión se encuentra una pista que indica que se deben observar las iniciales de las oraciones presentes en la página

Al aplicar esta técnica, se obtiene la cadena: ```WinServerRootFakeNews``` siendo esta la contraseña de **root**

<img width="748" height="455" alt="Pasted image 20260730224421" src="https://github.com/user-attachments/assets/0da41f69-a807-4f4a-9ece-54094c10fc27" />

Esta información permite descubrir directamente la flag de **root**, completando así la máquina

<img width="570" height="371" alt="Pasted image 20260730224711" src="https://github.com/user-attachments/assets/2154712e-c958-4f4d-a649-53630a980ce1" />

## 🏁 Conclusión

Este laboratorio demuestra la importancia de realizar una enumeración exhaustiva, incluso sobre elementos que inicialmente pueden parecer irrelevantes.

Un simple valor encontrado en el código fuente terminó siendo útil para identificar un posible usuario, mientras que una pista aparentemente sencilla dentro del contenido de la web permitió descubrir información relacionada con la escalada de privilegios.
