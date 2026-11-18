# warc-gpt

warc-gpt is an experimental Python retrieval-augmented generation (RAG) pipeline for exploring web archive collections. It extracts text from WARC files, indexes it for retrieval, and uses relevant excerpts as context for questions asked through a web UI or REST API.

## Features

- Extract text from HTML and PDF response records in WARC files.
- Split extracted text into chunks, generate embeddings, and store them in a vector store.
- Query archived content through a web UI or REST API.
- Generate responses using OpenAI, Ollama, or OpenAI-compatible providers.
- Customize prompts and model settings through configuration.

## Installation

Install Python 3.11 or later and Poetry. From the project's root directory, run:

```bash
poetry env use 3.11
poetry install
```

Alternatively, install into a virtual environment:

```bash
python3 -m venv env
. env/bin/activate
pip install .
```

If you use a virtual environment instead of Poetry, remove `poetry run ` from the commands below.

## Usage

Copy the example configuration and edit `.env` for your model provider and embedding settings:

```bash
cp .env.example .env
```

At least one model provider must be available. The application supports OpenAI and Ollama and defaults to connecting to Ollama at `http://localhost:11434`. For an OpenAI-compatible provider, configure both `OPENAI_BASE_URL` and `OPENAI_COMPATIBLE_MODEL`. See `.env.example` for individual settings.

Place your WARC files in `./warc`, then ingest them:

```bash
poetry run flask ingest
```

Ingestion extracts text from `text/html` and `application/pdf` response records, generates embeddings, and stores them for retrieval. **Running ingestion clears the `./chromadb` folder**, replacing the existing index.

If needed, process one text chunk at a time:

```bash
poetry run flask ingest --batch-size 1
```

Start the server:

```bash
poetry run flask run
```

Open `http://localhost:5000` and ask a question about your collection. Unless RAG search is disabled in the settings, the application retrieves relevant excerpts from the ingested archives to help answer it. Generated answers can be incorrect; check them against the archived material.

The REST API exposes `GET /api/models`, `POST /api/search`, and `POST /api/complete`. For example, search the indexed collection with:

```bash
curl -X POST http://localhost:5000/api/search \
  -H 'Content-Type: application/json' \
  -d '{"message":"What does this collection say about web archiving?"}'
```

## License

MIT. This project is based on [harvard-lil/warc-gpt](https://github.com/harvard-lil/warc-gpt), which is also licensed under MIT. Retain the upstream copyright and permission notice in copies or substantial portions of its code.
