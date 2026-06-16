---
title: Sistema RAG para Consultas Normativas y Legales
date: 2026-05-05 10:00:00 +0200
categories: [Proyectos, Inteligencia Artificial]
tags: [IA, RAG, LLM, Python, Normativa, LegalTech]
---

Desarrollo de un sistema de Generación Aumentada por Recuperación (RAG) diseñado para realizar consultas precisas sobre corpus normativos y jurídicos utilizando modelos de lenguaje locales.

## Arquitectura del Sistema

* **Motor Local:** Uso de **Ollama** para la ejecución de modelos de lenguaje sin dependencia de la nube.
* **Base de Datos Vectorial:** Implementación de **ChromaDB** para el almacenamiento y recuperación eficiente de fragmentos de texto basados en embeddings.
* **Flujo Agéntico:** Diseño de una arquitectura multiagente que incluye subagentes especializados en normativa y correctores de estilo/formato.
* **Procesamiento de Documentos:** Ingesta de archivos Markdown y PDFs para crear una base de conocimiento jurídica actualizada.

## Casos de Uso y Aplicación

El sistema permite a profesionales del derecho y la ciberseguridad realizar consultas sobre regulaciones complejas, obteniendo respuestas fundamentadas en el corpus cargado y minimizando las alucinaciones del modelo gracias a la técnica RAG.

---
Puedes consultar el código del MVP y la documentación del diseño en el repositorio:
[GitHub - regulatory-agentic-rag-system](https://github.com/xlmega/regulatory-agentic-rag-system)
