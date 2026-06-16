---
layout: page
title: "Write-up: Wgel CTF"
platform: "TryHackMe"
difficulty: "Medium"
category: "Boot2Root"
---
**Write-up**: **Wgel CTF** - TryHackMe  
**Fecha**: 01/05/2026  
**Autor**: Adrian Sanchez Torrejon  
**Dificultad**: Medio
**IP de la Máquina**: 10.130.184.160

---

# 1. Reconocimiento y Enumeración

## 1.1 Escaneo de Puertos (Nmap)

Se realiza un escaneo inicial para identificar los puertos abiertos y los servicios en ejecución.

```bash
nmap -sV -O -Pn 10.130.184.160
```

**Resultados:** 

![image.png](/assets/images/Wgel%20CTF/image.png)

## 1.2 Escaneo Web

Se ejecutó un escaneo de directorios con Gobuster para buscar rutas ocultas en el servidor web.

- **Herramienta usada:** gobuster

```bash
gobuster dir -u http://10.130.184.160 -w /usr/share/wordlists/dirbuster/directory-list-2.3-small.txt
```

- **Hallazgos:**

![image.png](/assets/images/Wgel%20CTF/image%201.png)

Se ejecutó un escaneo de directorios con Gobuster para buscar rutas ocultas en el subdirectorio sitemap.

- **Herramienta usada:** gobuster

```bash
gobuster dir -u http://10.130.188.249/sitemap/ -w /usr/share/wordlists/dirb/common.txt -t 25 -x php,html,txt -q
```

- **Hallazgos:**

![image.png](/assets/images/Wgel%20CTF/image%202.png)

# 2. Explotación (Acceso Inicial)

## 2.1 Ejecución y Shell

1. Revisando el codigo fuente consigo un posible usuario `jessie`.

![image.png](/assets/images/Wgel%20CTF/image%203.png)

1. Al entar en el directorio sitemap/.ssh/ consigo un rsa

![image.png](/assets/images/Wgel%20CTF/image%204.png)

1. Lo descargue y use para conectarme como jessie

![image.png](/assets/images/Wgel%20CTF/image%205.png)

![image.png](/assets/images/Easy%20Peasy/image%2014.png)

# 3. Escalada de Privilegios (System / Root)

## 3.1 Explotación y Acceso SYSTEM

1. Mire que permisos tengo con sudo -l

![image.png](/assets/images/Wgel%20CTF/image%206.png)

1. Reviso con gtfobins que puedo hacer con ese comando

![image.png](/assets/images/Wgel%20CTF/image%207.png)

1. Levante un servidor con un archivo sudoers con todos los permisos para jessie

![image.png](/assets/images/Wgel%20CTF/image%208.png)

1. Descargue el archivo en `/etc/sudoers.d/jessie`  y me loguee como root

![image.png](/assets/images/Wgel%20CTF/image%209.png)

¡Acceso como **Root** concedido!