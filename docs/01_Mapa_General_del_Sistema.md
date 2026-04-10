# 📄 Documento 1 — Mapa General del Sistema

**Proyecto:** HOFLOC S.A. — Plataforma Web de Gestión Agropecuaria  
**Versión:** v1.1 | **Fecha:** 2026-04-10 | *Actualizado con base en las transcripciones de visita de campo (Celeste, Jaseth, Chombo)*

---

## 1.1 Visión General de la Plataforma

La plataforma HOFLOC es un sistema web híbrido que combina un **portal público** orientado a clientes externos y un **panel de administración interno** para empleados y propietarios. Ambos conviven bajo el mismo dominio, con autenticación diferenciada por rol.

---

## 1.2 Portal Público

| # | Módulo | Descripción Funcional | Usuario |
|---|--------|-----------------------|---------|
| P-01 | **Página de Inicio** | Presentación institucional de la finca HOFLOC: misión, historia, valores, galería fotográfica y datos de contacto rápido. | Cliente externo |
| P-02 | **Catálogo de Animales en Venta** | Fichas individuales por animal disponible para venta (estilo tarjeta Amazon): foto, nombre/número, padres, producción histórica, peso. Canal de contacto doble: WhatsApp o correo. Propietario confirma disponibilidad. | Cliente externo |
| P-03 | **Solicitud de Visita** | Formulario para programar visitas guiadas a la finca. Captura nombre, contacto, fecha preferida, número de personas y propósito. | Cliente externo |
| P-04 | **Formulario de Contacto** | Canal de comunicación general. Soporta dos canales: WhatsApp y correo electrónico. La administradora prefiere correo; el propietario prefiere WhatsApp. Ambos deben estar disponibles. | Cliente externo |
| P-05 | **Posicionamiento / Sobre Nosotros** | Sección de diferenciación de marca: método de alimentación con levadura inactiva, prácticas ecológicas, reutilización de subproductos cerveceros. Conecta con Instagram. No es un blog editorial activo. | Cliente externo |
| P-06 | **¿Cómo nos conociste?** | Formulario breve en el catálogo o contacto para registrar la fuente de referencia del cliente: radio, referido, Instagram, otro. Permite evaluar qué canal de publicidad es efectivo. | Cliente externo |

---

## 1.3 Panel de Administración Interno

| # | Módulo | Descripción Funcional | Usuario | Prioridad |
|---|--------|-----------------------|---------|-----------|
| A-01 | **Dashboard General** | Vista resumen con indicadores clave: total de vacas activas, litros producidos hoy, animales en alerta (gestación, vacunación, medicamento), próximos partos esperados. | Empleado / Admin | Alta |
| A-02 🔴 | **Gestión de Hembras** | Registro completo del hato hembra. Ficha individual por animal: estado reproductivo (LACT, NVIE, SECA, NLEV), potrero, lote, reproductor, arete, padres, fecha de nacimiento/ingreso. Datos reales: ~74–114 vacas proyectadas Abr 2026–Mar 2027. Incluye curva de producción por animal (ej. vaca 119: ~5,000 litros acumulados). | Empleado / Admin | **CRÍTICA** |
| A-03 🔴 | **Control de Gestación y Reproducción** | Seguimiento de hembras preñadas. Registro de: fecha de preñez, tipo de servicio (IA o monta natural), reproductor, días de gestación. Ciclo: 45 días post-parto → inseminación; celo cada 21 días; palpación a los 32 días; secado ≈ día 200 de lactancia. **Alertas automáticas a ≥60 días gestación. Alerta de celo si no queda preñada a los 21 días.** | Empleado / Admin | **CRÍTICA** |
| A-04 🔴 | **Registro de Ordeño Diario** | Captura de producción de leche por vaca, dos veces al día (AM y PM). Basado en la planilla física "Pesa de Leche" de HOFLOC S.A. (~60–70 vacas por sesión). Cálculo automático del total diario y acumulado semanal por animal. La leche de vacas con medicamento activo **no se registra en el garrafón comercial** — se destina al destete. | Empleado / Admin | **CRÍTICA** |
| A-05 🔴 | **Estadísticas de Producción** | Promedios semanales y mensuales por vaca, por lote (ALTA, MANGO, UVERO, CRÍAS, CHINO) y totales del hato. **Curvas de producción individuales por vaca** (dato clave: permite decidir cuándo descartar). % de vacas en producción. Producción total actual: ~800–900 litros/día. | Admin | **CRÍTICA** |
| A-06 | **Registro de Nacimientos y Pesaje de Terneros** | Al nacer: arete, sexo, peso, foto, número asignado internamente, número de madre. A los 60 días se reporta a MIDA para número oficial. Registro de pesajes periódicos. Curva de crecimiento. Alertas de bajo crecimiento. Terneros reciben ~2 litros de leche AM; la cantidad se lleva manualmente. | Empleado / Admin | Alta |
| A-07 🔴 | **Trazabilidad de Medicamentos** | Registro de medicamentos aplicados por animal: nombre del producto, ingrediente activo, fecha, dosis, período de retiro (días). **Alerta automática mientras el período de retiro está activo** — impide que la leche de ese animal vaya al circuito comercial. Basado en legislación panameña vigente (depende de etiqueta del producto). | Empleado / Admin | **CRÍTICA** |
| A-08 🔴 | **Alertas Sanitarias y Vacunación** | Calendario de vacunación por animal y por hato. **Alerta automática de vacuna próxima o vencida.** Historia real: 7 vacas muertas por no vacunar contra rabia a tiempo. Integra con el módulo de medicamentos para evitar doble registro. | Empleado / Admin | **CRÍTICA** |
| A-09 | **Gestión de Proveedores** | Catálogo de proveedores de insumos (veterinarios, alimentación, equipos). Registro de contacto, categoría y historial de compras básico. | Admin | Media |
| A-10 | **Gestión de Usuarios** | Alta, baja y modificación de cuentas de empleados y administradores. Asignación de roles y permisos. | Admin | Alta |
| A-11 | **Proyecciones del Hato** | Vista de proyección mensual: total vacas, vacas en lactancia, vacas a secar, % en producción, partos esperados. Basado en la tabla de proyecciones existente (Abr 2026 – Mar 2027). | Admin | Media |
| A-12 | **Módulo de Reportes** | Exportación de informes en PDF/Excel: producción mensual, estado reproductivo, inventario de animales, crecimiento de terneros, alertas sanitarias y trazabilidad de medicamentos. | Admin | Media |

---

## 1.4 Leyenda de Complejidad

| Símbolo | Significado |
|---------|-------------|
| 🔴 | Módulo crítico: alta complejidad lógica, datos sensibles o con alertas automáticas |
| ⚠️ | Decisión pendiente de definición humana |
