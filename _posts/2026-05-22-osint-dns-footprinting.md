---
title: Auditoría OSINT - Análisis de Superficie de Exposición
date: 2026-05-22 15:00:00 +0200
categories: [Proyectos, Auditoría]
tags: [OSINT, Reconocimiento, DNS, Whois, Footprinting]
---

Informe detallado sobre la recopilación de Inteligencia de Fuentes Abiertas (OSINT) para identificar la superficie de ataque y exposición digital de una organización.

## Metodología y Alcance

* **Footprinting DNS:** Enumeración de registros MX, NS, A y descubrimiento de subdominios para mapear la infraestructura.
* **Análisis Whois:** Obtención de datos de registro de dominios, contactos técnicos y fechas clave de expiración.
* **Exposición en Redes Sociales:** Identificación de perfiles oficiales y posibles vectores de ingeniería social.
* **Herramientas Utilizadas:** ViewDNS, Whois, Dig, Sublist3r y técnicas de Google Dorking.

## Hallazgos Clave

La investigación pasiva permitió identificar servidores de correo críticos, subdominios de desarrollo expuestos y una estructura de nombres de servidor que revela parte de la arquitectura interna, todo ello sin interactuar directamente con los sistemas de la organización.

---
Puedes consultar el informe OSINT completo en el repositorio:
[GitHub - osint-dns-footprinting](https://github.com/xlmega/osint-dns-footprinting)
