# Pix ETL Pipeline

High-volume ETL for Brazilian Pix transaction data — ingestion, validation, reconciliation and antifraud cross-checks, built to the patterns used while processing millions of events per day in regulated payments.

> **Note:** a from-scratch reference build. No production data, credentials or proprietary code are included — the repo demonstrates the pipeline design and data-quality strategy.

## Architecture

```
Pix events (JSON lines) → ingest → validate → reconcile → marts
                            │         │           │
                            │     quality        │
                            │      gates         │
                         raw zone         recon report
```

- **Ingest** — idempotent batch loading with duplicate detection (event id + retry window).
- **Validate** — schema + business rules (amounts, timestamps, CNPJ format, check digits); rejects land in a quarantine table with reasons.
- **Reconcile** — end-of-day balance: entries vs exits vs reference totals, with tolerance rules and break report.
- **Marts** — aggregate tables by day/counterparty for fraud and finance queries.

## Stack

Python · PySpark (batch) · PostgreSQL · pytest

## Data

A generator script creates synthetic Pix events (deterministic seed) so the whole pipeline runs locally with zero credentials.

```bash
python -m scripts.generate_data --events 100000
python -m pipeline.run
pytest
```

## Status

Ingestion, validation gates, reconciliation and synthetic data generator implemented and tested.
