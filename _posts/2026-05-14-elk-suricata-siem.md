---
title: Despliegue de SIEM con ELK y Suricata
date: 2026-05-14 15:00:00 +0200
categories: [Proyectos, Gestión de Incidentes]
tags: [SIEM, ELK, Suricata, SOC]
---

Este proyecto detalla la implementación de un sistema de gestión de eventos e información de seguridad (SIEM) utilizando el stack **ELK** (Elasticsearch, Logstash, Kibana) integrado con **Suricata** como sistema de detección de intrusiones (IDS).

## Componentes del Sistema

* **Suricata:** Generación de alertas de red y logs de tráfico.
* **Filebeat:** Recolección y envío de logs hacia Logstash.
* **Logstash:** Procesamiento y enriquecimiento de eventos.
* **Elasticsearch:** Almacenamiento y motor de búsqueda.
* **Kibana:** Visualización y creación de dashboards de seguridad.

## Casos de Uso Implementados

1. **Detección de Escaneos de Puertos:** Alertas automáticas ante actividad de Nmap.
2. **Monitorización de Fuerza Bruta SSH:** Visualización de intentos de acceso fallidos.
3. **Análisis de Tráfico HTTP:** Identificación de peticiones maliciosas.

---
Puedes ver la configuración y los dashboards en el repositorio:
[GitHub - elk-suricata-siem-deployment](https://github.com/IES-Rafael-Alberti/2526-u2-2-2-1-elk-y-casouso-xlmega)
