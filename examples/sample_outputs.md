# Logpsy Sample Outputs

Below is the structured, cautious root-cause analysis output returned by `logpsy-7b-v2` for the Kafka broker failure incident:

```json
{
  "decision": "insufficient_telemetry",
  "confidence": 0.7,
  "incident_summary": "Checkout failures observed after fraud-engine timeout and kafka broker disk pressure.",
  "observed_facts": [
    "fraud-engine feature generation timed out",
    "kafka-broker-3 flush failed due to disk pressure",
    "payment-service authorization unavailable from fraud validation"
  ],
  "symptoms": [
    "checkout-api request failures",
    "customer-facing payment errors",
    "authorization latency above SLO"
  ],
  "likely_causes": [
    "fraud-engine timeout caused by kafka consumer lag and broker disk pressure",
    "payment-service authorization unavailable from fraud validation failure"
  ],
  "ruled_out": [
    "database lock contention or query duration elevated in checkout-api",
    "checkout-api request queue depth increasing"
  ],
  "missing_telemetry": [
    "checkout-api request queue metrics",
    "db connection pool wait histograms",
    "payment-service authorization latency by upstream",
    "fraud-engine kafka consumer lag over time"
  ],
  "next_checks": [
    "verify checkout-api request queue depth and worker saturation",
    "check payment-service authorization latency from fraud validation",
    "review db logs for lock contention or query duration",
    "monitor kafka broker disk pressure and replica health"
  ],
  "operator_risk": "Do not assume database lock contention is the root cause. Verify causality before taking action against checkout-api queues.",
  "abstention_reason": "Insufficient telemetry distinguishes between checkout queue saturation and fraud-engine/db impact."
}
```
