---
title: "Fundamentos de IA — Agentes, Tools, RAG y arquitecturas"
date: 2026-03-30
lastUpdated: 2026-03-30
tags:
  - ia
  - agentes
  - rag
  - tools
  - function-calling
  - vector-db
  - fundamentos
category: "00 Fundamentos"
subcategory: ""
author: Daniel
version: 1.0.0
status: active
related:
  - "[[Fundamentos de IA - LLMs]]"
  - "[[Prompting avanzado]]"
  - "[[OpenCode]]"
  - "[[Skills en Claude Code]]"
---

# Fundamentos de IA — Agentes, Tools, RAG y arquitecturas

Más allá de responder preguntas, los LLMs modernos pueden tomar acciones, consultar fuentes externas y ejecutar tareas de múltiples pasos de forma autónoma. Estas capacidades son la base de sistemas como Claude Code, OpenCode y cualquier agente IA personalizado.

---

## De chatbot a agente

Un chatbot clásico recibe un mensaje y devuelve texto. Un agente añade un bucle de razonamiento y acción:

```
Chatbot:
  Input → [LLM] → Output (texto)

Agente:
  Goal → [LLM: planificar] → [Tool: ejecutar] → [LLM: observar] → [Tool: ...] → Output
           ↑_________________________↓
                  loop hasta completar
```

El LLM actúa como el "cerebro" que decide qué herramientas usar y en qué orden. Las herramientas son los "brazos" que interactúan con el mundo.

---

## Tool Use / Function Calling

La capacidad de llamar funciones externas es lo que convierte un LLM en un agente. El modelo puede solicitar ejecutar una función y recibir su resultado para continuar razonando.

```typescript
// Definir las tools disponibles para el modelo
const tools = [
  {
    name: 'get_weather',
    description: 'Get current weather for a city',
    input_schema: {
      type: 'object',
      properties: {
        city: { type: 'string', description: 'City name' },
        units: { type: 'string', enum: ['celsius', 'fahrenheit'] },
      },
      required: ['city'],
    },
  },
  {
    name: 'search_web',
    description: 'Search the web for recent information',
    input_schema: {
      type: 'object',
      properties: {
        query: { type: 'string', description: 'Search query' },
      },
      required: ['query'],
    },
  },
];

// El modelo decide cuándo y cómo usar las tools
const response = await anthropic.messages.create({
  model: 'claude-sonnet-4-6',
  tools,
  messages: [{ role: 'user', content: '¿Qué tiempo hace en Madrid ahora?' }],
});

// Si el modelo quiere usar una tool, devuelve tool_use en lugar de text
if (response.stop_reason === 'tool_use') {
  const toolUse = response.content.find(b => b.type === 'tool_use');

  // Ejecutar la función real
  const weatherData = await getWeather(toolUse.input.city);

  // Devolver el resultado al modelo para que continúe
  const finalResponse = await anthropic.messages.create({
    model: 'claude-sonnet-4-6',
    tools,
    messages: [
      { role: 'user', content: '¿Qué tiempo hace en Madrid ahora?' },
      { role: 'assistant', content: response.content },
      {
        role: 'user',
        content: [{
          type: 'tool_result',
          tool_use_id: toolUse.id,
          content: JSON.stringify(weatherData),
        }],
      },
    ],
  });
}
```

---

## El bucle ReAct (Reason + Act)

El patrón de razonamiento más común en agentes. El modelo alterna entre pensar y actuar:

```
Thought: Necesito saber el tiempo actual en Madrid.
Action: get_weather({ city: "Madrid", units: "celsius" })
Observation: { temp: 22, condition: "sunny", humidity: 45 }

Thought: Tengo los datos. Puedo responder al usuario.
Answer: "En Madrid ahora mismo hay 22°C con cielo despejado..."
```

```typescript
// Implementación del loop ReAct
async function runAgent(goal: string, tools: Tool[]): Promise<string> {
  const messages: Message[] = [{ role: 'user', content: goal }];

  while (true) {
    const response = await llm.complete({ messages, tools });

    if (response.stop_reason === 'end_turn') {
      // El agente terminó — devolver la respuesta final
      return response.content.find(b => b.type === 'text')?.text ?? '';
    }

    if (response.stop_reason === 'tool_use') {
      // Ejecutar cada tool solicitada
      messages.push({ role: 'assistant', content: response.content });

      const toolResults = await Promise.all(
        response.content
          .filter(b => b.type === 'tool_use')
          .map(async (toolUse) => ({
            type: 'tool_result' as const,
            tool_use_id: toolUse.id,
            content: JSON.stringify(await executeTool(toolUse.name, toolUse.input)),
          }))
      );

      messages.push({ role: 'user', content: toolResults });
    }
  }
}
```

---

## RAG — Retrieval-Augmented Generation

El problema del knowledge cutoff: el LLM no sabe nada de tu documentación interna, tu base de código, ni eventos recientes. RAG resuelve esto recuperando información relevante en tiempo real y añadiéndola al contexto.

```
Sin RAG:
  "¿Cómo funciona nuestro sistema de autenticación?"
  → El modelo no sabe nada de tu sistema → hallucina o dice "no sé"

Con RAG:
  1. Convertir la pregunta en embedding
  2. Buscar chunks similares en la vector DB (tu documentación)
  3. Inyectar los chunks relevantes en el prompt
  4. El modelo responde con información real de tu sistema
```

### Pipeline RAG básico

