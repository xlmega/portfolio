---
layout: page
title: "Write-up: GamingServer"
platform: "TryHackMe"
difficulty: "Medium"
category: "Boot2Root"
---
**Write-up**: **GamingServer** - TryHackMe  
**Fecha**: 01/05/2026  
**Autor**: Adrian Sanchez Torrejon  
**Dificultad**: Medio
**IP de la Máquina**: 10.128.162.48

---

# 1. Reconocimiento y Enumeración

## 1.1 Escaneo de Puertos (Nmap)

Se realiza un escaneo inicial para identificar los puertos abiertos y los servicios en ejecución.

```bash
nmap -sV -O -Pn 10.128.162.48
```

**Resultados:** 

![image.png](/assets/images/GamingServer/image.png)

## 1.2 Escaneo Web

Se ejecutó un escaneo de directorios con Gobuster para buscar rutas ocultas en el servidor web.

- **Herramienta usada:** gobuster

```jsx
gobuster dir -u http://10.128.162.48 -w /usr/share/wordlists/dirbuster/directory-list-2.3-small.txt
```

- **Hallazgos:**

![image.png](/assets/images/GamingServer/image%201.png)

# 2. Explotación (Acceso Inicial)

## 2.1 Ejecución y Shell

1. Viendo el codigo de la pagina veo que se menciona el usuario john

![image.png](/assets/images/GamingServer/image%202.png)

1. En los otros 2 directorios encontre uan lista de contraseñas y una clave rsa, me descargue ambas

![image.png](/assets/images/GamingServer/image%203.png)

![image.png](/assets/images/GamingServer/image%204.png)

1. Usamos johntheripper para conseguir el passphrase con el diccionario que nos dan

Passphrase: `letmein`

![image.png](/assets/images/GamingServer/image%205.png)

1. Nos conectamos por ssh usando el rsa

![image.png](/assets/images/GamingServer/image%206.png)

# 3. Escalada de Privilegios (System / Root)

## 3.1 Explotación y Acceso ROOT

1. Descargamos el linpeas y levantamos un servidor para descargarlo

![image.png](/assets/images/GamingServer/image%207.png)

1. Lo descargamos en el ordenador de la victima, le cambiamos los permisos y lo ejecutamos

![image.png](/assets/images/GamingServer/image%208.png)

1. Parece que tenemos que usar una vulnerabilidad relacionada con lxd

![image.png](/assets/images/GamingServer/image%209.png)

1. Creamos una imagen con la vulnerabilidad

![image.png](/assets/images/GamingServer/image%2010.png)

1. Se la pasamos a la victima con scp

![image.png](/assets/images/GamingServer/image%2011.png)

Creamos un contenedor y le montamos el disco dentro del contenedor para poder acceder a el

![image.png](/assets/images/GamingServer/image%2012.png)

¡Acceso como **Root** concedido!