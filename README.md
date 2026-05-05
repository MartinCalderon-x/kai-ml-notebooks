# Kai ML Notebooks

Notebooks de Google Colab para fine-tuning de modelos LLM — [Kai, la comunidad AI de LATAM](https://kaizen.la).

---

## Qué es esto

Un notebook que te permite **entrenar tu propio modelo de lenguaje** en Google Colab gratuito y usarlo offline en tu Mac. No necesitás pagar nada, no necesitás una GPU propia.

El resultado final es un archivo `.gguf` que podés correr localmente con Ollama o Unsloth Studio, sin internet, sin APIs, sin costos por token.

---

## Notebooks disponibles

| Notebook | Modelos | Abrir en Colab |
|----------|---------|----------------|
| [Fine-tuning con Unsloth](qwen35_gemma4_finetune_colab.ipynb) | Qwen3.5-4B · Gemma 4 E2B | [![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/MartinCalderon-x/kai-ml-notebooks/blob/main/qwen35_gemma4_finetune_colab.ipynb) |

---

## Lo que entrenamos

En la primera corrida del notebook usamos:

| Parámetro | Valor |
|-----------|-------|
| **Modelo base** | Qwen3.5-4B (Alibaba, Feb 2026) |
| **Técnica** | LoRA — r=16, alpha=16 |
| **Dataset** | [LAION OIG chip2](https://huggingface.co/datasets/laion/OIG) — 500 ejemplos de instrucción en inglés |
| **Pasos** | 100 steps (~5 min en T4) |
| **Hardware** | Google Colab T4 gratuito (15GB VRAM) |
| **Export** | GGUF q4_k_m (~2.5GB) |

> **Nota:** 100 pasos con el dataset demo es suficiente para verificar que todo el pipeline funciona. Para un modelo con comportamiento real diferenciado necesitás tu propio dataset y al menos 500-1000 pasos.

---

## Antes y después del fine-tuning

### Con el modelo base (sin entrenar)

```
Pregunta: ¿Cuál es la capital de Francia?
Respuesta: La capital de Francia es París.
```

El modelo base sabe mucho, pero responde de forma genérica y no tiene ningún estilo ni contexto particular.

### Con fine-tuning en chip2 (dataset demo)

```
Human: What are some ways to improve my writing skills?
Assistant: There are several ways to improve your writing skills:
1. Read widely across different genres...
2. Write every day, even if just a journal entry...
3. Get feedback from others...
```

El modelo sigue el formato instrucción → respuesta del dataset chip2. Con tu propio dataset en español, el modelo adoptaría tu tono, vocabulario y dominio específico.

### Con tu propio dataset (el caso real)

Si entrenás con datos propios — soporte técnico, documentación interna, conversaciones de tu producto — el modelo aprende a responder como tu equipo lo haría:

```
Usuario: ¿Cómo configuro el webhook de notificaciones?
Asistente: Para configurar el webhook, vas a Settings → Integrations → Webhooks.
           Pegá tu URL y seleccioná los eventos que querés recibir...
```

---

## Flujo completo

```
COLAB T4 (gratuito)                     MAC M1/M2/M3
───────────────────                     ─────────────
Paso 0: Verificar GPU
Paso 1: Instalar Unsloth (git nightly)
Paso 2: Importar Unsloth primero
Paso 3: Cargar Qwen3.5-4B o Gemma 4
Paso 4: Elegir dataset
Paso 5: Entrenar (~5 min, 100 steps)
Paso 6: Testear inferencia
Paso 7: Descargar LoRA + instalar          Recibís el .gguf en Descargas
        llama.cpp + exportar GGUF    →
                                           ollama create mi-modelo -f Modelfile
                                           ollama run mi-modelo
```

---

## Cómo usar el GGUF descargado

Una vez que tenés el `.gguf` en tu Mac:

**Con Ollama:**
```bash
# Crear el Modelfile
echo 'FROM ./kai-qwen35-finetuned-q4_k_m.gguf' > Modelfile

# Registrar el modelo
ollama create kai-qwen35 -f Modelfile

# Correrlo
ollama run kai-qwen35
```

**Con Unsloth Studio** (interfaz visual, sin código):

Abrí [Unsloth Studio](https://unsloth.ai/docs/new/studio), cargá el `.gguf` y chateá directo.

---

## Cómo entrenar con tus propios datos

Creá un archivo `mi-dataset.jsonl` con el formato:

```jsonl
{"text": "### Instrucción:\n¿Cómo reseteo mi contraseña?\n\n### Respuesta:\nPodés resetear tu contraseña desde Configuración → Cuenta → Cambiar contraseña."}
{"text": "### Instrucción:\nExplicá qué es un webhook.\n\n### Respuesta:\nUn webhook es una URL que recibe notificaciones automáticas cuando ocurre un evento en otro sistema."}
```

En el Paso 4 del notebook elegí la **Opción B**, subí tu archivo desde Google Drive y cambiá `OPCION_DATASET = "B"`.

---

## Stack

| Librería | Para qué |
|----------|----------|
| [Unsloth](https://github.com/unslothai/unsloth) (MIT) | Fine-tuning 2x más rápido, 70% menos VRAM |
| `transformers >= 5.0` | Requerido para Qwen3.5 |
| `trl` + `peft` | SFTTrainer + LoRA adapters |
| `bitsandbytes` + `xformers` | Quantización y atención eficiente |
| `llama.cpp` | Conversión a formato GGUF |

---

## Problemas conocidos y soluciones

| Error | Causa | Solución |
|-------|-------|----------|
| `ImportError: _unsloth_get_mm_token_id` | PyPI de unsloth_zoo desactualizado | El notebook instala desde git (ya resuelto) |
| `ModuleNotFoundError: bitsandbytes` | Dependencia no instalada por git install | El notebook lo instala explícitamente (ya resuelto) |
| `RuntimeError: No config file found` | Nombre de modelo incorrecto | Usar `unsloth/Qwen3.5-4B` sin `-Instruct` |
| `TypeError: string indices must be integers` | Qwen3.5 usa Processor multimodal | El notebook usa `inner_tok` (ya resuelto) |
| `RuntimeError: You do not have internet connection` | llama.cpp no instalado | El Paso 7 lo instala antes de exportar |
| `NameError: MODELO is not defined` | Kernel reiniciado | Correr desde Paso 1 o redefinir variables |

---

*[kaizen.la](https://kaizen.la) — La comunidad AI de Latinoamérica · Notebook bajo [MIT License](LICENSE)*
