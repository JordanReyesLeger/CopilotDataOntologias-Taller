# Taller GitHub Copilot & Ontologías — INEGI

> **Guía práctica del taller.** La teoría completa está en presentation/taller-copilot-ontologias-inegi.html.

## Pre-requisitos

- [ ] VS Code instalado
- [ ] GitHub Copilot activo (Individual, Business o Enterprise)
- [ ] Extensiones: GitHub Copilot + GitHub Copilot Chat
- [ ] Clonar este repositorio

---

## Estructura del proyecto

`
SE-INEGI-TallerOntologias/
├── README.md                              ← esta guía
├── .github/
│   ├── copilot-instructions.md            ← reglas del proyecto para Copilot
│   └── agents/
│       └── analista-censo.agent.md        ← agente personalizado
├── data/
│   └── censo_2020_sample.csv              ← datos del Censo 2020 (con problemas intencionales)
├── ontology/
│   └── censo_ontology.json                ← ontología semántica del censo
└── presentation/
    └── taller-copilot-ontologias-inegi.html  ← presentación teórica
`

---

## ¿Qué es el Modo Ask?

El **Modo Ask** es la forma más básica de interactuar con GitHub Copilot. Es un chat donde tú preguntas y Copilot responde.

**¿Cómo funciona por dentro?**
- Copilot toma tu pregunta + los **archivos abiertos** en el editor + las **instrucciones del proyecto** y lo envía al LLM como contexto.
- El LLM genera una respuesta basada en todo ese contexto.
- **No** puede crear archivos, ejecutar comandos ni modificar código. Solo responde.

**¿Cuándo usarlo?**
- Explorar y entender datos
- Hacer preguntas sobre código o esquemas
- Pedir explicaciones o documentación
- Generar ideas antes de ejecutar

**Atajos:**
| Acción | Atajo |
|--------|-------|
| Abrir chat | `Ctrl+L` |
| Referenciar archivo | Escribir `#file:nombre.ext` |
| Referenciar workspace | Escribir `@workspace` |

---

## Ejercicio 1 — Modo Ask: Explorar el CSV

1. Abre `data/censo_2020_sample.csv` en VS Code
2. Presiona `Ctrl+L` para abrir Copilot Chat
3. Prueba estos prompts en orden:

**Prompt 1 — Análisis exploratorio:**
`
Analiza este CSV del Censo de Población 2020 del INEGI.
Dame: (1) granularidad de los datos, (2) posibles claves primarias,
(3) problemas de calidad que detectes, (4) entidades del dominio.
`

**Prompt 2 — Entender las columnas:**
`
Genera un diccionario de datos en Markdown con: nombre técnico,
nombre semántico en español, descripción, tipo esperado, y un ejemplo.
Marca las columnas que tengan riesgo de calidad.
`

**Prompt 3 — Preguntas de negocio:**
`
¿Cuáles son los 5 municipios con mayor Población Económicamente Activa?
¿Qué porcentaje de la población es femenina en cada entidad?
¿Hay registros con problemas de calidad? Cuáles y por qué.
`

> 💡 **Observa:** Copilot responde diferente si el archivo está abierto vs si no lo está. Eso es el **contexto**.

---

## ¿Qué es el Modo Agent?

El **Modo Agent** es el modo más poderoso de Copilot. A diferencia del modo Ask (que solo responde), el agente puede **actuar**:

| Modo Ask | Modo Agent |
|----------|------------|
| Solo responde texto | Lee y escribe archivos |
| No modifica nada | Ejecuta comandos en terminal |
| Una respuesta por turno | Múltiples pasos autónomos |
| Tú copias/pegas | Él crea/edita directamente |

**¿Cómo funciona por dentro?**
1. Recibe tu prompt
2. Analiza el workspace (archivos, instrucciones, MCPs)
3. Descompone la tarea en pasos
4. Ejecuta cada paso: leer → pensar → escribir → validar
5. Si hay errores, intenta corregir
6. Te muestra los cambios para que los aceptes o rechaces

**¿Cuándo usarlo?**
- Crear archivos nuevos (ontologías, scripts, DDL)
- Tareas multi-paso que involucren leer y escribir
- Generar código que necesite validación
- Transformaciones de datos completas

**Cómo activarlo:**
1. Abre Copilot Chat (`Ctrl+L`)
2. En la parte superior del chat, haz clic en el menú de modo
3. Selecciona **Agent** (ícono 🤖)

> ⚠️ **Importante:** El agente te pedirá confirmación antes de ejecutar acciones. Siempre revisa lo que propone antes de aceptar.

