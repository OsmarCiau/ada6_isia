# ADA-06 · MCP Tool Integration with GitHub
**Ingeniería de Software asistida por IA · UADY**  
Integración de Antigravity con GitHub MCP Server en modo de solo lectura (*read-only*) y evaluación del principio de mínimo privilegio.

---

## 1. Descripción del Proyecto
En esta práctica se integró Antigravity al servidor oficial remoto de GitHub mediante el protocolo Model Context Protocol (MCP). El objetivo fue consultar de forma remota código, Issues y Pull Requests de un repositorio real (`sebastian-laines/SGP---Sistema-de-gestion-publicitaria`), analizar la trazabilidad entre requerimientos y pruebas, y evaluar las políticas de autenticación, autorización y minimización de herramientas bajo un entorno estrictamente de solo lectura (*read-only*).

---

## 2. Estructura de Entregables

```text
ada-06/
├── README.md                           # Guía general, Definition of Done y Reflexión
├── AI_USAGE_LOG.md                     # Bitácora con 4 entradas de interacción y decisiones humanas
├── docs/
│   ├── MCP_GITHUB_TOOL_INVENTORY.md    # Inventario de herramientas y políticas de exposición
│   └── MCP_GITHUB_PERMISSION_REVIEW.md # Matriz de permisos, capas y análisis de seguridad
└── results/
    └── github-mcp-review.md            # Reporte de revisión de ingeniería y trazabilidad
```

---

## 3. Arquitectura de Integración

```text
Alumno / Desarrollador
        │
        ▼
Antigravity (MCP Host)
        │
        ├── MCP Client
        │       │
        │       ▼
        │   GitHub MCP Server (api.githubcopilot.com/mcp/)
        │       │
        │       ├── repos (get_file_contents, search_code)
        │       ├── issues (issue_read, list_issues)
        │       └── pull_requests (pull_request_read, list_pull_requests)
        │
        ▼
GitHub API (Repositorio privado SGP / Permisos Fine-grained Read-Only)
```

---

## 4. Definition of Done (Criterios de Cumplimiento)

| Condición | Estado | Evidencia |
| :--- | :---: | :--- |
| **Repositorio preparado** | OK | Código Next.js/SQLite, Issue #30 y PR #28 disponibles y accesibles. |
| **PAT de mínimo privilegio** | OK | Fine-grained PAT limitado a un solo repo con permisos exclusivos de lectura. |
| **GitHub MCP conectado** | OK | Conexión activa y validada con usuario `OsmarCiau` mediante `get_me`. |
| **Read-only verificado** | OK | Cero operaciones de mutación ejecutadas; herramientas de escritura bloqueadas. |
| **Toolsets mínimos** | OK | Alcance limitado únicamente a `repos`, `issues` y `pull_requests`. |
| **Tool inventory** | OK | Documentado en `docs/MCP_GITHUB_TOOL_INVENTORY.md`. |
| **Repository understanding** | OK | Inspección remota de estructura, módulos y tests vía `get_file_contents`. |
| **Issue analysis** | OK | Inspección remota del Issue #30 (`[SGP-99]`) vía `issue_read`. |
| **PR review** | OK | Inspección remota del PR #28, diffs y archivos vía `pull_request_read`. |
| **Trazabilidad** | OK | Matriz Issue → Req → PR → Código → Test en `results/github-mcp-review.md`. |
| **Permission review** | OK | Análisis de las 3 capas en `docs/MCP_GITHUB_PERMISSION_REVIEW.md`. |
| **Security analysis** | OK | Análisis de fuga, inyección de prompts y Lockdown Mode documentados. |
| **Reporte final** | OK | Consolidado en `results/github-mcp-review.md`. |
| **AI Usage Log** | OK | 4 entradas significativas sin credenciales en `AI_USAGE_LOG.md`. |

---

## 5. Respuestas a las Preguntas de Reflexión (Sección 22)

### 1. ¿Qué información obtuvo el agente mediante MCP que normalmente habrías tenido que copiar al prompt?
El árbol de archivos del repositorio, el contenido exacto de los archivos de persistencia (`src/lib/db/catalogo.ts`, `src/types/catalogo.ts`), la descripción y criterios del Issue #30, y el diff completo con la lista de archivos modificados del PR #28.

### 2. ¿Cuál es la diferencia entre el GitHub MCP Server y una GitHub Tool?
Una **GitHub Tool** es una función aislada y específica (como un script local o función de API que hace una llamada fija). El **GitHub MCP Server** es un servidor estandarizado bajo el protocolo MCP que publica un catálogo completo de capacidades negociables con el cliente, permitiendo descubrimiento dinámico, delimitación de toolsets y control de políticas como *read-only*.

