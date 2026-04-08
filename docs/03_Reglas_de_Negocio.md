# 📄 Documento 3 — Reglas de Negocio

**Proyecto:** HOFLOC S.A. — Plataforma Web de Gestión Agropecuaria  
**Versión:** v1.0 | **Fecha:** 2026-04-08

---

## RN-001 — Alerta de Gestación Avanzada

**Módulo:** A-03 Gestión de Gestación  
**Condición:** Los días de gestación de una hembra preñada alcanzan o superan los 60 días (`dias_gestacion >= 60`).  
**Acción:** El sistema marca la hembra con indicador visual 🟡 en el panel. Al superar 80 días, el indicador cambia a 🔴. Se genera una notificación en el dashboard (A-01). El personal debe revisar y planificar el preparto.  
**Fuente real:** En los datos actuales, la hembra 8802 tiene 101 días de gestación (preñada desde 24/12/25) y la 8105 tiene 66 días — ambas requieren atención inmediata.  
**Equipo responsable:** BD (trigger/función de cálculo) + Formularios (lógica de notificación) + Interfaz (semáforo visual)

---

## RN-002 — Solo Vacas en Lactancia Pueden Registrar Ordeño

**Módulo:** A-04 Registro de Ordeño  
**Condición:** Se intenta registrar producción de leche para una vaca cuyo estado sea distinto de `LACT`.  
**Acción:** El sistema rechaza el registro y muestra mensaje: *"La vaca [N°] no está en estado de lactancia. Estado actual: [ESTADO]."*  
**Equipo responsable:** Formularios (validación) + BD (constraint por estado)

---

## RN-003 — Validación de Doble Registro Diario de Ordeño

**Módulo:** A-04 Registro de Ordeño  
**Condición:** El usuario intenta registrar una segunda sesión AM o PM del mismo día para la misma vaca.  
**Acción:** El sistema advierte: *"Ya existe un registro [AM/PM] para la vaca [N°] el día [FECHA]. ¿Desea sobrescribir?"*. Se requiere confirmación explícita para actualizar.  
**Equipo responsable:** Formularios + BD (índice único en `animal_id + fecha + turno`)

---

## RN-004 — Cálculo de Producción Diaria Total por Vaca

**Módulo:** A-04, A-05  
**Condición:** Al finalizar el registro de ordeño del día (AM + PM).  
**Acción:** El sistema calcula automáticamente: `produccion_diaria = litros_AM + litros_PM`. Este valor se almacena en `OrdeñoRegistro` y se usa para estadísticas.  
**Ejemplo real:** Vaca "Ceniza" el 29/03/26: AM=13, PM=11 → Total=24 litros.  
**Equipo responsable:** Formularios (cálculo en captura) + BD (campo calculado o columna derivada)

---

## RN-005 — Cálculo de Promedio Semanal de Producción

**Módulo:** A-05 Estadísticas  
**Condición:** El usuario consulta el reporte semanal de producción de una vaca o del hato.  
**Acción:** El sistema calcula: `promedio_semanal = SUM(produccion_diaria) / días_con_registro_en_semana`. Solo se cuentan días con registro válido (no días sin datos).  
**Equipo responsable:** BD (vista `v_produccion_semanal`) + Formularios (lógica de consulta y formateo)

---

## RN-006 — Cálculo de Promedio Mensual de Producción

**Módulo:** A-05 Estadísticas  
**Condición:** El usuario consulta el reporte mensual o el dashboard general.  
**Acción:** El sistema calcula: `promedio_mensual = SUM(produccion_diaria_mes) / días_con_registro_en_mes`. Se proyecta el % de vacas en producción sobre el total del hato (dato ya llevado en proyecciones: Ej. Abril 2026 = 78.38%).  
**Equipo responsable:** BD (vista `v_produccion_mensual`) + Interfaz (gráfica de tendencia)

---

## RN-007 — Curva de Crecimiento de Terneros

**Módulo:** A-06 Pesaje de Terneros  
**Condición:** Se registra un nuevo pesaje para un ternero (fecha + peso).  
**Acción:** El sistema actualiza la gráfica de curva de crecimiento del ternero. Calcula la ganancia diaria promedio entre pesajes: `ganancia_diaria = (peso_actual - peso_anterior) / días_entre_pesajes`. Si la ganancia es menor a un umbral definido (⚠️ **DECISIÓN PENDIENTE**: ¿cuántos gramos/día es normal para los terneros de HOFLOC?), se genera una alerta.  
**Equipo responsable:** Formularios (cálculo de ganancia) + BD (índice en animal + fecha) + Interfaz (gráfica)

---

## RN-008 — Estado Reproductivo y Transiciones Válidas

**Módulo:** A-02 Gestión de Hembras  
**Condición:** Se intenta cambiar el estado reproductivo de una hembra.  
**Acción:** Solo se permiten transiciones de estado válidas:

```
NVIE → [LACT, SECA, NLEV]
LACT → [SECA, NVIE]
SECA → [LACT, NVIE]
NLEV → [LACT, NVIE]
```

Cualquier transición no listada debe ser bloqueada con mensaje de error explicativo.  

> ⚠️ **DECISIÓN PENDIENTE** — Validar con el veterinario/encargado de HOFLOC si todas las transiciones anteriores son agronómicamente correctas.

**Equipo responsable:** Formularios (validación de flujo) + BD (enum de estados)

---

## RN-009 — Unicidad de Número de Hembra

**Módulo:** A-02 Gestión de Hembras  
**Condición:** Se registra o edita una hembra con un Número de Hembra.  
**Acción:** El sistema verifica que el Número de Hembra sea único en todo el sistema. Si ya existe, muestra error: *"El número [N°] ya está asignado a [Nombre/ID]"*.  
**Equipo responsable:** BD (UNIQUE constraint en `numero_hembra`) + Formularios (validación en tiempo real)

---

## RN-010 — Restricción de Ordeño a Vacas con Días de Lactancia Activos

**Módulo:** A-04  
**Condición:** Se registra un ordeño para una vaca en estado LACT.  
**Acción:** El sistema incrementa automáticamente el contador de `dias_lactancia` de la vaca. Si `dias_lactancia` supera un umbral (⚠️ **DECISIÓN PENDIENTE**: ¿cuántos días es el período de lactancia estándar en HOFLOC?), se genera una alerta para revisar si debe pasar a SECA.  
**Equipo responsable:** BD (trigger o función) + Formularios (validación de alerta)

---

## RN-011 — Acceso al Panel Interno Solo con Autenticación

**Módulo:** A-08 Usuarios  
**Condición:** Cualquier intento de acceso a rutas del panel `/admin/*`.  
**Acción:** El sistema redirige al login si no hay sesión activa. Los roles determinan qué módulos son visibles: Empleado ve A-02, A-03, A-04, A-06. Admin ve todos los módulos.  
**Equipo responsable:** Formularios (lógica de autenticación/JWT) + BD (tabla Usuario + Rol) + Interfaz (guard de rutas)

---

## RN-012 — Vaca en Gestación No Puede Registrar Ordeño Simultáneo

**Módulo:** A-03, A-04  
**Condición:** Una hembra tiene estado `NVIE` o `SECA` y simultáneamente aparece en el módulo de ordeño.  
**Acción:** El sistema bloquea el registro de ordeño para esa hembra. Solo se permite ordeño si estado = `LACT`.  
**Nota real:** En los datos del sistema, hay hembras NVIE con días de gestación registrados (ej. #07, #153, #17) que no deben aparecer en la planilla de ordeño.  
**Equipo responsable:** Formularios + BD
