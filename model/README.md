# Logpsy Model Details

The Logpsy model is fine-tuned for structured, cautious SRE incident analysis.

## Specifications
- **Base Model:** `Qwen2.5-Coder-7B-Instruct`
- **Tuning Method:** PEFT LoRA (LoRA rank=16, alpha=16) on H100 NVL
- **Quantization Format:** `Q4_K_M` GGUF

## Ollama Integration

### 1. Register the Model
Create the model using the Modelfile config:
```bash
ollama create logpsy-7b-v2 -f Modelfile
```

### 2. Run Locally
Start interactive mode:
```bash
ollama run logpsy-7b-v2
```

## Prompt Schema
Inputs should use the standard system prompt and pass the raw incident logs as user content. The model is trained to output JSON only matching the schema:
- `decision` (e.g. `insufficient_telemetry`, `cause_identified`)
- `confidence`
- `incident_summary`
- `observed_facts`
- `symptoms`
- `likely_causes`
- `ruled_out`
- `missing_telemetry`
- `next_checks`
- `operator_risk`
- `abstention_reason`
