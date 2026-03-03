# Candy

## 🔎 Enumeración

Se realiza un escaneo de puertos utilizando Nmap, identificando que el puerto:

* 80/tcp – HTTP

se encuentra abierto.

<img width="797" height="270" alt="Pasted image 20260219115728" src="https://github.com/user-attachments/assets/3ff7e958-4197-432b-a820-c4b472f4f5f8" />

Con esto, se procede a analizar la aplicación web

<img width="1368" height="697" alt="Pasted image 20260219115907" src="https://github.com/user-attachments/assets/39d361ef-8b99-4301-9cb1-a87d19b74f1b" />

## 🌐 Análisis del servicio web

Al acceder al sitio, se observa un panel de login

Se intenta realizar:

* Inyección SQL → ❌ Sin éxito

<img width="383" height="382" alt="Pasted image 20260219120143" src="https://github.com/user-attachments/assets/201c756c-7a00-4521-a49a-3563b34fd3ca" />

* Credenciales por defecto → ❌ Sin éxito

<img width="451" height="397" alt="Pasted image 20260219120232" src="https://github.com/user-attachments/assets/4a374791-b835-4b4b-a526-0616fa0ff2bb" />

Posteriormente, se realiza fuzzing de directorios, encontrando múltiples rutas interesantes, entre ellas: ```robots.txt```

<img width="881" height="547" alt="Pasted image 20260219120820" src="https://github.com/user-attachments/assets/bc9f25f6-880e-4317-a841-330c492a8684" />

Dentro del archivo se encuentra lo que aparenta ser un usuario y una contraseña codificada

<img width="627" height="585" alt="Pasted image 20260219120625" src="https://github.com/user-attachments/assets/037ab9b0-ddc6-4315-b7b8-625f757a84bb" />

Hacemos uso de la IA

<img width="380" height="110" alt="Pasted image 20260219121205" src="https://github.com/user-attachments/assets/d71762a4-dd0f-4d5a-872d-8ee3e0bd3f2b" />

```⚠️ Nota: El acceso se realiza mediante la ruta encontrada: administrator```

<img width="593" height="510" alt="Pasted image 20260219121821" src="https://github.com/user-attachments/assets/eb62abfa-8e62-43e8-9b34-3504a8663593" />

## Obtención de Reverse Shell

Una vez dentro del panel administrativo, se busca la posibilidad de ejecutar código

<img width="1837" height="746" alt="Pasted image 20260219121954" src="https://github.com/user-attachments/assets/84dbe003-02d1-426f-b8ff-7363545b39d0" />

Se navega hacia:

```System → Templates → Cassiopeia Details and Files```

<img width="617" height="737" alt="Pasted image 20260219122250" src="https://github.com/user-attachments/assets/7df65ec0-7186-46cc-8cc0-1ef4b3d3c681" />

<img width="1562" height="542" alt="Pasted image 20260219123617" src="https://github.com/user-attachments/assets/d7e058b0-4b2a-4612-b76d-2b5a8ed6f67b" />

Dentro de esta sección, se crea un archivo PHP que contiene una reverse shell, generada por [Reverse Shell Generator](https://www.revshells.com/)

<img width="982" height="78" alt="Pasted image 20260219123511" src="https://github.com/user-attachments/assets/77de97f7-44ba-40de-b839-a6bb13cbb405" />

<img width="950" height="291" alt="Pasted image 20260219124257" src="https://github.com/user-attachments/assets/0dbd320d-fd7c-4b78-b25e-17897647faec" />

<img width="1117" height="775" alt="Pasted image 20260219125916" src="https://github.com/user-attachments/assets/3bde2cf8-49eb-4681-884a-ae94ebd14792" />

Se coloca el código malicioso en el archivo creado

<img width="1737" height="573" alt="Pasted image 20260219130021" src="https://github.com/user-attachments/assets/7ea7cca4-9d7e-412b-8dcc-209f3ae74ae2" />

Se pone el listener en el puerto 4545

<img width="292" height="82" alt="Pasted image 20260219130135" src="https://github.com/user-attachments/assets/8676ad45-14d6-4b03-9025-c96cb9717894" />

Se accede a la ruta del archivo:

```http://172.17.0.2/templates/cassiopeia/reverseshell.php```

La página queda cargando, mientras que se obtiene acceso como: ```www-data```

<img width="697" height="170" alt="Pasted image 20260219130337" src="https://github.com/user-attachments/assets/4fc690a0-ab03-4c4e-8329-5005c9bece3b" />

Hacemos tratamiento de TTY

<img width="240" height="140" alt="Pasted image 20251222222913" src="https://github.com/user-attachments/assets/e18cf3f8-061e-4b53-b804-dd81f6ade91b" />

## Enumeración y Movimiento Lateral

Se intenta escalar privilegios, pero el sistema solicita contraseña

<img width="392" height="62" alt="Pasted image 20260219130725" src="https://github.com/user-attachments/assets/deaea1ce-4c5d-4e36-bfe9-7d896f9f4ef9" />

Se buscan binarios con permisos **SUID**, sin encontrar resultados relevantes

<img width="731" height="246" alt="Pasted image 20260219130806" src="https://github.com/user-attachments/assets/6c2b618c-86e5-48fa-b61c-2a927fc17111" />

Continuando con la enumeración manual, se encuentra un archivo en:

```/var/backups/hidden```

Dentro de este directorio se localiza un archivo *.txt* que contiene la contraseña del usuario **Luisillo**

<img width="1188" height="756" alt="Pasted image 20260219131231" src="https://github.com/user-attachments/assets/5f74257f-924e-452c-ac16-09cc56ae76e8" />

## 👑 Escalada de Privilegios Final

Como usuario **Luisillo**, se revisan los permisos y se identifica un binario que puede ejecutarse como cualquier usuario sin necesidad de contraseña

<img width="976" height="158" alt="Pasted image 20260219131317" src="https://github.com/user-attachments/assets/80b2804c-2906-4d95-aac1-66d852e46322" />

Se consulta [GTFOBins](https://gtfobins.org/)

<img width="948" height="402" alt="Pasted image 20260219131739" src="https://github.com/user-attachments/assets/34b0a75a-0455-4236-ab44-aa4fd45a2000" />

Se adapta el exploit a nuestro caso y se logra ser usuario **root**

<img width="1047" height="162" alt="Pasted image 20260219132122" src="https://github.com/user-attachments/assets/dedb7f16-f7e4-4fad-9463-0cc05014c956" />

## 🏁 Conclusión

Este laboratorio es un recordatorio de que la seguridad no es un solo candado, sino una cadena: desde detalles que parecen menores, como no dejar pistas en el robots.txt, hasta configuraciones críticas de sudo y el manejo de credenciales.
