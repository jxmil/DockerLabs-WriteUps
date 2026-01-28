# JenkHack

## 🔎 Enumeración de puertos

Se realiza un escaneo de puertos utilizando Nmap, donde se identifican los siguientes servicios abiertos:
* 80/tcp – HTTP
* 443/tcp – HTTPS
* 8080/tcp – HTTP

<img width="938" height="412" alt="Pasted image 20260127104557" src="https://github.com/user-attachments/assets/8c132f22-f1c1-443b-b154-fe78a8b66f1d" />

## 🌐 Análisis web inicial

Se visita la página web alojada en el puerto 80

<img width="1377" height="572" alt="Pasted image 20260127104805" src="https://github.com/user-attachments/assets/139630f3-9d73-4af4-a9d8-e5145d9e67bd" />

Se realiza un análisis básico de los servicios utilizando WhatWeb para identificar tecnologías en uso.

<img width="1865" height="143" alt="Pasted image 20260127105852" src="https://github.com/user-attachments/assets/03b704a0-ad1b-47f6-8af6-0243c6308d0d" />

Posteriormente, se realizan búsquedas de posibles vulnerabilidades en Metasploit, sin encontrar resultados relevantes.

También se realiza enumeración de directorios, sin éxito 

<img width="880" height="482" alt="Pasted image 20260127113446" src="https://github.com/user-attachments/assets/fc4507ef-4ff9-4078-96ba-b5d8e587b8e9" />

## 🔐 Acceso al panel de administración

Al revisar el servicio en el puerto 8080, se identifica un panel de login
Se prueban técnicas comunes como inyección SQL y credenciales por defecto, pero no se obtiene acceso.

<img width="710" height="711" alt="Pasted image 20260127113314" src="https://github.com/user-attachments/assets/1c069337-3afd-4a42-892a-aabe42675823" />

Continuando con el análisis, se inspecciona nuevamente la web del puerto 80, donde se detectan posibles credenciales expuestas.

<img width="1245" height="142" alt="Pasted image 20260127113104" src="https://github.com/user-attachments/assets/8bd6c849-085d-4e94-8a5b-67cd838eb192" />

Estas credenciales son probadas en el login del puerto 8080, logrando el acceso con éxito.

<img width="527" height="527" alt="Pasted image 20260127113628" src="https://github.com/user-attachments/assets/a42e6538-21c1-4f81-8183-c3c27f385f17" />

<img width="1543" height="392" alt="Pasted image 20260127113648" src="https://github.com/user-attachments/assets/189c00a3-8988-477c-b4f0-be60dd09680d" />

## ⚙️ Ejecución de código en Jenkins

Una vez dentro del panel, se identifica que el servicio corresponde a Jenkins y que el código que se ejecuta en segundo plano utiliza Groovy.

<img width="562" height="851" alt="Pasted image 20260127122211" src="https://github.com/user-attachments/assets/ba20d579-d5c7-4ad1-8070-2ad72f7ca866" />

<img width="830" height="130" alt="Pasted image 20260127122501" src="https://github.com/user-attachments/assets/3ea4f676-1aaa-4aec-9648-be11ffae7386" />

Se hace uso de un generador de reverse shells [Reverse Shell Generator](https://www.revshells.com/)

<img width="1157" height="697" alt="Pasted image 20260127122654" src="https://github.com/user-attachments/assets/74cad4cd-091a-4d6e-8e33-95c5258af6cc" />

Se prepara una escucha en el puerto 4545

<img width="392" height="62" alt="Pasted image 20260127123019" src="https://github.com/user-attachments/assets/2382fdb2-da10-4ba2-b37e-79d9372a59f3" />

Se pega el payload en Jenkins y se ejecuta correctamente.

<img width="1233" height="355" alt="Pasted image 20260127122855" src="https://github.com/user-attachments/assets/c3a3f23d-0cf8-4ebb-b84c-f0cda57c69a7" />

Esto permite obtener una reverse shell con éxito en el sistema

<img width="608" height="155" alt="Pasted image 20260127123058" src="https://github.com/user-attachments/assets/19d99c45-ef4a-419f-b797-33a86a61c171" />

Hacemos tratamiento de TTY

<img width="240" height="140" alt="Pasted image 20251222222913" src="https://github.com/user-attachments/assets/280aa143-ebfe-42ed-bb7b-86e6ba21c077" />

## 🔍 Enumeración interna

Ya dentro del sistema, se intenta escalar privilegios, pero se solicita una contraseña.

<img width="491" height="67" alt="Pasted image 20260127130015" src="https://github.com/user-attachments/assets/0cbd2a8c-ba48-4981-bb82-e307f6924a3e" />

Durante la enumeración del sistema de archivos, se encuentra una nota codificada que no está en texto plano.

<img width="560" height="158" alt="Pasted image 20260127125950" src="https://github.com/user-attachments/assets/d075a761-b073-422f-96b1-49dbac17f759" />

Tras analizarla, se identifica que está codificada en Base85, por lo que se utiliza [CyberChef](https://gchq.github.io/CyberChef/) para decodificarla y obtener una contraseña válida.

<img width="810" height="550" alt="Pasted image 20260127131028" src="https://github.com/user-attachments/assets/b95e6462-3a86-4a3e-8567-6792ab897a86" />

Con esta contraseña, se logra cambiar a un nuevo usuario dentro del sistema.

<img width="562" height="107" alt="Pasted image 20260127131144" src="https://github.com/user-attachments/assets/40cf939e-1af7-43fe-b69d-70792320c906" />

## 🔐Escalada de privilegios

Se intenta escalar privilegios

<img width="947" height="163" alt="Pasted image 20260127131335" src="https://github.com/user-attachments/assets/7794ec5f-a3ad-42b1-ac0d-b0f3d2b33c3a" />

Se detecta la existencia de un archivo que se ejecuta desde el directorio **/opt**. 

<img width="1857" height="347" alt="Pasted image 20260127131924" src="https://github.com/user-attachments/assets/663f774f-8a30-494d-a21b-dec1f6d4ae4a" />

Leo que hay en ese archivo y decido reemplazarlo

<img width="457" height="171" alt="Pasted image 20260127132041" src="https://github.com/user-attachments/assets/080b8b7b-c6fb-48c9-8273-e01c3f754a09" />

Se elimina el archivo original y se crea uno nuevo con el mismo nombre, incluyendo instrucciones maliciosas que permiten modificar el archivo /etc/passwd.

<img width="576" height="60" alt="Pasted image 20260127132601" src="https://github.com/user-attachments/assets/e26271b4-7b04-45ec-8d7a-2c482b4af3f4" />

**Nota: Se asignan permisos de ejecución al archivo con chmod +x**

Luego se ejecuta el archivo
```
sudo /usr/local/bin/bash
```
Eliminaremos esa **X** que indica que nos pide contraseña 

<img width="513" height="120" alt="Pasted image 20260127132930" src="https://github.com/user-attachments/assets/156aec46-9bae-4330-a45d-548975b00d41" />

<img width="872" height="461" alt="Pasted image 20260127133651" src="https://github.com/user-attachments/assets/89ed7d2b-2200-42ae-8dbf-1763223e6c47" />

Finalmente, se cambia al usuario root y se obtiene control total del sistema

<img width="272" height="48" alt="Pasted image 20260127133735" src="https://github.com/user-attachments/assets/6c7a1750-3faa-4d2f-be14-8c96481de4b5" />

## 🏁 Conclusión

Este laboratorio demuestra cómo una mala gestión de servicios web y permisos puede permitir una cadena de ataque completa, desde el acceso inicial hasta la escalada total de privilegios.