---

## Cambiar el modelo de IA

Copilot puede usar **diferentes modelos de IA**. Cada uno tiene fortalezas distintas:

| Modelo | Fortaleza | Cuándo usarlo |
|--------|-----------|---------------|
| **GPT-4o** | Rápido, buen balance | Preguntas generales, código |
| **Claude Sonnet** | Razonamiento largo, detallado | Análisis complejos, ontologías |
| **Claude Opus** | El más capaz, profundo | Tareas multi-paso complejas |
| **Gemini** | Contexto muy largo | Archivos grandes, CSVs extensos |
| **o1 / o3** | Razonamiento matemático | Lógica, validaciones, algoritmos |

**Cómo cambiar el modelo:**
1. En el chat de Copilot, haz clic en el **nombre del modelo** (esquina inferior o menú superior)
2. Selecciona el modelo que quieras usar
3. El cambio aplica solo a esa conversación

> 💡 **Tip:** Para este taller, prueba usar **Claude Sonnet** o **Claude Opus** para los ejercicios de ontología — son mejores para razonamiento semántico.

---

## Ejercicio 2 — Modo Agent: Generar la ontología

1. Cambia a **Agent Mode** (ícono 🤖 en el chat)
2. Usa este prompt:

`
Lee el archivo data/censo_2020_sample.csv.
Genera el archivo ontology/censo_ontology.json con una ontología
que describa la estructura semántica del dataset.

La ontología debe incluir:
- classes: EntidadFederativa, Municipio, RegistroCensal
- properties: cada columna del CSV con label, description, type, category
- relationships: Municipio → EntidadFederativa
- quality_rules: reglas de validación (ej: pob_fem + pob_mas = pob_total)
- example_questions: 5 preguntas que un usuario podría hacer


`

> 💡 **Observa:** El agente lee el CSV, analiza las columnas, crea el archivo y lo escribe. Todo autónomamente.

> ⚠️ **Nota:** Ya existe un censo_ontology.json de referencia. Compara lo que genera el agente con la referencia.

---

## ¿Qué son las instrucciones del proyecto?

El archivo `.github/copilot-instructions.md` es un archivo Markdown que **Copilot lee automáticamente** cada vez que abres el chat en ese proyecto. Es como un "system prompt" permanente.

**¿Para qué sirve?**
- Definir reglas de naming (`snake_case`, sin acentos)
- Indicar el idioma de respuesta (español)
- Decirle qué archivos consultar siempre (la ontología)
- Establecer convenciones del proyecto (namespace, formatos)

**¿Cómo funciona?**
- Copilot lo lee **automáticamente** — no necesitas referenciarlo
- Aplica a **todos los modos** (Ask, Edit, Agent)
- Se combina con el prompt que tú escribas
- Si hay conflicto, tu prompt tiene prioridad

> 💡 Otros archivos que Copilot también lee: `AGENTS.md` (para coding agents de GitHub), `CLAUDE.md`, `GEMINI.md` (para agentes específicos).

---

## Ejercicio 3 — Crear instrucciones del proyecto

1. En **Agent Mode**, ejecuta:

`
Crea el archivo .github/copilot-instructions.md con reglas para este proyecto:
- Responde en español
- Usa snake_case sin acentos
- Para ontologías usa namespace http://inegi.org.mx/ontology#
- Siempre consulta ontology/censo_ontology.json antes de responder sobre datos
- Reporta problemas de calidad cuando los detectes
`

2. Cierra y vuelve a abrir el chat. Ahora haz la misma pregunta del Ejercicio 1.

> 💡 **Observa:** Las respuestas ahora siguen las reglas que definiste. Copilot lee automáticamente el archivo de instrucciones.

---

## ¿Qué es un Agente Personalizado (Custom Agent)?

Un **Custom Agent** es un archivo `.agent.md` que le dice a Copilot **cómo comportarse**. Es como crear un "asistente especializado" que ya sabe:

- **Qué archivos leer** automáticamente (la ontología, los datos)
- **Cómo responder** (en español, con tablas, citando fuentes)
- **Qué reglas seguir** (validar calidad, usar nombres semánticos)
- **Qué herramientas usar** (codebase, terminal, etc.)

**¿Dónde va el archivo?**
```
.github/agents/nombre-del-agente.agent.md
```

**Estructura del archivo:**

```
---
description: "Descripción corta del agente"
tools: ["codebase", "changes", "search", "runCommands"]
---

# Nombre del Agente

Eres un [rol]. Tu trabajo es [objetivo].

Qué archivos leer:
- SIEMPRE lee archivo1.json
- SIEMPRE lee archivo2.csv

Cómo responder:
- Presenta resultados en tablas Markdown
- Responde en español
```

