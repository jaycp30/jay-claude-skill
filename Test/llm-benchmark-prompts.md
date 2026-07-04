# LLM Benchmark Prompt Set
**Purpose:** Compare reasoning, coding, and long-context performance across models
(e.g. Nvidia Nemotron 3 Super 120B, Claude Haiku 4.5, Claude Sonnet 4.6, Amazon Nova Lite, Amazon Titan)

**How to use:** Copy each prompt block (including the synthetic input where provided) and paste directly into Bedrock Playground or any LLM interface. Run the same prompt on each model and compare.

---

## SECTION 1: REASONING

---

### R1 — Multi-step Logical Deduction

**Tests:** Constraint satisfaction, step-by-step deductive reasoning, ability to track multiple rules simultaneously without contradiction.

```
There are five engineers: Alice, Bob, Carol, Dave, and Eve.

- Exactly one of them is on-call this weekend.
- Alice is on-call only if Bob is not.
- If Carol is on-call, then Dave is also on-call.
- Dave is never on-call when Eve is available.
- Eve is available this weekend.
- Bob is not on-call.

Who is on-call? Show your full reasoning step by step before giving the final answer.
```

---

### R2 — Causal Chain Reasoning (Incident Timeline)

**Tests:** Ability to infer causality from a sequence of events, understand system behavior under load, and identify non-obvious conclusions (e.g. why a symptom persisted after the root cause was resolved).

```
A production RDS instance in us-east-1 starts throwing connection timeout errors at 2:14 AM.
At 2:10 AM, an automated Lambda ran a bulk UPDATE on 4.2 million rows with no WHERE clause limit.
At 2:12 AM, CloudWatch shows FreeableMemory dropped from 4GB to 312MB.
At 2:13 AM, a read replica fell behind by 47 seconds.
At 2:15 AM, the application load balancer started returning 504s.
At 2:18 AM, an engineer manually rebooted the RDS instance.
At 2:24 AM, errors stopped but the replica lag remained at 47 seconds for another 12 minutes.

Question: Why did the replica lag persist even after the primary recovered? What does this tell you about what actually happened during the incident?
```

---

## SECTION 2: CODING

---

### C1 — Bug Identification

**Tests:** Code comprehension, ability to spot subtle logic errors (not just syntax), quality of explanation, and correctness of the fix.

```
The following Python function is supposed to find all pairs of numbers in a list that sum to a target value. It has at least two bugs. Identify them, explain why each one is a bug, and provide a corrected version.

def find_pairs(nums, target):
    seen = {}
    pairs = []
    for i, num in enumerate(nums):
        complement = target - num
        if complement in seen:
            pairs.append((complement, num))
            seen[num] = i
        else:
            seen[num] = i
    return list(set(pairs))
```

---

### C2 — Code Generation from a Complex Spec

**Tests:** Ability to translate multi-condition requirements into working code, handle edge cases explicitly called out in the spec, write clean and idiomatic Python, and explain design decisions.

```
Write a Python function called rate_limited_retry that wraps any function call with the following behavior:

- Retries up to N times on failure
- Uses exponential backoff starting at 1 second, doubling each retry
- If the exception message contains the word "RateLimit", add a random jitter between 0 and 2 seconds on top of the backoff
- If the exception message contains "Fatal", do not retry at all, raise immediately
- After all retries are exhausted, raise a custom exception called MaxRetriesExceeded that includes the original exception and the number of attempts made

Include a working usage example and explain any design decisions you made.
```

---

## SECTION 3: LONG-CONTEXT REASONING

For these prompts, paste the synthetic input block first, then the question below it in the same message.

---

### LC1 — Incident Log Analysis

**Tests:** Multi-document reading comprehension, extraction of specific facts scattered across a long input, numerical reasoning (SLA calculation), and open-ended analysis where the model must distinguish between confirmed facts, inferences, and unknowns.

**Step 1 — Paste this synthetic input:**

```
=== INCIDENT LOG: SYS-2024-0847 ===
Date: 2024-11-03
System: Payment Processing Platform
Environment: Production (eu-west-1)

00:00 - Scheduled maintenance window begins. Team deploys v3.4.1 of payment-service.
00:08 - Deployment completes. Health checks pass. Monitoring shows nominal.
00:15 - First anomaly: payment-service pod restarts once. Attributed to warm-up. Ignored.
00:23 - Pod restarts again. On-call engineer notified but no action taken.
00:31 - Payment success rate drops from 99.2% to 96.1%.
00:34 - Engineering lead joins the call.
00:39 - Pod restarts a third time. Engineer checks logs, sees OutOfMemoryError in heap dump.
00:45 - Decision made to roll back to v3.4.0.
00:47 - Rollback initiated.
00:52 - Rollback completes. Pod restarts stop.
00:54 - Payment success rate climbs to 97.4%.
01:03 - Payment success rate reaches 98.1%. Team stands down.
01:15 - Monitoring shows 98.1% sustained. Incident declared resolved.

=== POST-INCIDENT REVIEW NOTES ===
- v3.4.1 introduced a new in-memory caching layer for exchange rate lookups
- Cache had no TTL configured and no max size limit
- Load during the maintenance window was approximately 40% of peak
- Team estimates cache would have been exhausted within 90 minutes at peak load
- The second pod restart at 00:23 was the clearest early signal that was missed
- Payment success rate never fully recovered to 99.2% baseline after rollback

=== ACTION ITEMS ===
AI-1: Add TTL and max-size to all cache configurations going forward
AI-2: Create runbook for OOM events
AI-3: Investigate why success rate did not return to baseline after rollback

=== FOLLOW-UP LOG (1 week later) ===
Date: 2024-11-10
- AI-1 completed. Cache configs updated in v3.4.2.
- AI-2 completed. Runbook published.
- AI-3 still open. Engineer assigned but no findings yet.
- v3.4.2 deployed to staging. No memory issues observed.
- Team noted that payment success rate in production is still at 98.1%, not 98.9% (new baseline measured over the week, adjusted from original 99.2% estimate).

=== CUSTOMER IMPACT REPORT ===
- 412 failed transactions between 00:31 and 00:52
- 7 enterprise customers affected
- 3 support tickets opened
- No chargebacks reported
- SLA breach threshold: >1% failure rate sustained for >10 minutes
- Actual breach: failure rate hit 3.9% for 21 minutes
```

