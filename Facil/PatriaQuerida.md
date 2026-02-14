# PatriaQuerida

## 🔎 Enumeración

Se realiza un escaneo de puertos con Nmap, donde se identifican los siguientes servicios abiertos:

* 22/tcp – SSH
* 80/tcp – HTTP

<img width="833" height="496" alt="Pasted image 20260211120650" src="https://github.com/user-attachments/assets/b81ffe16-4402-4580-9602-95c3b9453e22" />

## 🌐 Análisis web y descubrimiento de LFI

Al acceder a la página web, no se observa información relevante a simple vista, por lo que se procede a realizar fuzzing web para descubrir rutas ocultas

<img width="1066" height="565" alt="Pasted image 20260211120820" src="https://github.com/user-attachments/assets/689fd971-2e87-4f05-85c0-1f2949d6b289" />

<img width="703" height="178" alt="Pasted image 20260211121104" src="https://github.com/user-attachments/assets/31ba057b-e38b-42de-b288-bc096ba35e39" />

Durante la enumeración destaca el archivo ```/index.php```

<img width="907" height="123" alt="Pasted image 20260211121205" src="https://github.com/user-attachments/assets/489426b7-8603-4064-98c0-a5553f0c5ad9" />

Para profundizar en su funcionamiento, se utiliza **wfuzz** con el objetivo de identificar qué parámetro de la URL permite incluir o leer archivos internos del sistema

```bash
wfuzz -c -w /usr/share/secLists/Discovery/Web-Content/DirBuster-2007_directory-list-2.3-medium.txt -u '172.17.0.2/index.php?
```

<img width="767" height="228" alt="Pasted image 20260211122256" src="https://github.com/user-attachments/assets/400537ef-eaae-45cb-a2b8-db363113d469" />

Probamos el parámetro encontrado y encontramos un posible usuario

<img width="862" height="125" alt="Pasted image 20260211122655" src="https://github.com/user-attachments/assets/6634b7a0-7602-409d-b87c-6a111802f1ad" />

Tras varias pruebas, se confirma que la aplicación es vulnerable a LFI (Local File Inclusion), lo que permite leer archivos del sistema

<img width="1880" height="147" alt="Pasted image 20260211124401" src="https://github.com/user-attachments/assets/fe080286-a03b-40fc-bea7-aec9b25e2556" />

A través de esta vulnerabilidad se enumeran los usuarios del sistema, encontrando:```pinguino``` y ```mario```

## 🔐 Acceso inicial

Previamente se había identificado la palabra ```balu```, la cual podría corresponder a una contraseña

Se prueban las combinaciones con ambos usuarios vía SSH, confirmando que la contraseña pertenece a ```pinguino```, logrando así acceso al sistema

<img width="426" height="425" alt="Pasted image 20260211124909" src="https://github.com/user-attachments/assets/8a9caef0-f5e1-4f8f-aef2-6d6cd60c1b90" />

## 🔐 Enumeración interna 

Una vez dentro, se continúa con la enumeración del sistema y se encuentra una nota con información relevante

<img width="467" height="42" alt="Pasted image 20260211125540" src="https://github.com/user-attachments/assets/903e2f0b-39d1-457d-bef3-e0c63da4b1d8" />

Tras cambiar de usuario y analizar los permisos, se observa que no hay privilegios sudo disponibles

<img width="392" height="81" alt="Pasted image 20260211125840" src="https://github.com/user-attachments/assets/7c34297b-bcd5-4cde-b9bd-03c59a8d2282" />

Se procede entonces a buscar binarios con permisos SUID en todo el sistema, encontrando que el binario python tiene este permiso activo

<img width="613" height="298" alt="Pasted image 20260211130053" src="https://github.com/user-attachments/assets/249c6581-8be4-4461-b853-352f7a1e05d3" />

Se consulta [GTFOBins](https://gtfobins.org/) 

<img width="977" height="392" alt="Pasted image 20260211130241" src="https://github.com/user-attachments/assets/2c0fd5c7-9d0d-4f7b-879f-7a011708cf90" />

Ejecutando el binario con la opción ```-p``` para mantener privilegios elevados, se obtiene una shell con permisos de root.

<img width="888" height="121" alt="Pasted image 20260211130603" src="https://github.com/user-attachments/assets/2dfc5735-a2d6-48c9-a518-b028885c24b2" />

## 🏁 Conclusión

Este laboratorio demuestra cómo una vulnerabilidad LFI puede ser utilizada para enumerar usuarios del sistema y obtener acceso inicial, y cómo una mala configuración de permisos SUID puede llevar a la elevación total de privilegios hasta root.
