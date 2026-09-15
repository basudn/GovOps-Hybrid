# Policy Decision Point (PDP)

In the GovOps framework, the Policy Decision Point (PDP) is the component responsible for evaluating policies and making access decisions.

## Characteristics

- **Stateless:** PDPs are stateless. Each decision request is evaluated independently, without any memory of previous requests. This makes them highly scalable and resilient.
- **Atomic:** Decisions are atomic. The PDP receives a request, evaluates it against the relevant policies, and returns a decision.
- **Policy-Neutral:** The GovOps framework is policy-neutral. It does not mandate a specific policy language or engine. This allows organizations to use the policy engine that best fits their needs, whether it's OPA, Cedar, or a custom solution.
- **Observable:** PDPs in a GovOps environment are expected to expose their decisions as observable events. This is a key enabler for the runtime observability features of the framework.

## PDP vs. PEP

The GovOps architecture maintains a clear separation between the Policy Decision Point (PDP) and the Policy Enforcement Point (PEP). The PEP is the component that enforces the decision made by the PDP. In most GovOps deployments, **the PEP is the application itself** — GovOps does not introduce a separate enforcement proxy or sidecar as part of its reference model; the requesting application is responsible for acting on whatever decision the PDP returns.

## Decision outcomes: allow, deny, and challenge

A PDP decision is not always a simple binary. GovOps recognizes a third outcome, **challenge**,
for cases where the PDP has enough information to know the request cannot be unconditionally
allowed, but also should not be flatly denied — for example, a step-up authentication requirement
before a high-risk capability can proceed.

| Field | Purpose |
|---|---|
| `challenge_id` | Unique identifier for this challenge instance, so a subsequent request can present evidence against it. |
| `capability_id` | The capability the original request was for. |
| `reason` | Why the request was challenged rather than allowed. |
| `required_evidence` | What the PEP/application must obtain and present to resolve the challenge (e.g., a step-up MFA assertion). |
| `expires_at` | When the challenge itself expires if not resolved. |

A challenge is recorded in the Runtime Authorization Context the same way an allow/deny decision
is (see `./runtime-authorization-context.md`), so that challenge issuance and resolution remain
part of the same auditable decision trail.
