# 📄 Documento 2 — Asignación de Responsabilidades por Equipo

**Proyecto:** HOFLOC S.A. — Plataforma Web de Gestión Agropecuaria  
**Versión:** v1.1 | **Fecha:** 2026-04-10 | *Actualizado con base en las transcripciones de visita de campo (Celeste, Jaseth, Chombo)*

---

## 2.1 Tabla de Responsabilidades

| Módulo | Equipo Interfaz (UI/UX) | Equipo Formularios | Equipo Base de Datos |
|--------|-------------------------|--------------------|----------------------|
| **P-01 Inicio** | Diseño de landing con secciones Hero, Sobre Nosotros, Galería, Contacto rápido. Responsive mobile-first. | — | — |
| **P-02 Catálogo de Animales** | Fichas individuales de animal (estilo Amazon): foto, nombre/arete, padres, producción, peso, botón de contacto. Scroll horizontal entre fichas. | Lógica de disponibilidad. Datos provenientes de la tabla `tbl_Animal` con `foto_url`, `madre_id`, `padre_id`. | Vista/consulta de tabla `tbl_Animal` filtrando animales marcados como en venta. |
| **P-03 Solicitud Visita** | Diseño del formulario de solicitud. Confirmación visual tras envío. | Validaciones: fecha no puede ser pasada, número de personas > 0, campos obligatorios. Envío de correo de confirmación. | Tabla `tbl_SolicitudVisita`. Índice por fecha para consultas de agenda. |
| **P-04 Contacto** | Formulario con selección de canal (WhatsApp / Correo) y feedback visual de envío. | Validación de email, anti-spam básico (honeypot o reCAPTCHA). Registro del canal elegido. | Tabla `tbl_MensajeContacto` con campo `canal` y timestamp. |
| **P-05 Posicionamiento** | Sección visual de diferenciación: alimentación (levadura), prácticas ecológicas, vínculo con Instagram. | — | — |
| **P-06 Fuente del cliente** | Campo simple embebido en catálogo o formulario de contacto. | Guardado en `tbl_FuenteCliente`. | Tabla `tbl_FuenteCliente`. |
| **A-01 Dashboard** | Layout de tarjetas KPI (vacas activas, litros hoy, alertas medicamento/vacuna/gestación, próximos partos). Gráficas resumen. | Lógica de agregación de datos para los KPI. Actualización en tiempo semi-real (polling o WebSocket). | Vistas/consultas optimizadas: `vw_resumen_diario`, `vw_alertas_activas`. |
| **A-02 🔴 Gestión Hembras** | Tabla paginada con columnas: Arete Interno, Nombre, Estado, Días Gest., Días Lact., Reproductor, Potrero, Lote, Foto. Fila destacada para alertas. | Validaciones: estado reproductivo válido, reproductor existente en catálogo, potrero válido. Lógica de cambio de estado. | Tabla `tbl_Animal`. Relaciones con `tbl_Potrero`, `tbl_Lote`, `tbl_Reproductor`. Índice en `estado` y `dias_lactancia`. |
| **A-03 🔴 Gestión Gestación** | Vista de tabla filtrable por días de gestación. Semáforo (verde <60, amarillo 60–80, rojo >80). Alerta de celo a día 21 post-servicio. | Lógica de cálculo de días de gestación. Alerta de celo (21 días post-servicio). Disparo de alertas al superar 60 días. | Tabla `tbl_Gestacion` vinculada a `tbl_Animal`. Función/trigger para calcular `dias_gestacion`. Índice en `fecha_prenez`. |
| **A-04 🔴 Ordeño Diario** | Interfaz de captura tipo planilla (AM/PM por vaca). Animales con retiro activo muestran 🚫 y campo deshabilitado. Totales calculados en tiempo real. | Validaciones: litros AM y PM ≥ 0, vaca debe estar en estado LACT y sin retiro activo para circuito comercial. Lógica de flag destete/comercial. | Tabla `tbl_OrdenoRegistro` con campo `destino` (COMERCIAL/DESTETE). Índice en `(animal_id, fecha)`. |
| **A-05 🔴 Estadísticas** | Gráficas de línea (curva de lactancia por vaca), barras (comparativo por lote), tablas de promedios. Filtros por rango de fechas, vaca y lote. | Lógica de cálculo de promedios semanales/mensuales. Formateo de datos para librerías de gráficas. | Vistas `vw_produccion_semanal`, `vw_produccion_mensual`. Función de cálculo de promedio ponderado. |
| **A-06 Nacimientos y Pesaje** | Formulario de registro de nacimiento (arete, madre, sexo, peso, foto). Alerta de 60 días para MIDA. Gráfica de curva de crecimiento. | Validación: peso > 0, fecha válida, animal existente. Cálculo de ganancia diaria. Alerta de los 60 días. | Tabla `tbl_PesajeTernero`. Campo `arete_mida` en `tbl_Animal`. Índice en `(animal_id, fecha_pesaje)`. |
| **A-07 🔴 Medicamentos** | Formulario de medicamento aplicado: nombre, ingrediente activo, dosis, fecha, días de retiro. Listado de animales en retiro activo. | Cálculo: `fecha_fin_retiro = fecha_aplicacion + dias_retiro`. Notificación al ordeño del flag de bloqueo. | Tabla `tbl_MedicamentoAplicado` con `fecha_fin_retiro` calculado. Job diario que desactiva alertas vencidas. |
| **A-08 🔴 Vacunación** | Calendario visual de vacunas. Panel de alertas de vacunas próximas y vencidas integrado en dashboard. Formulario de aplicación de vacuna. | Lógica de alerta: 🟡 a 7 días, 🔴 al vencer. Reprogramación automática según frecuencia. | Tablas `tbl_CalendarioVacunacion` y `tbl_VacunaAplicada`. Job diario de alertas. |
| **A-09 Proveedores** | Formulario CRUD de proveedor. Lista con búsqueda y filtrado por categoría. | Validaciones: RUC/cédula única, teléfono con formato válido, categoría obligatoria. | Tabla `tbl_Proveedor`. Índice en `ruc` y `categoria`. |
| **A-10 Usuarios** | Panel de gestión de usuarios con roles. Formulario de creación/edición. Toggle de activación. | Validaciones: email único, contraseña con requisitos mínimos, rol asignado obligatoriamente. Flujo de recuperación de contraseña. | Tabla `tbl_Usuario` con hash de contraseña. Tabla `tbl_Rol`. Relación `UsuarioRol`. |
| **A-11 Proyecciones** | Tabla mensual proyectada con resaltado de celdas fuera de rango. Edición inline de proyecciones. | Lógica de importación de proyecciones desde Excel (⚠️ **DECISIÓN PENDIENTE**: ¿manual o automática?). | Tabla `tbl_ProyeccionHato` con campos por mes. |
| **A-12 Reportes** | Botones de exportación por módulo. Previzualización antes de descargar. | Lógica de generación de PDF (librería: jsPDF o backend render). Generación de Excel (SheetJS). Incluye reportes de trazabilidad de medicamentos y vacunación. | Consultas de reporte optimizadas con parámetros de fecha. Vistas consolidadas para reportes. |er). Generación de Excel (SheetJS). | Consultas de reporte optimizadas con parámetros de fecha. Vistas consolidadas para reportes. |

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
