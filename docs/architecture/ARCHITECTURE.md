# Arquitectura de MarkDB

**Versión:** 1.0  
**Estado:** Arquitectura base del MVP  
**Producto:** MarkDB  
**Última actualización:** 2026-11-13

## 1. Propósito

MarkDB es una herramienta para diseñar visualmente esquemas de bases de datos, documentarlos en Markdown y exportar su estructura como SQL compatible con PostgreSQL y MariaDB.

El MVP estará compuesto por:

- Una aplicación web.
- Una extensión para Visual Studio Code.
- Persistencia local.
- Archivos de proyecto `.markdb`.
- Documentación Markdown editable.
- Exportación DDL para PostgreSQL y MariaDB.

El backend, las cuentas de usuario, las sesiones y la sincronización remota quedan fuera del MVP.

## 2. Principios arquitectónicos

### 2.1 Modelo estructurado como estado aplicado

El modelo estructurado del proyecto representa el único estado válido y aplicado del esquema.

El canvas visual, el Markdown y los archivos SQL son representaciones del modelo o propuestas de cambios sobre él.

```text
Modelo estructurado aplicado
        ├── Canvas visual
        ├── Markdown generado
        └── SQL DDL generado
```

### 2.2 Núcleo independiente de las interfaces

La lógica del modelo no debe depender de React, Next.js ni de la API de VS Code.

La aplicación web y la extensión deben utilizar los mismos paquetes de dominio, validación y generación.

### 2.3 Cambios explícitos desde Markdown

El Markdown puede editarse manualmente o mediante una herramienta de inteligencia artificial, pero sus cambios no se aplican automáticamente al modelo.

Los cambios se leen, validan y aplican mediante una acción explícita del usuario.

### 2.4 Persistencia mediante adaptadores

La lógica de aplicación no debe depender directamente de IndexedDB ni del sistema de archivos. Las implementaciones concretas deben cumplir una interfaz de almacenamiento común.

## 3. Vista general del sistema

```text
┌─────────────────────┐       ┌────────────────────────┐
│ Aplicación web      │       │ Extensión VS Code      │
│ Next.js + React     │       │ Extension Host +       │
│                     │       │ Webview React          │
└──────────┬──────────┘       └───────────┬────────────┘
           │                              │
           └──────────────┬───────────────┘
                          │
              ┌───────────▼───────────┐
              │ Paquetes compartidos  │
              ├───────────────────────┤
              │ schema-core           │
              │ shared-types          │
              │ markdown-engine       │
              │ markdown-parser       │
              │ sql-generator         │
              └───────────┬───────────┘
                          │
              ┌───────────▼───────────┐
              │ Adaptadores locales   │
              ├───────────────────────┤
              │ IndexedDB             │
              │ Sistema de archivos   │
              └───────────────────────┘
```

## 4. Estructura del monorepo

```text
MarkDB/
├── frontend/
├── vscode-extension/
├── packages/
│   ├── schema-core/
│   ├── shared-types/
│   ├── markdown-engine/
│   ├── markdown-parser/
│   ├── sql-generator/
│   └── ui/
├── docs/
│   ├── specs/
│   └── architecture/
└── backend/                  # Futuro
```

### 4.1 `schema-core`

Responsabilidades:

- Modelo de proyectos, tablas, columnas y relaciones.
- Operaciones de creación, edición y eliminación.
- Validación del esquema.
- Reglas de negocio.
- Gestión de relaciones y claves.

No debe importar código de React, Next.js o VS Code.

### 4.2 `shared-types`

Contiene tipos compartidos entre aplicaciones y paquetes:

- `DatabaseProject`.
- `DatabaseTable`.
- `DatabaseColumn`.
- `Relationship`.
- `ProjectMetadata`.
- Tipos de resultado y errores de validación.

### 4.3 `markdown-engine`

Convierte el modelo aplicado en documentación Markdown válida y consistente.

### 4.4 `markdown-parser`

Analiza el Markdown compatible con MarkDB y produce una propuesta de modelo.

El parser debe devolver errores estructurados y no debe modificar el modelo aplicado directamente.

### 4.5 `sql-generator`

Genera DDL sin datos para los dialectos soportados:

```text
PostgreSQLGenerator
MariaDBGenerator
```

El generador debe trabajar exclusivamente con el modelo validado.

