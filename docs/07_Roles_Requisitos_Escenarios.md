# 📄 Documento 7 — Roles, Requisitos Funcionales, Escenarios y Requisitos No Funcionales

**Proyecto:** HOFLOC S.A. — Plataforma Web de Gestión Agropecuaria  
**Versión:** v1.0 | **Fecha:** 2026-04-09  
**Referencia:** Derivado de Doc 1–6 del análisis arquitectónico

---

## SECCIÓN 1 — ROLES DEL SISTEMA

### 1.1 Definición de Roles

| ID Rol | Nombre | Descripción | Tipo de acceso |
|--------|--------|-------------|----------------|
| ROL-01 | **Cliente Externo** | Visitante del portal público. No requiere autenticación. Puede consultar información, catálogo y enviar formularios de contacto/visita. | Solo portal público |
| ROL-02 | **Empleado** | Personal operativo de la finca (ordeñadores, encargados de campo). Requiere autenticación. Accede a módulos de captura de datos diarios. | Panel interno (limitado) |
| ROL-03 | **Administrador** | Propietario o encargado principal de la finca. Acceso total al sistema. Gestiona usuarios, consulta estadísticas, reportes y proyecciones. | Panel interno (completo) + Portal |

---

### 1.2 Permisos por Módulo

| Módulo | Cliente Externo | Empleado | Administrador |
|--------|:-:|:-:|:-:|
| P-01 Página de Inicio | ✅ | ✅ | ✅ |
| P-02 Catálogo de Productos | ✅ | ✅ | ✅ |
| P-03 Solicitud de Visita | ✅ | ✅ | ✅ |
| P-04 Formulario de Contacto | ✅ | ✅ | ✅ |
| A-01 Dashboard General | ❌ | ✅ (vista básica) | ✅ (completo) |
| A-02 Gestión de Hembras | ❌ | ✅ (lectura + edición) | ✅ (CRUD completo) |
| A-03 Control de Gestación | ❌ | ✅ (registro) | ✅ (CRUD + alertas) |
| A-04 Registro de Ordeño | ❌ | ✅ | ✅ |
| A-05 Estadísticas de Producción | ❌ | ❌ | ✅ |
| A-06 Pesaje de Terneros | ❌ | ✅ | ✅ |
| A-07 Gestión de Proveedores | ❌ | ❌ | ✅ |
| A-08 Gestión de Usuarios | ❌ | ❌ | ✅ |
| A-09 Proyecciones del Hato | ❌ | ❌ | ✅ |
| A-10 Módulo de Reportes | ❌ | ❌ | ✅ |

---

## SECCIÓN 2 — REQUISITOS FUNCIONALES

Los requisitos funcionales se organizan por módulo, usando el formato:  
`[RF-XX] Nombre — Descripción`

---

### 2.1 Portal Público

**RF-01 — Visualización institucional**  
El sistema debe mostrar una página de inicio con información de la finca HOFLOC: misión, historia, galería de imágenes y datos de contacto.  
*Actor:* Cliente Externo | *Módulo:* P-01

**RF-02 — Catálogo de productos**  
El sistema debe mostrar los productos disponibles (leche fresca, terneros, visitas) con imagen, descripción, precio indicativo y estado de disponibilidad.  
*Actor:* Cliente Externo | *Módulo:* P-02

**RF-03 — Formulario de solicitud de visita**  
El sistema debe permitir al cliente ingresar: nombre completo, correo electrónico, teléfono, fecha preferida, número de personas y propósito de la visita.  
*Actor:* Cliente Externo | *Módulo:* P-03

**RF-04 — Formulario de contacto**  
El sistema debe permitir al cliente enviar un mensaje general. Campos: nombre, email y mensaje libre.  
*Actor:* Cliente Externo | *Módulo:* P-04

