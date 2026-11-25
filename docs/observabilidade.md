# Padrão de observabilidade

- Logs: JSON estruturado, campos obrigatórios `timestamp`, `level`, `service`, `env`, `trace_id`, `msg`.
- Métricas: prefixo `branchbox_`, snake_case, unidade no nome (`_seconds`, `_bytes`, `_total`).
- Labels/tags obrigatórios: `service`, `env`, `owner`.
- Traces: OpenTelemetry, propagando contexto entre serviços e filas.
- Retention padrão de log group: 7 dias no lab.
