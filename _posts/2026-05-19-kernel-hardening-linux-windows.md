---
title: Hardening de Kernel en Linux y Windows
date: 2026-05-19 12:00:00 +0200
categories: [Proyectos, Bastionado]
tags: [Hardening, Kernel, Linux, Windows, Seguridad]
---

Guía detallada sobre el endurecimiento del Kernel en sistemas operativos Windows y Linux para mitigar vulnerabilidades a bajo nivel y fortalecer la seguridad del sistema.

## Hardening en Linux (Ubuntu Server)

* **Parámetros de Sysctl:** Restricción de acceso a logs de kernel (dmesg), desactivación de IPv6 si no es necesario, y protección contra ataques ICMP.
* **Seguridad de Memoria:** Configuración de ASLR y protección contra ejecución en stack.
* **Módulos del Kernel:** Desactivación de carga de módulos innecesarios para reducir la superficie de ataque.

## Hardening en Windows

* **Virtualization-Based Security (VBS):** Activación de HVCI para garantizar la integridad del código en modo kernel.
* **Protecciones de Explotación:** Configuración avanzada de DEP, ASLR y SEHOP.
* **Políticas de Grupo:** Restricción de acceso a controladores no firmados y auditoría de eventos de kernel.

---
Puedes consultar las guías completas en el repositorio:
[GitHub - os-kernel-hardening](https://github.com/xlmega/os-kernel-hardening)
