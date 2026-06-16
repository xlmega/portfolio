---
layout: page
title: "Write-up: Brooklyn Nine Nine"
platform: "TryHackMe"
difficulty: "Medium"
category: "Boot2Root"
---
**Write-up**: Brooklyn Nine Nine - TryHackMe  
**Fecha**: 01/05/2026  
**Autor**: Adrian Sanchez Torrejon  
**Dificultad**: Medio
**IP de la Máquina**: 10.128.172.193

---

# 1. Reconocimiento y Enumeración

## 1.1 Escaneo de Puertos (Nmap)

Se realiza un escaneo inicial para identificar los puertos abiertos y los servicios en ejecución.

```bash
nmap -sV -O -Pn 10.129.188.229
```

**Resultados:** 

![image.png](/assets/images/Brooklyn%20Nine%20Nine/image.png)

# 2. Explotación (Acceso Inicial)

## 2.1 Ejecución y Shell

1. Nos concetamos al servidor ftp como “anonymous” y leemos el unico archivo

![image.png](/assets/images/Brooklyn%20Nine%20Nine/image%201.png)

1. Usamos hydra para conseguir la contraseña por fuerza bruta

![image.png](/assets/images/Brooklyn%20Nine%20Nine/image%202.png)

1. Nos conectamos por ssh con las credenciales que hemos conseguido

![image.png](/assets/images/Brooklyn%20Nine%20Nine/image%203.png)

# 3. Escalada de Privilegios (System / Root)

## 3.1 Explotación y Acceso SYSTEM

1. Enumeramos los permisos de sudo

![image.png](/assets/images/Brooklyn%20Nine%20Nine/image%204.png)

1. Buscamos en gtfobins

![image.png](/assets/images/Brooklyn%20Nine%20Nine/image%205.png)

1. Ejecutamos el comando que nos da y ay esta

![image.png](/assets/images/Brooklyn%20Nine%20Nine/image%206.png)

¡Acceso como **Root** concedido!