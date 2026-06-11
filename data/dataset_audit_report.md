# Logpsy Dataset Quality Audit Report

This report presents a rigorous audit of the `logpsy_gold_dataset.jsonl` and `logpsy_multitask_training_dataset.jsonl` files.

---

## 1. Programmatic Structural Statistics (Phase 1)

- **Total Gold Incidents:** 5,000
- **Total Multitask SFT Samples:** 35,000
- **Average Sample Size (serialized):** 16652.8 bytes
- **Average Log Input Length:** 15980.8 characters
- **Average Answer Output Length:** 203.1 characters
- **Empty Fields Found:** 5
- **Malformed JSON Records:** 0
- **Exact Duplicate Samples:** 4

### Task Type Distribution
- **Root Cause Identification:** 5,000 samples (14.3%)
- **Evidence Extraction:** 5,000 samples (14.3%)
- **Symptom Separation:** 5,000 samples (14.3%)
- **Causal Chain Construction:** 5,000 samples (14.3%)
- **Multiple Choice RCA:** 5,000 samples (14.3%)
- **Hard Negative RCA:** 5,000 samples (14.3%)
- **Insufficient Evidence:** 5,000 samples (14.3%)

---

## 2. Tech Stack Distribution Analysis (Phase 7)
- **Docker:** 366 incidents (7.3%)
- **Apache Kafka:** 456 incidents (9.1%)
- **Elasticsearch:** 274 incidents (5.5%)
- **Microservices/Other:** 2,097 incidents (41.9%)
- **RabbitMQ:** 312 incidents (6.2%)
- **Kubernetes:** 205 incidents (4.1%)
- **Nginx:** 389 incidents (7.8%)
- **Prometheus/Grafana:** 124 incidents (2.5%)
- **MongoDB:** 243 incidents (4.9%)
- **Redis:** 454 incidents (9.1%)
- **PostgreSQL:** 80 incidents (1.6%)

---

## 3. Top 50 Most Common Root Causes (Phase 3)

| # | Root Cause | Frequency |
|---|---|---|
| 1 | `Elasticsearch hot-tier disk exhaustion` | 51 |
| 2 | `/var/lib/docker inode exhaustion` | 28 |
| 3 | `RabbitMQ quorum WAL disk exhaustion` | 11 |
| 4 | `checkout-api unbounded metric labels` | 9 |
| 5 | `etcd backend quota exhaustion` | 8 |
| 6 | `Nginx variable proxy_pass bypassed upstream keepalive` | 8 |
| 7 | `Inactive Debezium logical slot retained WAL` | 8 |
| 8 | `PostgreSQL data volume exhaustion` | 8 |
| 9 | `NVMe media failure` | 7 |
| 10 | `Unbounded Docker json-file logs filled /var/lib/docker` | 7 |
| 11 | `NVMe write I/O failure` | 7 |
| 12 | `nvme1n1 write I/O failure` | 7 |
| 13 | `checkout-api high-cardinality histogram labels` | 6 |
| 14 | `Prometheus TSDB disk exhaustion` | 6 |
| 15 | `Redis AOF rewrite cgroup OOM` | 6 |
| 16 | `BuildKit cache inode exhaustion` | 6 |
| 17 | `rabbit@rmq-2 disk exhaustion` | 6 |
| 18 | `Docker data-root inode exhaustion` | 6 |
| 19 | `Redis AOF rewrite memory/disk exhaustion` | 6 |
| 20 | `Redis persistence memory/disk exhaustion` | 5 |
| 21 | `Inactive logical replication slot retained WAL` | 5 |
| 22 | `Redis AOF rewrite fork memory exhaustion` | 5 |
| 23 | `Stalled Debezium logical slot WAL retention` | 5 |
| 24 | `Redis AOF rewrite COW memory exhaustion` | 5 |
| 25 | `Redis BGSAVE fork memory exhaustion` | 5 |
| 26 | `Docker /var/lib/docker inode exhaustion` | 5 |
| 27 | `ILM rollover alias misconfiguration` | 5 |
| 28 | `rabbitmq-2 disk exhaustion` | 4 |
| 29 | `Inactive Debezium logical slot WAL retention` | 4 |
| 30 | `rmq-2 NVMe write I/O failure` | 4 |
| 31 | `Inactive logical replication slots retained WAL` | 4 |
| 32 | `Docker json-file log rotation disabled` | 4 |
| 33 | `Elasticsearch hot-node disk exhaustion` | 4 |
| 34 | `rabbitmq-2 NVMe write I/O failure` | 4 |
| 35 | `Unbounded Docker json-file logs exhausted /var/lib/docker` | 4 |
| 36 | `Redis AOF rewrite CoW memory exhaustion` | 4 |
| 37 | `/var/lib/docker filesystem exhaustion` | 4 |
| 38 | `/var/lib/docker block+inode exhaustion` | 4 |
| 39 | `RabbitMQ PVC disk exhaustion` | 3 |
| 40 | `Docker devicemapper thin-pool metadata exhaustion` | 3 |
| 41 | `rmq-prod-2 disk exhaustion` | 3 |
| 42 | `Unbounded Docker json-file logging` | 3 |
| 43 | `Stale Debezium logical slot retaining WAL` | 3 |
| 44 | `Expired Elasticsearch transport TLS certificate` | 3 |
| 45 | `orders-events dynamic mapping explosion` | 3 |
| 46 | `kube-state-metrics wildcard pod annotation allowlist` | 3 |
| 47 | `rabbit@rmq-1 disk exhaustion` | 3 |
| 48 | `RabbitMQ storage exhaustion` | 3 |
| 49 | `payment-api unbounded metric labels` | 3 |
| 50 | `Redis persistence resource exhaustion` | 3 |

