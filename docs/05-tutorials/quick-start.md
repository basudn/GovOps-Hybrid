# Quick Start: Emitting Your First GovOps Event

This tutorial will guide you through a simple, practical example of how to implement the core GovOps observability pattern. We will create a mock application that makes a policy decision and emits a GovOps-compliant "Observable Event".

**Goal:** Understand how an application can produce observable events that a governance system can consume.

**Prerequisites:** Python 3

---

### Step 1: Define Your Capability

First, let's define a capability in a simple `acc.yaml` file. Our capability will be `approve-loan` for a `banking-app`.

```yaml
# acc.yaml
capability:
  id: "cap:01H9J8K7N6P5R7Z3Y1X0W4B2D1"
  action: "approve-loan"
  resource: "banking-app"
  description: "The capability to approve a customer loan application."
  risk: "high"
  business_impact: "significant"
  category: "finance:lending"
```

### Step 2: Create the Mock Application (PDP)

Next, let's create a simple Python script named `pdp_app.py`. This script will simulate a Policy Decision Point (PDP). It will read the capability, make a hardcoded "allow" decision, and then construct and print a GovOps event to the console (`stdout`).

```python
# pdp_app.py
import json
import datetime
import yaml

def make_decision_and_emit_event():
    # Load the capability definition
    with open("acc.yaml", "r") as f:
        capability = yaml.safe_load(f)["capability"]

    # 1. A mock authorization request comes in
    # (In a real app, this would come from a client)
    mock_request = {
        "user": "trading_bot_42",
        "amount": 50000
    }

    # 2. The PDP makes a decision (hardcoded for this example)
    decision = "allow"

    # 3. Construct the GovOps Observable Event
    event = {
        "capability_id": capability["id"],
        "decision": decision,
        "policy_store_id": "policy_store_v1.2",
        "policy_store_version": "1.2.3",
        "actor_identity": mock_request["user"],
        "context": {
            "request_amount": mock_request["amount"],
            "risk_score": 0.85
        },
        "timestamp": datetime.datetime.utcnow().isoformat() + "Z"
    }

    # 4. Emit the event by printing the JSON to stdout
    print(json.dumps(event))

if __name__ == "__main__":
    make_decision_and_emit_event()

```

### Step 3: Create the Observer

Now, let's create a simple "observer" script named `observer.py`. This script will simulate a governance monitoring tool that listens for events. In this example, it will simply read the JSON event from `stdin` (the output of our PDP app) and print a formatted message.

```python
# observer.py
import sys
import json

def observe_events():
    print("--- Governance Observer is listening for events ---")
    for line in sys.stdin:
        try:
            event = json.loads(line)
            print("\n[EVENT DETECTED]")
            print(f"  Capability: {event.get('capability_id')}")
            print(f"  Decision:   {event.get('decision').upper()}")
            print(f"  Actor:      {event.get('actor_identity')}")
            print(f"  Timestamp:  {event.get('timestamp')}")
        except json.JSONDecodeError:
            print("Received non-JSON input.", file=sys.stderr)

if __name__ == "__main__":
    observe_events()

```

### Step 4: Run the Example

Now, open your terminal and run the two scripts, piping the output of the application (`pdp_app.py`) into the observer (`observer.py`).

```bash
python3 pdp_app.py | python3 observer.py
```

### Expected Output

You should see the following output from the observer, indicating it has successfully received and parsed the GovOps event:

```
--- Governance Observer is listening for events ---

[EVENT DETECTED]
  Capability: cap:01H9J8K7N6P5R7Z3Y1X0W4B2D1
  Decision:   ALLOW
  Actor:      trading_bot_42
  Timestamp:  202... (current timestamp)
```

---

## Conclusion

This simple example demonstrates the core of GovOps observability. The application (`pdp_app.py`) focuses on its business logic and making a decision, and then emits a standardized event. The governance tool (`observer.py`) is completely decoupled and simply listens for these events to perform its monitoring and logging function. This separation of concerns is a key benefit of the GovOps framework.
