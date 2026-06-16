---
layout: page
title: "Write-up: Bounty Hacker"
platform: "TryHackMe"
difficulty: "Medium"
category: "Boot2Root"
---
**Write-up**: Bounty Hacker - TryHackMe  
**Fecha**: 01/05/2026  
**Autor**: Adrian Sanchez Torrejon  
**Dificultad**: Medio
**IP de la Máquina**: 10.129.130.75

---

# 1. Reconocimiento y Enumeración

## 1.1 Escaneo de Puertos (Nmap)

Se realiza un escaneo inicial para identificar los puertos abiertos y los servicios en ejecución.

```bash
nmap -sV -Pn 10.129.130.75
```

**Resultados:** 

![image.png](/assets/images/Bounty%20Hacker/image.png)

# 2. Explotación (Acceso Inicial)

## 2.1 Ejecución y Shell

1. Nos conectamos como anonymous al ftp y descargamos los archivos

![image.png](/assets/images/Bounty%20Hacker/image%201.png)

1. Mirando los archivos uno parece una lista de posibles contraseñas y el otro nos da un posible usuario “lin”

![image.png](/assets/images/Bounty%20Hacker/image%202.png)

1. Usamos hydra para realizar un ataque por fuerza bruta usando el documento para las contraseñas

![image.png](/assets/images/Bounty%20Hacker/image%203.png)

1. Nos logueamos usando las credenciales que hemos conseguido por ssh

![image.png](/assets/images/Bounty%20Hacker/image%204.png)

# 3. Escalada de Privilegios (System / Root)

## 3.1 Explotación y Acceso SYSTEM

1. Miramos los permiso con sudo -l

![image.png](/assets/images/Bounty%20Hacker/image%205.png)

1. Buscamos en gtfobins tar

[https://gtfobins.org/gtfobins/tar/](https://gtfobins.org/gtfobins/tar/)

![image.png](/assets/images/Bounty%20Hacker/image%206.png)

1. Ejecutamos el comando

![image.png](/assets/images/Bounty%20Hacker/image%207.png)

¡Acceso como **Root** concedido!