---

## 4. Cross-Dataset Consistency Audit (Phase 9)

- **Exact Matches:** 5,000 (100.00%)
- **Mismatches:** 0 (0.00%)
- **Target Threshold:** < 1.00% corruption rate
- **Status:** [PASS] ✅

### Programmatic Mismatch Samples (First 5):
- None. All multitask examples perfectly matched the gold dataset.

---

## 5. Qualitative LLM Audit Findings (Phases 2, 3, 4, 5, 6, 8)

## 1. Executive Summary

The dataset shows strong domain intent and covers useful RCA subtasks: root cause identification, evidence extraction, symptom/root-cause separation, causal chain construction, multiple choice RCA, hard negatives, and insufficient-evidence behavior. The labels are generally SRE-realistic and use plausible production failure modes: Kafka log-dir failures, XFS/NVMe errors, etcd NOSPACE, Postgres WAL retention, conntrack misconfiguration, Nginx temp-dir policy issues, and Prometheus PV exhaustion.

However, the biggest issue is **grounding**. The provided inputs are explicitly described as the “first 300 chars” of the log snippet, while many outputs contain highly specific root causes, device names, partition counts, topic names, queue contents, and causal details that are **not visible in the supplied input**. If the model is actually trained on only these 300-character snippets, the dataset will teach the model to hallucinate specific RCAs from weak prefixes. If the full logs exist elsewhere in the actual training records, then the sample presentation is misleading, and the audit cannot fully verify correctness from the visible input.

Primary risks:

- **High hallucination risk from ungrounded specificity.**
  - Examples: `broker-7 nvme2n1 failure`, `etcd NVMe write I/O failure`, `Duplicate Kafka brokerId from reused meta.properties`, `Terraform lowered conntrack sysctls`.
  - These are plausible but not demonstrated in the visible snippets.

- **Evidence extraction task is underspecified.**
  - The instruction says “Extract evidence supporting the root cause” but does not provide the root cause as an input.
  - This forces the model to infer the RCA and evidence simultaneously, increasing leakage and hallucination risk.

- **Root cause labels are often good in form but unverifiable from the presented input.**
  - They are specific and mostly not symptoms.
  - But they often require hardware/kernel/storage/config logs not present in the first 300 chars.

