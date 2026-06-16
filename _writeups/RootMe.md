---
layout: page
title: "Write-up: RootMe"
platform: "TryHackMe"
difficulty: "Medium"
category: "Boot2Root"
---
**Write-up**: RootMe - TryHackMe  
**Fecha**: 01/05/2026  
**Autor**: Adrian Sanchez Torrejon  
**Dificultad**: Medio
**IP de la Máquina**: 10.129.187.91

---

# 1. Reconocimiento y Enumeración

## 1.1 Escaneo de Puertos (Nmap)

Se realiza un escaneo inicial para identificar los puertos abiertos y los servicios en ejecución.

```bash
nmap -sV 10.129.187.91
```

**Resultados:** 

![image.png](/assets/images/RootMe/image.png)

## 1.2 Escaneo Web

Se ejecutó un escaneo de directorios con Gobuster para buscar rutas ocultas en el servidor web.

- **Herramienta usada:** gobuster

`gobuster dir -u http://10.129.187.91 -w /usr/share/wordlists/dirbuster/directory-list-2.3-small.txt`

- **Hallazgos:**

![image.png](/assets/images/RootMe/image%201.png)

# 2. Explotación (Acceso Inicial)

## 2.2 Ejecución y Shell

1. Accedemos a la pagina /panel

![image.png](/assets/images/RootMe/image%202.png)

1. Descargamos un reverse shell de php y lo modificamos pa poner nuestras variables
2. https://github.com/pentestmonkey/php-reverse-shell/blob/master/php-reverse-shell.php

![image.png](/assets/images/RootMe/image%203.png)

1. Ponemos nc en modo escucha con el puerto especificado

![image.png](/assets/images/RootMe/image%204.png)

1. Subimos el archivo en la pagina cambiandole la extension a ‘.php.phtml’ y le damos a “Veja!”

![image.png](/assets/images/RootMe/image%205.png)

1. Y con eso ya tendriamos el usuario

![image.png](/assets/images/RootMe/image%206.png)

# 3. Escalada de Privilegios (System / Root)

## 3.1 Explotación y Acceso SYSTEM

1. Miramos archivos con permisos incorrectos

![image.png](/assets/images/RootMe/image%207.png)

1. Parece que python es el objetivo miramos en gtfobins el comando para conseguir root

[https://gtfobins.org/gtfobins/python/#suid](https://gtfobins.org/gtfobins/python/#suid)

![image.png](/assets/images/RootMe/image%208.png)

1. Ejecutamos el comando y ya esta

```jsx
python -c 'import os; os.execl("/bin/sh", "sh", "-p")'
```

![image.png](/assets/images/RootMe/image%209.png)

¡Acceso como **Root** concedido!