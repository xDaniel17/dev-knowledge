---
title: "Fundamentos de IA — LLMs, tokens, contexto y parámetros"
date: 2026-03-30
lastUpdated: 2026-03-30
tags:
  - ia
  - llm
  - tokens
  - context-window
  - embeddings
  - fundamentos
category: "00 Fundamentos"
subcategory: ""
author: Daniel
version: 1.0.0
status: active
related:
  - "[[Fundamentos de IA - Agentes y RAG]]"
  - "[[Prompting avanzado]]"
  - "[[OpenCode]]"
  - "[[Skills en Claude Code]]"
---

# Fundamentos de IA — LLMs, tokens, contexto y parámetros

Un Large Language Model (LLM) es un modelo de red neuronal entrenado sobre cantidades masivas de texto para predecir el siguiente token más probable en una secuencia. Entender cómo funcionan internamente explica por qué responden bien a ciertos prompts y mal a otros.

---

## ¿Qué es un LLM?

Un LLM es, en esencia, una función de probabilidad condicional:

```
P(siguiente_token | todos_los_tokens_anteriores)
```

Durante el entrenamiento, el modelo vio miles de millones de ejemplos de texto y ajustó sus parámetros (los "pesos") para mejorar esta predicción. En inferencia (cuando lo usas), genera texto token a token eligiendo el siguiente basado en lo que ya escribió.

```
Input:  "El capital de Francia es"
Token 1: " Paris"  → P=0.97
Token 2: "."       → P=0.89
Token 3: "\n"      → P=0.72
...
```

Esto es todo. No "piensa", no "entiende", no "recuerda" entre conversaciones. Es un generador de texto extremadamente sofisticado que produce salidas sorprendentemente coherentes porque el texto del mundo real es coherente.

---

## Tokens — la unidad de medida real

Los modelos no procesan caracteres ni palabras — procesan **tokens**, que son secuencias de caracteres frecuentes en el corpus de entrenamiento.

```
"JavaScript"  → 1 token   (palabra común, ya tokenizada)
"uncommonly"  → 3 tokens  ["un", "common", "ly"]
"2026-03-30"  → 4 tokens  ["202", "6", "-03", "-30"]
"¿"           → 2 tokens  (carácter poco frecuente en inglés)
```

**Regla aproximada para inglés/español:**
- 1 token ≈ 4 caracteres ≈ 0.75 palabras
- 1.000 palabras ≈ 1.300 tokens
- Una página de texto ≈ 400-500 tokens

**Por qué importa:**
- Los LLMs tienen un límite de tokens total (input + output combinados)
- El costo de las APIs se calcula en tokens
- Los modelos "ven" el texto como secuencias de tokens, no palabras — esto explica que fallen en tareas como contar letras

```
¿Cuántas "r" tiene "strawberry"?
→ El modelo ve: ["str", "aw", "berry"] — no ve las letras individualmente
→ Por eso puede equivocarse con estas tareas
```

---

## Context Window — la memoria de trabajo

La ventana de contexto es cuántos tokens puede "ver" el modelo a la vez. Todo lo que no está en la ventana no existe para el modelo.

```
Conversación completa:
┌─────────────────────────────────────────────────────┐
│  System Prompt  │  Historial  │  Mensaje actual  │  │
│   (instruc.)    │  (turnos)   │  (tu pregunta)   │  │
└─────────────────────────────────────────────────────┘
←────────────────── Context Window ─────────────────→
                    (ej: 200k tokens en Claude)
```

**Implicaciones prácticas:**
- En conversaciones largas, el modelo "olvida" el principio cuando se llena la ventana
- Pegar código completo de un proyecto grande puede consumir toda la ventana
- El contexto al inicio y al final del prompt tiene más peso que el del medio (sesgo de posición)

```typescript
// Al usar la API, el context window incluye TODO
const messages = [
  { role: 'system', content: systemPrompt },      // cuenta
  { role: 'user', content: prevMessage },          // cuenta
  { role: 'assistant', content: prevResponse },    // cuenta
  { role: 'user', content: currentMessage },       // cuenta
  // La respuesta también consumirá tokens del budget
];
```

---

## Temperatura y parámetros de sampling

El modelo genera una distribución de probabilidad sobre todos los tokens posibles. Los parámetros de sampling controlan cómo se elige el siguiente token.

### Temperature

Controla la "creatividad" de las respuestas.

```
Temperature = 0.0  → Siempre el token más probable (determinista)
Temperature = 0.5  → Algo de variación, respuestas más enfocadas
Temperature = 1.0  → Distribución original del modelo
Temperature > 1.0  → Muy creativo / aleatorio / incoherente
```

