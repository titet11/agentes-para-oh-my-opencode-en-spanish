# 🤖 Agentes en Español para Oh My OpenCode

[![Idioma](https://img.shields.io/badge/Idioma-Espa%C3%B1ol-red?style=for-the-badge)](https://github.com/titet11/agentes-para-oh-my-opencode-en-spanish)
[![Agentes](https://img.shields.io/badge/Agentes-14-blue?style=for-the-badge)](https://github.com/titet11/agentes-para-oh-my-opencode-en-spanish)
[![Categorías](https://img.shields.io/badge/Categor%C3%ADas-8-green?style=for-the-badge)](https://github.com/titet11/agentes-para-oh-my-opencode-en-spanish)
[![Plataforma](https://img.shields.io/badge/Plataforma-OpenCode%20Desktop-purple?style=for-the-badge)](https://github.com/titet11/agentes-para-oh-my-opencode-en-spanish)
[![Modelo](https://img.shields.io/badge/Modelo-antigravity--claude--opus--4--6--thinking-orange?style=for-the-badge)](https://github.com/titet11/agentes-para-oh-my-opencode-en-spanish)

---

## 📋 Descripción General

Este repositorio contiene un sistema completo de **prompts personalizados en español** para los agentes de **Oh My OpenAgent**, la extensión de [OpenCode Desktop](https://github.com/nicepkg/opencode) que transforma tu editor en un entorno de desarrollo asistido por inteligencia artificial.

¿Por qué en español? Porque los prompts originales están en inglés, y aunque los modelos de lenguaje entienden ambos idiomas, un prompt diseñado nativamente en español permite instrucciones más precisas, expresiones idiomáticas naturales, y un flujo de trabajo que se siente propio. No es una traducción: es una reimplementación desde cero pensada para hispanohablantes.

El sistema está construido sobre una premisa clara: **la calidad no es negociable**. Cada agente tiene directivas explícitas que prohíben soluciones mediocres, parches temporales, y atajos que aparentan funcionar pero generan deuda técnica. El resultado es un pipeline de agentes que diagnostica antes de actuar, verifica después de implementar, y se autocuestiona constantemente.

Los 4 prompts principales suman más de **166 KB de instrucciones detalladas**. Esto no es un conjunto de prompts casuales: es un sistema de ingeniería de prompts diseñado para forzar calidad industrial en cada interacción.

---

## 🏗️ Arquitectura del Sistema

El corazón de este sistema es un **pipeline de 2 agentes para código** (Oracle → Hephaestus), orquestado por un agente central llamado Sisyphus. Para tareas de escritura, un agente secundario (Sisyphus-Junior) se encarga directamente.

El flujo completo se ve así:

```
┌─────────────────────────────────────────────────────────────────────┐
│                      SOLICITUD DEL USUARIO                        │
└───────────────────────────────┬─────────────────────────────────────┘
                                │
                                ▼
                ┌───────────────────────────────┐
                │      🧠 SISYPHUS              │
                │      (Orquestador)            │
                │                               │
                │  Sistema de 5 Preguntas de    │
                │  Enrutamiento Inteligente     │
                └───────┬───────┬───────┬───────┘
                        │       │       │
           ┌────────────┘       │       └────────────┐
           │                    │                    │
           ▼                    ▼                    ▼
   ┌───────────────┐   ┌───────────────┐   ┌───────────────┐
   │ ✍️ Sisyphus-  │   │ 🔮 ORACLE    │   │ 🔍 EXPLORE   │
   │   Junior      │   │ (Diagnóstico)│   │ 📚 LIBRARIAN │
   │ (Escritura)   │   │  READ-ONLY   │   │ (Búsqueda)   │
   └───────────────┘   └──────┬────────┘   └───────────────┘
                              │
           Archivos de texto  │  Reporte diagnóstico
           Trabajos escolares │  con código antes/después
           Documentos         │
                              ▼
                      ┌───────────────┐
                      │ ⚒️ HEPHAESTUS│
                      │(Implementador)│
                      │  Ejecuta los  │
                      │   cambios     │
                      └──────┬────────┘
                             │
                             ▼
                    ┌─────────────────┐
                    │  ✅ VERIFICACIÓN│
                    │  lsp_diagnostics│
                    │  Tests & Build  │
                    └─────────────────┘
```

### Flujo de enrutamiento detallado

Cuando llega una solicitud, Sisyphus la procesa a través de un sistema de **5 preguntas secuenciales** que determina qué agente (o combinación de agentes) debe encargarse:

```
Solicitud del usuario
        │
        ▼
  ┌─ PREGUNTA 1: ¿Involucra archivos de texto (.txt, docs)?
  │     SÍ ──► Sisyphus-Junior
  │     NO ──▼
  │
  ├─ PREGUNTA 2: ¿Es trabajo escolar o académico?
  │     SÍ ──► Sisyphus-Junior (con humanización)
  │     NO ──▼
  │
  ├─ PREGUNTA 3: ¿Requiere modificar/crear/corregir código?
  │     SÍ ──► Oracle (diagnóstico) → Hephaestus (implementación)
  │     NO ──▼
  │
  ├─ PREGUNTA 4: ¿Necesita investigación o exploración?
  │     SÍ ──► Explore (interno) / Librarian (externo) / Oracle (análisis)
  │     NO ──▼
  │
  └─ PREGUNTA 5: ¿El usuario pidió explícitamente que lo haga yo?
        SÍ ──► Override: Sisyphus ejecuta directamente
        NO ──► Solicitar clarificación
```

Este enrutamiento garantiza que cada solicitud llega al agente correcto sin ambigüedad. No hay improvisación: hay un protocolo claro.

---

## 🧩 Los Agentes en Detalle

### 🧠 Sisyphus — El Orquestador

| Propiedad | Detalle |
|---|---|
| **Archivo** | `sisyphus.md` |
| **Tamaño** | 51.4 KB |
| **Rol** | Orquestador central |
| **Edita código** | ❌ Nunca |
| **Se activa** | Siempre (primer agente en recibir cualquier solicitud) |

Sisyphus es el cerebro del sistema. Cada solicitud del usuario pasa primero por él, sin excepción. Su trabajo no es implementar ni diagnosticar: es **analizar, clasificar y delegar**.

Piensa en Sisyphus como un director de orquesta. No toca ningún instrumento, pero decide quién toca qué, en qué momento, y verifica que el resultado suene bien. Si algo sale mal, coordina la corrección.

#### ¿Qué hace exactamente?

1. **Clasifica la solicitud** usando el sistema de 5 preguntas de enrutamiento descrito arriba.
2. **Delega al agente correcto**: Oracle para código, Sisyphus-Junior para texto, Explore/Librarian para investigación.
3. **Gestiona el ciclo de vida completo**: desde la delegación inicial hasta la verificación final.
4. **Lanza agentes en paralelo** cuando las tareas son independientes. Por ejemplo, puede lanzar Explore para investigar la estructura del proyecto mientras Oracle analiza un bug, siempre que las tareas no se solapen.
5. **Aplica la regla anti-duplicación**: si ya delegó una búsqueda a Explore, no la repite él mismo. Si necesita los resultados y no están listos, espera.
6. **Gestiona TODOs**: cada tarea de 2 o más pasos se rastrea con un sistema de todos que se actualiza en tiempo real.
7. **Verifica post-implementación**: después de que Hephaestus termina, Sisyphus confirma que `lsp_diagnostics` muestre cero errores, que los tests pasen, y que el build sea exitoso.

#### Características clave

- **Delegación inteligente**: no solo elige al agente correcto, sino que proporciona contexto relevante en cada delegación.
- **Protección contra scope creep**: implementa exactamente lo que se pidió. No agrega features, no embellece la UX, no expande el alcance.
- **Protocolo de ambigüedad**: ante solicitudes ambiguas, explora primero con herramientas antes de preguntar al usuario. Preguntar es el último recurso.
- **Comunicación proactiva**: informa al usuario qué está haciendo y por qué en cada paso significativo.

---

### 🔮 Oracle — El Diagnosticador

| Propiedad | Detalle |
|---|---|
| **Archivo** | `oracle.md` |
| **Tamaño** | 62.2 KB |
| **Rol** | Diagnóstico y prescripción de soluciones |
| **Edita código** | ❌ Nunca (READ-ONLY) |
| **Se activa** | Cuando hay que corregir, modificar, crear o tocar código |

Oracle es el agente más grande del sistema (62.2 KB de instrucciones) y por buena razón: diagnosticar correctamente es más difícil que implementar. Un diagnóstico erróneo produce una implementación perfecta... del cambio equivocado.

Oracle opera bajo un principio fundamental: **leer todo, entender todo, no tocar nada**. Es un agente estrictamente READ-ONLY. Lee archivos, analiza código, busca patrones, identifica la causa raíz de los problemas, y produce un reporte diagnóstico detallado con el código exacto que debe cambiar (antes y después). Pero nunca abre un editor. Nunca modifica un archivo.

#### ¿Qué hace exactamente?

1. **Analiza la solicitud** para entender qué se necesita realmente (no solo lo que se pidió).
2. **Lee y comprende el código** relevante, incluyendo dependencias, imports, y patrones existentes.
3. **Diagnostica la causa raíz**: no el síntoma, sino el origen real del problema.
4. **Prescribe la solución exacta**: incluye bloques de código con el estado actual (ANTES) y el estado deseado (DESPUÉS).
5. **Evalúa riesgos**: analiza race conditions, efectos secundarios, y posibles regresiones.
6. **Produce un reporte estructurado** que Hephaestus puede ejecutar sin ambigüedad.

#### La Directiva de Desconfianza (3 Fases)

Oracle incorpora un protocolo de desconfianza de sí mismo en tres fases que se ejecuta antes de entregar cualquier diagnóstico:

**FASE 1: Pre-resultado**
Antes de llegar a una conclusión, Oracle se detiene y desconfía de su propia dirección. ¿Está siguiendo el camino correcto? ¿Hay algo que no ha considerado? Esta fase ocurre durante el análisis, no después.

**FASE 2: Post-resultado (10 puntos de desconfianza)**
Después de llegar a una solución, Oracle se somete a 10 preguntas de autocuestionamiento:

1. ¿Esta es realmente la causa raíz, o estoy tratando un síntoma?
2. ¿Mi cambio introduce algún efecto secundario?
3. ¿Hay algún caso borde que no estoy considerando?
4. ¿Mi solución funciona para todos los casos, o solo para el reportado?
5. ¿Estoy cambiando más de lo necesario?
6. ¿Estoy cambiando menos de lo necesario?
7. ¿Mi cambio rompe alguna funcionalidad existente?
8. ¿Existe una solución más simple y robusta?
9. ¿Mi solución depende de suposiciones frágiles?
10. ¿Qué pasaría si ejecuto esto en un contexto diferente al que tengo en mente?

Si cualquiera de estas preguntas revela una debilidad, Oracle vuelve atrás y refina su diagnóstico.

**FASE 3: Re-lectura de directivas**
Antes de entregar el reporte final, Oracle relee todas sus directivas de calidad para asegurarse de que su solución cumple con cada una de ellas.

#### Cuándo se activa Oracle

- Cualquier solicitud que requiera **modificar, crear, corregir o refactorizar código**.
- Consultas de **arquitectura** que necesiten análisis profundo.
- Después de **2 o más intentos fallidos** de corregir un problema (escalación).
- Cuando se necesita un **análisis de impacto** antes de un cambio grande.

---

### ⚒️ Hephaestus — El Implementador

| Propiedad | Detalle |
|---|---|
| **Archivo** | `hephaestus.md` |
| **Tamaño** | 24.3 KB |
| **Rol** | Implementación quirúrgica de cambios |
| **Edita código** | ✅ Sí (único agente que edita archivos de código) |
| **Se activa** | Solo después de recibir un diagnóstico de Oracle |

Hephaestus recibe su nombre del dios griego de la forja, y su función es exactamente esa: forjar. No diseña. No diagnostica. No debate. Recibe el reporte de Oracle y lo ejecuta con precisión quirúrgica.

#### ¿Qué hace exactamente?

1. **Recibe el reporte diagnóstico** de Oracle con las instrucciones exactas de qué cambiar.
2. **Ejecuta los cambios** línea por línea, archivo por archivo, tal como Oracle los prescribió.
3. **Verifica cada cambio** con `lsp_diagnostics` inmediatamente después de aplicarlo.
4. **Reporta discrepancias**: si algo en el reporte de Oracle no cuadra con la realidad del código (por ejemplo, una línea que debería existir pero no existe), Hephaestus **no improvisa**. Reporta la discrepancia a Sisyphus para que Oracle re-diagnostique.
5. **Valida documentación de librerías** usando Context7 cuando trabaja con dependencias externas.

#### Lo que Hephaestus NO hace

- ❌ **No re-diagnostica**: confía en el diagnóstico de Oracle y lo ejecuta.
- ❌ **No improvisa soluciones**: si algo no cuadra, escala en lugar de adivinar.
- ❌ **No se activa solo**: siempre es invocado por Sisyphus después de un diagnóstico.
- ❌ **No toma decisiones de diseño**: esas las toma Oracle.

#### Verificación post-implementación

Después de aplicar todos los cambios, Hephaestus ejecuta una secuencia de verificación obligatoria:

1. `lsp_diagnostics` en todos los archivos modificados (cero errores requeridos).
2. Ejecución de tests relacionados (si `foo.ts` fue modificado, busca y ejecuta `foo.test.ts`).
3. Typecheck si el proyecto usa TypeScript.
4. Build completo si aplica (exit code 0 requerido).

Solo después de que todas las verificaciones pasan, la tarea se marca como completada.

---

### ✍️ Sisyphus-Junior — El Escritor

| Propiedad | Detalle |
|---|---|
| **Archivo** | `sisyphus-junior.md` |
| **Tamaño** | 28.8 KB |
| **Rol** | Redacción de textos, documentos y trabajos académicos |
| **Edita código** | ❌ Nunca (solo archivos de texto) |
| **Se activa** | Cuando la solicitud involucra texto, documentos o trabajos escolares |

Sisyphus-Junior se encarga de todo lo que no es código. Archivos de texto, notas, documentos, trabajos escolares, investigaciones, resúmenes, ensayos, reportes. Si el resultado final es prosa y no programación, Sisyphus-Junior es el agente indicado.

#### ¿Qué hace exactamente?

1. **Clasifica el contenido**: determina si el texto es académico o no usando una pregunta de detección obligatoria.
2. **Redacta el contenido** con el tono y estilo apropiados para el contexto.
3. **Aplica humanización** cuando detecta contenido académico, usando un sistema sofisticado para que el texto suene natural.
4. **Gestiona verificaciones** con `lsp_diagnostics` en archivos modificados (incluso archivos de texto pueden tener formatos que validar).

#### El Sistema de Humanización Académica

Cuando Sisyphus-Junior detecta que el contenido es académico (trabajo escolar, ensayo, reporte educativo), activa un sistema de humanización con **14 categorías semánticas de frases** y **6 reglas de humanización**.

**Las 14 categorías semánticas:**

| # | Categoría | Ejemplo |
|---|---|---|
| 1 | Conectores causales y explicativos | "la verdad", "ya que", "porque aparte de ser" |
| 2 | Conectores adversativos y concesivos | "pero aún así", "aunque también", "y aunque se implementó" |
| 3 | Conectores aditivos y de ampliación | "y también es", "sobre todo", "desde luego" |
| 4 | Expresiones de opinión personal | "pienso", "considero", "me parece" |
| 5 | Expresiones de existencia y descripción | "es algo", "es necesario", "es más como un" |
| 6 | Expresiones de resultado y consecuencia | "esto también puede", "termina siendo", "se convierte en algo" |
| 7 | Expresiones de caso y condición | "en el caso de", "cuando en realidad", "no se trata tanto" |
| 8 | Expresiones con pronombres y artículos | "nos ayuda", "nos contribuye", "de aquellos" |
| 9 | Expresiones de posibilidad y logro | "que podemos", "que lograron", "que nos ha demostrado" |
| 10 | Expresiones de finalidad y necesidad | "para que", "hay que", "es necesario" |
| 11 | Expresiones de origen y referencia | "gracias a esto", "basado en", "que forma parte de" |
| 12 | Expresiones de resultado final | "al final", "al final contribuirá", "no solamente" |
| 13 | Expresiones de comparación y contraste | "mientras que", "lo que pasa", "con una gran notoriedad" |
| 14 | Expresiones de continuidad y adición | "aparte que esto", "se pueda fortalecer", "y ha logrado" |

**Las 6 reglas de humanización:**

1. **Uso obligatorio de frases humanas**: incorporar las frases de las 14 categorías de forma adaptativa y natural.
2. **No usar las frases en el mismo orden**: mezclarlas, distribuirlas, adaptarlas al contexto.
3. **Tono natural**: opiniones personales intercaladas, conectores informales pero académicamente aceptables.
4. **Adaptación al contexto**: las frases se ajustan al tema del trabajo (historia, ciencia, análisis social, etc.).
5. **Distribución natural**: las frases se distribuyen uniformemente a lo largo de todo el texto.
6. **Preservar imperfecciones**: algunos errores ortográficos intencionales se mantienen para contribuir a la autenticidad.

#### Clasificación de instrucciones en archivos

Cuando Sisyphus-Junior lee un archivo que contiene instrucciones, aplica un protocolo de clasificación:

- Si las instrucciones piden **crear apuntes o anotaciones** → Lo ejecuta como tarea académica.
- Si las instrucciones piden **modificar archivos de código** → Se detiene inmediatamente y reporta a Sisyphus para que delegue al flujo Oracle → Hephaestus.

Esto previene que Sisyphus-Junior intente modificar código fuente, algo para lo que no está diseñado.

---

### 🔍 Explore — El Explorador Interno

| Propiedad | Detalle |
|---|---|
| **Rol** | Búsqueda de patrones y conexiones dentro del proyecto |
| **Alcance** | Interno (codebase actual) |
| **Se activa** | Cuando se necesita entender la estructura del proyecto |

Explore es el agente que conoce tu proyecto por dentro. Cuando necesitas saber cómo está organizado el código, dónde se define una función, qué archivos usan un patrón determinado, o cómo se conectan las diferentes partes de tu aplicación, Explore es quien responde.

Funciona como un `grep` inteligente y contextual. No solo encuentra texto: entiende estructura, relaciones entre archivos, y patrones arquitectónicos.

#### Cuándo se usa

- Para **descubrir la estructura** de un proyecto desconocido.
- Para **encontrar patrones existentes** que deben replicarse en nuevo código.
- Para **rastrear dependencias** entre módulos.
- Para **entender convenciones** de naming, imports, y estilos del proyecto.
- Cuando Oracle necesita contexto adicional antes de diagnosticar.

---

### 📚 Librarian — El Bibliotecario Externo

| Propiedad | Detalle |
|---|---|
| **Rol** | Búsqueda de información fuera del proyecto |
| **Alcance** | Externo (documentación oficial, GitHub, APIs) |
| **Se activa** | Cuando se trabaja con librerías desconocidas o se necesita documentación |

Librarian busca fuera de tu proyecto. Si Explore conoce tu código, Librarian conoce el mundo exterior: documentación oficial de librerías, ejemplos en repositorios de código abierto, guías de APIs, y mejores prácticas publicadas.

#### Cuándo se usa

- Cuando se trabaja con una **librería desconocida** y se necesitan ejemplos de uso.
- Para consultar **documentación oficial** de frameworks y herramientas.
- Para encontrar **ejemplos de implementación** en proyectos de código abierto.
- Cuando se necesita verificar la **API correcta** de una dependencia externa.
- Como complemento de Context7 cuando se necesita información más amplia.

---

### 🧩 Metis — El Analista de Requisitos

| Propiedad | Detalle |
|---|---|
| **Rol** | Análisis de requisitos y detección de ambigüedades |
| **Se activa** | Tareas con requisitos confusos o alcance incierto |

Metis toma su nombre de la diosa griega de la sabiduría y el buen consejo. Su función es analizar solicitudes complejas antes de que el trabajo comience, identificando ambigüedades, intenciones ocultas, contradicciones, y posibles puntos de falla.

#### Cuándo se usa

- Cuando una solicitud tiene **múltiples interpretaciones posibles** y hay que elegir la correcta.
- Cuando el alcance de una tarea es **incierto o demasiado amplio**.
- Para **descomponer tareas complejas** en subtareas claras y ejecutables.
- Cuando se necesita **validar requisitos** antes de invertir tiempo en implementación.

---

### 👁️ Momus — El Revisor de Planes

| Propiedad | Detalle |
|---|---|
| **Rol** | Evaluación de planes de trabajo |
| **Se activa** | Después de crear un plan, antes de ejecutarlo |

Momus recibe su nombre del dios griego de la crítica y la sátira. Su rol es evaluar planes de trabajo contra estándares de claridad, verificabilidad y completitud. No crea planes; los critica constructivamente.

#### Cuándo se usa

- Después de que Sisyphus crea un **plan de trabajo** para una tarea compleja.
- Para verificar que un plan es **ejecutable y verificable** antes de comenzar.
- Para detectar **pasos faltantes, dependencias ocultas, o riesgos** en un plan.
- Como segunda opinión sobre la **viabilidad** de un enfoque propuesto.

---

## 🛡️ Directivas de Calidad

El sistema opera bajo **6 directivas obligatorias** que se aplican a todos los agentes, especialmente a Oracle. Estas directivas existen para prevenir soluciones mediocres y forzar calidad industrial en cada cambio.

### Directiva 1: Preservar la Lógica Existente

Cualquier solución debe respetar la lógica que ya funciona. Si el código existente maneja un caso correctamente, la solución no puede romper ese comportamiento. Esto parece obvio, pero bajo presión de tiempo, es sorprendentemente fácil introducir regresiones "arreglando" algo que no estaba roto.

### Directiva 2: Soluciones Prohibidas

Hay categorías de soluciones que están **explícitamente prohibidas**:

| Tipo de solución | Por qué está prohibida |
|---|---|
| **Hardcoding** | Funciona para un caso. Rompe para todos los demás. |
| **Delays arbitrarios** (`setTimeout`, `sleep`) | Oculta race conditions en lugar de resolverlas. |
| **Polling** sin justificación | Desperdicia recursos y no resuelve el problema subyacente. |
| **Parches temporales** | "Temporal" se convierte en permanente. Siempre. |
| **Try-catch genéricos** que tragan errores | Oculta bugs en lugar de corregirlos. |
| **Flags booleanos** para controlar flujo complejo | Se vuelven inmanejables con 2+ condiciones. |

Si Oracle propone cualquiera de estas soluciones, sus propias directivas lo obligan a volver atrás y buscar una solución real.

### Directiva 3: Tipo de Solución Requerida

Las soluciones deben ser:

- **Deterministas**: producen el mismo resultado con las mismas entradas.
- **Robustas**: manejan casos borde sin fallar silenciosamente.
- **Mínimas**: cambian solo lo necesario, ni más ni menos.
- **Verificables**: se puede confirmar que funcionan con tests o diagnósticos.

### Directiva 4: Análisis Obligatorio con 12 Autocuestionamientos

Antes de entregar cualquier solución, el agente debe autocuestionarse al menos **12 veces**. No es una sugerencia: es un requisito. Las 12 preguntas cubren causa raíz, efectos secundarios, casos borde, alcance del cambio, funcionalidad existente, simplicidad, fragilidad, contextos alternativos, comprensión del código, y aprobación hipotética de otro ingeniero.

Este protocolo existe porque la primera solución que parece correcta muchas veces no lo es. El autocuestionamiento forzado descubre debilidades que la confianza inicial oculta.

### Directiva 5: Pensamiento Prolongado Obligatorio

Los agentes deben dedicar **más tiempo del habitual** a pensar antes de actuar. Si la solución llega rápido, eso es una señal de alerta, no de eficiencia. La complejidad real requiere reflexión sostenida.

Esta directiva combate la tendencia de los modelos de lenguaje a producir respuestas inmediatas que suenan bien pero no han sido sometidas a análisis profundo.

### Directiva 6: Prohibición Final

Antes de entregar un resultado final, el agente debe releer todas sus directivas y verificar que la solución cumple con cada una. Si no cumple, vuelve atrás. No hay atajos.

---

## 📖 Integración con Context7

El sistema incluye una integración obligatoria con **Context7**, un MCP (Model Context Protocol) que proporciona acceso a documentación oficial actualizada de librerías y frameworks.

### ¿Cómo funciona?

Cuando un agente (generalmente Hephaestus) trabaja con una librería externa, el protocolo es:

1. **Resolver el ID de la librería** usando `context7_resolve-library-id`.
2. **Obtener la documentación relevante** usando `context7_get-library-docs` con un topic específico.
3. **Usar la documentación oficial** para validar que la implementación usa la API correcta.

### ¿Por qué es obligatorio?

Los modelos de lenguaje tienen conocimiento de librerías basado en sus datos de entrenamiento, que pueden estar desactualizados. Context7 proporciona documentación en tiempo real, lo que previene el uso de APIs deprecadas, parámetros obsoletos, o patrones que ya no son recomendados.

```
Ejemplo de flujo:

Hephaestus necesita usar una función de React Query
    │
    ▼
context7_resolve-library-id("tanstack react query")
    │
    ▼
Obtiene: /tanstack/query
    │
    ▼
context7_get-library-docs("/tanstack/query", topic="useQuery")
    │
    ▼
Recibe documentación actualizada con la API correcta
    │
    ▼
Implementa usando la API verificada
```

---

## 📥 Cómo Instalar

### Prerrequisitos

1. **OpenCode Desktop** instalado y configurado.
2. **Oh My OpenAgent** instalado como extensión de OpenCode Desktop.
3. Un modelo compatible configurado (este sistema usa `antigravity-claude-opus-4-6-thinking` variante `max`).

### Pasos de instalación

**Paso 1:** Clona este repositorio.

```bash
git clone https://github.com/titet11/agentes-para-oh-my-opencode-en-spanish.git
```

**Paso 2:** Copia la carpeta `.config/opencode/` al directorio raíz de tu proyecto (o al directorio donde OpenCode Desktop busca su configuración).

```bash
cp -r agentes-para-oh-my-opencode-en-spanish/.config/opencode/ tu-proyecto/.config/opencode/
```

**Paso 3:** Verifica que la estructura quedó correcta:

```
tu-proyecto/
└── .config/
    └── opencode/
        ├── oh-my-opencode.json
        └── prompts/
            ├── sisyphus.md
            ├── oracle.md
            ├── hephaestus.md
            └── sisyphus-junior.md
```

**Paso 4:** Abre tu proyecto con OpenCode Desktop. Los agentes se cargarán automáticamente desde los archivos de configuración.

**Paso 5:** Verifica que los agentes aparecen correctamente en la interfaz de Oh My OpenAgent.

> **Nota:** Si usas un modelo diferente a `antigravity-claude-opus-4-6-thinking`, puedes cambiarlo en el archivo `oh-my-opencode.json`. Los prompts están diseñados para modelos con capacidad de pensamiento extendido, así que resultados pueden variar con modelos más pequeños.

---

## 📁 Estructura del Repositorio

```
agentes-para-oh-my-opencode-en-spanish/
│
├── README.md                                    ← Este archivo
│
└── .config/
    └── opencode/
        │
        ├── oh-my-opencode.json                  ← Configuración principal
        │                                          14 agentes, 8 categorías
        │                                          Modelo: antigravity-claude-opus-4-6-thinking
        │
        └── prompts/
            │
            ├── sisyphus.md                      ← Orquestador principal (51.4 KB)
            │                                      Sistema de 5 preguntas
            │                                      Delegación y verificación
            │
            ├── oracle.md                        ← Diagnosticador READ-ONLY (62.2 KB)
            │                                      Desconfianza en 3 fases
            │                                      12 autocuestionamientos
            │
            ├── hephaestus.md                    ← Implementador quirúrgico (24.3 KB)
            │                                      Ejecuta diagnósticos de Oracle
            │                                      Verificación con lsp_diagnostics
            │
            └── sisyphus-junior.md               ← Escritor especializado (28.8 KB)
                                                   Humanización académica
                                                   14 categorías semánticas
```

### Tamaños de los prompts

| Archivo | Tamaño | Líneas estimadas |
|---|---|---|
| `oracle.md` | 62.2 KB | ~1,500+ líneas |
| `sisyphus.md` | 51.4 KB | ~1,200+ líneas |
| `sisyphus-junior.md` | 28.8 KB | ~700+ líneas |
| `hephaestus.md` | 24.3 KB | ~600+ líneas |
| **Total** | **~166.7 KB** | **~4,000+ líneas** |

---

## ⚙️ Requisitos

| Requisito | Detalle |
|---|---|
| **OpenCode Desktop** | Instalado y funcional |
| **Oh My OpenAgent** | Extensión instalada en OpenCode Desktop |
| **Modelo de IA** | `antigravity-claude-opus-4-6-thinking` (variante `max`) recomendado |
| **Sistema operativo** | Windows, macOS, o Linux |
| **Idioma** | Todos los prompts están en español |

### Sobre el modelo

Este sistema fue diseñado y probado con `antigravity-claude-opus-4-6-thinking` en su variante `max`. Este modelo soporta pensamiento extendido (extended thinking), que es fundamental para las directivas de pensamiento prolongado y autocuestionamiento.

Puedes usar otros modelos compatibles con OpenCode Desktop, pero ten en cuenta:

- Modelos sin capacidad de pensamiento extendido pueden no beneficiarse de las directivas de desconfianza.
- Modelos más pequeños pueden no seguir instrucciones tan complejas con la misma precisión.
- Los prompts son extensos (166+ KB en total), así que necesitas un modelo con ventana de contexto suficiente.

---

## 🤔 Preguntas Frecuentes

**¿Puedo usar solo algunos agentes?**
Sí. Cada prompt es independiente. Puedes copiar solo los que necesites. Sin embargo, el flujo completo Oracle → Hephaestus necesita a Sisyphus como orquestador para funcionar correctamente.

**¿Los prompts funcionan en inglés?**
Están escritos completamente en español. Los agentes responderán en español por defecto. Si les escribes en inglés, probablemente respondan en español de todas formas.

**¿Puedo modificar los prompts?**
Por supuesto. Son archivos Markdown. Puedes ajustarlos a tus necesidades. Solo ten cuidado de no eliminar directivas de verificación, ya que son las que garantizan la calidad del output.

**¿Por qué Oracle no edita archivos directamente?**
Separar diagnóstico de implementación reduce errores. Cuando el mismo agente diagnostica y ejecuta, tiende a confirmar su propio diagnóstico sin cuestionarlo. Al separar los roles, Oracle puede enfocarse en entender y Hephaestus en ejecutar, cada uno con sus propias verificaciones.

**¿Qué pasa si Oracle se equivoca?**
Hephaestus detecta discrepancias entre el diagnóstico y el código real. Cuando esto ocurre, no improvisa: reporta a Sisyphus, que solicita un nuevo diagnóstico a Oracle con la información corregida.

---

## 📊 Resumen Visual del Sistema

```
╔══════════════════════════════════════════════════════════════╗
║                    SISTEMA DE AGENTES                       ║
║                  Oh My OpenCode en Español                  ║
╠══════════════════════════════════════════════════════════════╣
║                                                              ║
║  🧠 Sisyphus ──── Orquesta todo, no implementa nada         ║
║  🔮 Oracle   ──── Diagnostica todo, no toca nada            ║
║  ⚒️ Hephaestus ── Implementa lo que Oracle prescribe        ║
║  ✍️ Junior   ──── Escribe texto, humaniza lo académico      ║
║  🔍 Explore  ──── Busca dentro del proyecto                 ║
║  📚 Librarian ─── Busca fuera del proyecto                  ║
║  🧩 Metis    ──── Analiza requisitos ambiguos               ║
║  👁️ Momus    ──── Revisa planes antes de ejecutar           ║
║                                                              ║
╠══════════════════════════════════════════════════════════════╣
║  📋 6 directivas de calidad obligatorias                     ║
║  📖 Integración con Context7 para docs actualizadas          ║
║  🇪🇸 100% en español                                        ║
║  🎯 166+ KB de instrucciones detalladas                      ║
╚══════════════════════════════════════════════════════════════╝
```

---

## 📝 Nota Final

Este sistema nació de una frustración real: las respuestas "casi correctas" que producen los asistentes de código cuando no tienen instrucciones suficientemente detalladas. Un fix que parece funcionar pero introduce un bug sutil. Un refactor que resuelve un problema y crea dos nuevos. Una solución que usa `setTimeout(500)` porque "así funciona" sin preguntarse por qué necesita un delay.

Los 166 KB de prompts en este repositorio existen para eliminar esa categoría de errores. Cada directiva, cada autocuestionamiento, cada fase de desconfianza, y cada verificación post-implementación existe porque en algún momento, sin ellos, algo salió mal.

Si encuentras útil este sistema, úsalo. Si encuentras formas de mejorarlo, contribuye. Los prompts son texto plano. Puedes leerlos, entenderlos, y adaptarlos.

---

<p align="center">
  Hecho con ☕ y muchas iteraciones
</p>
