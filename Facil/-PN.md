# PN

## 🔎 Enumeración

Se realiza un escaneo de puertos utilizando Nmap, donde se identifican los siguientes servicios abiertos:
* 21/tcp – FTP
* 8080/tcp – HTTP

<img width="731" height="448" alt="Pasted image 20260120112123" src="https://github.com/user-attachments/assets/6e761453-a723-4af7-b98c-c849a1929d07" />

## 📁 Acceso al servicio FTP

Como primer paso, se intenta acceder al servicio FTP utilizando el usuario **anonymous**, logrando iniciar sesión exitosamente.

<img width="742" height="270" alt="Pasted image 20260120112231" src="https://github.com/user-attachments/assets/a73ebed1-a3ec-4c8f-bc6f-aaf29d6c48a2" />

Descargamos el archivo y leemos el contenido

<img width="855" height="110" alt="Pasted image 20260120112430" src="https://github.com/user-attachments/assets/8d97aae7-6597-4a84-8bd4-93de20aa09bf" />

## 🌐 Análisis del servicio web

Posteriormente, se accede al servicio web que corre en el puerto 8080.

<img width="1198" height="966" alt="Pasted image 20260120100656" src="https://github.com/user-attachments/assets/f979e258-f29e-4235-9eb4-0ee194549a4b" />

Para descubrir rutas ocultas, se realiza fuzzing web, encontrando varios directorios.

<img width="867" height="502" alt="Pasted image 20260120101524" src="https://github.com/user-attachments/assets/15f9f591-4c15-4ebd-bbb4-b2ff5ec4046c" />

Entre ellos, destaca el directorio **/manager**, el cual corresponde al panel de administración de Apache Tomcat y solicita credenciales.

<img width="347" height="257" alt="Pasted image 20260120114254" src="https://github.com/user-attachments/assets/5736121b-7e51-425b-8698-43d094c898a5" />

## 🔐 Acceso al panel Tomcat

Inicialmente se prueban credenciales comunes sin éxito. Luego, tras consultar documentación pública [HackTricks](https://book.hacktricks.wiki/en/index.html), se utilizan las credenciales:
`tomcat : s3cr3t`

<img width="792" height="292" alt="Pasted image 20260120115020" src="https://github.com/user-attachments/assets/f013ba05-1471-4f7a-8149-3e759408922c" />

Con estas credenciales se logra acceder al panel de administración.

<img width="1897" height="593" alt="Pasted image 20260120115424" src="https://github.com/user-attachments/assets/e462167a-cacc-42d7-b0d0-d141416ef438" />

## Obtención de acceso remoto

Dentro del panel de Tomcat, se identifica una funcionalidad que permite subir archivos WAR.

<img width="1137" height="87" alt="Pasted image 20260120120153" src="https://github.com/user-attachments/assets/8079e48a-d7e0-48bd-b6d7-7b4e337227cf" />

Esta característica es aprovechada para subir un archivo WAR malicioso que contiene una reverse shell.

<img width="931" height="126" alt="Pasted image 20260120120054" src="https://github.com/user-attachments/assets/6997ba20-bce9-40ed-9a00-baaa1111a4ca" />

Una vez cargado el archivo

<img width="405" height="747" alt="Pasted image 20260120120500" src="https://github.com/user-attachments/assets/75f48383-ead0-4671-b27e-f2b3725738e6" />

Se prepara un listener en el puerto 443

<img width="338" height="91" alt="Pasted image 20260120120303" src="https://github.com/user-attachments/assets/0f346ec1-4d85-4dbd-ad4f-b7c27b76b536" />

Se ejecuta la aplicación cargada desde el panel

<img width="422" height="135" alt="Pasted image 20260120120622" src="https://github.com/user-attachments/assets/b303fb79-dfcd-4a54-bc0f-9b94faa9d767" />

Y somos root

<img width="615" height="117" alt="Pasted image 20260120120705" src="https://github.com/user-attachments/assets/fd2cb2bb-cd6d-4123-abec-a9330c2b87ca" />

## 🏁 Conclusión

Este laboratorio resalta la importancia de asegurar correctamente los servicios expuestos a la red. El acceso FTP anónimo, junto con un panel de administración de Tomcat accesible y protegido por credenciales débiles, facilitó la obtención de acceso remoto.