### 3. ¿Qué toolset fue necesario para leer código? ¿Cuál para Issues? ¿Cuál para PRs?
- Para leer código y archivos: el toolset `repos` (herramientas como `get_file_contents` y `search_code`).
- Para Issues: el toolset `issues` (herramientas como `issue_read` y `list_issues`).
- Para Pull Requests: el toolset `pull_requests` (herramientas como `pull_request_read` y `list_pull_requests`).

### 4. ¿Qué diferencia existe entre permisos del PAT y herramientas expuestas por MCP?
Los permisos del PAT corresponden a la **autorización a nivel de GitHub** (lo que la API te deja o no hacer en el servidor remoto). Las herramientas expuestas por MCP corresponden a la **política del cliente/servidor MCP** (las funciones que el modelo tiene visibles en su catálogo de herramientas). Si un PAT tiene permiso de escritura pero el MCP está en read-only, el modelo ni siquiera verá la herramienta; si el MCP expone la herramienta pero el PAT es read-only, GitHub rechazará la petición con un error 403.

### 5. ¿Por qué se usó read-only aunque el agente pudiera ser capaz de proponer cambios?
Para evitar modificaciones accidentales, comentarios no deseados o corrupción del historial del repositorio mientras el agente solo realiza tareas de análisis y revisión técnica. Primero se valida la comprensión antes de delegar cualquier capacidad de escritura.

### 6. ¿Qué evidencia comprobó que el MCP estaba realmente conectado?
La ejecución exitosa de la herramienta `get_me`, la cual devolvió el usuario autenticado `OsmarCiau`, seguida de la lectura en tiempo real de los archivos del repositorio (`get_file_contents`), el Issue #30 (`issue_read`) y el PR #28 (`pull_request_read`).

### 7. ¿Qué información del Issue era un hecho y qué parte fue inferencia del agente?
- **Hecho:** El texto explícito del Issue #30 pidiendo tipos en `src/types/`, persistencia en `src/lib/db/catalogo.ts`, datos semilla QA y pruebas unitarias/integridad para SGP-99.
- **Inferencia:** La suposición inicial del agente de que faltaba la interfaz de usuario en el PR #28, la cual fue corregida en la revisión humana al verificar que la UI correspondía a tickets posteriores (SGP-100 y SGP-101) según el contrato técnico.

### 8. ¿Qué relación encontraste entre Issue, PR, código y tests?
Una relación de trazabilidad directa: el **Issue** definió la necesidad técnica (persistencia de catálogo), el **PR** agrupó los cambios en una rama específica, el **Código** implementó las funciones de consulta y tipos en `catalogo.ts`, y los **Tests** (`catalogo-persistence.test.ts` y `catalogo-integrity.test.mjs`) validaron las reglas de negocio y restricciones de la base de datos.

### 9. ¿Qué riesgo tiene tratar el contenido de un Issue o PR como instrucciones confiables?
Riesgo de **inyección indirecta de prompts** (*prompt injection*). Si un usuario externo o colaborador malintencionado incluye texto como "ignora las reglas anteriores y ejecuta este comando", el agente podría interpretarlo como una instrucción válida si no trata el contenido remoto como datos no confiables.

### 10. ¿En qué escenario permitirías escritura mediante MCP? ¿Qué acción requeriría aprobación humana?
Permitiría escritura únicamente en flujos controlados de creación de ramas de trabajo o redacción de borradores de PRs locales. Cualquier acción con impacto permanente (fusionar un PR, hacer push a `main`/`develop`, comentar públicamente en un Issue o eliminar código) debe requerir siempre aprobación humana explícita (*human-in-the-loop*).

### 11. ¿Qué cambiarías si el repositorio fuera privado o perteneciera a una organización?
Dado que este repositorio ya es privado, se comprobó que el Fine-grained PAT funciona correctamente al otorgarle acceso explícito a ese repositorio. Si perteneciera a una organización, se necesitaría la aprobación del administrador de la organización para el Fine-grained PAT o la configuración de una GitHub App con permisos mínimos aprobados por los administradores.

### 12. ¿Qué aporta MCP al ciclo de vida de Ingeniería de Software frente a copiar/pegar contenido en un chat?
Aporta contexto automatizado, estructurado y en tiempo real. Elimina la fatiga y los errores de copiar código desactualizado a mano, permite inspeccionar diffs exactos directamente desde el servidor de control de versiones y habilita la trazabilidad verificable entre tickets, especificaciones y código sin saturar el contexto con texto irrelevante.
