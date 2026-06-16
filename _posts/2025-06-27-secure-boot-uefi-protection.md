---
title: Seguridad en el Arranque y Protección UEFI
date: 2025-06-27 20:00:00 +0200
categories: [Proyectos, Bastionado]
tags: [UEFI, Secure Boot, BIOS, Hardening, Seguridad de Hardware]
---

Guía técnica y mejores prácticas para el aseguramiento del proceso de arranque (Boot) mediante la configuración avanzada de UEFI y el uso de Secure Boot.

## Medidas de Protección UEFI

* **Secure Boot:** Configuración y gestión de llaves (PK, KEK, db) para garantizar que solo se cargue software firmado y de confianza.
* **Contraseñas de BIOS/UEFI:** Implementación de barreras físicas y lógicas contra el acceso no autorizado a la configuración del sistema.
* **Actualizaciones de Firmware:** Metodología para mantener el microcódigo y el firmware del sistema protegidos contra vulnerabilidades de hardware.

## Beneficios para la Seguridad

El endurecimiento del arranque es la primera línea de defensa contra bootkits y ataques que intentan comprometer el sistema operativo antes de que se activen las protecciones de nivel de software.

---
Puedes consultar la guía completa y el codelab en el repositorio:
[GitHub - secure-boot-protection-guide](https://github.com/xlmega/secure-boot-protection-guide)
