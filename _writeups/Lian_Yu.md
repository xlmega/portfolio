---
layout: page
title: "Write-up: Lian_Yu"
platform: "TryHackMe"
difficulty: "Medium"
category: "Boot2Root"
---
**Write-up**: **Lian_Yu** - TryHackMe  
**Fecha**: 01/05/2026  
**Autor**: Adrian Sanchez Torrejon  
**Dificultad**: Medio
**IP de la Máquina**: 10.130.144.214

---

# 1. Reconocimiento y Enumeración

## 1.1 Escaneo de Puertos (Nmap)

Se realiza un escaneo inicial para identificar los puertos abiertos y los servicios en ejecución.

```bash
nmap -sV -O -Pn 10.130.144.214
```

**Resultados:** 

![image.png](/assets/images/Lian_Yu/image.png)

## 1.2 Escaneo Web

Se ejecutó un escaneo de directorios con Gobuster para buscar rutas ocultas en el servidor web.

- **Herramienta usada:** gobuster

```bash
gobuster dir -u http://10.130.144.214 -w /usr/share/wordlists/dirbuster/directory-list-2.3-small.txt
```

- **Hallazgos:**

![image.png](/assets/images/Lian_Yu/image%201.png)

Se ejecutó un escaneo de directorios con Gobuster para buscar rutas ocultas en el directorio island.

- **Herramienta usada:** gobuster

```bash
gobuster dir -u http://10.130.144.214/island -w /usr/share/wordlists/dirbuster/directory-list-2.3-small.txt
```

- **Hallazgos:**

![image.png](/assets/images/Lian_Yu/image%202.png)

Se ejecutó un escaneo de directorios con Gobuster para buscar rutas ocultas en el directorio island/2100 buscando especificamente .ticket.

- **Herramienta usada:** gobuster

```bash
gobuster dir -u http://10.130.144.214/island/2100 -w /usr/share/wordlists/dirbuster/directory-list-2.3-small.txt -x .ticket
```

- **Hallazgos:**

`/green_arrow.ticket`

# 2. Explotación (Acceso Inicial)

## 2.1 Ejecución y Shell

1. Revisando el codigo de la pagina island/ encontre la palabra `vigilante` escondida

![image.png](/assets/images/Lian_Yu/image%203.png)

1. Revisando el codigo de la pagina island/2100/ me pide buscar un .ticket

![image.png](/assets/images/Lian_Yu/image%204.png)

1. Consegui el ticket pero parece estar codificado

![image.png](/assets/images/Lian_Yu/image%205.png)

1. Intentando en Cyberchef consigo decodificarlo de Base58

![image.png](/assets/images/Lian_Yu/image%206.png)

1. Me consegui conectar al servidor ftp con las credenciales `vigilante:!#th3h00d`

![image.png](/assets/images/Lian_Yu/image%207.png)

1. Descargue los archivos que habia y revise la carpeta de home para ver los otros usuario que habia

![image.png](/assets/images/Lian_Yu/image%208.png)

1. Pase Leave_me_alone por Aperi primero ya que no enseñaba ninguna imagen y consegui la palabra `password` cuando arreglaron las cabeceras

![image.png](/assets/images/Lian_Yu/image%209.png)

1. Analice el archivo aa.jpg usando la palabra de antes y saco un archivo escondido

![image.png](/assets/images/Lian_Yu/image%2010.png)

1. Dentro del zip encontramos una posible contraseña para el otro usuario

![image.png](/assets/images/Lian_Yu/image%2011.png)

1. Nos conectamos por ssh

Credenciales: `slade:M3tahuman` 

![image.png](/assets/images/Lian_Yu/image%2012.png)

# 3. Escalada de Privilegios (System / Root)

## 3.1 Explotación y Acceso SYSTEM

1. Compruebo los permiso del usuario con sudo

![image.png](/assets/images/Lian_Yu/image%2013.png)

1. Reviso gtfobins para ver como conseguir el root

![image.png](/assets/images/Lian_Yu/image%2014.png)

1. Ejecuto el exploit

![image.png](/assets/images/Lian_Yu/image%2015.png)

¡Acceso como **Root** concedido!