---
layout: page
title: "Write-up: LazyAdmin"
platform: "TryHackMe"
difficulty: "Medium"
category: "Boot2Root"
---
**Write-up**: LazyAdmin - TryHackMe  
**Fecha**: 01/05/2026  
**Autor**: Adrian Sanchez Torrejon  
**Dificultad**: Medio
**IP de la Máquina**: 10.128.172.193

---

# 1. Reconocimiento y Enumeración

## 1.1 Escaneo de Puertos (Nmap)

Se realiza un escaneo inicial para identificar los puertos abiertos y los servicios en ejecución.

```bash
nmap -sV -O -Pn 10.129.169.1
```

**Resultados:** 

![image.png](/assets/images/LazyAdmin/image.png)

## 1.2 Escaneo Web

Se ejecutó un escaneo de directorios con Gobuster para buscar rutas ocultas en el servidor web.

- **Herramienta usada:** gobuster

`gobuster dir -u http://10.129.169.1 -w /usr/share/wordlists/dirbuster/directory-list-2.3-small.txt`

- **Hallazgos:**

![image.png](/assets/images/LazyAdmin/image%201.png)

Se ejecutó un escaneo de subdirectorios con Gobuster para buscar rutas ocultas en el servidor web.

- **Herramienta usada:** gobuster

`gobuster dir -u http://10.129.169.1/content -w /usr/share/wordlists/dirbuster/directory-list-2.3-small.txt`

- **Hallazgos:**

![image.png](/assets/images/LazyAdmin/image%202.png)

# 2. Explotación (Acceso Inicial)

## 2.1 Ejecución y Shell

1. En el directorio content/inc/ existe una carpeta llamada mysql_backup por lo que accedemos a ella y descargamos el archivo que contiene

![image.png](/assets/images/LazyAdmin/image%203.png)

1. Mirando el archivo encontramos un posible usuario admin/manager y un hash que parece contener su contraseña.

![image.png](/assets/images/LazyAdmin/image%204.png)

1. Usamos john the ripper para conseguir la contraseña

![image.png](/assets/images/LazyAdmin/image%205.png)

1. Vamos a usar las credenciales que hemos conseguido en el login que hay en content/as/

Credenciales `manager:Password123`

![image.png](/assets/images/LazyAdmin/image%206.png)

1. En la pestaña de Ads vamos a meterle el codigo de una reverse shell por php

![image.png](/assets/images/LazyAdmin/image%207.png)

1. Accedemos a la shell desde content/inc/ads/

![image.png](/assets/images/LazyAdmin/image%208.png)

# 3. Escalada de Privilegios (System / Root)

## 3.1 Explotación y Acceso SYSTEM

1. Comprobamos los permisos que tenemos con sudo, parece que podemos ejecutar un script con perl

![image.png](/assets/images/LazyAdmin/image%209.png)

1. Revisando el script parece que este ejecuta un sh que podemos modificar

![image.png](/assets/images/LazyAdmin/image%2010.png)

1. Modificamos el script para crear un reverse shell y ya esta

![image.png](/assets/images/LazyAdmin/image%2011.png)

¡Acceso como **Root** concedido!