# HedgeHog

##  🔎 Enumeración

Se realiza un reconocimiento inicial utilizando Nmap, identificando que los siguientes puertos se encuentran abiertos:
* 22/tcp – SSH
* 80/tcp – HTTP

<img width="948" height="312" alt="Pasted image 20260116142520" src="https://github.com/user-attachments/assets/543dcfd0-3ecb-4227-ab55-dfea0744a5e7" />

## 🌐 Análisis Web

Se accede a la aplicación web y se identifica un posible nombre de usuario expuesto

<img width="530" height="125" alt="Pasted image 20260116142701" src="https://github.com/user-attachments/assets/7b9d0b4b-803e-4f10-8816-9ff6aa249068" />

Inicialmente, se considera realizar un ataque de fuerza bruta utilizando el diccionario rockyou.txt, sin embargo, debido al tamaño del diccionario, el proceso resultaría demasiado lento.

Para optimizar el ataque, se realiza lo siguiente:

Se invierte el orden del diccionario rockyou.txt para comenzar desde el final

<img width="722" height="42" alt="Pasted image 20260116143246" src="https://github.com/user-attachments/assets/6ea03879-5c43-40ea-a357-4ed88b533727" />

Se eliminan espacios en blanco innecesarios

<img width="561" height="28" alt="Pasted image 20260116143413" src="https://github.com/user-attachments/assets/0b989167-e6f2-40e3-ae22-cabefcf7bcfe" />

## ⚔️ Acceso al sistema

Con el diccionario modificado, se ejecuta nuevamente el ataque de fuerza bruta utilizando Hydra, logrando obtener credenciales válidas

<img width="665" height="30" alt="Pasted image 20260116143538" src="https://github.com/user-attachments/assets/9dc4daab-285b-4d6f-b2fb-94f2fe8217b2" />

Posteriormente, se inicia sesión en el sistema a través de SSH

<img width="816" height="240" alt="Pasted image 20260116143723" src="https://github.com/user-attachments/assets/47f039d9-1cdc-4fe1-b060-eb546c4c03e4" />

## 🔐 Escalada de privilegios

Una vez dentro del sistema, se intenta elevar privilegios, pero el usuario actual no cuenta con permisos sudo.

<img width="666" height="66" alt="Pasted image 20260116143905" src="https://github.com/user-attachments/assets/cce0437e-babc-4eec-a302-7b8fa450c2cb" />

Durante la enumeración, se identifica que el usuario Sonic sí dispone de permisos elevados.
Aprovechando esta configuración, se ejecuta el siguiente comando:

```
sudo -u Sonic /bin/bash
```
Esto permite cambiar al usuario Sonic y obtener una shell con privilegios root, comprometiendo completamente el sistema.

<img width="672" height="170" alt="Pasted image 20260116144047" src="https://github.com/user-attachments/assets/bb0a4bde-dcba-448d-b110-e6ed47571905" />

## 🏁 Conclusión

Este laboratorio demuestra cómo la exposición de información sensible en aplicaciones web puede facilitar el acceso inicial a un sistema.

Además, una mala configuración de permisos entre usuarios permitió escalar privilegios de manera directa, destacando la importancia de una correcta gestión de cuentas y permisos en sistemas Linux.
