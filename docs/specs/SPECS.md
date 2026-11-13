# MarkDB
# Especificaciones Técnicas y Funcionales
## Database Schema Designer

**Versión:** 1.2  
**Estado:** Especificación base para desarrollo  
**Tipo de producto:** Aplicación de diseño visual y documentación de esquemas de bases de datos  
**Framework web:** Next.js  
**Lenguaje principal:** TypeScript  
**Arquitectura:** Monorepo modular  
**Plataformas previstas:** Aplicación web y extensión para Visual Studio Code

---

# 1. Descripción general

MarkDB es una herramienta orientada a desarrolladores que permite diseñar, modelar y documentar esquemas de bases de datos relacionales mediante una interfaz visual interactiva.

La aplicación permitirá crear tablas, definir columnas, configurar tipos de datos, establecer claves primarias y foráneas, administrar restricciones y representar gráficamente las relaciones entre entidades.

El sistema contará con documentación Markdown editable y sincronizada con el esquema diseñado. Los cambios realizados desde el editor visual actualizarán el Markdown, mientras que los cambios realizados en Markdown deberán ser leídos, validados y aplicados explícitamente al modelo.

MarkDB se desarrollará inicialmente como una aplicación web utilizando Next.js y una extensión para Visual Studio Code. Su arquitectura estará organizada como un monorepo para compartir el núcleo de modelado, la generación documental y la exportación SQL. El backend independiente desarrollado en Go se incorporará posteriormente.

El producto no incluirá integración directa con inteligencia artificial en su versión inicial.

# 2. Objetivos

## 2.1 Objetivo general

Desarrollar una aplicación que permita diseñar visualmente esquemas de bases de datos y generar documentación técnica sincronizada en Markdown, facilitando la organización, comprensión y reutilización de la estructura de datos de proyectos de software.

## 2.2 Objetivos específicos

- Facilitar la creación visual de tablas y columnas.
- Permitir la definición de claves primarias y foráneas.
- Representar gráficamente las relaciones entre tablas.
- Administrar restricciones y propiedades de las columnas.
- Generar documentación Markdown automáticamente.
- Mantener sincronizados el esquema visual y la documentación.
- Permitir la persistencia y recuperación de proyectos.
- Exportar los esquemas en formatos reutilizables.
- Establecer una arquitectura modular y escalable.
- Facilitar la integración con Visual Studio Code desde el MVP.
- Preparar el sistema para incorporar un backend independiente.

# 3. Alcance del producto

## 3.1 Alcance del MVP

La primera versión de MarkDB incluirá la aplicación web y una extensión para Visual Studio Code.

Incluye:

- Administración de proyectos.
- Editor visual de esquemas.
- Creación y edición de tablas.
- Creación y edición de columnas.
- Configuración de tipos de datos.
- Definición de claves primarias.
- Definición de claves foráneas.
- Creación de relaciones entre tablas.
- Configuración de restricciones.
- Canvas interactivo.
- Panel de propiedades.
- Generación de Markdown en tiempo real.
- Vista previa de Markdown.
- Copia y exportación de documentación.
- Persistencia local.
- Importación y exportación de proyectos `.markdb`.
- Archivos de proyecto con extensión `.markdb`.
- Archivo `STRUCTURE.md` con las reglas del formato Markdown.
- Extensión para Visual Studio Code para abrir y editar proyectos.
- Exportación SQL para PostgreSQL y MariaDB.
- Validaciones del esquema.

## 3.2 Funcionalidades futuras

Se contemplan para versiones posteriores:

- Backend desarrollado en Go.
- Base de datos PostgreSQL para almacenamiento remoto.
- Autenticación y administración de usuarios.
- Sincronización en la nube.
- Colaboración en tiempo real.
- Compartir proyectos.
- Importación de esquemas desde bases de datos existentes.
- Sincronización con repositorios Git.
- Integración con herramientas de inteligencia artificial.

# 4. Arquitectura del proyecto

## 4.1 Tipo de arquitectura

MarkDB utilizará una arquitectura de monorepo modular mediante pnpm Workspaces.

