# Policy Decision Point (PDP)

In the GovOps framework, the Policy Decision Point (PDP) is the component responsible for evaluating policies and making access decisions.

## Characteristics

- **Stateless:** PDPs are stateless. Each decision request is evaluated independently, without any memory of previous requests. This makes them highly scalable and resilient.
- **Atomic:** Decisions are atomic. The PDP receives a request, evaluates it against the relevant policies, and returns a decision.
- **Policy-Neutral:** The GovOps framework is policy-neutral. It does not mandate a specific policy language or engine. This allows organizations to use the policy engine that best fits their needs, whether it's OPA, Cedar, or a custom solution.
- **Observable:** PDPs in a GovOps environment are expected to expose their decisions as observable events. This is a key enabler for the runtime observability features of the framework.

## PDP vs. PEP

The GovOps architecture maintains a clear separation between the Policy Decision Point (PDP) and the Policy Enforcement Point (PEP). The PEP is the component that enforces the decision made by the PDP. In many cases, the PEP is simply the application or service that is requesting the access decision.
