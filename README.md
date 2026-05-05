# Kai ML Notebooks

Notebooks de Google Colab para fine-tuning de modelos LLM — [Kai, la comunidad AI de LATAM](https://kaizen.la).

## Notebooks disponibles

| Notebook | Descripción | Abrir en Colab |
|----------|-------------|----------------|
| [Qwen3.5-4B + Gemma 4 E2B](qwen35_gemma4_finetune_colab.ipynb) | Fine-tuning LoRA con Unsloth en T4 gratuito, export a GGUF | [![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/MartinCalderon-x/kai-ml-notebooks/blob/main/qwen35_gemma4_finetune_colab.ipynb) |

## Requisitos

- Google Colab con GPU T4 (gratuito)
- Para Qwen3.5-4B: ~10GB VRAM (bf16 LoRA)
- Para Gemma 4 E2B: ~8GB VRAM (4-bit LoRA)

## Stack

- [Unsloth](https://github.com/unslothai/unsloth) — fine-tuning 2x más rápido, 70% menos VRAM
- `transformers >= 5.0` — requerido para Qwen3.5
- `trl` — SFTTrainer
- `peft` — LoRA adapters

## Flujo completo

```
Colab T4 (gratuito)          →    Mac M1 16GB
──────────────────                ────────────
1. Cargar modelo Unsloth          4. Cargar GGUF en Ollama
2. Fine-tune LoRA                    ollama create mi-modelo -f Modelfile
3. Export a GGUF (q4_k_m)           ollama run mi-modelo
```

---

*[kaizen.la](https://kaizen.la) — La comunidad AI de Latinoamérica*
