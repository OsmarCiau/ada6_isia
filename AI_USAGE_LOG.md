# Registro de Uso de Inteligencia Artificial (AI Usage Log)

Bitácora de interacciones con el asistente de IA durante el desarrollo de la práctica ADA-06, registrando las decisiones humanas, la validación de resultados y las medidas de seguridad adoptadas.

---

## Entrada 1 — Configuración de MCP y Mínimo Privilegio
- **Etapa:** Configuración inicial del servidor MCP y credenciales.
- **Qué propuso o detectó la IA:** Identificó la necesidad de conectar el servidor oficial de GitHub MCP (`https://api.githubcopilot.com/mcp/`) y sugirió restringir los toolsets a `repos`, `issues` y `pull_requests`.
- **Decisión y verificación humana:** Generé un Fine-grained Personal Access Token en GitHub con alcance exclusivo al repositorio `sebastian-laines/SGP---Sistema-de-gestion-publicitaria` y permisos estrictos de solo lectura (`Contents: Read`, `Issues: Read`, `Pull requests: Read`). Decidí no incluir permisos de escritura ni usar un token clásico para cumplir con el principio de mínimo privilegio. La credencial se configuró fuera del control de versiones.

---

## Entrada 2 — Análisis del Repositorio Remoto
- **Etapa:** Exploración de arquitectura y contexto del repositorio.
- **Qué propuso o detectó la IA:** Mediante la herramienta `get_file_contents` del toolset `repos`, el agente inspeccionó la raíz del repositorio, identificando que el proyecto es una aplicación Next.js con SQLite para la Revista Playlist, con módulos en `src/lib/db/` y documentación en `docs/` y `SPEC.md`.
- **Decisión y verificación humana:** Verifiqué que la estructura leída correspondiera con la versión actual del proyecto en la rama `develop` y validé que el agente no intentara descargar dependencias pesadas de `node_modules` ni archivos innecesarios.

---

## Entrada 3 — Análisis de Issue y Pull Request
- **Etapa:** Inspección remota de Issue #30 y Pull Request #28.
- **Qué propuso o detectó la IA:** El agente ejecutó `issue_read` para obtener los requerimientos del ticket SGP-99 (persistencia del catálogo y fechas) y `pull_request_read` para revisar el diff y los archivos afectados del PR #28. La IA propuso la matriz de trazabilidad relacionando el Issue con las funciones en `src/lib/db/catalogo.ts` y las pruebas en Vitest y `node:test`.
- **Decisión y verificación humana:** Corroboré que la relación entre el Issue #30 y el PR #28 fuera exacta. Noté que el agente inicialmente cuestionaba la ausencia de componentes de UI en el PR, por lo que le aclaré que la interfaz gráfica correspondía a tickets posteriores (SGP-100 y SGP-101), según lo establecido en el contrato de arquitectura.

---

## Entrada 4 — Revisión de Permisos y Frontera de Seguridad
- **Etapa:** Comprobación del boundary de seguridad y análisis de riesgos.
- **Qué propuso o detectó la IA:** Analizó los vectores de ataque asociados a la integración con GitHub MCP: fuga de credenciales, inyecciones de prompt indirectas en textos de Issues/PRs y el funcionamiento del Lockdown Mode. Concluyó que las operaciones de mutación no estaban disponibles y no debían intentarse.
- **Decisión y verificación humana:** Aprobé mantener el agente en modo estrictamente de solo lectura durante todo el ejercicio, confirmando que la verdadera garantía de seguridad no dependía solo de las instrucciones del prompt, sino del token con permisos de solo lectura a nivel de GitHub API.
