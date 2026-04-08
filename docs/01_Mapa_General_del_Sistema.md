# 📄 Documento 1 — Mapa General del Sistema

**Proyecto:** HOFLOC S.A. — Plataforma Web de Gestión Agropecuaria  
**Versión:** v1.0 | **Fecha:** 2026-04-08

---

## 1.1 Visión General de la Plataforma

La plataforma HOFLOC es un sistema web híbrido que combina un **portal público** orientado a clientes externos y un **panel de administración interno** para empleados y propietarios. Ambos conviven bajo el mismo dominio, con autenticación diferenciada por rol.

---

## 1.2 Portal Público

| # | Módulo | Descripción Funcional | Usuario |
|---|--------|-----------------------|---------|
| P-01 | **Página de Inicio** | Presentación institucional de la finca HOFLOC: misión, historia, valores, galería fotográfica y datos de contacto rápido. | Cliente externo |
| P-02 | **Catálogo de Productos** | Vitrina de productos disponibles: leche fresca, terneros en venta, paquetes de visita educativa a la finca. Incluye precios indicativos y disponibilidad. | Cliente externo |
| P-03 | **Solicitud de Visita** | Formulario para programar visitas guiadas a la finca. Captura nombre, contacto, fecha preferida, número de personas y propósito. | Cliente externo |
| P-04 | **Formulario de Contacto** | Canal de comunicación general para consultas de clientes sobre productos, precios y disponibilidad. | Cliente externo |
| P-05 | **Noticias / Blog** | ⚠️ **DECISIÓN PENDIENTE** — Sección opcional de publicaciones sobre actividades de la finca, buenas prácticas ganaderas o novedades. Evaluar si el equipo tiene capacidad editorial. | Cliente externo |

---

## 1.3 Panel de Administración Interno

| # | Módulo | Descripción Funcional | Usuario | Prioridad |
|---|--------|-----------------------|---------|-----------|
| A-01 | **Dashboard General** | Vista resumen con indicadores clave: total de vacas activas, litros producidos hoy, animales en alerta, próximos partos esperados. | Empleado / Admin | Alta |
| A-02 🔴 | **Gestión de Hembras** | Registro completo del hato hembra. Incluye ficha individual por animal con estado reproductivo (LACT, NVIE, SECA, NLEV), potrero asignado, lote y reproductor. Datos reales del sistema actual: ~74–114 vacas proyectadas en el período Abr 2026–Mar 2027. | Empleado / Admin | **CRÍTICA** |
| A-03 🔴 | **Control de Gestación** | Seguimiento de hembras preñadas. Registro de fecha de preñez, reproductor (toro), días de gestación acumulados y estado confirmado. El sistema actual maneja campos: Gest, F. Preñ, Estado reprod., Preñada de. **Alerta automática a ≥ 60 días.** | Empleado / Admin | **CRÍTICA** |
| A-04 🔴 | **Registro de Ordeño Diario** | Captura de producción de leche por vaca, dos veces al día (AM y PM). Basado en la planilla física "Pesa de Leche" de HOFLOC S.A. (~60–70 vacas por sesión). Cálculo automático del total diario por animal. | Empleado / Admin | **CRÍTICA** |
| A-05 🔴 | **Estadísticas de Producción** | Promedios semanales y mensuales por vaca, por lote (ALTA, MANGO, UVERO, CRÍAS, CHINO) y totales del hato. Curvas de producción. % de vacas en producción (campo ya llevado en proyección actual). | Admin | **CRÍTICA** |
| A-06 | **Pesaje y Crecimiento de Terneros** | Registro de pesajes periódicos por ternero (fecha + peso en kg). Generación de curva de crecimiento individual. Alertas de bajo crecimiento. | Empleado / Admin | Alta |
| A-07 | **Gestión de Proveedores** | Catálogo de proveedores de insumos (veterinarios, alimentación, equipos). Registro de contacto, categoría y historial de compras básico. | Admin | Media |
| A-08 | **Gestión de Usuarios** | Alta, baja y modificación de cuentas de empleados y administradores. Asignación de roles y permisos. | Admin | Alta |
| A-09 | **Proyecciones del Hato** | Vista de proyección mensual: total vacas, vacas en lactancia, vacas a secar, % en producción, partos esperados. Basado en la tabla de proyecciones existente (Abr 2026 – Mar 2027). | Admin | Media |
| A-10 | **Módulo de Reportes** | Exportación de informes en PDF/Excel: producción mensual, estado reproductivo, inventario de animales, crecimiento de terneros. | Admin | Media |

---

## 1.4 Leyenda de Complejidad

| Símbolo | Significado |
|---------|-------------|
| 🔴 | Módulo crítico: alta complejidad lógica, datos sensibles o con alertas automáticas |
| ⚠️ | Decisión pendiente de definición humana |