**RF-05 — Confirmación de envío**  
Tras enviar cualquier formulario público, el sistema debe mostrar una confirmación visual en pantalla.  
*Actor:* Cliente Externo | *Módulos:* P-03, P-04

---

### 2.2 Autenticación y Usuarios

**RF-06 — Inicio de sesión**  
El sistema debe permitir el acceso al panel interno mediante email y contraseña. Debe devolver un token de sesión (JWT) al autenticarse correctamente.  
*Actor:* Empleado, Administrador | *Módulo:* A-08

**RF-07 — Cierre de sesión**  
El sistema debe invalidar la sesión activa al hacer logout.  
*Actor:* Empleado, Administrador | *Módulo:* A-08

**RF-08 — Gestión de usuarios (CRUD)**  
El Administrador debe poder crear, editar, desactivar y reactivar cuentas de usuarios. Al crear, debe asignar: nombre completo, email y rol (EMPLEADO o ADMIN).  
*Actor:* Administrador | *Módulo:* A-08

**RF-09 — Recuperación de contraseña**  
El sistema debe ofrecer un flujo de recuperación de contraseña por email.  
*Actor:* Empleado, Administrador | *Módulo:* A-08

---

### 2.3 Gestión de Hembras

**RF-10 — Registro de hembra**  
El sistema debe permitir crear una ficha de hembra con campos: número de hembra (único), nombre, estado reproductivo (LACT/NVIE/SECA/NLEV), detalle de estado, potrero, lote y reproductor asociado.  
*Actor:* Empleado, Administrador | *Módulo:* A-02

**RF-11 — Edición de ficha de hembra**  
El sistema debe permitir editar todos los campos de la ficha de un animal existente.  
*Actor:* Empleado, Administrador | *Módulo:* A-02

**RF-12 — Listado del hato con filtros**  
El sistema debe mostrar la lista completa de hembras con paginación. Debe permitir filtrar por estado, potrero y lote.  
*Actor:* Empleado, Administrador | *Módulo:* A-02

**RF-13 — Baja lógica de animal**  
El sistema debe permitir desactivar un animal sin eliminarlo de la base de datos, manteniendo su historial intacto.  
*Actor:* Administrador | *Módulo:* A-02

**RF-14 — Cambio de estado reproductivo con validación de transición**  
El sistema debe validar que el cambio de estado reproductivo sea una transición permitida entre los estados definidos (ver RN-008 en Doc 3).  
*Actor:* Empleado, Administrador | *Módulo:* A-02

---

### 2.4 Control de Gestación

**RF-15 — Registro de gestación**  
El sistema debe permitir registrar una gestación para una hembra: fecha de preñez y reproductor (seleccionable del catálogo).  
*Actor:* Empleado, Administrador | *Módulo:* A-03

**RF-16 — Cálculo automático de días de gestación**  
El sistema debe calcular y mostrar automáticamente los días de gestación desde `fecha_prenez` hasta la fecha actual.  
*Actor:* Sistema | *Módulo:* A-03

**RF-17 — Alerta visual por días de gestación**  
El sistema debe mostrar un indicador de semáforo:
- 🟢 Verde: < 60 días
- 🟡 Amarillo: 60–80 días
- 🔴 Rojo: > 80 días  

*Actor:* Sistema | *Módulo:* A-03

**RF-18 — Registro de parto**  
Al registrar el parto, el sistema debe: cerrar la gestación (estado = PARTO), registrar fecha real de parto, y cambiar el estado de la hembra a LACT.  
*Actor:* Empleado, Administrador | *Módulo:* A-03

**RF-19 — Vista filtrable por días de gestación**  
El sistema debe permitir ordenar y filtrar la lista de gestaciones activas por número de días (ascendente/descendente).  
*Actor:* Empleado, Administrador | *Módulo:* A-03

---

### 2.5 Registro de Ordeño

**RF-20 — Captura de ordeño AM y PM**  
El sistema debe permitir registrar, para cada vaca en estado LACT, los litros producidos en turno AM y PM para una fecha específica (default: hoy).  
*Actor:* Empleado | *Módulo:* A-04

