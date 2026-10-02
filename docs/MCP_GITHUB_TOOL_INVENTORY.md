# Inventario de Herramientas GitHub MCP (Tool Inventory)

Este documento registra las capacidades provistas por el servidor oficial remoto de GitHub MCP en Antigravity para la práctica ADA-06, configurado bajo el principio de mínimo privilegio y modo de solo lectura (*read-only*).

---

## 1. Inventario de Capacidades

| Tool / Capacidad | Toolset | Read/Write | Uso en el ADA | Nivel de Riesgo |
| :--- | :--- | :--- | :--- | :--- |
| `get_file_contents` | `repos` | Read | Inspeccionar archivos del repositorio, árbol de directorios, especificaciones (`SPEC.md`) y código fuente. | Bajo |
| `search_code` / `search_repositories` | `repos` | Read | Localizar archivos clave, módulos de persistencia y pruebas unitarias dentro del repositorio. | Bajo |
| `issue_read` | `issues` | Read | Consultar los detalles del Issue #30 (`[SGP-99]`) para extraer requerimientos y criterios de aceptación. | Medio (datos no confiables) |
| `list_issues` | `issues` | Read | Listar y verificar la existencia de Issues abiertos o cerrados en el repositorio sin alterar el backlog. | Medio (datos no confiables) |
| `pull_request_read` | `pull_requests` | Read | Inspeccionar metadata, archivos cambiados (`get_files`) y diff del PR #28 (`SGP-99`). | Medio (datos no confiables) |
| `list_pull_requests` | `pull_requests` | Read | Enumerar los PRs del proyecto para seleccionar el PR de análisis y contrastar ramas. | Medio (datos no confiables) |
| `get_me` | N/A | Read | Validar la identidad del usuario autenticado (`OsmarCiau`) y confirmar conectividad. | Bajo |
| *Herramientas de escritura (`issue_write`, `push_files`, `create_pull_request`, etc.)* | — | Write | **Bloqueadas / No utilizadas.** Ninguna operación de mutación fue permitida en esta sesión. | Alto si estuvieran habilitadas |

---

## 2. Política de Reducción de Capacidades

1. **Toolsets delimitados:** Se limitó el alcance a tres toolsets esenciales de ingeniería de software: `repos`, `issues` y `pull_requests`. Se excluyen capacidades no requeridas como `actions`, `security` o `administration`.
2. **Modo Read-Only estricto:** La regla del laboratorio prohíbe cualquier mutación sobre GitHub. El servidor y el agente operan exclusivamente como consumidores de contexto, eliminando riesgos de modificaciones accidentales al código base o a los tickets del equipo.
3. **Manejo de contenido no confiable:** Los Issues y Pull Requests pueden contener texto arbitrario ingresado por terceros (potencial *prompt injection* indirecto). Por ello, su lectura se clasifica con riesgo medio y su contenido se trata como datos a analizar, nunca como instrucciones ejecutables.
