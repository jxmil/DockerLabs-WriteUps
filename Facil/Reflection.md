# Reflection

## 🔎 Enumeración

Se realiza un escaneo de puertos utilizando la herramienta Nmap, donde se identifican los siguientes servicios abiertos:

* 22/tcp – SSH
* 80/tcp – HTTP

<img width="753" height="347" alt="Pasted image 20251222110213" src="https://github.com/user-attachments/assets/c3abe6fb-0396-4909-bae5-47a2df635acc" />

## 🌐 Análisis de la aplicación web

Al acceder a la página web, se indica que se trata de un laboratorio enfocado en vulnerabilidades ```XSS```, por lo que se procede a resolver los distintos escenarios propuestos

<img width="1118" height="571" alt="Pasted image 20251222110420" src="https://github.com/user-attachments/assets/01a9da9c-5094-46c1-aa57-e40b967f55af" />

## 🧪 Laboratorio 1 – XSS Reflejado

El ```XSS``` reflejado (no persistente) ocurre cuando un atacante inyecta código malicioso (por ejemplo, JavaScript) a través de una URL o parámetro, y la aplicación lo devuelve directamente en la respuesta sin almacenarlo

<img width="646" height="408" alt="Pasted image 20251222120123" src="https://github.com/user-attachments/assets/22d357f6-b5e9-4371-9f4f-5d640b0f1087" />

El script se ejecuta en el navegador de la víctima únicamente cuando esta accede al enlace manipulado.

<img width="602" height="32" alt="Pasted image 20251222120212" src="https://github.com/user-attachments/assets/247c4795-b69d-4c86-94d0-3dd3737c486c" />

## 🧪 Laboratorio 2 – XSS Almacenado

El ```XSS``` almacenado (persistente) permite que el código malicioso inyectado sea guardado permanentemente en el servidor (por ejemplo, en una base de datos)

<img width="676" height="422" alt="Pasted image 20251222125308" src="https://github.com/user-attachments/assets/7a030a41-45e5-467a-a900-1dee928e4240" />

Cuando cualquier usuario visita la página afectada, el script se carga desde el servidor y se ejecuta automáticamente en su navegador, lo que puede permitir:

* Robo de cookies 
* Redirecciones maliciosas
* Ejecución de acciones bajo la sesión del usuario

Al reiniciar la página, el payload continúa ejecutándose, confirmando que se encuentra almacenado en la base de datos

<img width="742" height="750" alt="Pasted image 20251222125454" src="https://github.com/user-attachments/assets/7fb17681-cdee-4346-943e-f485f50d4eda" />

## 🧪 Laboratorio 3 – XSS en DropDowns

Un ```XSS``` en listas desplegables (dropdowns) ocurre cuando la aplicación no valida correctamente los valores seleccionados o enviados por el usuario

<img width="1072" height="827" alt="Pasted image 20251222131818" src="https://github.com/user-attachments/assets/72abd856-70bb-47c8-b054-d04cdedc6ed2" />

Esto permite inyectar código malicioso que se ejecuta cuando otros usuarios interactúan con el dropdown, comprometiendo su sesión o información.

Se prueba modificando los valores enviados desde la URL:

```URL original ```

<img width="892" height="40" alt="Pasted image 20251222131858" src="https://github.com/user-attachments/assets/fd3bc87f-66e3-4cb7-8219-3bd5f426a448" />


```URL modificada```


<img width="907" height="22" alt="Pasted image 20251222132043" src="https://github.com/user-attachments/assets/7df91e2e-01ff-4262-884f-68894431fd16" />

La ejecución del payload confirma la vulnerabilidad

<img width="638" height="650" alt="Pasted image 20251222132127" src="https://github.com/user-attachments/assets/91243084-4409-4578-9da2-b59490ba2201" />

## 🧪 Laboratorio 4 – XSS vía parámetros en URL:

En este escenario, la aplicación indica que se debe agregar el parámetro:

```
?data=
```

A continuación, se introduce un payload ```XSS``` directamente en dicho parámetro, logrando que el código se ejecute en el navegador

<img width="947" height="747" alt="Pasted image 20251222134852" src="https://github.com/user-attachments/assets/5fe25d62-cfe1-4456-b15e-7dba6b15f386" />

Esto evidencia una omisión en la depuración y el filtrado de los datos recibidos a través de la URL

## 🔐 Acceso al sistema 

Tras completar los laboratorios web, se procede a iniciar sesión por **SSH**

<img width="1206" height="600" alt="Pasted image 20251222135004" src="https://github.com/user-attachments/assets/75cc54e9-0185-4c9b-909e-f2bfd374e327" />

<img width="797" height="392" alt="Pasted image 20251222162655" src="https://github.com/user-attachments/assets/afacbc68-d329-42b4-94e3-642006cd4be7" />

Una vez dentro del sistema, se enumeran los binarios que pueden ejecutarse con privilegios elevados. Durante esta revisión, se identifica que el binario:

```
env
```

<img width="638" height="251" alt="Pasted image 20251222170038" src="https://github.com/user-attachments/assets/5b40b424-4ef0-41aa-b32d-3439f31926b6" />

Se consulta [GTFOBins](https://gtfobins.org/) para buscar técnicas relacionadas con el binario

<img width="921" height="322" alt="Pasted image 20251222170224" src="https://github.com/user-attachments/assets/21f5f460-f55f-4f4f-87ac-e23d6b442792" />

Aprovechando esta configuración insegura, se ejecuta el binario de forma adecuada y se obtiene una shell con privilegios de **root**

<img width="527" height="92" alt="Pasted image 20251222170249" src="https://github.com/user-attachments/assets/0bd2db89-c315-4e50-a5e3-d81ad2010f7a" />

## 🏁 Conclusión

Este laboratorio demuestra cómo múltiples variantes de XSS, combinadas con una mala configuración de privilegios a nivel de sistema, pueden derivar en un compromiso completo de la máquina.
