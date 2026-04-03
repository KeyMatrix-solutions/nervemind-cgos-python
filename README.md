# NerveMind CGOS Python SDK

Developer-oriented client for **decision intake (v2)**, **proof validation**, and **execution invoke** (gateway).

## Install (local / editable)

```bash
cd sdks/python
pip install -e .
```

## Quick start

```python
from cgos_sdk import CGOSClient

client = CGOSClient(
    base_url="https://cgos-api.example.com",
    api_key="your-api-key",
    internal_service_token="...",  # for verify_proof / invoke_execution
)

out = client.submit_decision(
    source_system="core-payments",
    sector="banking",
    decision_type="limit_increase",
    decision_id="dec-001",
    context={"amount": 5000},
    policy_set="ORGANIZATION_POLICY_V1",
    callback_url="https://your-bank.example/callbacks/cgos",
    correlation_id="trace-abc",
)

proof_check = client.verify_proof(out.get("proof_id") or "prf_...")
exec_resp = client.invoke_execution(
    proof_id="prf_...",
    path="/api/v1/payments/transfer",
    organization_id="org_123",
    http_method="POST",
    json_body={"to": "x", "amount": 1},
)
```

## Auth

| Call | Header |
|------|--------|
| Intake v2 | `X-API-Key` or `Authorization: Bearer <api_key>` (matches brain-api) |
| Proof validate / execution | `X-CGOS-Internal-Token` (S2S) |

Optional **bearer JWT** (UI / ops) enables `wait_for_decision()` polling on `GET /api/v1/cgos/decisions/{id}`.

## Reliability

`CGOSClient` supports `timeout_s`, `max_retries`, `Idempotency-Key` on intake (passed as header when provided), and optional `traceparent` / `correlation_id` on every request.

## External attestation

This SDK cannot prove mesh or gateway posture. Pair with SPIFFE/SPIRE, signed policy bundles, and CI validators — see `docs/CGOS_EXTERNAL_ATTESTATION_AND_SDK.md` at repo root.
