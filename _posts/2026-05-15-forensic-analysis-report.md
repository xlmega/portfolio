---
title: Informe de Análisis Forense - Incidente en Servidor Linux
date: 2026-05-15 12:00:00 +0200
categories: [Proyectos, Análisis Forense]
tags: [DFIR, WordPress, Linux, Volatility]
---

Investigación técnica de un incidente de seguridad detectado en un servidor Linux, comprometido mediante la explotación de una vulnerabilidad en una aplicación web WordPress.

## Resumen Ejecutivo

Del examen técnico-forense practicado sobre la imagen de disco y el volcado de memoria RAM, se concluye la existencia de un ataque de tipo **defacement**, materializado mediante la explotación de una vulnerabilidad en el plugin **ReFlex Gallery**.

La causa inmediata del compromiso se vincula a la deficiente validación de archivos en el componente `FileUploader`, permitiendo la subida de ficheros PHP ejecutables en el directorio de recursos estáticos.

## Objetivos de la Investigación

1. **Determinar la vulnerabilidad explotada.**
2. **Identificar el origen del ataque (IP 94.242.54.22).**
3. **Analizar la posible exfiltración de información.**
4. **Proponer medidas correctivas.**

## Metodología y Herramientas

Se emplearon herramientas estándares de la industria para garantizar la integridad de las evidencias:
* **Autopsy/Sleuth Kit:** Análisis de imagen de disco.
* **Volatility:** Análisis de memoria RAM.
* **WPScan:** Reconocimiento de vulnerabilidades en WordPress.

---
Puedes consultar el informe completo y las evidencias en el repositorio:
[GitHub - forensic-analysis-p5b](https://github.com/IES-Rafael-Alberti/P5b-Analisis-Forense-G1)
