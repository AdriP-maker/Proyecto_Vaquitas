# 📄 Documento 6 — Plan de Desarrollo Recomendado

**Proyecto:** HOFLOC S.A. — Plataforma Web de Gestión Agropecuaria  
**Versión:** v1.0 | **Fecha:** 2026-04-08

---

## Principios del Plan

1. **Paralelismo desde el día 1**: Los 3 equipos pueden iniciar simultáneamente con entregables diferenciados.
2. **Valor incremental**: Cada fase entrega funcionalidad real y usable, no solo avances técnicos.
3. **BD primero en módulos críticos**: Las tablas centrales de Animal, Gestación y Ordeño deben estar listas antes de que UI y Formularios puedan integrar.
4. **Portal público al final de Fase 1**: Es independiente y da visibilidad temprana del proyecto.

---

## 🔵 FASE 1 — Fundamentos y MVP (Semanas 1–6)

**Objetivo:** Sistema funcional mínimo que permita digitalizar las dos operaciones más críticas y diarias de la finca: el control del hato y el registro de ordeño.

### Módulos incluidos:
- **A-08** Gestión de Usuarios (autenticación básica)
- **A-02** Gestión de Hembras (CRUD básico)
- **A-04** Registro de Ordeño Diario
- **P-01** Página de Inicio del portal público
- **P-04** Formulario de Contacto

### Por equipo:

| Equipo | Entregables Fase 1 |
|--------|--------------------|
| **BD** | Esquema completo de tablas: `Animal`, `OrdeñoRegistro`, `Usuario`, `Potrero`, `Lote`, `Reproductor`, `MensajeContacto`. Scripts de migración. Datos de prueba (seed) basados en el hato real. |
| **Formularios** | Sistema de login/logout con JWT. CRUD de hembras con validaciones. Formulario de ordeño AM/PM con cálculo de total diario. Validación de estado LACT para ordeño. |
| **Interfaz** | Sistema de diseño (colores, tipografía, componentes base). Layout del panel interno. Tabla de hembras. Formulario de ordeño tipo planilla. Landing page pública. |

### Dependencias previas: 
- Ninguna (inicio paralelo)
- BD entrega esquema en día 3 para que Formularios e Interfaz diseñen contra él

### ✅ MVP al final de Fase 1:
> Un empleado puede autenticarse, ver y editar la lista de hembras del hato, y registrar la producción diaria de leche AM/PM para cada vaca en lactancia. Un cliente puede visitar la página institucional y dejar un mensaje.

---

## 🟡 FASE 2 — Módulos Reproductivos y Estadísticas (Semanas 7–12)

**Objetivo:** Implementar el seguimiento de gestación con alertas automáticas y el primer sistema de estadísticas de producción.

### Módulos incluidos:
- **A-03** Gestión de Gestación (con alertas ≥ 60 días)
- **A-01** Dashboard General
- **A-05** Estadísticas de Producción
- **A-06** Pesaje y Crecimiento de Terneros
- **P-02** Catálogo de Productos
- **P-03** Solicitud de Visita

### Por equipo:

| Equipo | Entregables Fase 2 |
|--------|--------------------|
| **BD** | Tablas `Gestacion`, `PesajeTernero`, `SolicitudVisita`. Vistas `v_produccion_semanal`, `v_produccion_mensual`. Query de alertas de gestación. Job de actualización diaria de días de gestación. |
| **Formularios** | Lógica de gestación: registro, cálculo de días, disparo de alertas. Cálculo de estadísticas de producción. Formulario de pesaje con cálculo de ganancia. Lógica de solicitud de visita con confirmación. |
| **Interfaz** | Semáforo visual de gestación. Gráficas de producción (línea y barras). Curva de crecimiento de terneros. Dashboard con KPIs. Catálogo de productos en portal. |

### Dependencias previas:
- Fase 1 completada (tablas Animal y OrdeñoRegistro disponibles)
- BD entrega tabla Gestación en semana 7 para desbloquear Formularios

---

## 🟢 FASE 3 — Módulos de Gestión y Reportes (Semanas 13–18)

**Objetivo:** Completar la plataforma con gestión de proveedores, proyecciones del hato y sistema de reportes exportables.

### Módulos incluidos:
- **A-07** Gestión de Proveedores
- **A-09** Proyecciones del Hato
- **A-10** Módulo de Reportes (PDF y Excel)
- **P-05** Noticias / Blog (⚠️ **DECISIÓN PENDIENTE**: confirmar si se incluye)

### Por equipo:

| Equipo | Entregables Fase 3 |
|--------|--------------------|
| **BD** | Tabla `Proveedor`, `ProyeccionHato`. Consultas optimizadas para reportes de producción, estado reproductivo e inventario de terneros. |
| **Formularios** | CRUD de proveedores. Importación/edición de proyecciones. Lógica de generación de PDFs y archivos Excel. |
| **Interfaz** | Formulario y lista de proveedores. Vista de tabla de proyecciones editable. Botones de exportación con previsualización. |

### Dependencias previas:
- Fases 1 y 2 completadas
- Módulo A-05 (estadísticas) disponible para generar reportes

---

## Hitos clave del proyecto

| Hito | Semana | Entregable |
|------|--------|------------|
| 🚀 Kick-off y revisión de documentación | 1 | Todos los equipos alinean dudas con este documento |
| 🗄️ BD v1 disponible | 1–2 | Esquema Fase 1 con seed de datos reales |
| 🔐 Login funcional | 3 | Primer acceso al panel interno |
| 📋 Hato digital | 4–5 | Lista de hembras reemplaza la planilla física |
| 🍼 Ordeño digital | 5–6 | Registro AM/PM funcional — **MVP** |
| 🤰 Alertas de gestación | 9 | Notificaciones automáticas ≥ 60 días |
| 📊 Estadísticas v1 | 10–11 | Gráficas de producción disponibles |
| 📄 Reportes | 16–17 | Exportación PDF/Excel |
| 🌐 Lanzamiento completo | 18 | Portal público + panel completo en producción |

---

## Stack Tecnológico Recomendado

> ⚠️ **DECISIÓN PENDIENTE** — Seleccionar stack antes del inicio de desarrollo. Las siguientes son las opciones más alineadas con el tipo de proyecto:

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
