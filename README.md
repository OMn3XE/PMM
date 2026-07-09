# AI Meeting Assistant

Secure offline AI-powered meeting assistant using PMM, WhisperX ASR, and Ollama.

## Architecture

```
User
  -> PMM
  -> whisper-asr (learnedmachine/whisperx-asr-service)
  -> PMM
  -> ollama (qwen3:8b)
  -> PMM
```

## Services

- `pmm`: Web application on `http://localhost:8899`
- `whisper-asr`: Offline transcription and speaker diarization
- `ollama`: Offline OpenAI-compatible text model API

## Project Structure

```
AI-Meeting-Assistant/
├── PMM/
├── docker-compose.yml
├── .env
├── .env.example
└── README.md
```

## Configure

Set your Hugging Face token in the root `.env` file:

```bash
HF_TOKEN=your_huggingface_token
```

PMM runtime configuration lives in `PMM/.env`. The offline defaults are:

```bash
TRANSCRIPTION_BASE_URL=http://whisper-asr:9000/v1
TRANSCRIPTION_CONNECTOR=asr_endpoint
TEXT_MODEL_BASE_URL=http://ollama:11434/v1
TEXT_MODEL_NAME=qwen3:8b
```

## Build

```bash
docker compose config
docker compose build
```

## Run

```bash
docker compose up -d
```

## Pull Qwen3

After the Ollama container is running:

```bash
docker compose exec ollama ollama pull qwen3:8b
```

## Verify

```bash
docker compose ps
docker compose logs pmm
docker compose logs whisper-asr
docker compose logs ollama
docker compose exec pmm python -c "from src.services.transcription.registry import get_registry; r=get_registry(); c=r.get_active_connector(); print(r.get_active_connector_name(), c.base_url, c.health_check())"
docker compose exec pmm python -c "import os, httpx; print(httpx.get(os.environ['TEXT_MODEL_BASE_URL'].rstrip('/') + '/models', timeout=10).status_code)"
```
