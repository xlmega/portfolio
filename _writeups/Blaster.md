---
layout: page
title: "Write-up: Blaster"
platform: "TryHackMe"
difficulty: "Medium"
category: "Boot2Root"
---

**Write-up**: Blaster - TryHackMe  
**Fecha**: 02/04/2026  
**Autor**: Adrian Sanchez Torrejon  
**Dificultad**: Media   
**IP de la Máquina**: 10.128.185.197

---

# 1. Resumen Ejecutivo

La máquina Blaster es de dificultad fácil y cuenta con un sistema operativo Windows. El acceso inicial se obtuvo tras enumerar un sitio web que albergaba un blog, donde se descubrieron credenciales de un usuario local en los comentarios. El vector de entrada fue a través del servicio de Escritorio Remoto (RDP). La escalada de privilegios se consiguió explotando una vulnerabilidad conocida en el Control de Cuentas de Usuario (UAC) ligada al instalador `hhupd.exe` y al navegador Internet Explorer (CVE-2019-1388).

- **Acceso a Usuario**: Conseguido
- **Objetivo Root (SYSTEM)**: Conseguido

# 2. Reconocimiento y Enumeración

## 2.1 Escaneo de Puertos (Nmap)

Se realiza un escaneo inicial para identificar los puertos abiertos y los servicios en ejecución.

```bash
nmap -sV -O -Pn --script vuln 10.128.185.197
```

**Resultados:**

![image.png](/assets/images/Blaster/image.png)

## 2.1 Escaneo Web

Se ejecutó un escaneo de directorios con Gobuster para buscar rutas ocultas en el servidor web.

- **Herramienta usada:** gobuster

`gobuster dir -u http://10.130.166.32 -w /usr/share/wordlists/dirbuster/directory-list-2.3-small.txt`

- **Hallazgos:**

![image.png](/assets/images/Blaster/image%201.png)

# 3. Explotación (Acceso Inicial)

## 3.1 Identificación de la Vulnerabilidad

Al acceder al directorio `/retro/`, se descubrió un blog activo sobre videojuegos y cultura retro. Al inspeccionar los artículos, puntualmente en el post titulado **"Ready Player One"**, se observó un comentario dejado por el autor **Wade**.

El comentario contenía el siguiente texto: *"Leaving myself a note here just in case I forget how to spell it: parzival"*.
Esto expuso un set de credenciales válidas:

- **Usuario:** `Wade`
- **Contraseña:** `parzival`

![image.png](/assets/images/Blaster/image%202.png)

## 3.2 Ejecución y Shell

Sabiendo que el puerto 3389 estaba abierto para conexiones RDP, utilicé las credenciales encontradas para autenticarme de manera remota.

```bash
xfreerdp /v:10.128.185.197  /u:Wade /p:parzival
```

![image.png](/assets/images/Blaster/image%203.png)

Se estableció la sesión con éxito y obtuve acceso directo al entorno gráfico del escritorio de Wade.

# 4. Escalada de Privilegios (System / Root)

## 4.1 Enumeración Interna

Estando en el escritorio de Wade, se detectó el archivo ejecutable `hhupd.exe`. Este binario es comúnmente vulnerable a bypasses de UAC si el sistema no está actualizado.

## 4.2 Explotación y Acceso SYSTEM

Aproveché la vulnerabilidad de elevación de privilegios del Certificado de Windows ejecutando los siguientes pasos:

1. Ejecuté `hhupd.exe` como administrador, levantando la ventana de confirmación del Control de Cuentas de Usuario (UAC). Y Hice clic en el hipervínculo de la firma: **"Show information about the publisher's certificate"**.

![image.png](/assets/images/Blaster/image%204.png)

1. En la nueva ventana de detalles del certificado, seleccioné el emisor: **"VeriSign Commercial Software Publishers CA"**. Esto forzó la apertura del navegador Internet Explorer en un contexto de altos privilegios (SYSTEM).

![image.png](/assets/images/Blaster/image%205.png)

1. En Internet Explorer, desplegué el menú de opciones y me dirigí a `File -> Save as...`

![image.png](/assets/images/Blaster/image%206.png)

1. En la ventana del explorador para guardar el archivo, escribí la ruta completa `C:\Windows\System32\`

![image.png](/assets/images/Blaster/image%207.png)

1. Y luego escribí la ruta completa `C:\Windows\System32\cmd.exe` y la ejecuté.

![image.png](/assets/images/Blaster/image%208.png)

La consola CMD se abrió en pantalla. Al verificar la identidad de la sesión en la terminal:

![image.png](/assets/images/Blaster/image%209.png)

¡Acceso como **SYSTEM** concedido!

# 5. Recomendaciones de Mitigación

Para evitar el compromiso de un sistema similar, se deben aplicar las siguientes mitigaciones:

1. **Gestión Segura de Credenciales:** Prohibir terminantemente el almacenamiento o exposición de contraseñas de usuarios en áreas de libre lectura (como comentarios de blogs, notas locales en texto claro o código fuente).
2. **Aplicación de Parches:** Instalar de manera prioritaria las actualizaciones de Microsoft que solucionan el exploit **CVE-2019-1388** en el sistema operativo Windows para evitar elevaciones de privilegios por medio de bypass en el Control de Cuentas de Usuario (UAC).
3. **Cierre de Servicios Innecesarios:** Si el uso de RDP no es indispensable para el funcionamiento básico de las aplicaciones del servidor, se sugiere deshabilitarlo o restringir su acceso únicamente a través de una VPN corporativa.