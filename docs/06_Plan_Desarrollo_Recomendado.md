# 📄 Documento 6 — Plan de Desarrollo Recomendado

**Proyecto:** HOFLOC S.A. — Plataforma Web de Gestión Agropecuaria  
**Versión:** v1.2 | **Fecha:** 2026-04-10 | *Ajustado a plazo máximo de 12 semanas*

---

## Principios del Plan

1. **Paralelismo desde el día 1**: Los 3 equipos inician simultáneamente con entregables diferenciados.
2. **Valor incremental**: Cada fase entrega funcionalidad real y usable.
3. **BD primero en módulos críticos**: Las tablas de Animal, Ordeño, Gestación y Medicamentos deben estar listas antes de que UI y Formularios integren.
4. **Plazo fijo de 12 semanas**: Los módulos de menor prioridad (Proveedores, Proyecciones, Reportes PDF/Excel) se documentan como Fase Futura y quedan fuera del alcance del semestre.

> ⏰ **INICIO ACORDADO EN VISITA:** En las próximas 3 semanas se comienza con formularios digitales. La meta inmediata es reemplazar los formularios físicos del veterinario y empezar a capturar datos digitalmente para generar gráficas.

---

## 🔵 FASE 1 — Fundamentos y MVP (Semanas 1–4)

**Objetivo:** Sistema autenticado con captura de datos operativos diarios del hato y ordeño.

### Módulos incluidos:
- **A-10** Gestión de Usuarios (login/logout básico)
- **A-02** Gestión de Hembras (CRUD con arete interno, foto, padres)
- **A-04** Registro de Ordeño Diario (AM/PM + flag comercial/destete)
- **A-06** Registro de Nacimientos (arete, madre, sexo, peso, foto)
- **P-01** Página de Inicio del portal público
- **P-04** Formulario de Contacto (WhatsApp + correo)

### Por equipo:

| Equipo | Entregables Fase 1 |
|--------|-------------------|
| **BD** | Esquema completo: `tbl_Animal` (con `arete_interno`, `arete_mida`, `foto_url`, `madre_id`, `padre_id`), `tbl_OrdenoRegistro` (con campo `destino`), `tbl_Usuario`, `tbl_Potrero`, `tbl_Lote`, `tbl_Reproductor`, `tbl_MensajeContacto`, `tbl_FuenteCliente`. Scripts de migración + seed con datos reales. |
| **Formularios** | Login/logout con JWT. CRUD de hembras con validaciones. Formulario de registro de nacimiento. Formulario de ordeño AM/PM con flag comercial/destete. Restricción de ordeño a estado LACT. |
| **Interfaz** | Sistema de diseño base. Layout panel interno + navegación lateral. Tabla de hembras. Planilla de ordeño con indicador 🚫 si hay retiro activo. Formulario de nacimiento. Landing page pública. |

### Dependencias:
- BD entrega esquema en día 2–3 para que los otros equipos diseñen contra él.

### ✅ MVP al final de Fase 1 (Semana 4):
> Un empleado puede autenticarse, gestionar el hato de hembras, registrar nacimientos de terneros, y capturar la producción diaria AM/PM. Un cliente puede visitar la página y contactar por WhatsApp o correo.

---

## 🟡 FASE 2 — Gestación, Medicamentos y Alertas (Semanas 5–8)

**Objetivo:** Implementar la lógica reproductiva completa, trazabilidad de medicamentos y alertas sanitarias — los tres pilares de riesgo identificados en la visita de campo.

### Módulos incluidos:
- **A-03** Control de Gestación y Reproducción (celo 21 días, palpación 32, alerta ≥60 días)
- **A-07** 🔴 Trazabilidad de Medicamentos + bloqueo de leche
- **A-08** 🔴 Alertas Sanitarias y Vacunación (motivado por pérdida de 7 vacas)
- **A-01** Dashboard General (KPIs + panel de alertas activas)

### Por equipo:

| Equipo | Entregables Fase 2 |
|--------|-------------------|
| **BD** | Tablas `tbl_Gestacion`, `tbl_MedicamentoAplicado`, `tbl_CalendarioVacunacion`, `tbl_VacunaAplicada`. Job diario: recalcula días de gestación, verifica retiros vencidos y alertas de vacuna. Vista `vw_alertas_activas`. |
| **Formularios** | Registro de gestación (tipo servicio IA/natural, reproductor, fecha). Alerta de celo a 21 días. Alerta gestación ≥60 días. Registro de medicamento con cálculo `fecha_fin_retiro`. Bloqueo en planilla de ordeño. Calendario de vacunación con alertas 7 días antes / 🔴 al vencer. |
| **Interfaz** | Semáforo visual de gestación. Indicador 🚫 en planilla de ordeño. Panel de alertas en dashboard (medicamentos + vacunas + gestación + próximos partos). KPI cards en A-01. |