La raíz del repositorio contendrá las aplicaciones independientes y los paquetes compartidos.

Cada aplicación tendrá su propia configuración y ciclo de desarrollo, mientras que los paquetes compartidos centralizarán la lógica reutilizable.

## 4.2 Estructura del repositorio

```text
MarkDB/
│
├── frontend/                         # Aplicación web Next.js
│   ├── public/
│   ├── src/
│   │   ├── app/                      # Next.js App Router
│   │   ├── components/                # Componentes compartidos de UI
│   │   ├── features/                  # Funcionalidades del producto
│   │   ├── hooks/
│   │   ├── lib/
│   │   ├── store/                     # Zustand
│   │   ├── services/                  # Servicios de aplicación
│   │   ├── types/
│   │   └── utils/
│   │
│   ├── package.json
│   ├── next.config.ts
│   └── tsconfig.json
│
├── vscode-extension/                  # Extensión VS Code del MVP
│   ├── src/
│   │   ├── extension.ts
│   │   ├── commands/
│   │   ├── providers/
│   │   └── webview/
│   ├── package.json
│   └── tsconfig.json
│
├── packages/                          # Paquetes compartidos
│   ├── schema-core/
│   │   ├── src/
│   │   │   ├── models/
│   │   │   ├── operations/
│   │   │   ├── validators/
│   │   │   ├── relationship-engine/
│   │   │   └── index.ts
│   │   └── package.json
│   │
│   ├── markdown-engine/
│   │   ├── src/
│   │   │   ├── generators/
│   │   │   ├── templates/
│   │   │   └── index.ts
│   │   └── package.json
│   │
│   ├── markdown-parser/
│   │   ├── src/
│   │   └── package.json
│   │
│   ├── sql-generator/
│   │   ├── src/
│   │   │   ├── generators/
│   │   │   │   ├── postgresql.ts
│   │   │   │   └── mariadb.ts
│   │   │   └── index.ts
│   │   └── package.json
│   │
│   ├── shared-types/
│   │   ├── src/
│   │   └── package.json
│   │
│   └── ui/                            # Componentes compartidos
│       ├── src/
│       └── package.json
│
├── backend/                            # Backend Go futuro
│   ├── cmd/
│   ├── internal/
│   ├── migrations/
│   ├── api/
│   └── go.mod
│
├── docs/
│   ├── specs/
│   └── architecture/
│
├── package.json                       # Configuración raíz
├── pnpm-workspace.yaml
├── turbo.json
├── .gitignore
└── README.md
```

La estructura anterior representa la arquitectura objetivo del proyecto. Las carpetas correspondientes a funcionalidades futuras podrán incorporarse cuando sean necesarias.

## 4.3 Responsabilidades de los módulos

| Módulo | Responsabilidad |
|---|---|
| `frontend/` | Interfaz web y experiencia de usuario |
| `vscode-extension/` | Integración con Visual Studio Code del MVP |
| `packages/schema-core/` | Lógica central de modelado |
| `packages/markdown-engine/` | Generación de documentación |
| `packages/markdown-parser/` | Lectura de Markdown compatible con MarkDB |
| `packages/sql-generator/` | Generación de SQL por dialecto |
| `packages/shared-types/` | Tipos e interfaces compartidos |
| `packages/ui/` | Componentes reutilizables entre aplicaciones |
| `backend/` | API, autenticación y persistencia remota futura |

El frontend y la extensión deberán utilizar el mismo núcleo de modelado para evitar duplicación de lógica.

# 5. Tecnologías

## 5.1 Aplicación web

| Tecnología | Función |
|---|---|
| Next.js | Framework web con App Router |
| React | Construcción de interfaces |
| TypeScript | Lenguaje de desarrollo |
| Tailwind CSS | Estilización |
| React Flow | Editor visual de diagramas |
| Zustand | Gestión del estado global |
| CodeMirror 6 | Editor de Markdown |
| React Markdown | Renderizado de Markdown |
| IndexedDB | Persistencia local |
| Vite | No será el entorno principal de la aplicación Next.js |

