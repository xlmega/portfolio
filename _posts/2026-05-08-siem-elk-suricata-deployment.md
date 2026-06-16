---
title: Despliegue de SIEM con ELK y Suricata
date: 2026-05-08 10:00:00 +0200
categories: [Proyectos, Incidentes]
tags: [ELK, Suricata, SIEM, IDS, Docker, CiberSOC]
---

Implementación de una infraestructura de seguridad centralizada basada en el stack **ELK** (Elasticsearch, Logstash, Kibana) e integrada con el IDS **Suricata** para la detección de amenazas en tiempo real.

## Arquitectura del SIEM

* **Detección (Suricata):** Monitoreo de tráfico de red para identificar patrones de ataque (Fuerza Bruta SSH) y generación de alertas en formato `fast.log`.
* **Ingesta (Filebeat):** Recolección y envío automático de logs desde los endpoints hacia el motor de procesamiento.
* **Procesamiento (Logstash):** Normalización y enriquecimiento de los eventos antes de su indexación.
* **Visualización (Kibana):** Creación de Dashboards personalizados para la monitorización continua y la respuesta ante incidentes desde un CyberSOC.

## Caso de Uso: Detección de Fuerza Bruta

El sistema fue validado mediante la simulación de ataques dirigidos, permitiendo verificar el flujo completo desde la generación de la alerta en Suricata hasta su representación gráfica en los paneles de Kibana, asegurando una visibilidad total sobre los intentos de acceso no autorizados.

---
Puedes consultar la configuración técnica y los paneles de control en el repositorio:
[GitHub - elk-suricata-siem-deployment](https://github.com/xlmega/elk-suricata-siem-deployment)
