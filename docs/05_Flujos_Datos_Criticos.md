# 📄 Documento 5 — Flujos de Datos Críticos

**Proyecto:** HOFLOC S.A. — Plataforma Web de Gestión Agropecuaria  
**Versión:** v1.1 | **Fecha:** 2026-04-10 | *Actualizado con base en las transcripciones de visita de campo (Celeste, Jaseth, Chombo)*

---

## Flujo 1 — Registro de Hembra en Gestación → Alerta → Seguimiento

### Actores: Empleado, Sistema, Administrador

```
PASO 1 — Captura del dato de preñez
  ├── Actor: Empleado
  ├── Equipo: Interfaz
  └── El empleado abre el formulario de gestación en el módulo A-03.
      Ingresa: N° de hembra, fecha de preñez (F. Preñ), reproductor seleccionado del catálogo.

PASO 2 — Validación del formulario
  ├── Actor: Sistema
  ├── Equipo: Formularios
  └── Valida: hembra existe en BD, estado actual no sea LACT activa sin secar primero,
      reproductor exista en catálogo, fecha no sea futura.
      Si hay error → muestra mensaje inline y bloquea envío.

PASO 3 — Persistencia en BD
  ├── Actor: Sistema
  ├── Equipo: Base de Datos
  └── Inserta registro en tabla `Gestacion`.
      El campo `dias_gestacion` comienza en 0 (o se calcula con NOW() - fecha_prenez).
      El estado del Animal en tabla `Animal` cambia a NVIE si estaba en otro estado válido.
      Trigger actualiza `confirmada_fecha` si aplica.

PASO 4 — Confirmación visual
  ├── Actor: Sistema → Empleado
  ├── Equipo: Interfaz
  └── Muestra confirmación en pantalla. La hembra aparece en la lista de gestación
      con indicador verde (< 60 días).

PASO 5 — Monitoreo diario automático
  ├── Actor: Sistema (job programado)
  ├── Equipo: Base de Datos + Formularios
  └── Cada día (cron job / scheduled function), el sistema recalcula dias_gestacion
      para todas las gestaciones ACTIVAS.
      Fórmula: dias_gestacion = CURRENT_DATE - fecha_prenez

PASO 6 — Disparo de Alerta (≥ 60 días)
  ├── Actor: Sistema
  ├── Equipo: Formularios (lógica) + Base de Datos (query)
  └── Si dias_gestacion >= 60:
        - Se crea registro en tabla `Alerta` (⚠️ DECISIÓN PENDIENTE: ¿tabla separada o flag en Gestación?)
        - Dashboard (A-01) muestra la hembra en panel de "Animales en Atención"
        - Indicador visual cambia a 🟡 (60-80 días) o 🔴 (> 80 días)

PASO 7 — Revisión por Administrador
  ├── Actor: Administrador
  ├── Equipo: Interfaz
  └── Admin ve en el dashboard la alerta. Puede acceder a la ficha completa del animal,
      registrar observaciones, actualizar estado (ej. marcar parto → actualiza estado a LACT).

PASO 8 — Cierre del flujo (Parto)
  ├── Actor: Empleado/Admin
  ├── Equipo: Formularios + BD
  └── Al registrar el parto: `Gestacion.estado = 'PARTO'`, `Gestacion.fecha_parto_real = HOY`.
      Animal pasa a LACT. Se inicia contador de días de lactancia.
      Si hubo cría, se puede crear registro en Animal para el ternero.
```

**Dato real de ejemplo:** Hembra 8802 (preñada desde 24/12/25) ya tiene 101 días de gestación al 08/04/26 → alerta 🔴 activa. Hembra 8105 (28/01/26) tiene 66 días → alerta 🟡.

---

## Flujo 2 — Registro de Ordeño Diario → Cálculo de Estadísticas → Visualización

### Actores: Empleado, Sistema, Administrador