**RF-21 — Cálculo de total diario por vaca**  
El sistema debe calcular automáticamente: `total_dia = litros_AM + litros_PM` y mostrarlo en tiempo real durante la captura.  
*Actor:* Sistema | *Módulo:* A-04

**RF-22 — Prevención de duplicados de ordeño**  
El sistema debe detectar si ya existe un registro AM o PM para una vaca en la fecha seleccionada y requerir confirmación explícita antes de sobrescribir.  
*Actor:* Sistema | *Módulo:* A-04

**RF-23 — Restricción de ordeño a vacas en LACT**  
El sistema debe impedir el registro de ordeño para cualquier vaca que no esté en estado LACT.  
*Actor:* Sistema | *Módulo:* A-04

---

### 2.6 Estadísticas de Producción

**RF-24 — Promedio semanal por vaca**  
El sistema debe calcular y mostrar el promedio de litros diarios producidos por cada vaca en un rango de 7 días.  
*Actor:* Administrador | *Módulo:* A-05

**RF-25 — Promedio mensual por vaca y por lote**  
El sistema debe calcular promedios mensuales de producción tanto a nivel individual como agrupado por lote (ALTA, MANGO, UVERO, CRÍAS, CHINO).  
*Actor:* Administrador | *Módulo:* A-05

**RF-26 — Gráficas de tendencia de producción**  
El sistema debe mostrar:
- Gráfica de línea: producción diaria de una vaca en el tiempo
- Gráfica de barras: comparativo de producción por lote  

*Actor:* Administrador | *Módulo:* A-05

**RF-27 — Porcentaje de vacas en producción**  
El sistema debe calcular y mostrar el % de vacas en estado LACT sobre el total del hato activo.  
*Actor:* Administrador | *Módulos:* A-01, A-05

---

### 2.7 Pesaje y Crecimiento de Terneros

**RF-28 — Registro de pesaje**  
El sistema debe permitir registrar un pesaje para un ternero: fecha y peso en kg.  
*Actor:* Empleado, Administrador | *Módulo:* A-06

**RF-29 — Cálculo de ganancia diaria de peso**  
El sistema debe calcular automáticamente la ganancia diaria: `(peso_actual - peso_anterior) / días_entre_pesajes`.  
*Actor:* Sistema | *Módulo:* A-06

**RF-30 — Curva de crecimiento individual**  
El sistema debe generar una gráfica de curva de crecimiento por cada ternero con todos sus pesajes históricos.  
*Actor:* Empleado, Administrador | *Módulo:* A-06

**RF-31 — Alerta de bajo crecimiento**  
El sistema debe emitir una alerta cuando la ganancia diaria de un ternero esté por debajo del umbral definido por el equipo veterinario.  
*Actor:* Sistema | *Módulo:* A-06

---

### 2.8 Gestión de Proveedores

**RF-32 — CRUD de proveedores**  
El Administrador debe poder crear, editar y desactivar proveedores. Campos: nombre, RUC/cédula, teléfono, email, categoría y contacto.  
*Actor:* Administrador | *Módulo:* A-07

**RF-33 — Filtrado por categoría**  
El sistema debe permitir filtrar la lista de proveedores por categoría (VETERINARIO, ALIMENTACION, EQUIPOS, TRANSPORTE, OTRO).  
*Actor:* Administrador | *Módulo:* A-07

---

### 2.9 Dashboard y Reportes

**RF-34 — Dashboard de indicadores clave (KPIs)**  
El dashboard debe mostrar en tiempo cuasi-real: total de vacas activas, litros producidos en el día, número de animales en alerta y próximos partos esperados.  
*Actor:* Empleado, Administrador | *Módulo:* A-01

