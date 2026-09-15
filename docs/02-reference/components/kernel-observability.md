# Observable Events

Runtime observability is a cornerstone of the GovOps framework. It is achieved through the generation of observable events at the kernel level.

## Event Content

When a Policy Decision Point (PDP) makes a decision, it generates an event that includes the following information:

- **Capability ID:** The unique identifier of the capability that was the subject of the access request.
- **Decision:** The outcome of the decision (e.g., `allow` or `deny`).
- **Policy Store ID:** The identifier of the policy store that was used to make the decision.
- **Policy Store Version:** The version of the policy store that was used.
- **JTI (JWT ID):** The unique identifier of the token used in the request, which can be used for correlation.

## Benefits

- **Real-time Visibility:** This mechanism provides real-time visibility into the access decisions being made across the system.
- **Granular Monitoring:** Kernel-level observability allows for a very granular level of monitoring, which is difficult to achieve with traditional logging and monitoring approaches.
- **Vendor-Neutral:** The event format is vendor-neutral, allowing for interoperability between different tools and technologies.
- **Input for Metrics:** These events provide the raw data needed to generate the GovOps metrics.

## Technology

This type of kernel-level observability can be achieved using tools such as:

- [Cilium](https://cilium.io/)
- [Sysdig](https://sysdig.com/)
- [Falco](https://falco.org/)
