# Exercise: Telemetry/Event Ingestion

Design an ingestion pipeline for high-volume events. Clarify event size/rate, peak factors, ordering, schema evolution, retention, query latency, sampling and data-loss tolerance. Estimate throughput/storage before choosing a stream or queue.

**First task:** estimate event volume and identify the backpressure boundary.

**Later constraints:** producers outpace consumers; schema change breaks readers; storage cost grows unexpectedly.
