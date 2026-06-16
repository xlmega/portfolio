---
layout: page
title: "Write-up: Poster"
platform: "TryHackMe"
difficulty: "Medium"
category: "Boot2Root"
---
**Write-up**: Poster - TryHackMe  
**Fecha**: 01/05/2026  
**Autor**: Adrian Sanchez Torrejon  
**Dificultad**: Medio
**IP de la Máquina**: 10.128.172.193

---

# 1. Reconocimiento y Enumeración

## 1.1 Escaneo de Puertos (Nmap)

Se realiza un escaneo inicial para identificar los puertos abiertos y los servicios en ejecución.

```bash
nmap -sV -Pn 10.129.133.32
```

**Resultados:** 

![image.png](/assets/images/Poster/image.png)

# 2. Explotación (Acceso Inicial)

## 2.1 Ejecución y Shell

1. Ejecutamos un modulo de msf para conseguir las credenciales de postgres

![image.png](/assets/images/Poster/image%201.png)

1. Usamos el modulo multi/postgres/postgres_copy_from_program_cmd_exec con las credenciales que hemos conseguido “postgres:password”

![image.png](/assets/images/Poster/image%202.png)

1. Spawneamos una terminal con python 

```jsx
python3 -c 'import pty; pty.spawn("/bin/bash")'
```

![image.png](/assets/images/Poster/image%203.png)

## 2.2 Pivoteo de usuarios

1. Buscando por carpetas vi que habia 2 usuarios y encontre las credenciales de dark

![image.png](/assets/images/Poster/image%204.png)

1. Me conecte al usuario dark

![image.png](/assets/images/Poster/image%205.png)

1. Buscando en /var/www/html encontre las credenciales de alison

![image.png](/assets/images/Poster/image%206.png)

1. Me loguee como alison

![image.png](/assets/images/Poster/image%207.png)

# 3. Escalada de Privilegios (System / Root)

## 3.1 Explotación y Acceso SYSTEM

1. Como tenia la contraseña y alison tenia suficientes permisos use sudo su

![image.png](/assets/images/Poster/image%208.png)

¡Acceso como **Root** concedido!