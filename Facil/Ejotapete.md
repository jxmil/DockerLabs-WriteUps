# Ejotapete

## 🔎 Enumeración

Se realizó un escaneo de puertos con Nmap, identificando el puerto 80 (HTTP) como abierto.

<img width="636" height="161" alt="Pasted image 20260102113625" src="https://github.com/user-attachments/assets/f206b0dd-c416-4707-9fd7-3709e05d441a" />

Se visitó la página web, pero no tenemos autorización

<img width="625" height="227" alt="Pasted image 20260102113744" src="https://github.com/user-attachments/assets/e96d21f2-17a2-4fbe-b1b0-87f58f9c1242" />

## 🌐 Enumeración web

A pesar de la restricción, se realizó fuzzing web, lo que permitió descubrir el directorio **/drupal**:

<img width="976" height="437" alt="Pasted image 20260102114729" src="https://github.com/user-attachments/assets/9c4219c9-f3fd-48a6-8abd-59c82617a7f4" />

Se utilizó **WhatWeb** para identificar la tecnología utilizada en la aplicación web, detectando que se trataba de **Drupal 8**

<img width="966" height="190" alt="Pasted image 20260102120427" src="https://github.com/user-attachments/assets/5d6716ea-0866-42bf-9cd4-6c107073033b" />

##  💥 Explotación

Al identificar la versión de Drupal, se hizo uso de Metasploit para obtener acceso al sistema

<img width="1092" height="706" alt="Pasted image 20260102120630" src="https://github.com/user-attachments/assets/a2446b17-9168-4c82-8768-2272ac07991d" />

Haremos uso de la primera opción

<img width="1048" height="725" alt="Pasted image 20260102120958" src="https://github.com/user-attachments/assets/a3052444-2d0b-4db2-95f1-250d3c7ee8f7" />

Establecemos la ip de la maquina objetivo 

<img width="1051" height="703" alt="Pasted image 20260102121148" src="https://github.com/user-attachments/assets/a11993a3-2cc7-4890-8682-cd348bd3203c" />

Ejecutamos y somos www-data

<img width="1046" height="248" alt="Pasted image 20260102121848" src="https://github.com/user-attachments/assets/8c98f861-297a-470f-8f48-2c37453021a4" />

## 🔐 Escalada de privilegios

Una vez dentro del sistema, se intentó escalar privilegios sin éxito.

Se procedió a buscar binarios que pudieran ejecutarse con privilegios elevados, llamando la atención el binario **/find**

<img width="772" height="227" alt="Pasted image 20260102123029" src="https://github.com/user-attachments/assets/14e9fa12-1cd2-439f-8455-5a70575cf75b" />

Se consultó [GTFOBins](https://gtfobins.org/) y se utilizó dicho binario para lograr la escalada de privilegios.

<img width="876" height="162" alt="Pasted image 20260102124349" src="https://github.com/user-attachments/assets/454eadaf-4582-4538-b4c3-ba116d458c8e" />

Se obtuvo acceso como root

<img width="840" height="133" alt="Pasted image 20260102124424" src="https://github.com/user-attachments/assets/5798ba03-1571-4c6c-81b9-0e9f35a3717a" />

## 🏁 Conclusiones

En este laboratorio se logró comprometer completamente la máquina mediante la identificación de servicios expuestos con configuraciones inseguras y el uso de software desactualizado, lo que facilitó el acceso inicial al sistema.