## 5.2 Monorepo

| Tecnología | Función |
|---|---|
| pnpm Workspaces | Administración del monorepo |
| Turborepo | Orquestación y optimización de tareas |
| TypeScript | Desarrollo de paquetes compartidos |

## 5.3 Backend futuro

| Tecnología | Función |
|---|---|
| Go | Desarrollo de la API |
| PostgreSQL | Base de datos de almacenamiento remoto |
| REST API | Comunicación entre frontend y backend |
| Autenticación segura | Gestión futura de usuarios y sesiones |

# 6. Requerimientos funcionales

## 6.1 Administración de proyectos

**FR-001. Crear proyecto**

El sistema deberá permitir crear un proyecto de esquema de base de datos.

**FR-002. Editar proyecto**

El usuario podrá modificar el nombre y la descripción del proyecto.

**FR-003. Guardar proyecto**

El sistema deberá permitir guardar los cambios realizados.

**FR-004. Recuperar proyecto**

El usuario podrá abrir proyectos previamente almacenados.

**FR-005. Eliminar proyecto**

El sistema deberá permitir eliminar proyectos con confirmación.

**FR-006. Exportar proyecto**

El sistema deberá permitir exportar un proyecto completo como archivo `.markdb`.

**FR-007. Importar proyecto**

El sistema deberá permitir importar archivos `.markdb` previamente exportados, validando su estructura y versión de formato.

## 6.2 Administración de tablas

**FR-008. Crear tabla**

El usuario podrá crear una tabla mediante el editor visual.

**FR-009. Editar tabla**

El usuario podrá modificar el nombre y la descripción de una tabla.

**FR-010. Eliminar tabla**

El sistema deberá permitir eliminar tablas y advertir sobre relaciones asociadas.

**FR-011. Posicionar tabla**

El usuario podrá mover las tablas libremente dentro del canvas.

**FR-012. Administrar columnas**

Cada tabla deberá permitir agregar, editar, eliminar y reordenar columnas.

## 6.3 Administración de columnas

Cada columna deberá contener:

| Propiedad | Descripción |
|---|---|
| ID | Identificador interno único |
| Nombre | Nombre de la columna |
| Tipo de dato | Tipo de dato asignado |
| Primary Key | Indica si pertenece a la clave primaria |
| Nullable | Permite valores NULL |
| Unique | Restringe valores duplicados |
| Auto Increment | Incremento automático |
| Default | Valor predeterminado |
| Check | Restricción de validación |
| Description | Descripción técnica |

## 6.4 Tipos de datos

El sistema deberá contemplar inicialmente:

**Numéricos**
- INT
- BIGINT
- SMALLINT
- DECIMAL
- NUMERIC
- FLOAT
- DOUBLE

**Texto**
- VARCHAR
- CHAR
- TEXT

**Fecha y hora**
- DATE
- TIME
- DATETIME
- TIMESTAMP

**Otros**
- BOOLEAN
- UUID
- JSON
- JSONB
- BLOB

Los tipos deberán poder adaptarse al motor de base de datos seleccionado en futuras versiones.

## 6.5 Claves primarias

El usuario podrá:

- Definir una columna como clave primaria.
- Definir claves primarias compuestas.
- Visualizar las claves primarias en el canvas.
- Consultar las claves primarias en el Markdown generado.

## 6.6 Claves foráneas

El usuario podrá:

- Seleccionar una columna de origen.
- Seleccionar una columna de destino.
- Definir la referencia.
- Configurar acciones referenciales en futuras ampliaciones.

Las claves foráneas deberán estar asociadas a relaciones válidas.

## 6.7 Relaciones entre tablas

El sistema deberá soportar:

- Uno a uno (1:1).
- Uno a muchos (1:N).
- Muchos a muchos (N:M).

Cada relación deberá contener:

- Identificador único.
- Tabla de origen.
- Columna de origen.
- Tabla de destino.
- Columna de destino.
- Cardinalidad.
- Nombre opcional.

