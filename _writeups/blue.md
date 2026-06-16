---
layout: page
title: "Write-up: Blue"
platform: "TryHackMe"
difficulty: "Easy"
category: "Boot2Root"
---

**Write-up**: **Blue** - TryHackMe  
**Fecha**: 01/04/2026  
**Autor**: Adrian Sanchez Torrejon  
**Dificultad**: Fácil  
**IP de la Máquina**: 10.129.143.209  

---

# 1. Resumen Ejecutivo

La máquina Blue es de dificultad fácil y cuenta con un sistema operativo Windows 7 Professional. El objetivo principal fue identificar y explotar una vulnerabilidad en el protocolo de compartición de archivos SMBv1. El acceso inicial se logró mediante el conocido exploit **EternalBlue (MS17-010)**, el cual permite la ejecución remota de código (RCE). Debido a la naturaleza de esta vulnerabilidad, el acceso obtenido otorgó directamente los máximos privilegios en el sistema.

- **Acceso a Usuario**: Se consiguio directamente SYSTEM
- **Acceso a Root(SYSTEM)**: Conseguido

# 2. Reconocimiento y Enumeración

## 2.1 Escaneo de Puertos (Nmap)

Se realiza un escaneo inicial para identificar los puertos abiertos y los servicios en ejecución.

```bash
nmap -sV --script vuln 10.129.143.209
```

**Resultados:**

![image.png](/assets/images/Blue/image.png)

# 3. Explotación (Acceso Inicial)

## 3.1 Identificación de la Vulnerabilidad

Cruzando la información del escaneo, el resultado `ms17-010` coincide directamente con el exploit **EternalBlue**. Procedí a abrir Metasploit Framework para buscar el módulo correspondiente.

```bash
msfconsole
msf > search ms17-010
```

Seleccioné el módulo de explotación: `exploit/windows/smb/ms17_010_eternalblue`

![image.png](/assets/images/Blue/image%201.png)

## 3.2 Ejecución y Shell

1. Configuré el parámetro `RHOSTS` con la IP de la máquina objetivo (`10.129.143.209`)

![image.png](/assets/images/Blue/image%202.png)

1. Al correr el modulo vemos que aunque la maquina es vulnerable parece que no consigue crear una sesion

![image.png](/assets/images/Blue/image%203.png)

1. Al estar trabajando dentro de la red de TryHackMe, las conexiones inversas requieren especificar la interfaz de la VPN. Procedí a cambiar el parámetro `LHOST` para que apuntara a mi IP asignada por OpenVPN (`192.168.146.227`).

![image.png](/assets/images/Blue/image%204.png)

# 4. Escalada de Privilegios (System / Root)

Debido a que el exploit EternalBlue aprovecha una corrupción de memoria en el kernel a través del protocolo SMB, el payload inyectado se ejecuta directamente bajo el contexto del sistema. No fue necesario realizar una fase de enumeración interna ni una escalada de privilegios secundaria.

# 5. Recomendaciones de Mitigación

Para remediar las debilidades explotadas en esta máquina, se sugieren las siguientes acciones correctivas:

1. **Actualización del Sistema:** Aplicar de forma inmediata el parche de seguridad de Microsoft MS17-010 para remediar la vulnerabilidad en el protocolo SMBv1.
2. **Desactivación de Protocolos Obsoletos:** Deshabilitar por completo el protocolo SMBv1 en toda la infraestructura de red, ya que carece de las protecciones de seguridad modernas y ha sido largamente descontinuado.
3. **Segmentación de Red:** Limitar y filtrar el acceso al puerto 445 (SMB) mediante reglas estrictas de firewall perimetral e interno, permitiendo el tráfico únicamente hacia los servidores que requieran estrictamente compartir recursos.