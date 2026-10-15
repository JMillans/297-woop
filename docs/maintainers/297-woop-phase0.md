# 297 WOOP Auditoría de fase 0

15 de octubre de 2026 · Beta · [by Jmillans](https://jmillans.es/)

## Conclusión

OpenSEO es una base aprovechable para 297 WOOP. Ya aporta proyectos, memoria compartida, informes, rastreo, GSC, código de GA4 y herramientas MCP. Sus procedimientos editoriales priorizan intención, adecuación al negocio y evidencia. Debemos ampliar la trazabilidad del conocimiento, corregir algunas instrucciones y separar las capacidades por proveedor antes de emplearlo como sistema habitual.

La base técnica local está probada. El usuario ha confirmado que dispone de Metricool Advanced o Custom, un plan con acceso a la API. La fase 0 mantiene pendiente la comprobación de las cuentas externas, especialmente las capacidades efectivas de Metricool y la viabilidad del uso previsto de Keyword Planner. Ninguna fuente externa está conectada a este entorno de prueba.

## Base y evidencia de ejecución

- Original: `every-app/open-seo`, commit `d7ea7852480601af9bc8870942af8311a410e567`.
- Fork: [JMillans/297-woop](https://github.com/JMillans/297-woop), registrado por GitHub como fork del original.
- Versión original: 0.1.13. Node utilizado: 24.19.0. Gestor fijado: pnpm 10.30.1.
- Instalación con lockfile congelado; no se han actualizado dependencias.
- Docker no está instalado en el equipo comprobado. Se ha usado la vía de desarrollo documentada por upstream, con Vite y Workers locales.
- Aplicación: `http://127.0.0.1:3001`. Salud y MCP devuelven HTTP 200.
- MCP descubre 76 herramientas, incluidas nueve de Analytics. El registro también incorpora `get_search_opportunities`.
- Prueba MCP: crear dos proyectos sintéticos en mercado España/español, guardar contexto en A y comprobar que B permanece vacío.
- Reinicio y restauración: detener el servidor, archivar `.wrangler`, apartar su estado original, extraer el archivo y arrancar de nuevo. Los identificadores y los contextos coinciden con los anteriores. Diez archivos SQLite restaurados pasan `integrity_check`.
- Pruebas existentes: 259 aprobadas en cinco archivos sobre contexto, creación de proyectos, GA4 y paridad SQLite/Postgres. No representan una prueba completa de toda la aplicación ni de cuentas Google reales.
- La interfaz muestra el dashboard después de cerrar el aviso de DataForSEO. Conserva la identidad original; la personalización visual está prevista para fase 1.

Los resultados sintéticos y el archivo de restauración se conservan fuera del repositorio, en la carpeta de evidencias del proyecto. La configuración local utiliza `local_noauth`, telemetría desactivada y ninguna clave de proveedor. El servidor escucha exclusivamente en loopback.

## Arquitectura que reutilizamos

| Área | Implementación examinada | Decisión |
| --- | --- | --- |
| Aplicación | React, TanStack Start y Vite | Mantener la aplicación web local. |
| Persistencia | Drizzle con D1/SQLite y Postgres opcional, `src/db/index.ts` | Mantener SQLite local y compatibilidad de ambos esquemas. |
| Proyectos | `ProjectService`, herramientas `list_projects` y `create_project` | Reutilizar; las dos altas sintéticas y sus contextos se han probado por MCP. |
| Contexto | `project-context.schema.ts`, servicio y herramientas de contexto | Ampliar con confirmación, procedencia y versiones; el esquema actual guarda contenido vigente, autor y fecha. |
| Autorización | `withMcpProjectAuth` y repositorios con proyecto explícito | Conservar y extender a futuros conectores; la prueba A/B no sustituye una auditoría completa de permisos. |
| Integración con agentes | `src/server/mcp/server.ts` y 12 skills distribuidas | Reutilizar el protocolo y adaptar los procedimientos según fuentes disponibles. |
| GSC y GA4 | Servicios, OAuth, esquemas, herramientas y pantallas | Validar conexiones reales en fase 3. GA4 ya tiene implementación y herramientas registradas: no hace falta construirlo desde cero. |
| Datos SEO | `src/server/lib/dataforseo`, investigación, dominio, backlinks y posiciones | Mantener como proveedor opcional; introducir selección de capacidades en fase 2. |
| Programador | `scripts/selfhost-scheduler.mjs` | El modo Docker incluye un bucle de cinco minutos. El servidor Vite probado no equivale a una recogida programada activa. |
| Informes | Servicios de informes y plantillas | Reutilizar; añadir identidad y vínculos a evidencia, acciones y versiones. |

## Auditoría de conocimiento SEO y AEO

La evaluación se apoya en las instrucciones originales y en su implementación. No asigna una puntuación global: una instrucción útil, una herramienta disponible y un resultado verificado son evidencias distintas.

| Área | Qué encontramos | Ampliación o corrección para 297 WOOP |
| --- | --- | --- |
| Empresa y objetivos | `seo-project-setup` recoge oferta, audiencia, mercados, objetivos, diferenciación y preferencias; exige confirmar antes de guardar. | Reutilizar. Entrevista de una pregunta cada vez y campos que separen respuestas, documentos e inferencias. Fase 1. |
| Continuidad | Contexto, competidores, páginas y registro de investigación compartidos por proyecto. El registro de investigación se poda a 90 días. | Conservar evidencia duradera independiente del registro breve; añadir versiones de contexto y decisiones. Fases 1 y 2. |
| Intención | `keyword-research` prioriza adecuación al negocio e intención; `keyword-clustering` contrasta intención y resultados de búsqueda. | Mantener el criterio y permitir trabajar con datos propios cuando falten volumen o dificultad. Fases 2 y 5. |
| Contenido útil | `seo-audit` lee páginas de varias familias, contrasta necesidades y desaconseja cambios sin evidencia suficiente. | Añadir ficha previa y revisión posterior para posts, servicios y productos, preservando voz y experiencia real. Fases 5 y 6. |
| Rastreo e indexación | Auditoría con errores HTTP, robots, noindex, canonicals, redirecciones, duplicados y enlaces. | Clasificar cada URL por su propósito antes de convertir avisos en acciones; una página legal no necesita indexarse por defecto. Fase 5. |
| Arquitectura | Páginas clave hub/spoke/money, clustering, enlaces rotos, profundidad y páginas huérfanas. | Registrar cambios de arquitectura y comprobar navegación y enlaces tras implementarlos. Fases 5 y 6. |
| Canibalización | Clustering usa consultas compartidas entre URLs como señal y llega a describirla como confirmación. | Tratarla como indicio: revisar intención, periodo, alternancia de URLs y rendimiento antes de fusionar páginas. Fase 5. |
| Datos estructurados | El catálogo `src/shared/audit-issues.ts` examinado no ofrece una categoría específica de validación de datos estructurados. | Añadir revisión por tipo de página y documentación oficial. Detectar marcado no prueba validez ni elegibilidad. Fase 5. |
| Experiencia de uso | Persistencia de resultados Lighthouse y avisos técnicos. | Distinguir laboratorio y datos de uso real; incorporar Clarity, GA4 y pruebas de tareas. Fases 4 y 6. |
| Medición | GA4 separa fuente, privacidad, cuotas y errores; su combinación con GSC contempla diferencias de fechas y cobertura. | Verificar eventos útiles del proyecto; evitar tratar clics, sesiones e interacción social como la misma medida. Fases 3 y 4. |
| AEO y GEO | `ai-visibility-audit` exige respuestas observadas, fuentes leídas, muestras y distinción entre API y producto de consumo. Prompt Research declara US English. | Mantener límites de muestra y mercado; añadir investigación España/español y revisión por motor. Fase 5. |
| Distribución | Los procedimientos principales examinados se centran en búsqueda y visibilidad en IA. | Añadir Metricool, relación publicación/página/UTM y calidad de las visitas posteriores. Fases 4 y 5. |

### Correcciones prioritarias del conocimiento

1. **Google-Extended:** la skill `ai-visibility-audit`, apartado de acceso, agrupa su bloqueo con entrenamiento y dice que no afecta a respuestas en vivo. Google documenta también su control sobre grounding en Gemini Apps y Vertex AI. La corrección debe distinguir productos; Google Search tiene sus propios controles. [Documentación de Google](https://developers.google.com/crawling/docs/crawlers-fetchers/google-common-crawlers#google-extended).
2. **Causas de citas:** que una fuente aparezca en una búsqueda derivada y en una respuesta es evidencia de asociación. Las frases que presentan ese patrón como explicación causal deben formularse como hipótesis.
3. **Mercado:** `seo-audit` incluye columnas y ejemplos US. País, idioma, periodo y ubicación deben derivarse del proyecto y mantenerse en consultas e informes.
4. **Reglas técnicas:** avisos de longitud, número de H1 o poco texto requieren interpretación por tipo de página; no deben convertirse automáticamente en instrucciones de reescritura.
5. **Conocimiento con fuentes:** cada procedimiento adaptado tendrá referencias oficiales, fecha de revisión y alcance. La utilidad y fiabilidad del contenido y los requisitos de búsqueda siguen siendo la base; Google no exige un schema especial para sus funciones de IA. [Contenido útil](https://developers.google.com/search/docs/fundamentals/creating-helpful-content), [funciones de IA](https://developers.google.com/search/docs/appearance/ai-features).

Estas correcciones quedan identificadas para fase 5; aún no se han modificado las skills originales ni sus copias distribuidas.

## Dependencias y cuentas

| Servicio | Estado observado | Acción pendiente |
| --- | --- | --- |
| GitHub | Autenticación válida y fork creado. | Mantener `upstream` para actualizaciones y `origin` para nuestro fork. |
| DataForSEO | Ausente; salud devuelve advertencia y la interfaz muestra un modal que se puede cerrar. MCP de proyectos y contexto funciona. | Sustituir avisos generales por disponibilidad por función; los servicios de datos siguen acoplados al proveedor. |
| OpenRouter | Ausente; IA integrada desactivada. | Mantener el recorrido con agente externo sin exigir esta clave. |
| GSC y GA4 | Implementación presente; sin OAuth configurado ni propiedades conectadas. | Configurar proyecto Google, APIs, callbacks y secreto de cifrado; verificar una propiedad real. |
| Ads y Keyword Planner | No hay acceso de cuenta verificado. | Comprobar developer token y nivel de planificación. La nota upstream `0004-keyword-data-source-routing.md` rechaza Keyword Planner directo para su uso SaaS por política; esa interpretación debe contrastarse con las condiciones oficiales y nuestro uso local concreto. No se da por resuelta por disponer de Basic o Standard. |
| Semrush | Sin cuenta ni plan verificados en este entorno. | Confirmar funciones, acceso API/MCP y límites antes de asumir que una prueba los incluye. |
| Metricool | El usuario confirma Advanced o Custom el 15 de octubre de 2026; acceso efectivo, marcas y permisos aún sin probar. | Priorizar la API para sincronización e histórico; validar una lectura de marcas y métricas. Evaluar MCP para consultas del agente. [API](https://help.metricool.com/es/guia-basica-para-la-integracion-de-api-r97af), [MCP](https://metricool.com/metricool-mcp-claude/). |
| Clarity | Conector pendiente de construir. | Verificar proyecto y acceso; diseñar recogida histórica y límites en fase 4. |

## Arranque reproducible

Requisitos: Node 20 o posterior, pnpm 10.30.1. En este equipo se ha utilizado el Node incluido en el runtime de Codex; el Node del sistema no estaba en PATH. No se han instalado herramientas globales.

Desde el checkout del fork, con Node y pnpm accesibles:

```sh
pnpm install --frozen-lockfile
```

Crear `.env.local` con estos valores, sin claves de proveedores para la prueba del núcleo:

```dotenv
AUTH_MODE=local_noauth
OPENSEO_TELEMETRY_DISABLED=1
DO_NOT_TRACK=1
VITE_SHOW_DEVTOOLS=false
```

```sh
pnpm run db:migrate:local
pnpm exec vite --host 127.0.0.1 --port 3001
```

Consultar `/api/health` y `/mcp`. El aviso de DataForSEO se puede cerrar. Mantener el servicio en loopback: `local_noauth` es un modo de desarrollo de confianza.

Para una copia completa de este estado local, detener el servicio antes de archivar `.wrangler`; restaurarlo en un checkout de la misma versión, arrancar y comprobar proyectos y contexto. El archivo sintético de fase 0 no sustituye una política de copias para futuros proyectos reales y sus secretos de cifrado.

## Estado de los criterios de fase 0

| Criterio | Resultado |
| --- | --- |
| Arranque desde checkout del fork | Verificado por Vite, salud HTTP y dashboard visible. Docker pendiente de instalar solo si elegimos esa vía. |
| Persistencia tras reinicio | Verificada con dos proyectos y su contexto. |
| Restauración de copia | Verificada con archivo de estado, SQLite y lectura MCP tras reinicio. |
| Acceso de agente mediante MCP | Herramientas descubiertas y llamadas de proyectos/contexto verificadas por JSON-RPC; sesión natural desde otro cliente aún pendiente del piloto. |
| Base original y dependencias registradas | Completado en esta auditoría. |
| Matriz de conocimiento y lagunas | Completada para las áreas y fuentes indicadas; correcciones asignadas a fases posteriores. |
| DataForSEO opcional para el núcleo | Arranque y contexto sin clave verificados; adaptación de capacidades y mensajes en fase 2. |
| Cuenta y capacidades Metricool | Plan con API confirmado por el usuario; pendiente prueba de cuenta, marcas y datos disponibles. |

## Siguiente entrega

Fase 1: identidad 297 WOOP con firma by Jmillans, contexto confirmado y versionado, entrevista progresiva y continuidad entre agentes. Reutilizar la implementación existente de GSC y GA4 al llegar a fase 3. Resolver en paralelo los accesos externos; Keyword Planner permanece condicionado a verificar tanto las condiciones de uso como el acceso técnico.
