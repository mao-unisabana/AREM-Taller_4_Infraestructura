# 🗒️ Registro de Trabajo en Clase - Taller 4: Mapa de Infraestructura y Diagnóstico Técnico

## 📆 Fecha de la sesión
2026-09-12

## 👥 Integrantes presentes
- Mao Suárez
- Nicolas Clavijo

## 🧠 Actividades realizadas en clase

- Se revisó la [Guía Paso a Paso: Mapa de Infraestructura y Diagnóstico Técnico](guia_paso_a_paso_infraestructura.md), en particular la leyenda de notación y la metodología de 5 pasos (mapa en los pasos 1-3, diagnóstico en los pasos 4-5).
- Se construyó el **mapa de infraestructura borrador** del caso base RedExpress siguiendo los 5 pasos de la guía:
  1. Identificación de componentes: los tres puntos de entrada de cliente (App Móvil Usuario Final, App Móvil Mensajero, Portal Web Operador), el Balanceador de Carga, los API Gateway regionales (Bogotá y Medellín), el Módulo de Procesamiento de Rutas y Paquetes, el Servicio de Monitoreo y Alertas, y la Base de Datos Distribuida.
  2. Agrupación en cuatro zonas: Clientes, Borde/Global, Región Bogotá y Región Medellín.
  3. Trazado del tráfico real: los clientes entran por el balanceador, que enruta a cada API Gateway regional; como Medellín no tiene módulo de rutas propio, su tráfico depende del módulo de Bogotá; ambas regiones escriben en la misma base de datos y reportan al servicio de monitoreo.
  4. Marcado de redundancia: Balanceador de Carga y Base de Datos Distribuida quedaron marcados como instancia única; el API Gateway de Medellín quedó marcado como dependiente de Bogotá para el procesamiento de rutas.
  5. Diagnóstico y priorización de los tres riesgos identificados (ver tabla abajo).
- Decisiones de modelado tomadas:
  - Se agrupó por zona geográfica y no solo por capa técnica, porque el caso base tiene un problema de escalabilidad explícitamente regional (Medellín dependiendo de Bogotá).
  - El Servicio de Monitoreo se conectó con línea punteada a los API Gateway para diferenciar el tráfico de telemetría del tráfico funcional del negocio.
  - Cada riesgo se clasificó en una sola categoría (disponibilidad, rendimiento o escalabilidad) para evitar diagnósticos ambiguos, siguiendo la advertencia de la guía sobre no confundir "cuello de botella" con "punto único de falla".
- Se validó el mapa contra la [checklist de autoevaluación](guia_paso_a_paso_infraestructura.md#5-checklist-de-autoevaluación-antes-de-entregar): todos los componentes están agrupados, las conexiones están trazadas, los componentes críticos indican redundancia, y cada riesgo del diagnóstico referencia un componente específico del mapa.

## 📋 Tabla de diagnóstico (caso base RedExpress)

| Componente | Riesgo diagnosticado | Categoría | Impacto si ocurre | Prioridad |
|---|---|---|---|---|
| Balanceador de Carga (instancia única) | Punto único de falla | Disponibilidad | Toda la plataforma queda inaccesible | Alta |
| Base de Datos Distribuida (escritura única en Bogotá) | Cuello de botella de latencia | Rendimiento | Lentitud en el rastreo en tiempo real para mensajeros fuera de Bogotá | Alta |
| Región Medellín sin módulo de rutas propio | Límite de escalabilidad geográfica | Escalabilidad | No se puede atender el crecimiento de demanda en Medellín sin saturar Bogotá | Media |

## 🧩 Boceto inicial del modelo

> Ver `mapa-borrador.drawio` en esta misma carpeta — abre directamente en draw.io / diagrams.net. Incluye los componentes en riesgo resaltados en rojo, igual que en el paso 5 de la guía.

---

_Este documento resume el trabajo colaborativo realizado durante la sesión del Taller 4 en el curso AREM - Universidad de La Sabana._