**¿Cómo usarlo después de crearlo?**
1. Recarga VS Code (`Ctrl+Shift+P` → "Reload Window")
2. En el chat, haz clic en el **menú de modo** (arriba del chat)
3. Selecciona el nombre de tu agente personalizado
4. Ahora cada pregunta pasa por las reglas de tu agente

> 💡 La diferencia con `copilot-instructions.md` es que las **instrucciones** aplican siempre, mientras que el **agente** solo aplica cuando lo seleccionas.

---

## Ejercicio 4 — Crear un agente personalizado

1. En **Agent Mode**, ejecuta:

`
Crea un agente personalizado en .github/agents/analista-censo.agent.md

El agente debe:
- Llamarse "Analista de Datos Censales INEGI"
- Siempre leer ontology/censo_ontology.json al inicio
- Siempre leer data/censo_2020_sample.csv cuando necesite datos
- Usar nombres semánticos de la ontología, no nombres técnicos
- Presentar resultados en tablas Markdown
- Reportar problemas de calidad automáticamente
- Responder en español
`

2. Recarga VS Code (Ctrl+Shift+P → Developer: Reload Window)
3. En el chat, cambia al modo **"Analista de Datos Censales INEGI"** (menú de modos)
4. Ahora pregunta:

`
¿Cuáles son los 3 municipios con mayor población total?
¿Hay problemas de calidad en los datos?
`

> 💡 **Observa:** El agente lee la ontología automáticamente y usa los nombres semánticos. Ya no necesitas explicarle qué significa cada columna.

> ⚠️ **Nota:** Ya existe un agente de referencia. Compara lo que generaste con `.github/agents/analista-censo.agent.md`.

---

## Ejercicio 5 — Usar el agente para consultas avanzadas

Con el agente **"Analista de Datos Censales INEGI"** activo, prueba:

`
¿Cuál es la tasa de analfabetismo por entidad federativa?
Calcula el porcentaje y ordena de mayor a menor.
`

`
Valida las quality_rules de la ontología contra los datos del CSV.
Genera un reporte de calidad en Markdown.
`

`
¿Qué entidad tiene la mayor proporción de población indígena?
Muestra los datos y explica usando la definición de la ontología.
`

`
Genera 3 consultas SQL (PostgreSQL) que respondan:
1. Top 5 municipios por PEA
2. Entidades con tasa de analfabetismo > 3%
3. Municipios donde viviendas deshabitadas > 10% del total
Usa los nombres semánticos de la ontología como comentarios.
`

---

## Ejercicio 6 — De datos a modelo AI-Ready

En **Agent Mode**:

`
Lee data/censo_2020_sample.csv y ontology/censo_ontology.json.

1. Detecta y reporta todos los problemas de calidad
2. Genera un script Python que limpie los datos y produzca data/censo_2020_clean.csv
3. Diseña un modelo dimensional AI-Ready con:
   - Tabla de hechos: hechos_censo (grano: entidad, municipio, año)
   - Dimensiones: dim_entidad, dim_municipio
   - Tabla metadata_semantica vinculando columnas a la ontología
4. Genera el DDL en sql/ddl_ai_ready.sql
5. Genera docs/model_card.md describiendo el dataset
`

---

## Referencia rápida

| Acción | Atajo |
|--------|-------|
| Abrir Copilot Chat | Ctrl+L |
| Chat inline (Edit) | Ctrl+I |
| Cambiar a Agent Mode | Menú en chat → 🤖 |
| Cambiar Chat Mode | Menú en chat → nombre del agente |
| Referenciar workspace | Escribir @workspace en el prompt |
| Referenciar archivo | Escribir #file:nombre.ext en el prompt |
| Recargar VS Code | Ctrl+Shift+P → "Reload Window" |

---

## Archivos de referencia incluidos

| Archivo | Qué es |
|---------|--------|
| data/censo_2020_sample.csv | 20 registros del Censo 2020 con problemas intencionales |
| ontology/censo_ontology.json | Ontología de referencia con clases, propiedades y reglas |
| .github/copilot-instructions.md | Instrucciones del proyecto para Copilot |
| .github/agents/analista-censo.agent.md | Agente personalizado de referencia |
| presentation/taller-copilot-ontologias-inegi.html | Presentación teórica completa |

---

> **Hecho para INEGI** — De la 3FN a la AI-Ready, sin perder el rigor.