Las relaciones deberán visualizarse mediante conexiones gráficas en el canvas.

Cuando el usuario seleccione una relación muchos a muchos, el sistema deberá crear automáticamente una tabla intermedia. La tabla intermedia formará parte del modelo y podrá modificarse inmediatamente, incluyendo su nombre, columnas, restricciones y relaciones.

## 6.8 Canvas de diseño

El canvas deberá permitir:

- Arrastrar tablas.
- Crear conexiones.
- Seleccionar tablas.
- Seleccionar relaciones.
- Zoom.
- Desplazamiento del área de trabajo.
- Eliminar elementos.
- Ajustar conexiones.
- Visualizar propiedades.
- Organizar tablas mediante acciones de distribución futuras.

El posicionamiento visual no deberá alterar la estructura semántica del esquema.

## 6.9 Panel de propiedades

El panel de propiedades deberá permitir modificar las características del elemento seleccionado.

Podrá mostrar:

- Propiedades de tabla.
- Propiedades de columna.
- Propiedades de relación.

El panel deberá actualizarse según la selección del usuario.

## 6.10 Generación de Markdown

**FR-013. Generación automática**

El sistema deberá generar documentación Markdown a partir del modelo estructurado del proyecto.

**FR-014. Sincronización desde el editor visual**

El Markdown deberá actualizarse al modificar tablas, columnas, relaciones o restricciones desde el editor visual.

**FR-015. Lectura de Markdown**

El sistema deberá permitir analizar un archivo Markdown compatible con MarkDB y generar una propuesta de cambios sobre el modelo estructurado.

**FR-016. Validación de Markdown**

El sistema deberá validar la propuesta de cambios antes de aplicarla al modelo.

**FR-017. Aplicación de cambios desde Markdown**

El usuario deberá confirmar explícitamente la aplicación de cambios válidos desde Markdown. Los cambios inválidos no deberán modificar el modelo ni el canvas.

**FR-018. Visualización y copia**

El sistema deberá permitir visualizar y copiar el contenido Markdown.

**FR-019. Exportar documentación**

El sistema deberá permitir descargar el Markdown como archivo `.md`.

La documentación deberá incluir:

- Nombre del proyecto.
- Descripción del esquema.
- Tablas.
- Columnas.
- Tipos de datos.
- Claves primarias.
- Claves foráneas.
- Restricciones.
- Relaciones.
- Descripciones y reglas de negocio documentadas.

El Markdown será editable, pero sus cambios deberán validarse y aplicarse explícitamente. El modelo estructurado aplicado seguirá siendo la fuente de verdad.

El formato Markdown compatible con MarkDB deberá documentarse en un archivo `STRUCTURE.md`, que explicará la sintaxis de tablas, columnas, claves, relaciones, restricciones y ejemplos válidos.

## 6.11 Exportación

**FR-020. Exportación PostgreSQL**

El sistema deberá generar un archivo SQL con la definición del esquema compatible con PostgreSQL.

**FR-021. Exportación MariaDB**

El sistema deberá generar un archivo SQL con la definición del esquema compatible con MariaDB.

El MVP deberá soportar:

- Markdown (`.md`).
- Proyecto MarkDB (`.markdb`, con contenido estructurado en JSON).
- SQL compatible con PostgreSQL (`.sql`).
- SQL compatible con MariaDB (`.sql`).

La exportación SQL generará archivos de definición del esquema. El MVP no ejecutará directamente comandos contra una base de datos.

# 7. Requerimientos no funcionales

## 7.1 Rendimiento

- Las operaciones principales deberán responder sin bloqueos perceptibles.
- La generación Markdown deberá ser prácticamente inmediata.
- El sistema tendrá como objetivo inicial soportar esquemas de hasta 100 tablas.
- Se deberán evitar actualizaciones innecesarias del estado global.

## 7.2 Usabilidad

- La interfaz deberá ser intuitiva.
- Las acciones principales deberán ser accesibles visualmente.
- Los paneles deberán ser ajustables.
- Los elementos seleccionados deberán contar con indicadores visuales.
- La interfaz deberá mantener consistencia entre sus componentes.

