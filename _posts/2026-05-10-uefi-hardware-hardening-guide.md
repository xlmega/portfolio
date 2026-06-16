---
title: Hardening de Hardware - Protección del Firmware UEFI
date: 2026-05-10 12:00:00 +0200
categories: [Proyectos, Bastionado]
tags: [UEFI, Secure Boot, Firmware, Hardening, Seguridad de Hardware]
---

Guía técnica centrada en el endurecimiento del hardware mediante la configuración avanzada de la interfaz UEFI, estableciendo una base de confianza desde el arranque del sistema.

## Pilares de la Protección del Firmware

* **Contraseñas de Administración (Supervisor):** Implementación de barreras de acceso para impedir modificaciones no autorizadas en la configuración de bajo nivel.
* **Secure Boot Avanzado:** Gestión de bases de datos de firmas (PK, KEK, db) para asegurar que solo se ejecuten cargadores de arranque y kernels verificados.
* **Seguridad de Almacenamiento:** Configuración de contraseñas a nivel de dispositivo (Power-On) que complementan el cifrado de disco.

## Importancia Estratégica

La seguridad del firmware es crítica para prevenir ataques persistentes como rootkits de nivel de hardware, que operan por debajo del sistema operativo y pueden evadir la mayoría de las soluciones de seguridad basadas en software.

---
Puedes consultar la guía técnica y los pasos de configuración en el repositorio:
[GitHub - uefi-security-guide](https://github.com/xlmega/uefi-security-guide)
