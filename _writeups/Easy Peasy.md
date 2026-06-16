---
layout: page
title: "Write-up: Easy Peasy"
platform: "TryHackMe"
difficulty: "Medium"
category: "Boot2Root"
---
**Write-up**: Easy Peasy - TryHackMe  
**Fecha**: 01/05/2026  
**Autor**: Adrian Sanchez Torrejon  
**Dificultad**: Medio
**IP de la Máquina**: 10.130.131.232
---

# 1. Reconocimiento y Enumeración

## 1.1 Escaneo de Puertos (Nmap)

Se realiza un escaneo inicial para identificar los puertos abiertos y los servicios en ejecución.

```bash
nmap -sV -p- -Pn 10.130.131.232
```

**Resultados:** 

![image.png](/assets/images/Easy%20Peasy/image.png)

## 1.2 Escaneo Web

Se ejecutó un escaneo de directorios con Gobuster para buscar rutas ocultas en el servidor web.

- **Herramienta usada:** gobuster

`gobuster dir -w /usr/share/wordlists/dirb/common.txt -u http://10.130.131.232:65524`

- **Hallazgos:**

![image.png](/assets/images/Easy%20Peasy/image%201.png)

# 2. Explotación (Acceso Inicial)

## 2.1 Ejecución y Shell

1. Accedir l robots.txt

![image.png](/assets/images/Easy%20Peasy/image%202.png)

1. Hice una curl a la pagina usando el agente

![image.png](/assets/images/Easy%20Peasy/image%203.png)

1. En esta vi una cadena escondida que parecia estar encriptada en baseXX

![image.png](/assets/images/Easy%20Peasy/image%204.png)

1. Por lo que use Cyberchef para decodificarlo (era Base62)

![image.png](/assets/images/Easy%20Peasy/image%205.png)

1. Entramos en el directorio que conseguimos y guardamos la imagen que llama la atencion

![image.png](/assets/images/Easy%20Peasy/image%206.png)

1. Antes de inspeccionar la imagen miramos el codigo y vemos una cadena de texto sospechosa

![image.png](/assets/images/Easy%20Peasy/image%207.png)

1. La desencriptamos usando johntheripper y el diccionario que venia en el reto

![image.png](/assets/images/Easy%20Peasy/image%208.png)

1. Con la contraseña en mano usamos Aperi´Solve para analizar la imagen usando la contraseña que hemos conseguido

![image.png](/assets/images/Easy%20Peasy/image%209.png)

1. Descargamos el archivo oculto

![image.png](/assets/images/Easy%20Peasy/image%2010.png)

1. Aqui viene el usuario y contraseña en binario

![image.png](/assets/images/Easy%20Peasy/image%2011.png)

1. Usamos CyberChef otra vez para sacar la contraseña

![image.png](/assets/images/Easy%20Peasy/image%2012.png)

1. Nos conectamos por ssh a la IP de la victima

![image.png](/assets/images/Easy%20Peasy/image%2013.png)

![image.png](/assets/images/Easy%20Peasy/image%2014.png)

# 3. Escalada de Privilegios (System / Root)

## 3.1 Explotación y Acceso Root

1. Liste las tareas programadas con contrab

![image.png](/assets/images/Easy%20Peasy/image%2015.png)

1. Modifique una tarea que me llamaba la atencion que se ejecutaba como root para que creara una reverse shell

![image.png](/assets/images/Easy%20Peasy/image%2016.png)

1. Puse mi ordenador en modo escucha con netcat para conseguir acceso a Root

![image.png](/assets/images/Easy%20Peasy/image%2017.png)

¡Acceso como **Root** concedido!