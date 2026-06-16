---
layout: page
title: "Write-up: Year of the Rabbit"
platform: "TryHackMe"
difficulty: "Medium"
category: "Boot2Root"
---
**Write-up**: **Year of the Rabbit** - TryHackMe  
**Fecha**: 01/05/2026  
**Autor**: Adrian Sanchez Torrejon  
**Dificultad**: Medio
**IP de la Máquina**: 10.130.145.206

---

# 1. Reconocimiento y Enumeración

## 1.1 Escaneo de Puertos (Nmap)

Se realiza un escaneo inicial para identificar los puertos abiertos y los servicios en ejecución.

```bash
nmap -sV -O -Pn 10.130.145.206
```

**Resultados:** 

![image.png](/assets/images/Ignite/image.png)

## 1.2 Escaneo Web

Se ejecutó un escaneo de directorios con Gobuster para buscar rutas ocultas en el servidor web.

- **Herramienta usada:** gobuster

```bash
gobuster dir -u http://10.130.145.206 -w /usr/share/wordlists/dirbuster/directory-list-2.3-small.txt
```

- **Hallazgos:**

![image.png](/assets/images/Kenobi/image%201.png)

# 2. Explotación (Acceso Inicial)

## 2.1 Ejecución y Shell

1. En asset/style.css me recomiendan mirar un directorio

![image.png](/assets/images/Year%20of%20the%20Rabbit/image.png)

1. La pagina me da la pista de desactivar javascript, ademas me doy cuenta de que parece que me han redirigido

![image.png](/assets/images/Year%20of%20the%20Rabbit/image%201.png)

1. Intento un curl en ambas paginas parece que en una no puedo hacer nada y la otra me intenta RickRollear por lo que voy a usar Burpsuite para ver mejor las redirreciones

![image.png](/assets/images/Year%20of%20the%20Rabbit/image%202.png)

1. Usando el burpsuite en la primera pagina me permitio descubrir un directorio escondido.

![image.png](/assets/images/Year%20of%20the%20Rabbit/image%203.png)

1. Accedi al repostirio usando burpsuite para evitar que me redirigar pude ver una imagen.

![image.png](/assets/images/Year%20of%20the%20Rabbit/image%204.png)

1. Use `strings Hot_Babe.png`  para revisar el archivo y dentro vi un usuario y posibles contraseñas

![image.png](/assets/images/Year%20of%20the%20Rabbit/image%205.png)

1. Guarde las contraseñas y hice un ataque de fuerza bruta sobre el servidor de ftp

![image.png](/assets/images/Year%20of%20the%20Rabbit/image%206.png)

1. En el servidor parece haber un archivo con credenciales codificadas

![image.png](/assets/images/Year%20of%20the%20Rabbit/image%207.png)

1. Lo decodifique y consegui asi las credenciales `eli:DSpDiM1wAEwid` 

![image.png](/assets/images/Year%20of%20the%20Rabbit/image%208.png)

1. Accedi por ssh

![image.png](/assets/images/Year%20of%20the%20Rabbit/image%209.png)

Parece que nos han dejado un mensaje secreto

Obtuve el acceso como el usuario de servicios web: `www-data`.

## 2.2 Pivoteo de Usuario

1. Buscamos el secreto que nos menciona el mensaje

![image.png](/assets/images/Year%20of%20the%20Rabbit/image%2010.png)

1. Parece que hemos conseguido la contraseña de otro usuario

Credenciales: `gwendoline:MniVCQVhQHUNI`

![image.png](/assets/images/Year%20of%20the%20Rabbit/image%2011.png)

1. Nos logueamos con estas nuevas credenciales

![image.png](/assets/images/Year%20of%20the%20Rabbit/image%2012.png)

# 3. Escalada de Privilegios (System / Root)

## 3.1 Explotación y Acceso SYSTEM

1. Compruebo los permisos con sudo -l, parece que puede ejecutar vi sobre un archivo como sudo

![image.png](/assets/images/Year%20of%20the%20Rabbit/image%2013.png)

1. Revisando la version de sudo encontre que este es vulnerable a un exploit

[https://www.exploit-db.com/exploits/47502](https://www.exploit-db.com/exploits/47502)

![image.png](/assets/images/Year%20of%20the%20Rabbit/image%2014.png)

1. Ejecutamos el comando 

```bash
sudo -u#-1 /usr/bin/vi /home/gwendoline/user.txt
:!/bin/bash
```

![image.png](/assets/images/Year%20of%20the%20Rabbit/image%2015.png)

![image.png](/assets/images/Year%20of%20the%20Rabbit/image%2016.png)

1. con esto ya lo habriamos conseguido

![image.png](/assets/images/Year%20of%20the%20Rabbit/image%2017.png)

¡Acceso como **Root** concedido!