# ApkAdmin

## 🔎 Enumeración

Se realiza un escaneo de puertos utilizando Nmap, identificando los siguientes servicios:

* 22/tcp – SSH
* 80/tcp – HTTP

<img width="862" height="310" alt="Pasted image 20260815122032" src="https://github.com/user-attachments/assets/c126df75-e8dd-4f80-97b2-638b9eb01400" />

Se accede a la aplicación web y durante la exploración se encuentra una aplicación para **Android** disponible para su descarga

<img width="927" height="635" alt="Pasted image 20260815122124" src="https://github.com/user-attachments/assets/a05ebd49-d77b-4b21-a5b9-2fb696747f03" />

<img width="823" height="263" alt="Pasted image 20260815122358" src="https://github.com/user-attachments/assets/14cce51e-be34-4bc6-98c9-0872dd474280" />

## 📱 Análisis de la aplicación Android

Al descargar la aplicación se identifica que se trata de un archivo con extensión: ```.apk```

Para realizar el análisis de la aplicación se utilizan las herramientas **JADX** y **Apktool**

Se procede a abrir y analizar los recursos de la aplicación mediante la interfaz gráfica para facilitar la revisión de los diferentes componentes

<img width="697" height="477" alt="Pasted image 20260815123523" src="https://github.com/user-attachments/assets/b9d51411-20f1-4705-a8cb-93f85b3973d0" />

## 📋 Análisis de AndroidManifest.xml

Uno de los primeros archivos revisados es ```AndroidManifest.xml```, ya que contiene la configuración central de una aplicación **Android**

En este archivo se declaran componentes como:

* Activities
* Services
* Broadcast Receivers
* Content Providers
* Permisos y restricciones de la aplicación

Durante el análisis se identifica una **Activity exportada** que resulta especialmente interesante

Una ```exported activity``` es una actividad que puede ser iniciada por otras aplicaciones o por el propio sistema operativo. Este comportamiento está controlado mediante el atributo: ```android:exported```

## 🚨 Identificación de AdminActivity

Al revisar el código de ```AndroidManifest.xml```, concretamente alrededor de la línea 55, se observa que:

```AdminActivity``` tiene ```android:exported="true"```

<img width="587" height="77" alt="Pasted image 20260815124047" src="https://github.com/user-attachments/assets/1e38aa65-1658-4cfc-8ebe-0695aea684e0" />

Además, no requiere ningún permiso especial para ser accedida

Esto significa que la actividad puede ser iniciada externamente, sin necesidad de seguir el flujo de autenticación previsto por la aplicación

Este comportamiento representa un problema de seguridad, ya que una funcionalidad administrativa queda expuesta sin las restricciones adecuadas

## 🔑 Obtención de credenciales

Una vez identificada la actividad administrativa, se procede a analizar el código de ```AdminActivity``` utilizando **JADX**

Durante el análisis del código fuente se encuentran credenciales almacenadas en la propia aplicación

<img width="1075" height="538" alt="Pasted image 20260815124859" src="https://github.com/user-attachments/assets/e5520da2-c85f-4f16-81c5-1f44b3a17719" />

Con las credenciales obtenidas se procede a probar el acceso mediante el servicio SSH

La autenticación es exitosa y se consigue acceso al sistema

## 👑 Escalada de privilegios

Una vez dentro del sistema, se continúa con la enumeración de privilegios

Durante las pruebas se descubre que la contraseña obtenida previamente también puede utilizarse para acceder como **root**, debido a una reutilización de credenciales

<img width="431" height="105" alt="Pasted image 20260815125358" src="https://github.com/user-attachments/assets/b6734a64-9b09-4ac7-b13a-b04a7c07e95d" />

Se utiliza la misma contraseña para autenticarse con privilegios elevados y finalmente se consigue acceso como **root**

## 🏁 Conclusión

Este laboratorio demuestra la importancia de realizar un análisis de seguridad sobre las aplicaciones móviles y no limitarse únicamente a la infraestructura que las soporta.

Una Activity administrativa configurada como exported y sin mecanismos de protección adecuados puede exponer funcionalidades que deberían estar restringidas. Además, almacenar credenciales directamente dentro del código de una aplicación permite que estas puedan ser recuperadas mediante técnicas de análisis estático.

Finalmente, la reutilización de credenciales facilita la escalada de privilegios y puede convertir una vulnerabilidad inicial en un compromiso completo del sistema.