```
PASO 1 — Inicio de sesión de ordeño
  ├── Actor: Empleado
  ├── Equipo: Interfaz
  └── El empleado abre el módulo A-04. Selecciona la fecha del día (default: hoy).
      El sistema carga la lista de vacas en estado LACT para ese día.
      La interfaz se presenta como una planilla (similar al formato físico "Pesa de Leche").

PASO 2 — Captura de producción AM
  ├── Actor: Empleado
  ├── Equipo: Interfaz + Formularios
  └── Para cada vaca en la lista, el empleado ingresa los litros del turno AM.
      El sistema valida en tiempo real: valor numérico ≥ 0, campo no vacío.
      Total AM acumulado se actualiza dinámicamente en la interfaz.

PASO 3 — Captura de producción PM
  ├── Actor: Empleado
  ├── Equipo: Interfaz + Formularios
  └── Mismo proceso para turno PM.
      El sistema calcula y muestra el total diario por vaca (AM + PM) en tiempo real.
      Ejemplo: Vaca 24 → AM=12, PM=14 → Total=26 litros (dato real 29/03/26).

PASO 4 — Validación y envío
  ├── Actor: Sistema
  ├── Equipo: Formularios
  └── Antes de guardar: verifica que no existan registros duplicados (misma vaca, fecha, turno).
      Si hay duplicado: alerta y confirmación de sobrescritura.
      Validación de que todos los campos tienen valor (permite 0 para animales enfermos).

PASO 5 — Persistencia en BD
  ├── Actor: Sistema
  ├── Equipo: Base de Datos
  └── INSERT en `OrdeñoRegistro` para cada vaca × turno.
      El campo `operario_id` registra quién hizo el ingreso.
      La constraint UNIQUE (animal_id, fecha, turno) previene duplicados a nivel BD.

PASO 6 — Actualización de estadísticas
  ├── Actor: Sistema
  ├── Equipo: Base de Datos
  └── Las vistas `v_produccion_semanal` y `v_produccion_mensual` se actualizan automáticamente
      al consultar (vistas calculadas en tiempo de consulta).
      ⚠️ DECISIÓN PENDIENTE: ¿Vistas calculadas on-demand vs. tabla de resúmenes pre-calculados
      (materialized views)? Impacta performance con volumen alto de datos.

PASO 7 — Visualización en dashboard y estadísticas
  ├── Actor: Administrador/Empleado
  ├── Equipo: Interfaz
  └── Dashboard (A-01) muestra: total litros del día, top 5 vacas productoras, alerta de caídas.
      Módulo A-05 muestra gráficas de línea por vaca, barras comparativas por lote,
      tabla de promedios semanal/mensual.
      El % de vacas en producción se recalcula automáticamente.
```

**Escala real:** El 29/03/26 se registraron aprox. 65 vacas en la planilla física con producción total estimada de ~1,400 litros diarios.

---

## Flujo 3 — Pesaje de Ternero → Actualización de Curva de Crecimiento

### Actores: Empleado, Sistema, Administrador

```
PASO 1 — Selección del ternero
  ├── Actor: Empleado
  ├── Equipo: Interfaz
  └── El empleado abre el módulo A-06. Busca al ternero por número o nombre.
      La interfaz muestra el historial de pesajes anteriores y la última curva generada.

PASO 2 — Registro del nuevo pesaje
  ├── Actor: Empleado
  ├── Equipo: Interfaz + Formularios
  └── Ingresa: fecha del pesaje (default: hoy), peso en kg.
      Validaciones: peso > 0, peso no mayor en más de X% que el pesaje anterior
      (⚠️ DECISIÓN PENDIENTE: ¿qué % de incremento/decremento es fisiológicamente posible?),
      fecha no puede ser anterior a un pesaje ya registrado para ese animal.

PASO 3 — Cálculo de ganancia de peso
  ├── Actor: Sistema
  ├── Equipo: Formularios
  └── Recupera el pesaje anterior del ternero desde BD.
      Calcula: ganancia_diaria = (peso_actual - peso_anterior) / días_entre_pesajes.
      Muestra al empleado antes de guardar: "Ganancia promedio: X kg/día".
      Si ganancia_diaria < umbral definido → genera alerta de bajo crecimiento.

PASO 4 — Persistencia
  ├── Actor: Sistema
  ├── Equipo: Base de Datos
  └── INSERT en `PesajeTernero` con fecha, peso y ganancia calculada.
      Índice (animal_id, fecha_pesaje) garantiza orden cronológico eficiente.

PASO 5 — Actualización de la curva de crecimiento
  ├── Actor: Sistema
  ├── Equipo: Interfaz + Formularios
  └── La gráfica de curva de crecimiento se regenera con el nuevo punto de dato.
      El sistema compara la curva del ternero con la curva de referencia esperada para su raza
      (⚠️ DECISIÓN PENDIENTE: ¿se carga una tabla de referencia de crecimiento estándar?).
      Si el animal está por debajo de la curva esperada durante 2+ pesajes consecutivos →
      alerta de revisión veterinaria.

PASO 6 — Consulta por el administrador
  ├── Actor: Administrador
  ├── Equipo: Interfaz
  └── El admin puede ver el cuadro de crecimiento consolidado de todos los terneros,
      ordenado por ganancia diaria (mejor y peor desempeño).
      Filtro por rango de fechas y por lote (ej. lote "CRÍAS").
```

