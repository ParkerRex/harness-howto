# Observability and Metrics

Legacy V0 and V1 both emit metrics and event logs.

Key files:

- Token usage, cost, latency: `openhands/llm/metrics.py`.
- Conversation stats service: `openhands/server/services/conversation_stats.py`.
- Monitoring hooks: `openhands/server/monitoring.py`.
- V1 event services: `openhands/app_server/event/*`.
- Event callbacks/webhooks: `openhands/app_server/event_callback/*`.