**RF-35 — Exportación de reportes en PDF**  
El sistema debe permitir exportar en PDF: producción mensual, estado reproductivo del hato e inventario de terneros.  
*Actor:* Administrador | *Módulo:* A-10

**RF-36 — Exportación de reportes en Excel**  
El sistema debe permitir exportar los mismos reportes en formato .xlsx.  
*Actor:* Administrador | *Módulo:* A-10

**RF-37 — Proyecciones mensuales del hato**  
El sistema debe mostrar y permitir editar la tabla de proyecciones (total vacas, en lactancia, a secar, % producción, partos) mes a mes.  
*Actor:* Administrador | *Módulo:* A-09

---

## SECCIÓN 3 — ESCENARIOS DE USO

Los escenarios describen situaciones reales y detalladas de interacción entre actores y el sistema.

---

### ESC-01 — Empleada registra el ordeño matutino

**Actor principal:** Empleado (ordeñador)  
**Precondición:** El empleado tiene sesión activa. Hay al menos 1 vaca en estado LACT.

| Paso | Actor | Acción |
|------|-------|--------|
| 1 | Empleado | Accede al módulo A-04 "Registro de Ordeño". |
| 2 | Sistema | Carga la lista de vacas en estado LACT para la fecha de hoy. |
| 3 | Empleado | Para cada vaca, ingresa los litros del turno AM en la planilla digital. |
| 4 | Sistema | Calcula en tiempo real el total AM acumulado del hato. |
| 5 | Empleado | Confirma el envío del registro AM. |
| 6 | Sistema | Valida que no exista un registro AM previo para la misma fecha. |
| 7 | Sistema | Guarda los registros en `OrdeñoRegistro`. Confirma exitosamente. |

**Flujo alternativo (duplicado detectado):**  
En el paso 6, si ya existe registro AM: el sistema advierte y solicita confirmación para sobrescribir. El empleado confirma o cancela.

**Postcondición:** Registros AM del día almacenados. Dashboard actualiza total de litros.

---

### ESC-02 — Administrador detecta alerta de hembra en gestación avanzada

**Actor principal:** Administrador  
**Precondición:** Existe una hembra con `dias_gestacion >= 60` en la BD.

| Paso | Actor | Acción |
|------|-------|--------|
| 1 | Administrador | Accede al dashboard (A-01). |
| 2 | Sistema | Muestra tarjeta "Animales en Atención" con la hembra #8802 (101 días). |
| 3 | Administrador | Hace clic en la alerta para ver la ficha completa. |
| 4 | Sistema | Muestra la ficha: fecha de preñez (24/12/25), reproductor, días gestación, fecha de parto esperado. |
| 5 | Administrador | Registra una observación de preparto. |
| 6 | Sistema | Guarda la observación con timestamp y usuario que la registró. |

**Postcondición:** La hembra queda monitoreada. La alerta persiste hasta que se registre el parto.

---

### ESC-03 — Empleado registra pesaje de un ternero

**Actor principal:** Empleado  
**Precondición:** El ternero existe en el sistema con al menos 1 pesaje previo.

| Paso | Actor | Acción |
|------|-------|--------|
| 1 | Empleado | Accede al módulo A-06 y busca el ternero por número. |
| 2 | Sistema | Muestra el historial de pesajes y la curva de crecimiento actual. |
| 3 | Empleado | Ingresa la fecha de hoy y el peso actual en kg. |
| 4 | Sistema | Calcula la ganancia diaria vs. el pesaje anterior. Muestra el resultado antes de guardar. |
| 5 | Empleado | Confirma el guardado. |
| 6 | Sistema | Inserta en `PesajeTernero`. Actualiza la curva de crecimiento. |
| 7 | Sistema | Si ganancia < umbral, emite alerta de revisión veterinaria. |

**Postcondición:** Nuevo punto en la curva de crecimiento. Alerta generada si aplica.

---

### ESC-04 — Administrador genera reporte mensual de producción

