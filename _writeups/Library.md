---
layout: page
title: "Write-up: Library"
platform: "TryHackMe"
difficulty: "Easy"
category: "Boot2Root"
---

**Write-up**: Library - TryHackMe  
**Fecha**: 02/04/2026  
**Autor**: Adrian Sanchez Torrejon  
**Dificultad**: Fácil  
**IP de la Máquina**: 10.130.188.79

---

# 1. Resumen Ejecutivo

La máquina Library es de dificultad fácil y cuenta con un sistema operativo Linux (Ubuntu). El acceso inicial se obtuvo tras enumerar el sistema y conseguir credenciales válidas para el usuario `meliodas`, logrando ingresar a través del servicio SSH. Posteriormente, la escalada de privilegios se realizó aprovechando que el usuario tenía permisos de Sudo para ejecutar un script de Python propio (`bak.py`). Al modificar este script, se logró elevar los privilegios y obtener acceso total como el usuario root.

- **Acceso a Usuario**: Conseguido
- **Acceso a Root**: Conseguido

# 2. Reconocimiento y Enumeración

## 2.1 Escaneo de Puertos (Nmap)

Se realiza un escaneo inicial para identificar los puertos abiertos y los servicios en ejecución.

```bash
nmap -sV -O -Pn --script vuln 10.130.188.79
```

**Resultados:**

![image.png](/assets/images/library/image.png)

# 3. Explotación (Acceso Inicial)

## 3.1 Identificación de la Vulnerabilidad

Tras realizar el análisis del sitio web en el puerto 80 se identifico el usuario `meliodas`.

![image.png](/assets/images/library/image%201.png)

## 3.2 Ejecución y Shell

1. Busque un modulo para conseguir las credenciales válidas para el usuario `meliodas` con un ataque de fuerza bruta con diccionario.

![image.png](/assets/images/library/image%202.png)

1. Ejecute el modulo y consegui como resultado las credenciales `meliodas:iloveyou1`

![image.png](/assets/images/library/image%203.png)

1. Procedí a conectarme a la máquina objetivo utilizando el protocolo SSH con el usuario descubierto:

![image.png](/assets/images/library/image%204.png)

Tras introducir la contraseña correcta, la sesión se estableció con éxito y obtuve acceso al sistema.

# 4. Escalada de Privilegios (System / Root)

## 4.1 Enumeración Interna

Al listar los archivos en el directorio home del usuario, se identificó un script llamado `bak.py`. Al revisar las reglas de sudoers (o los permisos de ejecución), se determinó que el usuario actual podía ejecutar este script utilizando Python con permisos de superusuario (`sudo`).

## 4.2 Explotación y Acceso Root

Debido a que el archivo se encontraba en el directorio personal del usuario (`/home/meliodas/bak.py`) y teníamos control total sobre él, procedí a realizar un "hijacking" o modificación del script.

- Primero, eliminé el archivo original:
    
    `rm -f bak.py`
    
- Posteriormente, creé un nuevo archivo `bak.py` inyectando código para spawnear una shell interactiva a través del módulo `pty` de Python:
    
    `echo 'import pty; pty.spawn("/bin/bash")' > bak.py`
    
- Finalmente, ejecuté el script haciendo uso de los privilegios `sudo`:
    
    `sudo python /home/meliodas/bak.py`
    

![image.png](/assets/images/library/image%205.png)

A pesar de algunos errores iniciales de sintaxis por caracteres invisibles en la terminal (los cuales se corrigieron rápidamente editando el archivo con `nano`), el comando se ejecutó de forma exitosa elevando el prompt.

¡Acceso como **Root** concedido!

# 5. Recomendaciones de Mitigación

Para evitar que un atacante replique este vector de ataque, se sugieren las siguientes mitigaciones:

1. **Robustecimiento de SSH:** Implementar políticas estrictas de contraseñas para evitar ataques de fuerza bruta exitosos sobre usuarios válidos, así como deshabilitar el login por contraseña en favor del uso de llaves criptográficas SSH.
2. **Principio de Menor Privilegio (Sudoers):** No permitir que los usuarios ejecuten con permisos de `sudo` scripts o binarios que se encuentren en directorios donde el propio usuario tenga permisos de escritura. De ser indispensable, el archivo deberá pertenecer a `root` y contar con permisos de solo lectura para el resto de usuarios.