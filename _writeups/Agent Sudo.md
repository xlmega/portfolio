---
layout: page
title: "Write-up: Agent Sudo"
platform: "TryHackMe"
difficulty: "Medium"
category: "Boot2Root"
---
**Write-up**: Agent Sudo - TryHackMe  
**Fecha**: 01/05/2026  
**Autor**: Adrian Sanchez Torrejon  
**Dificultad**: Medio  
**IP de la Máquina**: 10.128.172.193

---

# 1. Reconocimiento y Enumeración

## 1.1 Escaneo de Puertos (Nmap)

Se realiza un escaneo inicial para identificar los puertos abiertos y los servicios en ejecución.

```bash
nmap -sV -O -Pn 10.130.161.177
```

**Resultados:** 

![image.png](/assets/images/Agent%20Sudo/image.png)

# 2. Explotación (Acceso Inicial)

## 2.1 Ejecución y Shell

1. Hice un curl a la pagina

![image.png](/assets/images/Agent%20Sudo/image%201.png)

1. Hice un curl especificando el agente

![image.png](/assets/images/Agent%20Sudo/image%202.png)

1. Hice un ataque por fuerza bruta ya que menciona que su contraseña es debil

![image.png](/assets/images/Agent%20Sudo/image%203.png)

1. Me conecte por ftp

![image.png](/assets/images/Agent%20Sudo/image%204.png)

1. Descargue los archivos que parecian relevantes

![image.png](/assets/images/Agent%20Sudo/image%205.png)

1. Parece que las imagenes tienen escondidas la contraseña a un usuario

![image.png](/assets/images/Agent%20Sudo/image%206.png)

1. Use binwal en el archivo y luego use dd para extraer el archivo

![image.png](/assets/images/Agent%20Sudo/image%207.png)

1. Use johntheripper para extraer la contraseña del zip

![image.png](/assets/images/Agent%20Sudo/image%208.png)

1. De esto sacamos un mensaje con una parte codificada

![image.png](/assets/images/Agent%20Sudo/image%209.png)

1. Esto parece ser base64

![image.png](/assets/images/Agent%20Sudo/image%2010.png)

1. Analizamos la otra imagen con Aperi’Solve

![image.png](/assets/images/Agent%20Sudo/image%2011.png)

1. Descargamos el archivo escondido

![image.png](/assets/images/Agent%20Sudo/image%2012.png)

1. Parece que en el mensaeje hay unas credenciales

![image.png](/assets/images/Agent%20Sudo/image%2013.png)

1. Nos conectamos por ssh

![image.png](/assets/images/Agent%20Sudo/image%2014.png)

![image.png](/assets/images/Easy%20Peasy/image%2014.png)

# 3. Escalada de Privilegios (System / Root)

## 3.1 Explotación y Acceso SYSTEM

1. Comprobamos los permiso que tiene james con sudo

![image.png](/assets/images/Agent%20Sudo/image%2015.png)

1. Buscando un explot encontramos este

[https://www.exploit-db.com/download/47502](https://www.exploit-db.com/download/47502)

Lo descargamos y lo compatimos con la maquina objetivo

![image.png](/assets/images/Agent%20Sudo/image%2016.png)

1. Descargamos el exploit y lo ejecutamos

![image.png](/assets/images/Agent%20Sudo/image%2017.png)

¡Acceso como **Root** concedido!