### 4.6 `frontend`

Responsabilidades:

- Dashboard.
- Editor visual.
- Panel de propiedades.
- Estado de interfaz.
- Persistencia IndexedDB.
- Edición y aplicación de Markdown.
- Exportación de archivos.

### 4.7 `vscode-extension`

Responsabilidades:

- Abrir proyectos `.markdb` desde el workspace.
- Mostrar el editor visual.
- Leer y guardar archivos del workspace.
- Detectar cambios en el Markdown.
- Validar y aplicar cambios mediante acciones explícitas.
- Exportar Markdown y SQL.
- Renombrar archivos de proyecto.

## 5. Archivos de proyecto

Un proyecto utiliza la siguiente estructura:

```text
mi-proyecto/
├── mi-proyecto.markdb
├── mi-proyecto.md
└── STRUCTURE.md
```

### 5.1 Archivo `.markdb`

Es un archivo JSON con una extensión propia. Contiene el modelo estructurado aplicado:

- Identidad del proyecto.
- Tablas.
- Columnas.
- Relaciones.
- Restricciones.
- Posiciones visuales.
- Metadatos.
- Versión del formato.

Ejemplo mínimo:

```json
{
  "formatVersion": 1,
  "project": {
    "id": "project-1",
    "name": "Mi proyecto",
    "description": ""
  },
  "tables": [],
  "relationships": []
}
```

### 5.2 Archivo Markdown

Contiene la documentación editable del proyecto y debe utilizar la sintaxis documentada en `STRUCTURE.md`.

El Markdown puede estar en uno de estos estados:

- Sin cambios pendientes.
- Modificado externamente.
- Analizado y válido.
- Analizado con errores.
- Aplicado al modelo.

### 5.3 Archivo `STRUCTURE.md`

Es un contrato de formato para usuarios y herramientas de inteligencia artificial.

Debe explicar:

- Cómo declarar tablas.
- Cómo declarar columnas.
- Cómo indicar tipos y restricciones.
- Cómo expresar claves y relaciones.
- Qué campos son obligatorios.
- Ejemplos válidos e inválidos.
- Qué contenido puede editarse libremente.

No contiene el esquema actual del proyecto.

## 6. Flujo de datos

### 6.1 Cambios desde el editor visual

```text
Acción del usuario
        ↓
Operación de schema-core
        ↓
Validación
        ↓
Modelo aplicado actualizado
        ├── Actualizar canvas
        ├── Regenerar Markdown
        └── Marcar proyecto como modificado
```

### 6.2 Cambios desde Markdown

```text
Edición del archivo Markdown
        ↓
Detectar cambios pendientes
        ↓
Leer cambios del Markdown
        ↓
markdown-parser
        ↓
Validar propuesta
        ↓
Mostrar diferencias
        ↓
Aplicar cambios
        ↓
Actualizar modelo y canvas
```

Si la propuesta contiene errores, el modelo aplicado y el canvas no deben modificarse.

### 6.3 Conflictos

Si existen cambios pendientes en Markdown y el usuario modifica el canvas, la aplicación debe solicitar una decisión antes de sobrescribir cambios:

- Revisar cambios pendientes.
- Aplicarlos.
- Descartarlos.
- Cancelar la operación.

## 7. Relaciones muchos-a-muchos

Cuando el usuario elige una relación `N:M`, MarkDB debe crear automáticamente una tabla intermedia.

Ejemplo:

```text
users N:M roles
```

Se transforma en:

```text
users 1:N users_roles
roles 1:N users_roles
```

La tabla intermedia forma parte normal del modelo y puede modificarse inmediatamente:

- Cambiar su nombre.
- Agregar columnas.
- Agregar restricciones.
- Cambiar sus relaciones.
- Añadir atributos como `created_at`.

El SQL se genera a partir de las tablas y relaciones resultantes, no de una relación `N:M` abstracta.

## 8. Exportación SQL

El MVP genera únicamente DDL:

- `CREATE TABLE`.
- Tipos de datos.
- `PRIMARY KEY`.
- `FOREIGN KEY`.
- `NOT NULL`.
- `UNIQUE`.
- `CHECK`, cuando sea compatible.
- Índices definidos por el modelo.

No se exportan datos ni sentencias `INSERT`.

Los archivos generados son:

