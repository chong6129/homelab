# Paperless-ngx and Local AI

Status recorded 2026-11-13 from the October 26 continuation notes. These notes
report the following working setup; this documentation pass did not inspect it.

| Component | Recorded state |
|---|---|
| Paperless-ngx | Running in Proxmox CT 104 |
| Ollama | `10.10.10.249:11434` |
| Language model | `qwen3:8b` |
| Embeddings | `mxbai-embed-large:latest` |
| Paperless AI integration | Connected; metadata suggestions working |
| Automatic metadata application | Not yet configured in the available record |

The AI work produced candidate tags, document types, and correspondents, with
some inconsistent suggestions. The next requested step was automatic tagging
using the existing vocabulary and a small test batch. There is no recorded
result proving automatic updates or archive-wide processing.

Follow-ups are to inventory the integration's actual software/configuration,
document persistence and backups, test controlled metadata application, and
verify processing behavior for new documents. Keep document contents, API
tokens, prompts containing personal records, and private metadata outside Git.

This deployment moves Paperless beyond the previous “future service” item;
automatic classification remains a separate unfinished milestone.