## 7.3 Persistencia

- El MVP utilizará IndexedDB.
- El almacenamiento deberá estar abstraído mediante una interfaz.
- Se deberá permitir sustituir la persistencia local por una API remota.

## 7.4 Mantenibilidad

- El código deberá utilizar TypeScript.
- La lógica del esquema deberá estar separada de la interfaz.
- La generación Markdown deberá estar desacoplada del frontend.
- Se deberán implementar pruebas unitarias.
- Los paquetes compartidos deberán tener límites de responsabilidad claros.

## 7.5 Compatibilidad

La aplicación web deberá ser compatible con navegadores modernos de escritorio.

La extensión deberá ser compatible con versiones soportadas de Visual Studio Code.

# 8. Modelo de datos

## 8.1 DatabaseProject

```ts
interface DatabaseProject {
  id: string;
  formatVersion: number;
  name: string;
  description: string;
  databaseEngine: DatabaseEngine;
  tables: DatabaseTable[];
  relationships: Relationship[];
  metadata: ProjectMetadata;
}
```

## 8.2 DatabaseTable

```ts
interface DatabaseTable {
  id: string;
  name: string;
  description?: string;
  columns: DatabaseColumn[];
  position: {
    x: number;
    y: number;
  };
}
```

## 8.3 DatabaseColumn

```ts
interface DatabaseColumn {
  id: string;
  name: string;
  dataType: string;
  primaryKey: boolean;
  nullable: boolean;
  unique: boolean;
  autoIncrement: boolean;
  defaultValue?: string;
  description?: string;
}
```

## 8.4 Relationship

```ts
interface Relationship {
  id: string;
  sourceTableId: string;
  sourceColumnId: string;
  targetTableId: string;
  targetColumnId: string;
  cardinality: "one-to-one" | "one-to-many" | "many-to-many";
  name?: string;
}
```

## 8.5 ProjectMetadata

```ts
interface ProjectMetadata {
  createdAt: string;
  updatedAt: string;
  version: string;
}
```

Los modelos anteriores representan una base inicial. Deberán ampliarse conforme se incorporen índices, restricciones avanzadas, esquemas y acciones referenciales.

# 9. Diseño de interfaz

## 9.1 Dashboard

El dashboard deberá permitir:

- Visualizar proyectos existentes.
- Crear proyectos.
- Abrir proyectos.
- Eliminar proyectos.
- Consultar información básica de cada proyecto.

## 9.2 Editor de esquemas

El editor deberá dividirse en tres áreas principales.

### Panel izquierdo

- Herramientas de creación.
- Lista de tablas.
- Acciones de importación y exportación.
- Navegación del proyecto.

### Canvas central

- Tablas visuales.
- Columnas.
- Relaciones.
- Controles de zoom.
- Selección de elementos.
- Área de diseño.

### Panel derecho

Modos principales:

**Propiedades**
- Edición de tablas.
- Edición de columnas.
- Configuración de relaciones.

**Documentación**
- Markdown generado.
- Vista previa.
- Copiar contenido.
- Exportar archivo.

# 10. Gestión del estado

La aplicación web utilizará Zustand para administrar el estado global.

Se recomienda separar el estado en:

- `schema-store`: tablas, columnas y relaciones.
- `project-store`: información del proyecto y persistencia.
- `editor-store`: selección, paneles, zoom y estado visual.

React Flow podrá administrar los aspectos propios del canvas, mientras que el modelo semántico del esquema será gestionado por el núcleo de MarkDB.

Se deberá evitar duplicar innecesariamente el estado de las tablas y relaciones entre Zustand y React Flow.

# 11. Persistencia y almacenamiento

## 11.1 MVP

La aplicación web utilizará IndexedDB para almacenar proyectos localmente. La extensión utilizará el sistema de archivos del workspace para leer y guardar archivos `.markdb` y Markdown.

Se deberá crear una abstracción que permita reemplazar la implementación local en el futuro.