**Actor principal:** Administrador  
**Precondición:** Existen registros de ordeño para el mes seleccionado.

| Paso | Actor | Acción |
|------|-------|--------|
| 1 | Administrador | Accede al módulo A-10 "Reportes". |
| 2 | Administrador | Selecciona tipo "Producción Mensual" y el mes/año deseado. |
| 3 | Sistema | Genera una previsualización del reporte con promedios por vaca y por lote. |
| 4 | Administrador | Elige exportar en PDF o Excel. |
| 5 | Sistema | Genera y descarga el archivo. |

**Postcondición:** Reporte descargado localmente por el administrador.

---

### ESC-05 — Cliente externo solicita una visita a la finca

**Actor principal:** Cliente Externo  
**Precondición:** Ninguna (acceso público sin autenticación).

| Paso | Actor | Acción |
|------|-------|--------|
| 1 | Cliente | Navega al portal público y accede a la sección "Solicitar Visita" (P-03). |
| 2 | Cliente | Completa el formulario: nombre, email, teléfono, fecha preferida, número de personas y propósito. |
| 3 | Sistema | Valida que la fecha no sea pasada y que el email tenga formato válido. |
| 4 | Sistema | Guarda la solicitud en la BD con estado "PENDIENTE". |
| 5 | Sistema | Muestra mensaje de confirmación en pantalla. Envía correo de acuse al cliente. |
| 6 | Administrador | Recibe la solicitud en el panel y la confirma o rechaza desde A-03. |

**Flujo alternativo (error de validación):**  
En el paso 3, si la fecha es pasada o el email es inválido, el sistema muestra el error debajo del campo correspondiente sin resetear el formulario.

**Postcondición:** Solicitud de visita registrada y pendiente de gestión por el administrador.

---

### ESC-06 — Empleado registra nueva hembra en el sistema

**Actor principal:** Empleado  
**Precondición:** El empleado tiene sesión activa con rol EMPLEADO o ADMIN.

| Paso | Actor | Acción |
|------|-------|--------|
| 1 | Empleado | Accede a A-02 y pulsa "Nueva Hembra". |
| 2 | Empleado | Completa la ficha: número de hembra, estado reproductivo, potrero, lote, reproductor. |
| 3 | Sistema | Verifica en tiempo real que el número de hembra no exista ya en la BD. |
| 4 | Sistema | Valida que el reproductor seleccionado esté en el catálogo activo. |
| 5 | Empleado | Confirma el guardado. |
| 6 | Sistema | Inserta el registro en `Animal`. El animal aparece inmediatamente en el listado del hato. |

**Flujo alternativo (número duplicado):**  
En el paso 3, si el número ya existe, el sistema muestra: *"El número [N°] ya está asignado a [nombre]"* e impide el guardado.

---

## SECCIÓN 4 — REQUISITOS NO FUNCIONALES

---

### 4.1 Rendimiento

| ID | Requisito | Métrica objetivo |
|----|-----------|-----------------|
| RNF-01 | El sistema debe cargar cualquier vista del panel interno en menos de 3 segundos en condiciones normales de red (≥ 10 Mbps). | Tiempo de carga < 3s |
| RNF-02 | El dashboard debe actualizar sus KPIs con un máximo de 30 segundos de retraso respecto a los datos en BD. | Latencia máx. 30s |
| RNF-03 | La generación de reportes PDF/Excel no debe superar los 10 segundos para reportes de hasta 12 meses de datos. | Tiempo generación < 10s |
| RNF-04 | La interfaz de captura de ordeño (A-04) debe calcular totales en tiempo real con latencia < 200ms por fila actualizada. | Latencia interacción < 200ms |

---

### 4.2 Seguridad