**Step 2 — Then ask this question:**

```
Based on the incident log above, answer the following:

1. Was the SLA breached? Show your calculation.
2. The payment success rate never returned to 99.2% even a week later. List every possible explanation the log supports, and for each one, state whether the log confirms it, suggests it, or leaves it open.
3. If you were the engineering lead, what is the single most important unresolved risk going into the following week, and why?
```

---

### LC2 — Needle in a Haystack (Config Misconfiguration)

**Tests:** Attention over a large, noisy context. The model must find one specific misconfiguration buried in a wall of valid-looking config. Good for measuring whether a model truly reads the full input or shortcuts to an answer.

**Step 1 — Paste this synthetic config block:**

```
# === PRODUCTION INFRASTRUCTURE CONFIG SNAPSHOT ===
# Generated: 2024-11-03T00:00:00Z
# Environment: eu-west-1 production

[service.api-gateway]
timeout_ms = 30000
max_connections = 500
rate_limit_rps = 1000
retry_attempts = 3
retry_backoff_base_ms = 200
health_check_path = "/health"
health_check_interval_s = 10
tls_enabled = true
tls_min_version = "TLSv1.2"

[service.payment-service]
timeout_ms = 15000
max_connections = 200
retry_attempts = 2
retry_backoff_base_ms = 500
health_check_path = "/ping"
health_check_interval_s = 10
tls_enabled = true
tls_min_version = "TLSv1.2"
cache.exchange_rates.enabled = true
cache.exchange_rates.ttl_s = 300
cache.exchange_rates.max_size = 10000

[service.auth-service]
timeout_ms = 5000
max_connections = 300
retry_attempts = 3
retry_backoff_base_ms = 100
health_check_path = "/health"
health_check_interval_s = 5
tls_enabled = true
tls_min_version = "TLSv1.2"
token_expiry_s = 3600
refresh_token_expiry_s = 86400

[service.notification-service]
timeout_ms = 10000
max_connections = 100
retry_attempts = 5
retry_backoff_base_ms = 1000
health_check_path = "/health"
health_check_interval_s = 30
tls_enabled = true
tls_min_version = "TLSv1.2"
email.batch_size = 50
email.rate_limit_per_min = 200
sms.rate_limit_per_min = 100

[service.data-pipeline]
timeout_ms = 120000
max_connections = 50
retry_attempts = 3
retry_backoff_base_ms = 2000
health_check_path = "/status"
health_check_interval_s = 60
tls_enabled = true
tls_min_version = "TLSv1.2"
batch_size = 1000
flush_interval_s = 30

[service.reporting-service]
timeout_ms = 60000
max_connections = 75
retry_attempts = 2
retry_backoff_base_ms = 500
health_check_path = "/health"
health_check_interval_s = 20
tls_enabled = true
tls_min_version = "TLSv1.0"
report_cache_ttl_s = 900
max_report_rows = 50000

[service.audit-logger]
timeout_ms = 5000
max_connections = 150
retry_attempts = 10
retry_backoff_base_ms = 50
health_check_path = "/health"
health_check_interval_s = 10
tls_enabled = true
tls_min_version = "TLSv1.2"
log_retention_days = 365
async_writes = true

[service.user-service]
timeout_ms = 8000
max_connections = 250
retry_attempts = 3
retry_backoff_base_ms = 300
health_check_path = "/health"
health_check_interval_s = 10
tls_enabled = true
tls_min_version = "TLSv1.2"
session_ttl_s = 1800
password_hash_rounds = 12

[service.file-storage]
timeout_ms = 45000
max_connections = 80
retry_attempts = 3
retry_backoff_base_ms = 1000
health_check_path = "/health"
health_check_interval_s = 15
tls_enabled = true
tls_min_version = "TLSv1.2"
max_upload_size_mb = 100
presigned_url_expiry_s = 3600

[service.search-service]
timeout_ms = 3000
max_connections = 400
retry_attempts = 1
retry_backoff_base_ms = 100
health_check_path = "/health"
health_check_interval_s = 10
tls_enabled = true
tls_min_version = "TLSv1.2"
index_refresh_interval_s = 5
max_results_per_query = 1000
```

**Step 2 — Then ask this question:**

```
Review the config snapshot above. One of the services has a security misconfiguration that violates current best practices and would likely fail a standard compliance audit. Identify the service, the specific misconfiguration, why it is a problem, and what the correct value should be.
```

---

## Scoring Guide

When comparing model outputs, consider the following for each prompt:

| Dimension | What to look for |
|---|---|
| Correctness | Is the final answer actually right? |
| Reasoning quality | Does it show its work clearly, or just assert the answer? |
| Handling of ambiguity | Does it acknowledge what the input does not tell us, or does it fabricate? |
| Code quality | Is the code runnable, idiomatic, and does it handle edge cases? |
| Instruction following | Did it answer everything asked, in the format requested? |
| Hallucination | Did it invent facts not present in the input? |