```ts
interface ProjectStorage {
  saveProject(project: DatabaseProject): Promise<void>;

  getProject(id: string): Promise<DatabaseProject | null>;

  deleteProject(id: string): Promise<void>;

  getAllProjects(): Promise<DatabaseProject[]>;
}
```

La implementación inicial será `IndexedDBProjectStorage`. La extensión deberá utilizar un adaptador equivalente para archivos del workspace.

## 11.2 Backend futuro

Posteriormente se incorporará una implementación `ApiProjectStorage`, encargada de comunicarse con el backend Go.

El frontend no deberá depender directamente de la implementación concreta de almacenamiento.

# 12. Extensión para Visual Studio Code

La extensión será una aplicación independiente ubicada en `vscode-extension/` y formará parte del MVP.

## 12.1 Funcionalidades previstas

- Abrir archivos `.markdb` desde el workspace.
- Visualizar el editor de MarkDB dentro de VS Code.
- Crear y modificar tablas, columnas y relaciones.
- Generar y actualizar el archivo Markdown asociado.
- Detectar cambios externos en Markdown.
- Leer y validar cambios de Markdown.
- Mostrar diferencias antes de aplicar cambios.
- Aplicar cambios de Markdown mediante confirmación explícita.
- Exportar SQL para PostgreSQL y MariaDB.
- Guardar cambios en el archivo `.markdb`.
- Permitir cambiar el nombre del archivo del proyecto y su Markdown asociado.
- Mantener `STRUCTURE.md` como archivo de reglas del formato.
- Utilizar los paquetes compartidos del monorepo.

## 12.2 Arquitectura

La extensión utilizará VS Code Extension API.

La interfaz gráfica podrá implementarse mediante una Webview con React.

Los paquetes `schema-core`, `markdown-engine`, `markdown-parser`, `sql-generator` y `shared-types` deberán poder reutilizarse en la extensión sin depender de Next.js.

# 13. Backend futuro

El backend se desarrollará de forma independiente utilizando Go.

## 13.1 Responsabilidades

- Autenticación.
- Administración de usuarios.
- Administración de proyectos.
- Persistencia remota.
- Sincronización.
- Gestión de permisos.
- API REST.

## 13.2 Base de datos

Se utilizará PostgreSQL para almacenar:

- Usuarios.
- Proyectos.
- Esquemas.
- Metadatos.
- Permisos.

La incorporación del backend no deberá requerir modificar sustancialmente el núcleo del editor.

# 14. Reglas de negocio

- Cada proyecto deberá poseer un identificador único.
- Cada tabla deberá poseer un identificador único.
- Cada columna deberá poseer un identificador único.
- Los nombres deberán validarse.
- Las claves primarias deberán referenciar columnas existentes.
- Las claves foráneas deberán referenciar elementos válidos.
- No se permitirán relaciones hacia elementos inexistentes.
- Las relaciones muchos a muchos deberán crear una tabla intermedia editable.
- La eliminación de tablas deberá advertir sobre sus relaciones.
- El esquema deberá mantenerse consistente después de cada operación.
- Los cambios de Markdown no deberán aplicarse parcialmente si fallan las validaciones.
- La posición visual de una tabla no deberá modificar su estructura semántica.
- El Markdown deberá reflejar el modelo actualizado.
- La documentación no deberá generarse desde elementos visuales directamente, sino desde el modelo estructurado.

# 15. Criterios de aceptación del MVP

El MVP se considerará funcional cuando:

1. Se pueda crear un proyecto.
2. Se puedan crear y modificar tablas.
3. Se puedan agregar, editar, eliminar y reordenar columnas.
4. Se puedan definir tipos de datos.
5. Se puedan establecer claves primarias.
6. Se puedan crear relaciones entre tablas.
7. Las tablas puedan moverse en el canvas.
8. El Markdown se actualice al modificar el editor visual.
9. Se puedan leer, validar y aplicar cambios de Markdown mediante confirmación explícita.
10. Se pueda copiar y exportar la documentación.
11. Se puedan guardar y recuperar proyectos localmente.
12. Se puedan exportar e importar proyectos `.markdb`.
13. Se ejecuten validaciones sobre el esquema.
14. Las relaciones `N:M` creen tablas intermedias editables.
15. La extensión de VS Code permita abrir, editar y guardar proyectos MarkDB.
16. Se puedan exportar esquemas SQL compatibles con PostgreSQL.
17. Se puedan exportar esquemas SQL compatibles con MariaDB.
18. Se pueda renombrar el archivo `.markdb` y su Markdown asociado.