- **Causal chain outputs are high-density and operationally useful but may overfit to synthetic-looking incident templates.**
  - They include exact counts and topic names.
  - Good for teaching structure, risky if not explicitly grounded.

- **Insufficient evidence examples are useful but underrepresented.**
  - The dataset correctly includes low-confidence labels.
  - More ambiguous and partially-evidenced cases are needed to calibrate abstention.

Overall, this dataset is promising but not training-ready without stricter grounding controls, full-input verification, evidence-span alignment, and clearer task schemas.

---

## 2. Quality Scores

Scoring convention:

- For **correctness, consistency, information density, instruction clarity**: `1 = poor`, `10 = excellent`.
- For **hallucination risk and ambiguity**: `1 = low risk/low ambiguity`, `10 = high risk/high ambiguity`.

| Task Category | Correctness | Consistency | Information Density | Hallucination Risk | Ambiguity | Instruction Clarity | Notes |
|---|---:|---:|---:|---:|---:|---:|---|
| Root Cause Identification | 6 | 7 | 7 | 8 | 6 | 8 | Labels are plausible and specific, but visible snippets do not ground device-level or storage-level RCAs. |
| Evidence Extraction | 7 | 7 | 9 | 6 | 7 | 5 | Evidence strings are strong if present in full logs, but the root cause is not provided and evidence is not visible in snippets. |
| Symptom Separation | 7 | 8 | 9 | 7 | 5 | 8 | Output structure is good. RCAs are specific and symptoms are well separated, but many details are unverifiable from visible input. |
| Causal Chain Construction | 7 | 8 | 9 | 7 | 5 | 8 | Chains are operationally useful and causal, but risk over-teaching exact synthetic chains and ungrounded specificity. |
| Multiple Choice RCA | 7 | 8 | 7 | 6 | 5 | 9 | Choices are realistic. Correct labels are plausible, but first 300 chars often make distractors look more directly supported. |
| Hard Negative RCA | 7 | 8 | 7 | 5 | 4 | 9 | Distractors are realistic and mostly distinguishable. Needs more evidence visibility to verify final labels. |
| Insufficient Evidence | 8 | 8 | 5 | 3 | 4 | 8 | Good abstention format. Needs more examples and more varied uncertainty levels. |

---

## 3. Root Cause Quality

### Overall assessment

The root cause labels are mostly **specific**, **operationally plausible**, and generally **not mere symptoms**. This is a strength. The dataset avoids many common weak RCA labels like “latency,” “timeout,” or “service unavailable.” Instead, it uses labels such as:

- `etcd NVMe write I/O failure`
- `broker-7 nvme2n1 failure`
- `Duplicate Kafka brokerId from reused meta.properties`
- `broker-7 nvme2n1 XFS I/O failure`
- `Blocked WAL recycling: KMS-denied archiving + inactive logical slot`
- `Terraform lowered conntrack sysctls`
- `Bad Nginx temp-dir permissions/policy`
- `Prometheus TSDB PV exhaustion`

These are generally good RCA candidates.

### Main weakness

The labels are often **more specific than the visible evidence supports**. This is the most serious quality issue.

If the first 300 chars are the actual model input, many labels are effectively hallucinated. For example:

#### Weak or insufficiently grounded labels

