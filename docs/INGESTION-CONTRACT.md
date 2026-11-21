# AntiGravity Device 2 export — ingestion and provenance contract

## Purpose

Store reviewed, redacted derivatives from the personal AntiGravity Device 2 environment in this dedicated public repository. The source of truth remains on the device until an export snapshot passes validation and redaction.

## Capture model

**Capture occurs on the device.** GitHub Actions runs in GitHub's hosted environment and cannot observe the live local AntiGravity runtime. The device-side exporter watches or periodically scans the local source surfaces, then publishes newly observed records. A scheduled GitHub workflow alone would provide scheduled ingestion only if the device first uploads a source artifact; it does not create real-time access to local files.

Initial source inventory supplied for this project:
- `history.jsonl`
- `brain/*/.system_generated/logs/transcript.jsonl`
- `brain/*/.system_generated/logs/transcript_full.jsonl`
- `conversation_summaries.db`
- per-conversation `brain/*/*.db` databases

Observed SQLite structures include `conversation_summaries`, `trajectory_meta`, `steps`, `gen_metadata`, and `executor_metadata`. The supplied inventory reports 14 summary rows and multiple trajectory databases. These counts describe the supplied scan, not a continuously refreshed total.

## Incremental append protocol

1. Enumerate source files and record relative path, byte length, modified time, and SHA-256.
2. Parse JSONL line-by-line and SQLite tables read-only.
3. Assign stable source identity: source-relative path + table/record key or JSONL line identity.
4. Normalize records while retaining original source identity and source hash.
5. Redact credential-shaped values and private material according to the export allowlist before serialization.
6. Deduplicate against previously published source identities and content hashes.
7. Append new sanitized records under a dated snapshot directory.
8. Publish a receipt containing counts, hashes, parser/redactor versions, and the authenticated publisher identity.
9. Verify the resulting commit and receipt from GitHub before advancing the local checkpoint.

The exporter uses a durable local checkpoint. It advances the checkpoint only after GitHub confirms the commit, making retries idempotent and preventing silent gaps.

## Provenance and PAT attribution

The repository provisioner is a separate control-plane operation. The source workflow uses the existing `OPERATOR_GITHUB_TOKEN` Actions secret, validates `gh api user` as the personal account `timerloggedout-spec`, creates the public repository through `POST /user/repos`, and records the returned repository metadata in the Actions summary.

For export publication, record:
- `publisher_login`: authenticated GitHub login returned by `GET /user`;
- `credential_ref`: secret/keyring name only (for example, `OPERATOR_GITHUB_TOKEN`); 
- `credential_class`: owner PAT, fine-grained PAT, or GitHub Actions token;
- `workflow_run_id` and `workflow_run_url`, when publication occurs in Actions;
- `source_device_id` and exporter version;
- source file hashes and resulting commit SHA.

A PAT authenticates as its owner. GitHub commit authorship alone therefore cannot distinguish the human or agent that initiated work while sharing that PAT. Preserve the initiating actor separately from `publisher_login`. Store token values only in GitHub Actions Secrets or the device keyring. Receipts and logs contain names/references and identity metadata only.

## Public-data boundary

The repository is public. Publish reviewed, redacted derivatives only. Keep raw SQLite databases, raw transcript exports, private reasoning, credentials, personal identifiers, and unreviewed message bodies in device-local or private vault storage. Preserve source hashes and redaction counts so the public derivative remains auditable without exposing the raw source.

## Current execution state

- Repository provisioning: complete.
- Contract publication: initialized here.
- Live device watcher: pending device-side installation and execution.
- First source payload: pending sanitized export upload.
- Continuous ingestion: pending observed device-side run and successful commit receipt.

A source inventory or a configured workflow is not evidence of a live ingestion run.