---

## Flujo 4 — Medicamento Aplicado → Período de Retiro → Bloqueo de Leche Comercial

### Actores: Empleado, Sistema, Administrador

```
PASO 1 — Registro del medicamento
  ├── Actor: Empleado
  ├── Equipo: Interfaz
  └── El empleado (o encargado de lechero) abre el módulo A-07 y selecciona la vaca.
      Ingresa: nombre del medicamento, ingrediente activo, dosis, fecha de aplicación,
      período de retiro en días (según etiqueta del producto).

PASO 2 — Cálculo de fecha fin de retiro
  ├── Actor: Sistema
  ├── Equipo: Formularios + Base de Datos
  └── `fecha_fin_retiro = fecha_aplicacion + periodo_retiro_dias`
      Se guarda en tabla `MedicamentoAplicado`.
      La vaca queda marcada con estado de retiro activo.

PASO 3 — Indicador de retiro en planilla de ordeño
  ├── Actor: Sistema
  ├── Equipo: Interfaz
  └── En el módulo A-04, la vaca con retiro activo aparece con indicador 🚫.
      El campo de litros comerciales está deshabilitado.
      El empleado puede registrar producción destinada a destete (no al garrafón).

PASO 4 — Vencimiento del período de retiro
  ├── Actor: Sistema (job programado)
  ├── Equipo: Base de Datos
  └── Cuando `CURRENT_DATE >= fecha_fin_retiro`, el sistema desactiva automáticamente
      el bloqueo. La vaca vuelve a aparecer como disponible en la planilla comercial.
      El dashboard puede mostrar una confirmación: "Vaca [N°] sale de período de retiro hoy."
```

**Ejemplo real:** Paramastite aplicada por mastitis. Si la etiqueta indica 3 días de retiro, la leche de esa vaca se destina a terneros durante esos 3 días. La finca trata de usar productos sin período de retiro, pero cuando tienen que usarlos, la separación es obligatoria.

---

## Flujo 5 — Alerta de Vacunación → Registro de Aplicación → Cierre de Alerta

### Actores: Administrador, Empleado, Sistema

```
PASO 1 — Configuración del calendario de vacunación
  ├── Actor: Administrador
  ├── Equipo: Interfaz
  └── El admin define el calendario de vacunas: nombre de la vacuna, fecha programada,
      si aplica a todo el hato o a animales específicos, y frecuencia de repetición.

PASO 2 — Alerta previa (7 días antes)
  ├── Actor: Sistema
  ├── Equipo: Base de Datos + Formularios
  └── Cuando `fecha_programada - CURRENT_DATE <= 7 días`, el dashboard muestra:
      "🟡 Vacuna [nombre] programada para [fecha]. [N] animales pendientes."

PASO 3 — Día de la vacunación (sin registrar)
  ├── Actor: Sistema
  ├── Equipo: Interfaz
  └── Si el día de la vacuna llega y no hay registro de aplicación:
      La alerta escala a 🔴: "Vacuna [nombre] vencida. Acción requerida."

PASO 4 — Registro de aplicación
  ├── Actor: Empleado/Admin
  ├── Equipo: Formularios + BD
  └── Se registra en `VacunaAplicada`: fecha real, dosis, lote del producto,
      operario responsable. La alerta se cierra.
      Si hay frecuencia configurada, el sistema programa automáticamente la próxima fecha.
```

**Motivación real:** La finca perdió 7 animales en un solo día por no vacunar contra rabia a tiempo. Este flujo tiene la máxima prioridad de alertas.
