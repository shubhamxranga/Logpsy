# Logpsy Sample Inputs

Below is a sample incident log sequence representing a cascading Kafka broker failure:

```text
2026-08-14T11:01:11.112Z pod=checkout-api INFO trace=tx-9182 Checkout started

2026-08-14T11:01:12.004Z pod=fraud-engine WARN Kafka consumer lag detected partition=17 lag=44219

2026-08-14T11:01:12.781Z pod=fraud-engine WARN Kafka consumer lag detected partition=17 lag=48802

2026-08-14T11:01:13.115Z pod=kafka-broker-3 ERROR Disk utilization exceeded threshold disk_used_pct=99.7

2026-08-14T11:01:13.211Z pod=kafka-broker-3 ERROR Unable to flush segment topic=fraud-events partition=17

2026-08-14T11:01:13.992Z pod=fraud-engine ERROR Feature generation timeout

2026-08-14T11:01:14.771Z pod=payment-service ERROR Fraud validation failed deadline exceeded

2026-08-14T11:01:15.002Z pod=checkout-api ERROR Checkout failed authorization unavailable
```
