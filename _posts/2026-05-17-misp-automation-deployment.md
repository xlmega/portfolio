---
title: Despliegue y Automatización de MISP
date: 2026-05-17 10:00:00 +0200
categories: [Proyectos, Automatización]
tags: [MISP, CTI, Docker, Python, Threat Intelligence]
---

Análisis técnico y despliegue de la plataforma MISP (Malware Information Sharing Platform) para la gestión de inteligencia de amenazas, incluyendo la automatización de consultas mediante PyMISP.

## Objetivos del Proyecto

* **Despliegue con Docker:** Implementación de un entorno reproducible para MISP.
* **Inteligencia de Amenazas:** Configuración de feeds públicos y análisis del modelo de datos (Events, Attributes, Objects).
* **Automatización:** Desarrollo de scripts en Python para la interacción con la API de MISP.

## Automatización con PyMISP

Se han desarrollado varios scripts para facilitar la gestión de eventos:
- `consulta_misp.py`: Búsqueda general de eventos y atributos.
- `misp_automation.py`: Creación automatizada de eventos y carga de IOCs.

---
Puedes consultar el informe completo y los scripts en el repositorio:
[GitHub - misp-automation-deployment](https://github.com/xlmega/misp-automation-deployment)
