---
layout: page
title: " Write-ups"
permalink: /writeups/
---

Aquí comparto la resolución técnica de máquinas y retos de ciberseguridad, enfocándome en la metodología y el endurecimiento de sistemas.

---

### Listado de Reportes

<ul>
  {% for report in site.writeups %}
    <li>
      <strong>[{{ report.platform }}]</strong> 
      <a href="{{ report.url | relative_url }}">{{ report.title }}</a> 
      - <span style="color: #888;">{{ report.category }} ({{ report.difficulty }})</span>
    </li>
  {% endfor %}
</ul>

---
*Nota: Estos reportes tienen fines educativos y de auditoría ética.*