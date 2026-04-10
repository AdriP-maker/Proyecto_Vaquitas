# Estándares de Nomenclatura y Prefijos para el Proyecto

Este documento establece los estándares de nomenclatura y los prefijos que se utilizarán en todo el desarrollo del sistema (interfaces, formularios y base de datos) para mantener un código limpio, organizado y fácil de mantener.

## 1. Prefijos para los elementos (UI)

Estos prefijos se utilizarán para nombrar los identificadores (`id`), clases, o variables relacionadas con los elementos de la interfaz de usuario en el código frontend.

| Elemento | Prefijo | Ejemplo de uso |
| :--- | :--- | :--- |
| Botón | `btn_` | `btn_guardar`, `btn_cancelar` |
| Label | `lbl_` | `lbl_nombre`, `lbl_precio` |
| Campo texto | `input_` | `input_nombre`, `input_email` |
| Selección | `select_` | `select_pais`, `select_opciones` |
| Checkbox | `chk_` | `chk_aceptar`, `chk_recibir` |
| Radio | `radio_` | `radio_masculino`, `radio_femenino` |
| Tablas | `table_` | `table_productos`, `table_usuarios` |
| Imágenes | `img_` | `img_logo`, `img_perfil` |
| Íconos | `icon_` | `icon_home`, `icon_edit` |

## 2. Prefijos para Interfaz (Estructura y Layout)

Para mantener la estructura del HTML o componentes visuales organizada, se sugiere utilizar estos prefijos en los contenedores principales y divisiones.

| Elemento | Prefijo | Ejemplo de uso |
| :--- | :--- | :--- |
| Contenedor Principal | `container_` | `container_dashboard`, `container_login` |
| Sección / Bloque | `sec_` | `sec_header`, `sec_footer`, `sec_detalles` |
| Tarjeta / Card | `card_` | `card_resumen`, `card_producto` |
| Modal / Diálogo | `modal_` | `modal_confirmacion`, `modal_edicion` |
| Barra de Navegación | `nav_` | `nav_principal`, `nav_lateral` |
| Alertas / Mensajes | `alert_` | `alert_error`, `alert_exito` |

## 3. Prefijos para Formularios

Identificadores específicos para la manipulación e interacciones de formularios.

| Elemento | Prefijo | Ejemplo de uso |
| :--- | :--- | :--- |
| Formulario Principal | `frm_` | `frm_registro_usuario`, `frm_login` |
| Grupo de campos | `group_` | `group_datos_personales`, `group_direccion` |
| Mensaje de Error (Validación) | `err_` | `err_contrasena`, `err_campo_requerido` |
| Mensaje de Ayuda | `help_` | `help_formato_fecha` |
| Botón de Envío (Submit) | `submit_` | `submit_guardar_cambios` |

## 4. Prefijos para Base de Datos

Estándares para nombrar los diferentes objetos dentro de la base de datos relacional para facilitar su identificación y consulta.

| Elemento | Prefijo | Ejemplo de uso |
| :--- | :--- | :--- |
| Tabla | `tbl_` | `tbl_usuarios`, `tbl_fincas`, `tbl_animales` |
| Vista (View) | `vw_` | `vw_resumen_produccion`, `vw_usuarios_activos` |
| Procedimiento Almacenado | `sp_` | `sp_insertar_animal`, `sp_actualizar_peso` |
| Clave Primaria (Primary Key) | `pk_` | `pk_id_usuario`, `pk_id_finca` |
| Clave Foránea (Foreign Key) | `fk_` | `fk_id_raza`, `fk_id_usuario_registra` |
| Índice (Index) | `idx_` | `idx_fecha_nacimiento`, `idx_nombre_animal` |
| Función (Function) | `fn_` | `fn_calcular_edad`, `fn_obtener_promedio` |
