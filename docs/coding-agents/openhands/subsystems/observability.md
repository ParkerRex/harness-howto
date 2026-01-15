# Observability and Metrics

Legacy V0 and V1 both emit metrics and event logs.

Key files:

- Token usage, cost, latency: `llm/metrics.py`.
- Conversation stats service: `server/services/conversation_stats.py`.
- Monitoring hooks: `server/monitoring.py`.
- V1 event services: `app_server/event/*`.
- Event callbacks/webhooks: `app_server/event_callback/*`.