| Sample | Label | Issue |
|---|---|---|
| Root Cause Identification Sample 1 | `etcd NVMe write I/O failure` | Visible snippet only shows kube-controller-manager leader election activity/error prefix. It suggests control-plane or etcd trouble but does not prove NVMe write failure. |
| Root Cause Identification Sample 2 | `broker-7 nvme2n1 failure` | Visible snippet shows Kafka controller forwarding and a partial warning. Device-specific failure is not visible. |
| Symptom Separation Sample 1 | `Duplicate Kafka brokerId from reused meta.properties` | Very specific and plausible, but not supported by the visible controller leader transition lines. |
| Symptom Separation Sample 2 | `broker-7 nvme2n1 XFS I/O failure` | Strong RCA if logs contain XFS/NVMe errors, but not visible in the snippet prefix. |
| Multiple Choice RCA Sample 1 | `Blocked WAL recycling: KMS-denied archiving + inactive logical slot` | Good root cause, but visible line shows slow INSERT, which could mislead toward app/db load without later WAL evidence. |
| Multiple Choice RCA Sample 2 | `Terraform lowered conntrack sysctls` | Specific config-management RCA, not visible in the first 300 chars. |
| Hard Negative Sample 2 | `Prometheus TSDB PV exhaustion` | Plausible, but visible prefix is only Prometheus startup logs. |
| Hard Negative Sample 1 | `Bad Nginx temp-dir permissions/policy` | Plausible, but slightly vague: “permissions/policy” combines two adjacent root causes. Better to distinguish Unix mode/ownership, SELinux/AppArmor, read-only FS, or Nginx temp path mismatch. |

### Stronger labels

These are good labels if backed by full logs:

- `Blocked WAL recycling: KMS-denied archiving + inactive logical slot`
  - Specific.
  - Causal.
  - Not a symptom.
  - Explains disk/WAL pressure and downstream DB issues.

- `Duplicate Kafka brokerId from reused meta.properties`
  - Specific.
  - Explains fencing loops, replica fetcher shutdown, leaderless partitions.

- `broker-7 nvme2n1 XFS I/O failure`
  - Strong if kernel/XFS/Kafka log-dir failure messages exist.
  - Properly identifies substrate failure rather than Kafka symptoms.

- `Terraform lowered conntrack sysctls`
  - Good config-regression RCA if linked to deploy/change logs and conntrack table behavior.

---

## 4. Evidence Quality

### Estimated evidence grounding

There are two different interpretations:

#### If only the shown first 300 chars are used as model input

- **Strongly grounded evidence percentage:** approximately **10–15%**
- Most outputs contain facts not present in the visible input.
- This would create a high hallucination risk.

#### If the full logs are actually present in the training records

- **Strongly grounded evidence percentage:** approximately **75–85%**
- Many evidence outputs look like exact log lines and operationally valid indicators.
- Still needs verification via span alignment.

### Strong evidence examples

The evidence extraction examples contain several high-quality evidence items:

```json
"\"msg\":\"backend quota exceeded\",\"quota-name\":\"v3-applier\",\"quota-size-bytes\":8589934592,\"quota-size\":\"8.6 GB\",\"db-size-bytes\":8612458496,\"db-size\":\"8.6 GB\""
```

Why strong:

- Directly indicates etcd quota exhaustion.
- Includes quota size and DB size.
- Supports an etcd NOSPACE/root-cause path.

```json
"\"msg\":\"mvcc database space exceeded\""
```

Why strong:

- Canonical etcd failure indicator.
- Supports writes failing due to MVCC backend space exhaustion.

```json
"endpoint status: https://127.0.0.1:2379, dbSize=8621162496, dbSizeInUse=4314927104, alarms=[NOSPACE]"
```

Why strong:

- Confirms etcd `NOSPACE` alarm.
- Includes endpoint and size metrics.
- Strongly causal for Kubernetes control-plane write failures.

```json
"ERROR:  could not create unique index \"inventory_reservations_cart_status_uniq\""
```

Why strong:

- Direct DB migration/index creation failure.
- Supports migration-induced incident.

```json
"DETAIL:  Key (cart_id, status)=(cart_2fbb4c91-6c09-4d74-9e8d-0c6813e0bfac, PENDING) is duplicated."
```

Why strong:

- Identifies exact data invariant violation.
- Not merely a symptom.

```json
"Migration of schema \"public\" to version \"202602170338__add_inventory_cart_status_unique\" failed!"
```

Why strong:

- Connects database error to Flyway migration.
- Good incident-level evidence.

### Weak evidence grounding examples

The following labels/evidence are weakly grounded in the visible snippets:

- `etcd NVMe write I/O failure`
  - Visible snippet shows kube-controller-manager leader election, not etcd disk I/O.