| ID | Requisito |
|----|-----------|
| RNF-05 | Las contraseñas de usuarios deben almacenarse siempre con algoritmo de hash seguro (bcrypt o Argon2). Nunca en texto plano. |
| RNF-06 | Toda comunicación entre el cliente (browser) y el servidor debe realizarse sobre HTTPS con certificado TLS válido. |
| RNF-07 | Las sesiones del panel interno deben expirar automáticamente tras 8 horas de inactividad. |
| RNF-08 | El acceso a cualquier ruta del panel `/admin/*` debe requerir token JWT válido. Cualquier request sin token debe devolver HTTP 401. |
| RNF-09 | El formulario de contacto y solicitud de visita debe incluir protección anti-spam (honeypot o reCAPTCHA v3). |
| RNF-10 | Los datos sensibles (emails, contraseñas, RUC) deben tratarse conforme a buenas prácticas de privacidad y no exponerse en logs. |

---

### 4.3 Usabilidad

| ID | Requisito |
|----|-----------|
| RNF-11 | El sistema debe ser responsive y funcionar correctamente en dispositivos móviles (mínimo 375px de ancho), tabletas y escritorio. |
| RNF-12 | La interfaz de captura de ordeño (A-04) debe replicar el formato de la planilla física "Pesa de Leche" para facilitar la adopción por parte del personal. |
| RNF-13 | El sistema debe mostrar mensajes de error claros y en español para todos los formularios, indicando el campo específico con el problema. |
| RNF-14 | El panel interno debe incluir un menú de navegación lateral siempre visible en escritorio y colapsable en móvil. |
| RNF-15 | Cualquier operación de guardado o envío debe mostrar un estado de carga (spinner o skeleton) mientras se procesa. |

---

### 4.4 Disponibilidad y Mantenimiento

| ID | Requisito |
|----|-----------|
| RNF-16 | El sistema debe tener una disponibilidad mínima del 99% mensual (máximo ~7h de caída por mes). |
| RNF-17 | La base de datos debe contar con backups automáticos diarios con retención mínima de 30 días. |
| RNF-18 | El código del proyecto debe estar versionado en Git con ramas separadas por equipo y entornos diferenciados (dev / staging / producción). |
| RNF-19 | El sistema debe incluir un archivo `.env` de configuración separado del código fuente para variables sensibles (credenciales de BD, claves de API). |

---

### 4.5 Escalabilidad

| ID | Requisito |
|----|-----------|
| RNF-20 | La arquitectura debe soportar el crecimiento del hato hasta al menos 300 animales sin degradación perceptible del rendimiento. |
| RNF-21 | La tabla `OrdeñoRegistro` debe estar diseñada para almacenar hasta 5 años de registros diarios (~100,000 filas) con tiempos de consulta aceptables gracias a los índices definidos. |
| RNF-22 | El sistema debe permitir agregar nuevos módulos sin necesidad de reescribir la arquitectura base (bajo acoplamiento, alta cohesión). |

---

### 4.6 Compatibilidad

| ID | Requisito |
|----|-----------|
| RNF-23 | El portal público y el panel interno deben funcionar correctamente en los navegadores: Chrome ≥ 100, Firefox ≥ 100, Safari ≥ 15 y Edge ≥ 100. |
| RNF-24 | Los archivos exportados en Excel deben ser compatibles con Microsoft Excel 2016+ y Google Sheets. |
| RNF-25 | Los archivos PDF exportados deben visualizarse correctamente en Adobe Acrobat Reader y visores integrados de Chrome/Edge. |

---

## Resumen de conteos

| Categoría | Cantidad |
|-----------|----------|
| Roles definidos | 3 |
| Requisitos Funcionales | 37 |
| Escenarios de uso | 6 |
| Requisitos No Funcionales | 25 |
| **Total de ítems documentados** | **71** |

---

*Documento de referencia para todos los equipos. Las decisiones pendientes marcadas con ⚠️ en documentos anteriores impactan directamente en los requisitos RF-31 (umbral de crecimiento), RF-18 (días de gestación estándar) y RNF-16 (stack de hosting).*
