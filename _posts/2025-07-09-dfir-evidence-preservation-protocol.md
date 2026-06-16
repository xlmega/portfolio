---
title: Adquisición Forense y Preservación de Evidencias Digitales
date: 2025-07-09 11:00:00 +0200
categories: [Proyectos, Análisis Forense]
tags: [DFIR, Cadena de Custodia, Hashing, FTK Imager, E01]
---

Guía práctica y ejecución de protocolos para la recolección, adquisición y preservación de evidencias digitales, garantizando la integridad técnica y la validez legal.

## Metodología DFIR Aplicada

* **Adquisición Segura:** Creación de imágenes forenses bit-a-bit utilizando **bloqueadores de escritura hardware** para evitar cualquier alteración de la fuente original.
* **Integridad Criptográfica:** Generación y verificación de hashes (SHA-256) inmediatamente después de la adquisición para asegurar que la copia es idéntica al original.
* **Cadena de Custodia:** Documentación exhaustiva de cada paso del proceso, desde la recepción del dispositivo hasta su almacenamiento seguro, cumpliendo con estándares internacionales.

## Herramientas y Formatos

El proceso se apoya en herramientas estándar de la industria como **FTK Imager**, utilizando el formato **E01** para encapsular la evidencia junto con sus metadatos y sumas de verificación, facilitando su análisis posterior en plataformas como Autopsy o EnCase.

---
Puedes consultar el protocolo detallado y la bitácora de investigación en el repositorio:
[GitHub - dfir-evidence-preservation](https://github.com/xlmega/dfir-evidence-preservation)