- `broker-7 nvme2n1 failure`
  - Visible snippet does not show NVMe, XFS, kernel, or Kafka log-dir failure.

- `Duplicate Kafka brokerId from reused meta.properties`
  - Visible snippet only shows Kafka controller leadership activity.

- `Terraform lowered conntrack sysctls`
  - Visible snippet mentions symptoms: 5xx, DNS failures, service routing instability. Terraform/sysctl causality is not visible.

### Evidence extraction schema issue

The task instruction:

> Extract evidence supporting the root cause.

But the input does not include the root cause. This is ambiguous.

Better schema:

```text
Instruction: Extract evidence from the logs that supports the given root cause.
Root cause: etcd backend quota exceeded / NOSPACE alarm
Logs: ...
Output: [...]
```

Without the root cause, the task is effectively:

1. infer RCA,
2. select supporting evidence,
3. output evidence,

which makes supervision noisier.

---

## 5. Hard Negative Quality

### Assessment

The hard negative examples are one of the stronger parts of the dataset. The choices are realistic and operationally plausible.

#### Hard Negative Sample 1

Options:

- upstream service outage
- client body too large limit
- edge disk exhaustion
- bad Nginx temp-dir permissions/policy
- Nginx config syntax error
- checkout-api BodyHashFilter bug

This is a good set. The visible log shows Nginx reload and syntax validation:

```text
nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
```

That helps eliminate `Nginx config syntax error`. The remaining options are realistic and require distinguishing between edge-layer issues, app-layer bugs, and upstream failures.

Label:

```text
D) Bad Nginx temp-dir permissions/policy
```

Plausible, though not fully visible from the 300-character prefix.

#### Hard Negative Sample 2

Options:

- Grafana query max_samples limit
- Prometheus TSDB PV exhaustion
- Thanos Receive timeout
- Prometheus WAL CRC corruption
- Alertmanager Slack notification failure
- duplicate recording rule reload failure

This is a realistic observability-stack negative set. The distractors are plausible but mostly separable if the logs include disk/PV exhaustion evidence.

Label:

```text
B) Prometheus TSDB PV exhaustion
```

Plausible, but not visible in the shown startup prefix.

### Hard negative quality score

**Hard Negative Quality Score: 7/10**

Strengths:

- Plausible alternatives.
- Realistic incident hypotheses.
- Distractors are not obviously silly.
- Good for teaching discrimination among adjacent causes.

Weaknesses:

- Correct labels often require unseen evidence.
- Some distractors are not equally close to the observed prefix.
- Need explicit evidence/rationale fields to ensure the model learns why the chosen option wins.

---

## 6. Insufficient Evidence Quality

### Assessment

The insufficient-evidence samples are valuable and mostly correct based on the visible snippets.

#### Sample 1

Visible input:

- kube-apiserver trace for event creation.
- No clear disk, network, etcd, auth, admission, or dependency failure shown.

Output:

```json
{"root_cause": "insufficient_evidence", "confidence": "low"}
```

This is appropriate from the visible evidence.

#### Sample 2

Visible input:

- Kafka AlterIsr request and partial replica-related warning.
- This suggests a Kafka replication event but does not establish root cause.

Output:

```json
{"root_cause": "insufficient_evidence", "confidence": "low"}
```

Also appropriate.

### Strengths

- Teaches abstention.
- Uses structured low-confidence output.
- Prevents forced RCA behavior.
- Good for production RCA agents, where incomplete telemetry is common.

### Weaknesses

- Only two examples shown.
- Needs more gradations:
  - `insufficient_evidence`
  - `probable_root_cause`
  - `multiple_plausible_causes`
  - `needs_more_data`
- Should include a `missing_evidence` field.

Better output format:

```json
{
  "root_cause": "insufficient_evidence",
  "confidence": "low",
  "missing_evidence": [
    "no kernel/storage logs",
    "no broker disk/log-dir failure messages",
    "no controller decision explaining ISR change"
  ]
}
```

### Does it correctly teach when not to answer?

