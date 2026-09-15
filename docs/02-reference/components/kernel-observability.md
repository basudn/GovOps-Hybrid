# Observable Events and Kernel Observability

Runtime observability is a cornerstone of the GovOps framework. It is achieved through the generation of observable events at the kernel level or an equivalent, high-performance local endpoint.

## Event Content

When a Policy Decision Point (PDP) makes a decision, it generates an event that includes the following information:

- **Capability ID:** The unique identifier of the capability that was the subject of the access request.
- **Decision:** The outcome of the decision (e.g., `allow` or `deny`).
- **Policy Store ID & Version:** The identifiers for the policy store and version used, ensuring reproducibility.
- **JTI (JWT ID):** The unique identifier of any token used in the request, which can be used for correlation.

## Event Propagation

The logical flow of an observable event is designed to be reliable, high-performance, and decoupled.

1.  **Origination:** The event is generated within the PDP at the moment an authorization decision is made.

2.  **Emission:** The PDP emits the event to a low-level, local, and highly reliable transport mechanism. The primary method is the operating system's kernel tracing facility (e.g., eBPF on Linux), but other high-performance local endpoints can be used. This ensures that event emission has minimal performance impact on the application.

3.  **Consumption:** A separate, privileged **Observer Agent** (e.g., Falco, Sysdig, or a custom agent) runs on the host and is responsible for listening to this low-level stream of events. This agent is decoupled from the application and the PDP.

4.  **Forwarding & Analysis:** The Observer Agent filters, potentially enriches, and forwards the events to higher-level systems for analysis and long-term storage. These systems can include:
    -   A SIEM (Security Information and Event Management) system.
    -   A data lake or log aggregation platform.
    -   An alerting engine for real-time threat response.

This decoupled, asynchronous propagation model ensures that the critical work of the PDP is not blocked by the downstream systems responsible for governance, monitoring, and analysis.

## Benefits

- **Real-time Visibility:** This mechanism provides real-time visibility into the access decisions being made across the system.
- **Granular Monitoring:** Kernel-level observability allows for a very granular level of monitoring, which is difficult to achieve with traditional logging.
- **Vendor-Neutral:** The event format is vendor-neutral, allowing for interoperability between different tools and technologies.
- **Input for Metrics:** These events provide the raw data needed to generate the GovOps metrics.

## Technology

This type of kernel-level observability can be achieved using tools such as:

- [Cilium](https://cilium.io/)
- [Sysdig](https://sysdig.com/)
- [Falco](https://falco.org/)
- Tetragon, Linux Audit, Windows ETW, and macOS Endpoint Security are also applicable, per
  `../architecture/system-context.md`.

## Known gap: no kernel tool emits `capability_id` natively

None of the tools above have any native concept of `capability_id` — it is a GovOps-specific
identifier, not something Cilium, Falco, or the OS audit subsystem know how to attach to an event
on their own. Mapping a raw kernel/process event (a syscall, a network connection, a file access)
to the `capability_id` that was in play when a PDP made its decision is currently the
responsibility of the Observer Agent or an equivalent enrichment step, and **exactly how that
mapping should work is not yet standardized**. This is the single largest open gap in making
kernel observability practically usable as independent proof of execution, rather than a
theoretical capability of the architecture.

## Comparison to Meta Muse

Meta Muse's approach goes further than GovOps's current model: it uses eBPF for **continuous
taint tracking** of an AI agent's outbound network egress, rather than emitting a single decision
event at authorization time. That is, rather than only proving what was *permitted*, it
continuously observes what the process actually *does* with its access. This is a more advanced
precedent than what GovOps's Kernel Observability component currently specifies, and is a useful
reference point for extending this component past emit-once decision correlation. See
`../../01-explanation/positioning/README.md#relationship-to-metas-muse` for the full
comparison, and `../../06-decisions/ADR-004-evidence-and-observability-model.md` for the related
authorization-vs-execution-evidence split.