```typescript
// API de Anthropic
const response = await anthropic.messages.create({
  model: 'claude-sonnet-4-6',
  temperature: 0.2,  // para código y tareas precisas: bajo
  // temperature: 0.9, // para brainstorming y creatividad: alto
  messages: [{ role: 'user', content: 'Escribe un test para esta función' }],
});
```

**Regla práctica:**
| Tarea | Temperature recomendada |
|-------|------------------------|
| Código, SQL, análisis | 0.0 – 0.3 |
| Respuestas técnicas | 0.3 – 0.5 |
| Redacción, resúmenes | 0.5 – 0.7 |
| Creatividad, brainstorming | 0.7 – 1.0 |

### Top-P (nucleus sampling)

Considera solo los tokens cuya probabilidad acumulada supera el valor P. `top_p=0.9` significa "considera solo los tokens que juntos suman el 90% de probabilidad".

Generalmente, ajusta temperature O top_p, no ambos.

---

## Embeddings — representación vectorial del significado

Un embedding es la representación numérica de un texto en un espacio de alta dimensión. Textos con significado similar tienen vectores cercanos.

```
"perro"     → [0.12, -0.43, 0.87, ...]  (vector de 1536 dimensiones)
"can"       → [0.11, -0.45, 0.86, ...]  (muy cercano → mismo concepto)
"gato"      → [0.09, -0.38, 0.79, ...]  (algo cercano → animal)
"javascript" → [-0.67, 0.23, -0.12, ...] (muy lejano → concepto diferente)
```

```typescript
// Generar embeddings con la API de OpenAI
import OpenAI from 'openai';

const openai = new OpenAI();

async function getEmbedding(text: string): Promise<number[]> {
  const response = await openai.embeddings.create({
    model: 'text-embedding-3-small',
    input: text,
  });
  return response.data[0].embedding;
}

// Similitud coseno entre dos vectores
function cosineSimilarity(a: number[], b: number[]): number {
  const dotProduct = a.reduce((sum, ai, i) => sum + ai * b[i], 0);
  const magA = Math.sqrt(a.reduce((sum, ai) => sum + ai * ai, 0));
  const magB = Math.sqrt(b.reduce((sum, bi) => sum + bi * bi, 0));
  return dotProduct / (magA * magB);
}

// 1.0 = idénticos, 0.0 = sin relación, -1.0 = opuestos
const similarity = cosineSimilarity(await getEmbedding('perro'), await getEmbedding('can'));
console.log(similarity); // ~0.92
```

**Los embeddings son la base de la búsqueda semántica y RAG.** Ver [[Fundamentos de IA - Agentes y RAG]].

---

## Fine-tuning vs Prompting

Dos formas de adaptar un LLM a una tarea específica:

### Prompting (sin modificar el modelo)

```typescript
// System prompt que define el comportamiento
const systemPrompt = `Eres un asistente de código TypeScript especializado en React.
Respondes solo en español.
Cuando encuentres un bug, explicas primero la causa y luego la solución.
Tus respuestas de código siempre incluyen TypeScript types.`;
```

**Cuándo usarlo:** la mayoría de los casos. Es más rápido, más barato y más flexible que fine-tuning.

### Fine-tuning (ajustar los pesos del modelo)

El modelo se reentrena con ejemplos específicos de tu dominio:

```json
[
  {
    "messages": [
      { "role": "user", "content": "Crea un endpoint de usuarios" },
      { "role": "assistant", "content": "// código con tus convenciones exactas..." }
    ]
  }
]
```

**Cuándo usarlo:**
- Cuando el modelo necesita seguir un formato muy específico de forma consistente
- Cuando el sistema prompt ya está al máximo de tokens
- Para tareas muy repetitivas donde el modelo generalista no llega al nivel requerido
- Cuando quieres reducir el tamaño del prompt en producción a gran escala

> [!WARNING]
> Fine-tuning **no añade conocimiento nuevo** al modelo — solo ajusta su estilo y formato de respuesta. Para que el modelo conozca tu documentación interna, usa RAG.

---

## Limitaciones fundamentales a tener en cuenta

| Limitación | Descripción | Mitigación |
|------------|-------------|------------|
| Hallucination | Genera información plausible pero incorrecta con confianza | Verificar, pedir fuentes, RAG |
| Knowledge cutoff | No conoce eventos después de su fecha de corte | Proporcionar contexto en el prompt |
| No persistencia | No recuerda conversaciones anteriores | Memoria externa, historial explícito |
| Context limit | No puede procesar documentos infinitos | RAG, chunking, resúmenes |
| Reasoning | Comete errores en razonamiento matemático y lógico complejo | Chain of thought, verificación |
| Counting/characters | Dificultad con operaciones a nivel de caracteres | Usar código para estas tareas |

---

## Control de versiones

| Versión | Fecha | Autor | Cambios |
|---------|-------|-------|---------|
| 1.0.0 | 2026-03-30 | Daniel | Creación inicial |
