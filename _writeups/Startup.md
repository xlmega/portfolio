---
layout: page
title: "Write-up: Startup"
platform: "TryHackMe"
difficulty: "Medium"
category: "Boot2Root"
---
**Write-up**: Startup - TryHackMe  
**Fecha**: 01/05/2026  
**Autor**: Adrian Sanchez Torrejon  
**Dificultad**: Medio
**IP de la Máquina**: 10.129.148.247

---

# 1. Reconocimiento y Enumeración

## 1.1 Escaneo de Puertos (Nmap)

Se realiza un escaneo inicial para identificar los puertos abiertos y los servicios en ejecución.

```bash
nmap -sV -Pn 10.129.148.247
```

**Resultados:** 

![image.png](/assets/images/Startup/image.png)

## 1.2 Escaneo Web

Se ejecutó un escaneo de directorios con Gobuster para buscar rutas ocultas en el servidor web.

- **Herramienta usada:** gobuster

`gobuster dir -u http://10.129.148.247 -w /usr/share/wordlists/dirbuster/directory-list-2.3-small.txt`

- **Hallazgos:**

![image.png](/assets/images/Startup/image%201.png)

## 1.3 Escaneo FTP

Consegui loguearme en el servidor ftp como anonymous y descargar los archivos

![image.png](/assets/images/Startup/image%202.png)

# 2. Explotación (Acceso Inicial)

## 2.1 Ejecución y Shell

1. Parece que el directorio /files nos da acceso al servidor ftp

![image.png](/assets/images/Startup/image%203.png)

1. Descargamos un reverse-shell.php, lo configuramos y subimos al servidor ftp

![image.png](/assets/images/Startup/image%204.png)

1. Accedemos al archivo desde la pagina web

![image.png](/assets/images/Startup/image%205.png)

1. Ya tenemos acceso al sistema

![image.png](/assets/images/Startup/image%206.png)

## 2.2 Pivoteo de Usuario

1. Mirando en la carpeta de incidentes vi un archivo de wireshark por lo que lo copie a la carpeta de ftp para poder descargarla

![image.png](/assets/images/Startup/image%207.png)

1. Descargue el archivo

![image.png](/assets/images/Startup/image%208.png)

1. Lo abri con el wireshark y le di a la opcion Follow>TCP Stream

![image.png](/assets/images/Startup/image%209.png)

1. Parece que en los registro sale alguien intentandose moverse a la carpeta de lennie y luego usando una contraseña que parecia fallar en otra cuenta por lo que voy a intentar usarla para lennie

Credenciales: `lennie:c4ntg3t3n0ughsp1c3`

![image.png](/assets/images/Startup/image%2010.png)

1. Me conecte usando las credenciales mencionadas por ssh

![image.png](/assets/images/Startup/image%2011.png)

# 3. Escalada de Privilegios (System / Root)

## 3.1 Explotación y Acceso SYSTEM

De acuerdo con las técnicas de **GTFOBins**, el binario `iconv` se puede usar para escribir datos en archivos con permisos restringidos usando el parámetro `-o` (output).

1. Mirando por las carpteas vi un script que pertenece a root y parece correr el script /etc/print.sh

![image.png](/assets/images/Startup/image%2012.png)

1. Descargue pspy para ver si el root estaba corriendo el script y lo subi al servidor ftp

![image.png](/assets/images/Startup/image%2013.png)

1. Parece que el script corre todos los minutos

![image.png](/assets/images/Startup/image%2014.png)

1. El script que corre le pertenece a lennie por lo que vamos a cambiarlo por un reverse shell

![image.png](/assets/images/Startup/image%2015.png)

¡Acceso como **Root** concedido!