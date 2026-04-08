# 📄 Documento 4 — Esquema de Base de Datos (ERD Simplificado)

**Proyecto:** HOFLOC S.A. — Plataforma Web de Gestión Agropecuaria  
**Versión:** v1.0 | **Fecha:** 2026-04-08

---

## 4.1 Entidades / Tablas Principales

### `Animal`
Entidad central del sistema. Representa cada hembra del hato.

| Campo | Tipo | Restricciones | Descripción |
|-------|------|---------------|-------------|
| `id` | INT | PK, AUTO_INCREMENT | Identificador interno |
| `numero_hembra` | VARCHAR(20) | UNIQUE, NOT NULL | Número identificador de campo (ej. "07", "098", "ABUELA") |
| `nombre` | VARCHAR(100) | NULL | Nombre asignado (ej. "0448", "ARMANDO CHOCOLATE", "Ceniza") |
| `estado` | ENUM('LACT','NVIE','SECA','NLEV') | NOT NULL | Estado reproductivo actual |
| `detalle_estado` | VARCHAR(200) | NULL | Texto libre de detalle (ej. "[SIN2] SINCRO 2 USO") |
| `dias_lactancia` | INT | DEFAULT 0 | Días acumulados en lactancia actual |
| `dias_abortos` | INT | DEFAULT 0 | Días desde último aborto |
| `reproductor_id` | INT | FK → Reproductor | Últiimo reproductor asociado |
| `potrero_id` | INT | FK → Potrero | Potrero asignado actualmente |
| `lote_id` | INT | FK → Lote | Lote del animal (ALTA, MANGO, UVERO, CRÍAS, CHINO) |
| `confirmada_fecha` | DATE | NULL | Fecha de confirmación de preñez (ej. 02/04/26) |
| `activo` | BOOLEAN | DEFAULT TRUE | Para dar de baja sin eliminar |
| `fecha_ingreso` | DATE | NOT NULL | Fecha de ingreso al sistema |
| `created_at` | TIMESTAMP | DEFAULT NOW() | |
| `updated_at` | TIMESTAMP | ON UPDATE NOW() | |

---

### `Gestacion`
Registra cada gestación de una hembra.

| Campo | Tipo | Restricciones | Descripción |
|-------|------|---------------|-------------|
| `id` | INT | PK, AUTO_INCREMENT | |
| `animal_id` | INT | FK → Animal, NOT NULL | Hembra preñada |
| `fecha_prenez` | DATE | NOT NULL | Fecha de preñez confirmada (ej. 02/03/26) |
| `dias_gestacion` | INT | COMPUTED/VIRTUAL | Calculado: `CURRENT_DATE - fecha_prenez` |
| `reproductor_id` | INT | FK → Reproductor | Toro padre (ej. 7JE1726, 7HO12819) |
| `estado` | ENUM('ACTIVA','PARTO','ABORTO','PERDIDA') | NOT NULL DEFAULT 'ACTIVA' | |
| `fecha_parto_esperado` | DATE | NULL | Calculado: `fecha_prenez + 280 días` (bovino estándar) ⚠️ DECISIÓN PENDIENTE: validar días exactos con veterinario |
| `fecha_parto_real` | DATE | NULL | Completar al momento del parto |
| `observaciones` | TEXT | NULL | |
| `created_at` | TIMESTAMP | DEFAULT NOW() | |

---

### `OrdeñoRegistro`
Captura producción de leche por vaca, por día y turno.

| Campo | Tipo | Restricciones | Descripción |
|-------|------|---------------|-------------|
| `id` | INT | PK, AUTO_INCREMENT | |
| `animal_id` | INT | FK → Animal, NOT NULL | Vaca ordeñada |
| `fecha` | DATE | NOT NULL | Fecha del registro (ej. 29-03-26) |
| `turno` | ENUM('AM','PM') | NOT NULL | Turno de ordeño |
| `litros` | DECIMAL(5,2) | NOT NULL, >= 0 | Litros producidos en ese turno |
| `litros_total_dia` | DECIMAL(5,2) | COMPUTED | `SUM litros AM+PM` para esa vaca y fecha |
| `operario_id` | INT | FK → Usuario | Empleado que realizó el registro |
| `observaciones` | VARCHAR(300) | NULL | |
| UNIQUE INDEX | | `(animal_id, fecha, turno)` | Evita duplicados |

---

### `PesajeTernero`
Registro de pesos para construcción de curva de crecimiento.

| Campo | Tipo | Restricciones | Descripción |
|-------|------|---------------|-------------|
| `id` | INT | PK, AUTO_INCREMENT | |
| `animal_id` | INT | FK → Animal, NOT NULL | Ternero/a pesado/a |
| `fecha_pesaje` | DATE | NOT NULL | Fecha del pesaje |
| `peso_kg` | DECIMAL(6,2) | NOT NULL, > 0 | Peso en kilogramos |
| `ganancia_diaria_kg` | DECIMAL(5,3) | COMPUTED | vs. pesaje anterior |
| `operario_id` | INT | FK → Usuario | Quien realizó el pesaje |
| `observaciones` | VARCHAR(300) | NULL | |
| INDEX | | `(animal_id, fecha_pesaje)` | Para consultas de curva |

---

### `Reproductor`
Catálogo de toros/reproductores utilizados.