### Dependencias:
- Fase 1 completada (tablas `tbl_Animal` y `tbl_OrdenoRegistro` disponibles)
- BD entrega tablas de Gestación y Medicamentos al inicio de semana 5

---

## 🟢 FASE 3 — Estadísticas, Catálogo Público y Cierre (Semanas 9–12)

**Objetivo:** Curvas de producción por vaca, catálogo de venta en el portal público, pesaje de terneros con alerta MIDA, y posicionamiento de marca.

### Módulos incluidos:
- **A-05** Estadísticas de Producción (curvas de lactancia, promedios, comparativo por lote)
- **A-06** Pesaje de terneros + curva de crecimiento + alerta MIDA a 60 días
- **P-02** Catálogo de Animales en Venta (fichas tipo Amazon)
- **P-03** Solicitud de Visita
- **P-05** Posicionamiento / Sobre Nosotros (marca ecológica)
- **P-06** Campo ¿Cómo nos conociste?

### Por equipo:

| Equipo | Entregables Fase 3 |
|--------|-------------------|
| **BD** | Tabla `tbl_PesajeTernero`, `tbl_SolicitudVisita`. Vistas `vw_produccion_semanal`, `vw_produccion_mensual`. Alerta de ternero a los 60 días. Campo `arete_mida` en `tbl_Animal`. |
| **Formularios** | Cálculo de promedios y curvas de producción. Pesaje de ternero con ganancia diaria. Alerta de reporte MIDA. Formulario de solicitud de visita. Captura de fuente del cliente. |
| **Interfaz** | Gráficas de curva de lactancia por vaca. Barras comparativas por lote. Catálogo de animales con fichas individuales (foto + padres + producción + CTA). Secciones Sobre Nosotros y Posicionamiento. |

### Dependencias:
- Fases 1 y 2 completadas
- Datos de ordeño reales acumulados (con datos seed si es necesario)

### ✅ Entregable final (Semana 12):
> Sistema completo en producción: panel interno con gestión del hato, ordeño diario, alertas de gestación/medicamento/vacuna, curvas de producción. Portal público con catálogo de animales en venta y formulario de contacto.

---

## ⚪ FASE FUTURA — Módulos diferidos (fuera del plazo de 12 semanas)

Estos módulos son importantes pero no críticos para el funcionamiento diario. Se documentan para ser retomados en una segunda fase post-semestre.

| Módulo | Razón de diferimiento |
|--------|----------------------|
| **A-09** Gestión de Proveedores | No bloquea ninguna operación crítica |
| **A-11** Proyecciones del Hato | Datos ya en Excel; migración puede esperar |
| **A-12** Reportes PDF/Excel | Funcionalidad de valor agregado, no MVP |

---

## Hitos clave del proyecto (12 semanas)

| Hito | Semana | Entregable |
|------|--------|------------|
| 🚀 Kick-off | **Inmediato** | Todos los equipos alinean con este documento. Inicio en 3 semanas. |
| 🗄️ BD v1 disponible | 1 | Esquema Fase 1 completo con seed de datos reales |
| 🔐 Login funcional | 2 | Primer acceso al panel interno |
| 📋 Hato digital + nacimientos | 3 | Lista de hembras y registro de terneros reemplaza planilla física |
| 🍼 **Ordeño digital — MVP** | 4 | Registro AM/PM funcional + flag leche/destete |
| 🔬 BD Fase 2 disponible | 5 | Gestación, medicamentos y vacunas en BD |
| 🤰 Alertas de gestación | 6 | Notificaciones automáticas ≥60 días + celo a 21 días |
| 💉 **Medicamentos y vacunas** | 7 | Trazabilidad activa + bloqueo de leche con retiro |
| 📊 Dashboard + alertas integradas | 8 | Panel de alertas completo en producción |
| 📈 Estadísticas y curvas | 10 | Curvas de lactancia por vaca + promedios por lote |
| 🛒 Catálogo público | 11 | Fichas de animales en venta con fotos |
| 🌐 **Lanzamiento completo** | 12 | Portal público + panel completo en producción |

---

## Stack Tecnológico Recomendado

> ⚠️ **DECISIÓN PENDIENTE** — Seleccionar stack antes del inicio de desarrollo.

| Capa | Opción A (Recomendada) | Opción B |
|------|------------------------|----------|
| **Frontend** | Next.js + React | Vue.js + Vite |
| **Backend** | Node.js + Express | Python + FastAPI |
| **Base de Datos** | PostgreSQL | MySQL 8+ |
| **Autenticación** | JWT + bcrypt | Supabase Auth |
| **Gráficas** | Chart.js / Recharts | ApexCharts |
| **PDF/Excel** | jsPDF + SheetJS | Puppeteer (PDF server-side) |
| **Hosting** | VPS (DigitalOcean/Render) | Shared hosting con cPanel |

---

*Documento generado para revisión del equipo humano. Todas las secciones marcadas con ⚠️ DECISIÓN PENDIENTE requieren validación antes de iniciar el desarrollo.*