Partially, yes.

The current examples teach the model that generic control-plane or Kafka activity should not automatically become a specific RCA. That is good.

But the rest of the dataset often does the opposite: it provides highly specific RCAs from snippets where the visible evidence is weak. This creates conflicting supervision. The model may still learn to guess specific RCAs rather than abstain.

---

## 7. Training Readiness Score

### Final readiness score

**Training Readiness Score: 62/100**

This is a promising dataset but not yet production-grade for fine-tuning an RCA model unless the full logs are present and verified.

### Expected hallucination risk

**Expected hallucination risk: Medium-high**

Main drivers:

- Specific root causes not visible in the presented snippets.
- Device names, partition counts, topic names, and exact failure chains may be learned as pattern completions rather than evidence-based inferences.
- Evidence extraction lacks an explicit root-cause input.
- Causal chain outputs may encourage overconfident narrative generation.

If the training inputs truly include full logs with all referenced evidence, hallucination risk drops to **medium**. If only the first 300 chars are used, hallucination risk is **high to severe**.

### Expected RCA capability gain

**Expected RCA capability gain: Moderate to high, conditional on grounding fixes**

Likely gains:

- Better recognition of common SRE failure modes.
- Improved symptom/root-cause separation.
- Better Kafka, Postgres, Kubernetes, Nginx, and Prometheus incident reasoning.
- Improved multiple-choice RCA selection.
- Some abstention behavior.

Likely failure modes after fine-tuning:

- Over-specific root cause guesses.
- Confident device-level or config-level claims without evidence.
- Synthetic incident pattern matching.
- Repeating memorized causal chains.
- Underuse of `insufficient_evidence` unless strongly represented.

---

## Top 10 Recommended Actions

1. **Verify full-log grounding for every label.**
   - Every root cause, evidence item, symptom, count, device name, and topic name should be traceable to the input logs.

2. **Stop training on 300-character prefixes if labels require later evidence.**
   - Either include the full incident log or make the output correspond only to the visible prefix.

3. **Add evidence spans to all RCA labels.**
   - Example:
     ```json
     {
       "root_cause": "broker-7 nvme2n1 XFS I/O failure",
       "evidence": [
         {"line": "...blk_update_request: I/O error, dev nvme2n1...", "supports": "device failure"},
         {"line": "...XFS metadata I/O error...", "supports": "filesystem corruption/failure"},
         {"line": "...Log directory /var/lib/kafka/data-2 is offline...", "supports": "Kafka impact"}
       ]
     }
     ```

4. **Separate root cause identification from evidence extraction.**
   - Evidence extraction should include the root cause as an explicit input.

5. **Add rationales for multiple-choice and hard-negative tasks.**
   - Include why the correct answer wins and why close distractors are wrong.

6. **Increase insufficient-evidence coverage.**
   - Target at least 15–25% of RCA examples with incomplete, ambiguous, or conflicting evidence.

7. **Introduce confidence calibration.**
   - Require outputs like:
     ```json
     {
       "root_cause": "...",
       "confidence": "medium",
       "grounding_strength": "2 direct log lines, 1 inferred dependency"
     }
     ```

8. **Normalize output schemas.**
   - Current outputs vary between plain strings, arrays, and JSON objects. Use consistent schemas per task to reduce fine-tuning noise.

9. **Reduce ungrounded exact counts unless directly evidenced.**
   - Counts like `493 leaderless partitions`, `2411 underReplicatedPartitions`, or `913 replicas offline` should only appear if present in logs.

10. **Add counterfactual and near-miss examples.**
   - For example:
     - Kafka ISR shrink caused by network partition, not disk failure.
     - Postgres pool saturation caused by app leak, not migration.
     - etcd leader election errors caused by network latency, not disk/NOSPACE.
     - Nginx 413 caused by body size config, not temp-dir permissions.

Final judgment: **Good domain coverage and useful task design, but grounding and ambiguity problems are serious. Do not fine-tune a production RCA model on this dataset until label-evidence alignment is enforced.**
