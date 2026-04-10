# 📄 Documento 3 — Reglas de Negocio

**Proyecto:** HOFLOC S.A. — Plataforma Web de Gestión Agropecuaria  
**Versión:** v1.1 | **Fecha:** 2026-04-10 | *Actualizado con base en las transcripciones de visita de campo (Celeste, Jaseth, Chombo)*

---

## RN-001 — Alerta de Gestación Avanzada y Ciclo Reproductivo

**Módulo:** A-03 Control de Gestación y Reproducción

**Ciclo reproductivo real documentado en la visita:**
- **Día 0** (parto): La vaca entra a lactancia.
- **Día 45 post-parto**: Primer intento de inseminación artificial o monta natural.
- **Día 21 post-servicio**: Si la vaca entra en celo de nuevo → no quedó preñada → alerta para repetir servicio.
- **Día 32 post-servicio**: El veterinario realiza la palpación para confirmar preñez.
- **Días 60–80 de gestación**: Zona de atención (🟡 amarillo). El personal debe revisar.
- **Más de 80 días de gestación**: Zona crítica (🔴 rojo). Preparación para preparto.
- **≈ Día 200 de lactancia**: Momento en que la vaca debe secarse (dejar de ordeñar) para recuperarse antes del próximo parto. **Esta regla resuelve la DECISIÓN PENDIENTE de RN-010.**

**Alerta automática de gestación:** El sistema marca la hembra con indicador visual 🟡 al superar 60 días; cambia a 🔴 al superar 80 días. Se genera notificación en el dashboard.

**Fuente real:** Hembra 8802 tiene 101 días de gestación → alerta 🔴 activa. Hembra 8105: 66 días → alerta 🟡.

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
**Acción:** El sistema incrementa automáticamente el contador de `dias_lactancia` de la vaca. Si `dias_lactancia` supera **200 días**, se genera una alerta para revisar si debe pasar a SECA (secado). Este umbral está confirmado por la práctica real de la finca: una vaca con más de 200 días de lactancia ya debería estar entrando al período de descanso (pre-secado). Sacar vacas en declive permite que las restantes produzcan más.  
**Equipo responsable:** BD (trigger o función) + Formularios (validación de alerta)

---

## RN-011 — Acceso al Panel Interno Solo con Autenticación

**Módulo:** A-10 Usuarios  
**Condición:** Cualquier intento de acceso a rutas del panel `/admin/*`.  
**Acción:** El sistema redirige al login si no hay sesión activa. Los roles determinan qué módulos son visibles: Empleado ve A-02, A-03, A-04, A-06, A-07, A-08. Admin ve todos los módulos.  
**Equipo responsable:** Formularios (lógica de autenticación/JWT) + BD (tabla Usuario + Rol) + Interfaz (guard de rutas)

---

## RN-012 — Vaca en Gestación No Puede Registrar Ordeño Simultáneo

**Módulo:** A-03, A-04  
**Condición:** Una hembra tiene estado `NVIE` o `SECA` y simultáneamente aparece en el módulo de ordeño.  
**Acción:** El sistema bloquea el registro de ordeño para esa hembra. Solo se permite ordeño si estado = `LACT`.  
**Nota real:** En los datos del sistema, hay hembras NVIE con días de gestación registrados (ej. #07, #153, #17) que no deben aparecer en la planilla de ordeño.  
**Equipo responsable:** Formularios + BD

---

## RN-013 — Trazabilidad de Medicamentos y Bloqueo de Leche Comercial

**Módulo:** A-07 Trazabilidad de Medicamentos  
**Condición:** Se registra un medicamento con período de retiro > 0 días para una vaca.  
**Acción:**  
- El sistema marca la vaca con alerta 🚫 (en retiro) visible en el módulo de ordeño y en el dashboard.
- La leche de ese animal **no puede registrarse en el garrafón comercial** durante el período de retiro.
- El sistema sugiere destinar la producción al circuit de destete (terneros).
- Al finalizar el período de retiro (fecha inicio + días de retiro del producto), la alerta se desactiva automáticamente.

**Ejemplo real:** Certimec (ivermectina) tiene período de retiro corto. Otros productos pueden tener residuos más prolongados según etiqueta. La ley panameña determina los tiempos mínimos permitidos.  
**Equipo responsable:** BD (tabla `MedicamentoAplicado` + cálculo de fecha fin retiro) + Formularios (bloqueo en ordeño) + Interfaz (indicador visual en planilla AM/PM)

---

## RN-014 — Alerta de Vacunación Vencida o Próxima

**Módulo:** A-08 Alertas Sanitarias y Vacunación  
**Condición:** La fecha programada de una vacuna se aproxima o ya venció sin que se registre su aplicación.  
**Acción:**  
- Con **7 días de anticipación**: el sistema muestra alerta 🟡 en el dashboard: *"Vacuna [nombre] próxima para [N animales]."*
- **Día de vencimiento sin registro**: alerta cambia a 🔴: *"Vacuna [nombre] vencida. Aplicación pendiente."*
- El admin puede registrar la aplicación (fecha, dosis, lote del producto, vacuna aplicada) para limpiar la alerta.

**Motivación real:** Por no vacunar contra rabia a tiempo, se perdieron 7 animales en un solo día. Esta regla es de alta prioridad.  
**Equipo responsable:** BD (tabla `CalendarioVacunacion` + tabla `VacunaAplicada`) + Formularios (lógica de alerta) + Interfaz (panel de alertas en dashboard)

---

## RN-015 — Registro de Nacimiento y Reporte al MIDA

**Módulo:** A-06 Registro de Nacimientos y Pesaje  
**Condición:** Nace un ternero en la finca.  
**Acción:**  
1. Se registra inmediatamente en el sistema: arete propio (número interno), número de la madre, sexo, peso al nacer, foto, fecha de nacimiento.
2. A los **60 días** (post-destete), el sistema genera alerta: *"Ternero [N°] listo para reportar al MIDA."*
3. El admin registra el número oficial MIDA asignado. Ese número pasa a ser el identificador oficial del animal en el sistema.
4. El animal queda con dos identificadores: número interno (provisional) y número MIDA (official, inmutable).

**Nota:** El sistema MIDA es externo y controlado por el veterinario. El sistema HOFLOC no se integra directamente con MIDA, pero sí registra el número oficial que el veterinario asigna.  
**Equipo responsable:** BD (campos `arete_interno` y `arete_mida` en tabla `Animal`) + Formularios (alerta de los 60 días) + Interfaz (flujo de registro de ternero)
