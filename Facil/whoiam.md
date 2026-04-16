# Whoiam

## 🔎 Enumeración

Se realiza un escaneo de puertos utilizando Nmap, identificando:

* 80/tcp – HTTP

<img width="700" height="155" alt="Pasted image 20260413113909" src="https://github.com/user-attachments/assets/4711a80e-343e-4e2c-9a91-96190c96ddc1" />

## 🌐 Análisis del servicio web

Se visita la página web y se inspecciona el código fuente, sin encontrar información relevante

<img width="636" height="280" alt="Pasted image 20260413114019" src="https://github.com/user-attachments/assets/c00f2d2a-d2ba-4624-83a7-06c3d8579f86" />

Se decide realizar una búsqueda de directorios utilizando fuzzing

<img width="890" height="385" alt="Pasted image 20260413114430" src="https://github.com/user-attachments/assets/48de34c4-2757-401d-9343-0c6485146394" />

## 📂 Descubrimiento de backups

Se encuentra el directorio ```/backups```

<img width="943" height="301" alt="Pasted image 20260413115114" src="https://github.com/user-attachments/assets/78050e12-21f4-47c8-841c-e86cfe631c12" />

Se extrae su contenido y se analiza

<img width="580" height="346" alt="Pasted image 20260413115238" src="https://github.com/user-attachments/assets/73fd191b-5f48-4672-aecd-4b464e340117" />

Dentro se encuentran credenciales en texto plano:

* Usuario
* Contraseña

<img width="495" height="172" alt="Pasted image 20260413115302" src="https://github.com/user-attachments/assets/a655ac87-417e-4fcb-801a-b5ad3414083c" />

## 🔑 Acceso al panel

Revisando nuevamente los resultados del fuzzing, se identifica un login

<img width="347" height="252" alt="Pasted image 20260413115717" src="https://github.com/user-attachments/assets/b6663f86-da5e-4100-b265-d6cd0f284a4f" />

Se ingresan las credenciales obtenidas, logrando acceso al sistema

<img width="932" height="75" alt="Pasted image 20260413120505" src="https://github.com/user-attachments/assets/b57943da-6c3c-4dad-81ed-9147a46c6ff0" />

## 🧩 Explotación de plugin vulnerable

Dentro del panel, en la sección de Plugins, se detecta un plugin desactualizado

<img width="932" height="75" alt="Pasted image 20260413120505" src="https://github.com/user-attachments/assets/9b315cfd-1dbf-4457-8ffa-2348dda5bc04" />

Se busca un exploit público para dicho plugin:

<img width="1177" height="158" alt="Pasted image 20260413120911" src="https://github.com/user-attachments/assets/d28ef8f1-cf4f-40db-b614-205a6e65c9c5" />

* Se descarga

<img width="988" height="135" alt="Pasted image 20260413124704" src="https://github.com/user-attachments/assets/f1aefb0e-bb4a-46e5-b31b-0ca12deb77fe" />

* Se ejecuta

<img width="1286" height="388" alt="Pasted image 20260413124737" src="https://github.com/user-attachments/assets/266fd113-0606-4a5d-aac2-dc9e80040667" />

Esto permite subir una web shell al servidor

## Obtención de acceso inicial

Se accede a la ruta donde se encuentra la shell subida

<img width="857" height="640" alt="Pasted image 20260413124817" src="https://github.com/user-attachments/assets/ecbbf2bb-b718-4bca-b37f-dbdffdcd2363" />

Se prepara una reverse shell

<img width="716" height="517" alt="Pasted image 20260413125156" src="https://github.com/user-attachments/assets/5aa2a4fb-ba01-4c96-bf54-397e4bc2d139" />

Antes de ejecutarla:

Se inicia un listener en el puerto 4444

<img width="792" height="192" alt="Pasted image 20260413125320" src="https://github.com/user-attachments/assets/4a107608-20c2-4b12-9883-33ef9099e1b7" />

Se ejecuta la shell y se obtiene acceso al sistema

Hacemos tratamiento de TTY

<img width="240" height="140" alt="Pasted image 20260413125617" src="https://github.com/user-attachments/assets/5c2f7f69-f3ba-43b8-8ce2-8d29e72574d8" />

## Escalada de privilegios (cadena de usuarios)

🔄 Usuario 1 → rafa

Se identifica que es posible ejecutar un binario como el usuario **rafa**

<img width="952" height="195" alt="Pasted image 20260413125547" src="https://github.com/user-attachments/assets/a2cf3258-eb02-4a15-b5d2-3d880debdad0" />

Se consulta [GTFOBins](https://gtfobins.org/) y se explota el binario

<img width="848" height="360" alt="Pasted image 20260413130031" src="https://github.com/user-attachments/assets/2b422fd2-face-467a-be43-559db18b3d6b" />

Resultado: acceso como **rafa**

🔄 Usuario 2 → ruben

Desde rafa, se detecta otro binario que permite ejecutar comandos como **ruben**

<img width="961" height="176" alt="Pasted image 20260413130522" src="https://github.com/user-attachments/assets/a25eb2aa-d3e1-4858-9c05-24f61d3dd3d1" />

Se utiliza nuevamente [GTFOBins](https://gtfobins.org/)

<img width="847" height="442" alt="Pasted image 20260413130659" src="https://github.com/user-attachments/assets/3edc10ea-0696-4256-a984-72ebf6edce41" />

Resultado: acceso como **ruben**

<img width="771" height="167" alt="Pasted image 20260413130857" src="https://github.com/user-attachments/assets/f16d595a-642b-4e67-be50-846405e9194e" />

## 👑 Escalada final 

Desde ruben, se encuentra un binario que puede ejecutarse como **root**

<img width="966" height="152" alt="Pasted image 20260413131331" src="https://github.com/user-attachments/assets/79f54534-a835-4c99-af60-ec4b5e4a2813" />

Se analiza el contenido del archivo y se identifica que es vulnerable a inyección de comandos

<img width="432" height="213" alt="Pasted image 20260413132430" src="https://github.com/user-attachments/assets/a95da35b-d088-465d-a021-5bb951991af5" />

Se ejecuta el siguiente payload:

```bash
test[$(bash -p)]+42
```

Finalmente siendo el usuario **root**

<img width="655" height="70" alt="Pasted image 20260413132128" src="https://github.com/user-attachments/assets/86cf8e91-f0fc-4bff-bac8-ddcd3b5d2e13" />

## 🏁 Conclusión

Este laboratorio nos deja una lección clara: la seguridad no falla por un solo error, sino por la suma de ellos. Lo que comenzó con la exposición de un backup con credenciales y el uso de plugins desactualizados, terminó abriendo la puerta a un compromiso total del sistema. Al combinar estas fallas con la falta de control en la ejecución de binarios, pudimos aplicar técnicas de inyección de comandos y aprovechar GTFOBins para escalar privilegios de forma efectiva. Al final, este proceso demuestra cómo piezas sueltas de mala configuración se encadenan hasta darnos el control total como root.
