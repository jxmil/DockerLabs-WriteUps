# Upload

## 🔎 Enumeración

Se realiza un escaneo de puertos con **Nmap**, identificando el puerto 80 (HTTP) como único servicio expuesto

<img width="727" height="147" alt="Pasted image 20260223103843" src="https://github.com/user-attachments/assets/d75fc139-1ae0-4e31-a0fd-d36325e99b63" />

## 🌐 Análisis de la aplicación web

Al visitar la página web, se observa que existe una funcionalidad para subir archivos. Esto abre la posibilidad de intentar una carga maliciosa para obtener ejecución remota de comandos

<img width="612" height="230" alt="Pasted image 20260223104008" src="https://github.com/user-attachments/assets/d992db6c-4f1c-4ceb-8a6a-1c3d5ca1f7bf" />

## Carga de archivo malicioso

Se genera una reverse shell en PHP utilizando [Reverse Shell Generator](https://www.revshells.com/)

<img width="1218" height="735" alt="Pasted image 20260223104340" src="https://github.com/user-attachments/assets/ef627398-fb96-40be-a806-0ce8d30bf5e6" />

El archivo se guarda localmente y posteriormente se carga a la aplicación mediante el formulario disponible

<img width="1016" height="258" alt="Pasted image 20260223104438" src="https://github.com/user-attachments/assets/2ec868f9-0685-434c-bc7c-9c40386e636a" />

<img width="467" height="278" alt="Pasted image 20260223104542" src="https://github.com/user-attachments/assets/d3a7b23e-8770-4289-95c5-37601c3dc911" />

Para localizar dónde fue almacenado el archivo subido, se realiza fuzzing web, lo que permite identificar el directorio ```/uploads``` como ubicación probable

<img width="867" height="210" alt="Pasted image 20260223104642" src="https://github.com/user-attachments/assets/b7f3e443-b2ed-4f58-a510-8c6872ca1a54" />

<img width="603" height="301" alt="Pasted image 20260223104730" src="https://github.com/user-attachments/assets/3f19a7b2-2ef0-4c36-9e70-65704cde3882" />

Una vez encontrado el archivo, se prepara un listener en el puerto **4545** y se ejecuta el archivo desde el navegador, obteniendo acceso al sistema

<img width="252" height="205" alt="Pasted image 20260223104857" src="https://github.com/user-attachments/assets/7f16d286-7dca-4329-bd7e-89116638807f" />

Hacemos tratamiento de TTY

<img width="240" height="140" alt="Pasted image 20251222222913" src="https://github.com/user-attachments/assets/560c936d-518b-4048-b0e7-80aac094247e" />

## 👑 Escalada de privilegios

Ya dentro del sistema, se procede a enumerar posibles vectores de escalada. Se detecta un binario que puede ejecutarse como root sin necesidad de contraseña

<img width="950" height="187" alt="Pasted image 20260223105119" src="https://github.com/user-attachments/assets/66e4c69d-2d71-423a-b5f8-c998f1cea49e" />

Se consulta [GTFOBins](https://gtfobins.org/) 

<img width="887" height="497" alt="Pasted image 20260223105423" src="https://github.com/user-attachments/assets/d29cab06-89d7-4123-8559-54ed5e59d02c" />

Se ejecuta el binario de manera adecuada para elevar privilegios

<img width="570" height="107" alt="Pasted image 20260223105455" src="https://github.com/user-attachments/assets/ec63047e-a19e-4bf6-8555-e59ebf647b74" />

Finalmente, se obtiene acceso como **root**

## 🏁 Conclusión

Este laboratorio refuerza la importancia de validar correctamente la subida de archivos en aplicaciones web, restringir la ejecución de archivos en directorios accesibles públicamente y revisar exhaustivamente las configuraciones de sudo y los permisos de binarios, demostrando que una mala configuración puede convertir una vulnerabilidad inicial sencilla en un compromiso total del sistema.