```typescript
// FASE 1: Indexación (se hace una vez o cuando cambia el contenido)

async function indexDocument(text: string, metadata: Record<string, string>) {
  // Dividir en chunks manejables
  const chunks = splitIntoChunks(text, { maxTokens: 500, overlap: 50 });

  for (const chunk of chunks) {
    // Generar embedding del chunk
    const embedding = await getEmbedding(chunk);

    // Guardar en vector DB con metadatos
    await vectorDB.upsert({
      id: generateId(),
      values: embedding,
      metadata: { ...metadata, text: chunk },
    });
  }
}

// Indexar toda la documentación
await indexDocument(fs.readFileSync('docs/auth.md', 'utf-8'), { source: 'auth-docs' });
await indexDocument(fs.readFileSync('docs/api.md', 'utf-8'),  { source: 'api-docs' });
```

```typescript
// FASE 2: Recuperación y generación (en cada query)

async function ragQuery(question: string): Promise<string> {
  // 1. Embedding de la pregunta
  const questionEmbedding = await getEmbedding(question);

  // 2. Buscar los chunks más similares en la vector DB
  const relevantChunks = await vectorDB.query({
    vector: questionEmbedding,
    topK: 5,
    includeMetadata: true,
  });

  // 3. Construir el contexto con los chunks recuperados
  const context = relevantChunks.matches
    .map(m => `[Fuente: ${m.metadata.source}]\n${m.metadata.text}`)
    .join('\n\n---\n\n');

  // 4. Prompt aumentado con el contexto
  const response = await anthropic.messages.create({
    model: 'claude-sonnet-4-6',
    messages: [{
      role: 'user',
      content: `Responde la siguiente pregunta basándote SOLO en el contexto proporcionado.
Si la respuesta no está en el contexto, dilo explícitamente.

<context>
${context}
</context>

<question>
${question}
</question>`,
    }],
  });

  return response.content[0].type === 'text' ? response.content[0].text : '';
}
```

---

## Vector Databases

Bases de datos optimizadas para buscar por similitud de embeddings en alta dimensión.

| DB | Tipo | Mejor para |
|----|------|-----------|
| **Pinecone** | Cloud managed | Producción rápida, sin infra |
| **Weaviate** | Self-hosted / Cloud | Open source, rich features |
| **pgvector** | Extensión PostgreSQL | Ya tienes Postgres, escala media |
| **Chroma** | Local / embebido | Desarrollo local, prototipado |
| **Qdrant** | Self-hosted / Cloud | Alta performance, open source |

```typescript
// pgvector — si ya usas PostgreSQL no necesitas otro servicio
// Esquema
CREATE TABLE documents (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  content TEXT NOT NULL,
  metadata JSONB,
  embedding VECTOR(1536)  -- dimensión del modelo de embedding
);

CREATE INDEX ON documents USING ivfflat (embedding vector_cosine_ops);

-- Búsqueda por similitud
SELECT content, metadata,
       1 - (embedding <=> $1) AS similarity
FROM documents
ORDER BY embedding <=> $1
LIMIT 5;
```

---

## Tipos de agentes y cuándo usarlos

### Agente de un solo paso (simple tool call)

```
User query → LLM selecciona tool → Tool ejecuta → LLM responde
```

Útil para: búsquedas, consultas de datos, transformaciones simples.

### Agente multi-paso (ReAct loop)

```
Goal → [Reason → Act → Observe] × N → Final answer
```

Útil para: tareas complejas que requieren múltiples pasos, como Claude Code o OpenCode.

### Agente multi-paso con memoria

```typescript
// Memoria persistente entre sesiones
const memory = {
  // Memoria de corto plazo: conversación actual (context window)
  shortTerm: messages,

  // Memoria de largo plazo: hechos importantes guardados externamente
  longTerm: await memoryStore.getRelevant(currentQuery),

  // Memoria de trabajo: estado actual de la tarea
  working: currentTaskState,
};
```

### Agentes en paralelo (multi-agent)

```
Orchestrator Agent
    │
    ├── Research Agent (busca información)
    ├── Analysis Agent (analiza datos)
    └── Writer Agent (redacta el output)
```

Útil para tareas complejas que se pueden dividir en subproblemas independientes. El riesgo: complejidad, costos y errores que se propagan entre agentes.

---

## Guardrails y fiabilidad

Los agentes cometen errores. Diseña para fallos:

```typescript
// 1. Límite de iteraciones — evitar loops infinitos
const MAX_ITERATIONS = 10;
let iterations = 0;

while (iterations < MAX_ITERATIONS) {
  iterations++;
  // ...
}

// 2. Timeout por operación
const result = await Promise.race([
  runAgent(goal),
  new Promise((_, reject) =>
    setTimeout(() => reject(new Error('Agent timeout')), 30_000)
  ),
]);

// 3. Validar outputs del agente antes de ejecutar acciones destructivas
if (toolCall.name === 'delete_record') {
  const confirmed = await requireHumanConfirmation(toolCall.input);
  if (!confirmed) throw new Error('Action cancelled by human');
}

// 4. Logging de cada paso para debuggear
logger.info('Tool called', { tool: toolCall.name, input: toolCall.input });
logger.info('Tool result', { tool: toolCall.name, output: result });
```

> [!IMPORTANT]
> Para acciones irreversibles (eliminar datos, enviar emails, ejecutar pagos), implementa siempre una confirmación humana o un mecanismo de dry-run. Los agentes pueden malinterpretar instrucciones ambiguas.

---

## Control de versiones

| Versión | Fecha | Autor | Cambios |
|---------|-------|-------|---------|
| 1.0.0 | 2026-03-30 | Daniel | Creación inicial |