# 16. Plan de desarrollo

## Fase 1 — Configuración inicial

- Crear repositorio MarkDB.
- Crear proyecto Next.js dentro de `frontend/`.
- Configurar TypeScript y Tailwind CSS.
- Configurar pnpm Workspaces.
- Definir la estructura modular.
- Crear los paquetes compartidos iniciales.
- Definir modelos del esquema.

## Fase 2 — Editor visual

- Implementar canvas con React Flow.
- Crear tablas.
- Crear columnas.
- Implementar selección y movimiento.
- Implementar panel de propiedades.
- Configurar Zustand.

## Fase 3 — Relaciones

- Implementar conexiones entre tablas.
- Configurar cardinalidades.
- Implementar claves foráneas.
- Crear validaciones.

## Fase 4 — Documentación

- Implementar generador Markdown.
- Definir el formato compatible de `STRUCTURE.md`.
- Implementar parser Markdown.
- Crear panel de documentación.
- Implementar vista previa.
- Implementar actualización desde el editor visual.
- Implementar lectura, validación y aplicación explícita de cambios Markdown.
- Implementar copia y exportación.

## Fase 5 — Persistencia

- Implementar IndexedDB.
- Crear servicio de almacenamiento.
- Implementar guardado automático.
- Implementar importación y exportación de proyectos `.markdb`.

## Fase 6 — Refinamiento

- Pruebas unitarias.
- Validaciones adicionales.
- Mejoras de UX.
- Optimización del canvas.
- Refinamiento de la arquitectura modular.

## Fase 7 — Extensión VS Code

- Crear proyecto de extensión.
- Implementar apertura y guardado de proyectos.
- Crear Webview.
- Integrar paquetes compartidos.
- Integrar generación y lectura de Markdown.
- Integrar generación SQL.
- Implementar renombrado de proyectos.

## Fase 8 — Backend

- Desarrollar API Go.
- Integrar PostgreSQL.
- Implementar autenticación.
- Incorporar almacenamiento remoto.

# 17. Consideraciones arquitectónicas

MarkDB deberá mantener una separación clara entre:

- Aplicación web.
- Extensión de VS Code.
- Núcleo de modelado.
- Generador Markdown.
- Parser Markdown.
- Generador SQL.
- Tipos compartidos.
- Persistencia.
- Backend.

El núcleo de modelado será independiente de React, Next.js y VS Code.

El generador Markdown deberá funcionar a partir del modelo estructurado. El parser Markdown deberá producir propuestas validadas y nunca modificar directamente el modelo aplicado.

La persistencia deberá implementarse mediante adaptadores.

El monorepo deberá facilitar la reutilización de código y la evolución independiente de las aplicaciones.

# 18. Resultado esperado

MarkDB deberá convertirse en una herramienta visual para el diseño, documentación y exportación de bases de datos relacionales.

La primera versión incluirá una aplicación web desarrollada con Next.js y una extensión para Visual Studio Code. Ambas permitirán modelar esquemas, administrar tablas y relaciones, generar documentación Markdown sincronizada y exportar archivos SQL compatibles con PostgreSQL y MariaDB.

La arquitectura monorepo permitirá compartir el núcleo de modelado entre las aplicaciones y evolucionar posteriormente hacia un backend en Go con PostgreSQL, sesiones de usuario y almacenamiento remoto.

El desarrollo inicial deberá priorizar la funcionalidad del editor, la consistencia del modelo de datos, la generación documental y la experiencia de usuario, manteniendo una base técnica preparada para futuras ampliaciones.
