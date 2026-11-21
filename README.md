# controller-2-export

Controller 🎮 2 Export Repository — provenance-aware, redacted AntiGravity Device 2 exports.

## Ingestion state

- Repository provisioning: complete; personal GitHub account `timerloggedout-spec`; public repository.
- Export payload ingestion: pending first sanitized device-side export.
- Capture cadence: device-side incremental export; GitHub Actions does not have access to the local AntiGravity runtime databases or live device filesystem.
- Publication: append-only commits on `main`; immutable source snapshots and receipts.
- Credential boundary: publish with the device's authorized GitHub credential or a narrowly scoped repository credential. Record the credential *name/reference* and authenticated owner identity; keep token values out of files, logs, artifacts, and receipts.

## Source surfaces observed

The supplied device inventory reports:
- `conversation_summaries.db` with 14 conversation summary rows.
- Per-conversation SQLite databases containing `trajectory_meta`, `steps`, `gen_metadata`, and `executor_metadata`.
- `history.jsonl`, `transcript.jsonl`, and `transcript_full.jsonl` exports.

The inventory is structural evidence only. It does not include the full source payloads. A first ingestion must carry the original source hashes, capture timestamp, source-relative paths, row counts, parser version, redaction counts, and a deterministic receipt.

## Contracts

- [Ingestion and provenance contract](docs/INGESTION-CONTRACT.md)
- [Export receipt schema](schemas/export-receipt-v1.schema.json)

Raw device databases and unredacted transcripts remain outside this public repository. Publish only reviewed, redacted derivatives.
