# PequeñasMentirosas

## 🔎 Enumeración 

Se realiza un escaneo de puertos utilizando **Nmap**, donde se identifican los siguientes servicios abiertos:

* 22/tcp – SSH
* 80/tcp – HTTP

<img width="930" height="297" alt="Pasted image 20260219103631" src="https://github.com/user-attachments/assets/ec2f6b25-7ee8-4e92-bc04-96ad6474fa1e" />

## 🌐 Análisis Web

Se visita la página web y se realiza fuzzing de directorios en busca de rutas ocultas o funcionalidades vulnerables. Sin embargo, no se encuentra nada relevante

<img width="935" height="62" alt="Pasted image 20260219103756" src="https://github.com/user-attachments/assets/44636f0b-a518-4457-af3a-64651243e46e" />

<img width="652" height="160" alt="Pasted image 20260219104317" src="https://github.com/user-attachments/assets/851a64e3-e78e-494a-95e0-710e7db674ef" />

Al regresar a la página principal, se observa que la letra ```A``` podría corresponder a un posible nombre de usuario

Se decide realizar un ataque de fuerza bruta contra el servicio **SSH**

<img width="606" height="48" alt="Pasted image 20260219104502" src="https://github.com/user-attachments/assets/be6696ea-b11e-4ec7-af62-583d2aacc84c" />

## 🔑 Acceso inicial

El ataque de fuerza bruta resulta exitoso y se logran obtener credenciales válidas

<img width="315" height="107" alt="Pasted image 20260219104605" src="https://github.com/user-attachments/assets/86843589-11ac-40a7-96b6-a0e464319038" />

## 🔍 Enumeración interna

Una vez dentro, se intenta escalar privilegios sin éxito inicial

<img width="517" height="128" alt="Pasted image 20260219104708" src="https://github.com/user-attachments/assets/8e881180-843e-4ecb-9a83-1eec24a73b62" />

Se procede a buscar binarios con permisos **SUID**, pero nada relevante

```bash
find / -perm -4000 2>/dev/null
```

<img width="532" height="228" alt="Pasted image 20260219105028" src="https://github.com/user-attachments/assets/905236f7-f9fa-4001-998d-9758ee2ab0e3" />

Tras navegar por el sistema durante un tiempo, se decide cambiar al usuario ```spencer```, ya que el usuario actual no posee privilegios suficientes

<img width="340" height="65" alt="Pasted image 20260219105344" src="https://github.com/user-attachments/assets/10c94150-0e36-4edf-8709-bf9499eb9162" />

## 🔐 Movimiento lateral

Antes de cambiar de usuario, se realiza un nuevo ataque de fuerza bruta debido a que no se contaba con la contraseña de ```spencer```

<img width="692" height="43" alt="Pasted image 20260219105444" src="https://github.com/user-attachments/assets/83b027b8-66ac-4ebd-a1a9-46fc37bb0a40" />

Se obtienen credenciales válidas y se inicia sesión correctamente como dicho usuario

## 🔐 Escalada de privilegios

Ya como spencer, se revisan los permisos sudo y se identifica que existe un binario **Python** que puede ejecutarse como **root** sin necesidad de contraseña

<img width="1177" height="121" alt="Pasted image 20260219105600" src="https://github.com/user-attachments/assets/54952a30-4ee0-42e5-ab52-70bd0ee4892f" />

Se consulta [GTFOBins](https://gtfobins.org/)

<img width="847" height="557" alt="Pasted image 20260219105851" src="https://github.com/user-attachments/assets/25ad02f6-7787-47b3-b296-3355e7ccd55b" />

Se copia el payload recomendado y se ejecuta mediante sudo

<img width="827" height="97" alt="Pasted image 20260219105921" src="https://github.com/user-attachments/assets/a7eaa550-a34b-49ef-9120-c3f0e42fc49b" />

## 🏁 Conclusión

Este laboratorio demuestra cómo una mala gestión de credenciales combinada con una configuración incorrecta de permisos sudo en binarios como Python puede permitir la escalada completa de privilegios hasta root.