```text
schema.postgresql.sql
schema.mariadb.sql
```

Cada generador debe manejar explícitamente las diferencias entre dialectos y reportar las características no soportadas.

## 9. Extensión de Visual Studio Code

La extensión utilizará las versiones estables de VS Code soportadas por el proyecto. No se dará soporte inicial al canal Insiders.

### 9.1 Interfaz

La extensión reutiliza el editor visual del producto, pero no necesita un visor Markdown integrado.

```text
archivo.markdb → Editor visual MarkDB
archivo.md     → Editor nativo de VS Code
```

La extensión debe proporcionar acciones para:

- Leer cambios del Markdown.
- Validar Markdown.
- Mostrar diferencias.
- Aplicar cambios al modelo.
- Regenerar Markdown desde el visor.
- Descartar cambios pendientes.
- Exportar SQL.

### 9.2 Comunicación con el Webview

El Extension Host administra:

- Lectura y escritura de archivos.
- Renombrado.
- Detección de cambios.
- Mensajes con el Webview.

El Webview administra:

- Canvas.
- Paneles.
- Selección.
- Edición visual.

Los mensajes deben utilizar tipos compartidos y devolver resultados explícitos de éxito o error.

## 10. Renombrado de proyectos

La extensión debe separar:

- Nombre visible del proyecto.
- Nombre del archivo.

Ejemplo:

```text
Nombre del proyecto: Sistema de usuarios
Nombre del archivo: users-schema.markdb
```

Al cambiar el nombre del archivo, la extensión debe:

1. Validar el nombre.
2. Comprobar que no exista un archivo destino.
3. Confirmar cambios pendientes.
4. Renombrar el archivo `.markdb`.
5. Renombrar el Markdown asociado.
6. Actualizar la pestaña y las referencias abiertas.

`STRUCTURE.md` conserva su nombre.

## 11. Persistencia

La aplicación web utilizará IndexedDB mediante un adaptador.

La extensión utilizará el sistema de archivos del workspace mediante otro adaptador.

Ambos deben cumplir una interfaz equivalente:

```ts
interface ProjectStorage {
  saveProject(project: DatabaseProject): Promise<void>;
  getProject(id: string): Promise<DatabaseProject | null>;
  deleteProject(id: string): Promise<void>;
  getAllProjects(): Promise<DatabaseProject[]>;
}
```

El backend futuro podrá implementar un adaptador remoto sin modificar `schema-core`.

## 12. Versionado y migraciones

Cada archivo `.markdb` debe incluir `formatVersion`.

```text
Archivo v1
   ↓
Migración v1 → v2
   ↓
Modelo actual
```

Las migraciones deben:

- Ser deterministas.
- Validar la versión de entrada.
- Crear respaldo antes de modificar archivos existentes.
- Reportar formatos incompatibles.
- Evitar pérdida silenciosa de información.

## 13. Errores y validación

Las operaciones deben devolver errores explícitos para:

- Nombres duplicados.
- Referencias inexistentes.
- Columnas incompatibles.
- Markdown inválido.
- Formato `.markdb` corrupto.
- Fallos de lectura o escritura.
- Conflictos de archivos.
- Características SQL no soportadas.

No deben aplicarse cambios parciales cuando una validación falla.

## 14. Backend futuro

El backend Go no forma parte del MVP. Deberá integrarse mediante adaptadores y proporcionar posteriormente:

- Usuarios.
- Sesiones.
- Autenticación.
- Proyectos remotos.
- Sincronización.
- Permisos.
- Colaboración.

La incorporación del backend no debe modificar el modelo de dominio ni los generadores existentes.

## 15. Decisiones arquitectónicas

| Decisión | Resolución |
|---|---|
| Formato de proyecto | `.markdb` |
| Exportación SQL | DDL únicamente |
| Dialectos iniciales | PostgreSQL y MariaDB |
| Relación `N:M` | Tabla intermedia automática y editable |
| Edición Markdown | Bidireccional con aplicación manual |
| Archivo de instrucciones | `STRUCTURE.md` |
| Extensión VS Code | Parte del MVP |
| Canal de VS Code | Versiones estables |
| Persistencia inicial | IndexedDB y archivos locales |
| Backend | Fuera del MVP |
| Usuarios y sesiones | Fuera del MVP |