| Campo | Tipo | Restricciones | Descripción |
|-------|------|---------------|-------------|
| `id` | INT | PK, AUTO_INCREMENT | |
| `codigo` | VARCHAR(30) | UNIQUE, NOT NULL | Código del reproductor (ej. 7JE1726, L0230, GIGANTE) |
| `nombre` | VARCHAR(100) | NULL | Nombre descriptivo |
| `raza` | VARCHAR(50) | NULL | |
| `tipo` | ENUM('SEMEN','NATURAL','IA') | NOT NULL | Tipo de servicio |
| `activo` | BOOLEAN | DEFAULT TRUE | |

---

### `Proveedor`
Catálogo de proveedores de insumos y servicios.

| Campo | Tipo | Restricciones | Descripción |
|-------|------|---------------|-------------|
| `id` | INT | PK, AUTO_INCREMENT | |
| `nombre` | VARCHAR(150) | NOT NULL | Nombre de la empresa o persona |
| `ruc_cedula` | VARCHAR(20) | UNIQUE | RUC o cédula |
| `telefono` | VARCHAR(20) | NULL | |
| `email` | VARCHAR(100) | NULL | |
| `categoria` | ENUM('VETERINARIO','ALIMENTACION','EQUIPOS','TRANSPORTE','OTRO') | NOT NULL | |
| `contacto_nombre` | VARCHAR(100) | NULL | Persona de contacto |
| `activo` | BOOLEAN | DEFAULT TRUE | |
| `created_at` | TIMESTAMP | DEFAULT NOW() | |

---

### `Usuario`
Cuentas del panel interno.

| Campo | Tipo | Restricciones | Descripción |
|-------|------|---------------|-------------|
| `id` | INT | PK, AUTO_INCREMENT | |
| `email` | VARCHAR(150) | UNIQUE, NOT NULL | |
| `password_hash` | VARCHAR(255) | NOT NULL | Nunca texto plano |
| `nombre_completo` | VARCHAR(150) | NOT NULL | |
| `rol` | ENUM('ADMIN','EMPLEADO') | NOT NULL DEFAULT 'EMPLEADO' | |
| `activo` | BOOLEAN | DEFAULT TRUE | |
| `ultimo_login` | TIMESTAMP | NULL | |
| `created_at` | TIMESTAMP | DEFAULT NOW() | |

---

### Tablas auxiliares

**`Potrero`** — Catálogo de potreros de la finca  
`id | nombre (ALTA, MANGO, UVERO, CHINO) | descripcion | capacidad_animales`

**`Lote`** — Agrupación operativa de animales  
`id | nombre (ALTA, MANGO, CRÍAS, etc.) | descripcion`

**`SolicitudVisita`** — Solicitudes del portal público  
`id | nombre_solicitante | email | telefono | fecha_preferida | num_personas | proposito | estado (PENDIENTE/CONFIRMADA/RECHAZADA) | created_at`

**`MensajeContacto`** — Formulario de contacto público  
`id | nombre | email | mensaje | leido (boolean) | created_at`

**`ProyeccionHato`** — Tabla de proyecciones mensuales  
`id | anio | mes | total_vacas | vacas_lactancia | vacas_secar | pct_produccion | partos_vacas | partos_novillas | total_partos`

---

## 4.2 Relaciones entre Tablas

| Relación | Tipo | Descripción |
|----------|------|-------------|
| `Animal` → `Reproductor` | N:1 | Cada animal tiene un último reproductor asignado |
| `Animal` → `Potrero` | N:1 | Cada animal está en un potrero |
| `Animal` → `Lote` | N:1 | Cada animal pertenece a un lote |
| `Gestacion` → `Animal` | N:1 | Una hembra puede tener múltiples gestaciones en su vida |
| `Gestacion` → `Reproductor` | N:1 | Cada gestación identifica al padre |
| `OrdeñoRegistro` → `Animal` | N:1 | Múltiples registros de ordeño por vaca |
| `OrdeñoRegistro` → `Usuario` | N:1 | Cada registro tiene un operario responsable |
| `PesajeTernero` → `Animal` | N:1 | Múltiples pesajes por ternero |
| `PesajeTernero` → `Usuario` | N:1 | Cada pesaje tiene un registrador |

---

## 4.3 Índices Prioritarios para Consultas Frecuentes

| Tabla | Index | Tipo | Justificación |
|-------|-------|------|---------------|
| `Animal` | `(estado)` | BTREE | Filtrar por LACT/NVIE/SECA en dashboard |
| `Animal` | `(lote_id, estado)` | BTREE | Reporte por lote y estado |
| `Gestacion` | `(animal_id, estado)` | BTREE | Gestaciones activas por animal |
| `Gestacion` | `(fecha_prenez)` | BTREE | Cálculo de días y alertas |
| `OrdeñoRegistro` | `(animal_id, fecha)` | UNIQUE | Consultas de producción diaria |
| `OrdeñoRegistro` | `(fecha)` | BTREE | Totales del día para dashboard |
| `PesajeTernero` | `(animal_id, fecha_pesaje)` | BTREE | Curva de crecimiento |

> ⚠️ **DECISIÓN PENDIENTE** — Selección del motor de base de datos: **PostgreSQL** (recomendado por soporte de columnas computadas y funciones de ventana para estadísticas) vs. **MySQL/MariaDB** (más común en hosting compartido). Impacta la sintaxis de funciones como `dias_gestacion` calculado.
