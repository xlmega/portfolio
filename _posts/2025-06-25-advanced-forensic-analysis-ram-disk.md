---
title: Análisis Forense Avanzado de Memoria RAM y Disco
date: 2025-06-25 18:00:00 +0200
categories: [Proyectos, Análisis Forense]
tags: [DFIR, Volatility, Autopsy, Forense, RAM, Disco]
---

Investigación forense profunda sobre un incidente de seguridad, incluyendo el análisis de artefactos en memoria RAM y disco para identificar el vector de ataque y el impacto del compromiso.

## Metodología de Análisis

* **Análisis de Memoria (RAM):** Uso de **Volatility** para identificar procesos maliciosos, conexiones de red activas durante el incidente y comandos ejecutados en la consola.
* **Análisis de Sistema de Archivos (Disco):** Utilización de **Autopsy** y herramientas de línea de comandos para examinar registros de Apache, identificar código vulnerable y detectar archivos modificados (Defacement).
* **Correlación de Eventos:** Cruce de datos entre la actividad en memoria y los logs de disco para construir una línea temporal precisa del ataque.

## Hallazgos Técnicos

La investigación permitió determinar que el atacante explotó una vulnerabilidad en una aplicación web (Apache) para obtener ejecución remota de comandos, logrando escalar privilegios y exfiltrar información sensible antes de realizar un ataque de defacement.

---
Puedes consultar el informe forense detallado y los anexos en el repositorio:
[GitHub - forensic-analysis-p5b](https://github.com/xlmega/forensic-analysis-p5b)
