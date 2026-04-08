# 📄 Documento 2 — Asignación de Responsabilidades por Equipo

**Proyecto:** HOFLOC S.A. — Plataforma Web de Gestión Agropecuaria  
**Versión:** v1.0 | **Fecha:** 2026-04-08

---

## 2.1 Tabla de Responsabilidades

| Módulo | Equipo Interfaz (UI/UX) | Equipo Formularios | Equipo Base de Datos |
|--------|-------------------------|--------------------|----------------------|
| **P-01 Inicio** | Diseño de landing con secciones Hero, Sobre Nosotros, Galería, Contacto rápido. Responsive mobile-first. | — | — |
| **P-02 Catálogo** | Componentes de tarjeta de producto con imagen, precio y CTA. Filtros visuales por categoría. | Lógica de disponibilidad dinámica (si hay stock de terneros en venta). | Vista/consulta de tabla `Producto` con disponibilidad. |
| **P-03 Solicitud Visita** | Diseño del formulario de solicitud. Confirmación visual tras envío. | Validaciones: fecha no puede ser pasada, número de personas > 0, campos obligatorios. Envío de correo de confirmación. | Tabla `SolicitudVisita`. Índice por fecha para consultas de agenda. |
| **P-04 Contacto** | Formulario simple con feedback visual de envío. | Validación de email, anti-spam básico (honeypot o reCAPTCHA). | Tabla `MensajeContacto` con timestamp. |
| **A-01 Dashboard** | Layout de tarjetas KPI (vacas activas, litros hoy, alertas, próximos partos). Gráficas resumen. | Lógica de agregación de datos para los KPI. Actualización en tiempo semi-real (polling o WebSocket). | Vistas/consultas optimizadas: `v_resumen_diario`, `v_alertas_activas`. |
| **A-02 🔴 Gestión Hembras** | Tabla paginada con columnas: N° Hembra, Nombre, Estado, Detalle, Días Gest., Días Lact., Reproductor, Potrero, Lote. Fila destacada para alertas. Formulario de ficha individual. | Validaciones: estado reproductivo válido (LACT/NVIE/SECA/NLEV), reproductor existente en catálogo, potrero válido. Lógica de cambio de estado. | Tabla `Animal`. Relaciones con `Potrero`, `Lote`, `Reproductor`. Índice en `estado` y `dias_gestacion`. |
| **A-03 🔴 Gestión Gestación** | Vista de tabla filtrable por días de gestación. Indicador visual de semáforo (verde < 60, amarillo 60–80, rojo > 80). Tarjeta de detalle por animal preñado. | Lógica de cálculo de días de gestación a partir de F. Preñ. Disparo de alertas al superar 60 días. | Tabla `Gestacion` vinculada a `Animal`. Función/trigger para calcular `dias_gestacion`. Índice en `fecha_prenez`. |
| **A-04 🔴 Ordeño Diario** | Interfaz de captura tipo planilla (AM/PM por vaca). Similar al formulario físico "Pesa de Leche". Totales calculados en tiempo real. | Validaciones: litros AM y PM ≥ 0, vaca debe estar en estado LACT, fecha no puede ser futura. Lógica de suma diaria. | Tabla `OrdeñoRegistro`. Índice en `(animal_id, fecha)`. Partición o archivado mensual recomendado. |
| **A-05 🔴 Estadísticas** | Gráficas de línea (producción por vaca en el tiempo), barras (comparativo por lote), tablas de promedios. Filtros por rango de fechas, vaca y lote. | Lógica de cálculo de promedios semanales/mensuales. Formateo de datos para librerías de gráficas. | Vistas `v_produccion_semanal`, `v_produccion_mensual`. Función de cálculo de promedio ponderado. |
| **A-06 Pesaje Terneros** | Formulario de registro de pesaje. Gráfica de curva de crecimiento individual por ternero. | Validación: peso > 0, fecha válida, ternero existente. Cálculo de ganancia diaria de peso. | Tabla `PesajeTernero`. Índice en `(animal_id, fecha_pesaje)`. |
| **A-07 Proveedores** | Formulario CRUD de proveedor. Lista con búsqueda y filtrado por categoría. | Validaciones: RUC/cédula única, teléfono con formato válido, categoría obligatoria. | Tabla `Proveedor`. Índice en `ruc` y `categoria`. |
| **A-08 Usuarios** | Panel de gestión de usuarios con roles. Formulario de creación/edición. Toggle de activación. | Validaciones: email único, contraseña con requisitos mínimos, rol asignado obligatoriamente. Flujo de recuperación de contraseña. | Tabla `Usuario` con hash de contraseña. Tabla `Rol`. Relación `UsuarioRol`. |
| **A-09 Proyecciones** | Tabla mensual proyectada con resaltado de celdas fuera de rango. Edición inline de proyecciones. | Lógica de importación de proyecciones desde Excel (⚠️ **DECISIÓN PENDIENTE**: ¿manual o automática?). | Tabla `ProyeccionHato` con campos por mes. |
| **A-10 Reportes** | Botones de exportación por módulo. Previsualización antes de descargar. | Lógica de generación de PDF (librería: jsPDF o backend render). Generación de Excel (SheetJS). | Consultas de reporte optimizadas con parámetros de fecha. Vistas consolidadas para reportes. |

---

## 2.2 Dependencias Cruzadas

### El Equipo Interfaz necesita de:

| Necesita de... | Qué necesita | Para qué módulo |
|----------------|--------------|-----------------|
| **Equipo BD** | Especificación de campos y tipos de dato exactos de cada tabla | A-02, A-03, A-04, A-06 |
| **Equipo BD** | Definición de valores válidos para campos enumerados (estados, lotes, potreros) | A-02, A-03 |
| **Equipo Formularios** | Contrato de respuesta de formularios (mensajes de error, estructura de respuesta API) | Todos los formularios |
| **Equipo Formularios** | Definición de reglas de negocio para activar estados visuales (semáforo de gestación) | A-03, A-05 |

### El Equipo Formularios necesita de:

| Necesita de... | Qué necesita | Para qué módulo |
|----------------|--------------|-----------------|
| **Equipo BD** | Esquema de tablas completo y restricciones (UNIQUE, NOT NULL, FK) | Todos los módulos con BD |
| **Equipo BD** | Confirmación de qué validaciones se delegan a la BD (constraints) vs. lógica de app | A-02, A-04, A-08 |
| **Equipo Interfaz** | Mockups de formularios para entender flujos completos de UX | A-03, A-04, A-06 |

### El Equipo Base de Datos necesita de:

| Necesita de... | Qué necesita | Para qué módulo |
|----------------|--------------|-----------------|
| **Equipo Formularios** | Listado definitivo de campos que se capturan por formulario | A-02, A-03, A-04, A-06 |
| **Equipo Interfaz** | Mockups de vistas de listado y filtros para identificar índices necesarios | A-01, A-05 |
| **Equipo Formularios** | Definición de las reglas de negocio que requieren triggers o procedimientos almacenados | A-03 (alerta 60 días) |
