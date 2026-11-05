# All diagrams, mermaid source

Every diagram across the kill switch fact file and the SOC enablement pack, as
editable mermaid. These render natively on GitHub, in VS Code with a mermaid
extension, and in any mermaid viewer. Paste a block into a markdown file to
render it. Line breaks use `<br/>`; dashed arrows mean preview or return.

Contents:
1. Kill switch, the SOAR sequence  (kill switch fact file)
2. Telemetry path to Sentinel  (kill switch fact file)
3. The token-propagation gap  (kill switch fact file)
4. Manual kill runbook  (kill switch fact file)
5. Licence to operate, stage gates  (kill switch fact file)
6. SOC log flow, all sources to Sentinel  (SOC pack)
7. The kill switch, logical buttons by plane  (SOC pack)
8. Threat actors, threats and STRIDE  (SOC pack)

---

## Diagram 1. Kill switch, the SOAR sequence
Source: kill switch fact file, section 10. What it shows: the automated path, a
Sentinel analytic fires a playbook that disables the identity, deletes the
runtime and cuts egress, with an approval gate graded by tier.

```mermaid
sequenceDiagram
    autonumber
    participant T as Telemetry (OTel to Sentinel)
    participant S as Sentinel (analytics + playbook)
    participant O as SOC / Owner
    participant E as Entra Agent ID
    participant F as Foundry Agent Service
    participant N as Network (VNet / Firewall)

    T->>S: Agent behaviour or intent-drift signal
    S->>S: Analytics rule fires, automation rule invokes playbook
    alt Medium-tier agent
        S->>O: Approval request (Teams adaptive card)
        O-->>S: Approve containment
    else High-tier agent
        S->>S: Auto-contain (approval required only to restore)
    end
    S->>E: Graph accountEnabled=false, revoke sessions
    E-->>S: Identity disabled, new tokens blocked
    S->>F: Delete agent, force=true
    F-->>S: Runtime stopped, active sessions ended
    S->>N: Cut agent subnet egress (backstop)
    S->>S: Log containment (immutable), raise incident
    Note over S,O: Restore requires a separate approval
```

---

## Diagram 2. Telemetry path to Sentinel
Source: kill switch fact file, section 9. What it shows: the OpenTelemetry route
is generally available and connector-independent. The preview connectors add
inventory and identity context on top.

```mermaid
flowchart LR
    A["AI agent<br/>OTel spans, gen_ai.agent.id"] --> AI["Application Insights<br/>connected to Foundry project"]
    AI --> LA["Log Analytics workspace<br/>Sentinel-enabled"]
    LA --> SEN["Microsoft Sentinel<br/>analytics - hunting - playbooks"]
    SEN -.triggers.-> KS["Kill switch<br/>SOAR playbook"]

    subgraph Preview["Preview, additive, not required"]
      A365["Agent 365 connector<br/>UnifiedAgentObservability"]
      AID["Agent Identities connector<br/>inventory + blueprints"]
    end
    A365 -.-> SEN
    AID -.-> SEN

    classDef ga fill:#f3faf6,stroke:#2e7d57,color:#16222e;
    classDef prev fill:#fdf8f0,stroke:#c77d11,color:#16222e;
    class A,AI,LA,SEN,KS ga;
    class A365,AID prev;
```

---

## Diagram 3. The token-propagation gap
Source: kill switch fact file, section 2.1. What it shows: disabling blocks new
tokens at once, but an already-issued token survives up to its lifetime unless
the resource supports Continuous Access Evaluation.

```mermaid
sequenceDiagram
    autonumber
    participant Adm as SOC / Admin
    participant E as Entra Agent ID
    participant R1 as Resource (no CAE)
    participant R2 as Resource (CAE-capable)

    Adm->>E: Disable agent identity
    E-->>Adm: New token requests blocked immediately
    Note over E,R1: Existing access token already issued
    E--xR1: Token stays valid until expiry (about 1 hour)
    E->>R2: CAE revocation event
    R2-->>Adm: Access revoked in near real time (minutes)
    Note over Adm,R2: Mitigate: CAE on sensitive resources,<br/>and revoke sessions for agent users
```

---

## Diagram 4. Manual kill runbook
Source: kill switch fact file, section 10. What it shows: the manual fallback and
platform-level path, from evidence capture to the decision to re-enable, re-scope
or retire.

```mermaid
flowchart TD
    Start([Trigger: SOC or owner pulls the switch]) --> Ev["Preserve evidence:<br/>identity, blueprint, object IDs,<br/>owner, triggering telemetry"]
    Ev --> Scope{Whole agent type<br/>implicated?}
    Scope -- Yes --> BP["Disable the blueprint<br/>plus any agent users"]
    Scope -- No --> ID["Disable the agent identity<br/>plus any agent user"]
    BP --> RT["Delete Foundry runtime, force=true<br/>end live conversations"]
    ID --> RT
    RT --> Net["Confirm network containment:<br/>egress cut, private access removed"]
    Net --> Conf{Behaviour stopped<br/>in telemetry?<br/>mind the token gap}
    Conf -- No --> Net
    Conf -- Yes --> Dec{Decision}
    Dec --> ReEnable[Re-enable]
    Dec --> ReScope[Re-scope]
    Dec --> Retire["Retire:<br/>delete identity once<br/>no workload depends on it"]
```

---

## Diagram 5. Licence to operate, stage gates
Source: kill switch fact file, section 3. What it shows: the dev to pre-production
to production progression, with the kill switch exercised before each promotion.

```mermaid
flowchart LR
    Dev["Development<br/>low privilege, synthetic data<br/><br/>Prove: manual kill works,<br/>telemetry flows, owner assigned"]
    Pre["Pre-production<br/>private networking, prod-like identity<br/><br/>Prove: automated kill plus approval,<br/>propagation measured, egress cut tested"]
    Prod["Production<br/>live data, graded by privilege<br/><br/>Prove: game-day exercised and evidenced,<br/>named authority, CAE confirmed"]

    Dev -->|gate: kill switch exercised| Pre
    Pre -->|gate: kill switch exercised| Prod

    classDef s fill:#f7f9fb,stroke:#3b5168,color:#16222e;
    class Dev,Pre,Prod s;
```

---

## Diagram 6. SOC log flow, all sources to Sentinel
Source: SOC pack, section 3. What it shows: every Azure resource in scope routing
into one Sentinel-enabled workspace through diagnostic settings, the App Insights
link and Entra diagnostic settings, with the preview connectors added on top.

```mermaid
flowchart LR
    F["Foundry / Cognitive Services<br/>Audit - RequestResponse - Trace - Usage"] --> DS["Diagnostic settings"]
    AR["Agent runtime<br/>OpenTelemetry spans"] --> AI["Application Insights"]
    E["Microsoft Entra ID<br/>AuditLogs - SignInLogs - Risk"] --> ED["Entra diagnostic settings"]
    D["Data services<br/>AI Search - Blob - Cosmos / SQL"] --> DS
    N["Network<br/>NSG flow - Azure Firewall - private endpoints"] --> DS
    C["Control plane / CI<br/>Activity Log - Functions - DevOps"] --> DS

    DS --> LAW["Sentinel-enabled<br/>Log Analytics workspace"]
    AI --> LAW
    ED --> LAW
    LAW --> SEN["Microsoft Sentinel<br/>analytics - hunting - playbooks - incidents"]
    SEN --> UC["Use cases + kill-switch triggers"]

    A365["Agent 365 connectors, preview<br/>UnifiedAgentObservability - inventory"] -.-> SEN

    classDef ga fill:#f3faf6,stroke:#2e7d57,color:#16222e;
    classDef prev fill:#fdf8f0,stroke:#c77d11,color:#16222e;
    class F,AR,E,D,N,C,DS,AI,ED,LAW,SEN,UC ga;
    class A365 prev;
```

---

## Diagram 7. The kill switch, logical buttons by plane
Source: SOC pack, section 5. What it shows: the kill switch is a set of logical
controls across five planes. Red = the core single-agent kill (disable identity,
delete runtime, cut egress); the rest are graded or situational.

```mermaid
flowchart TB
    subgraph ID["Identity plane"]
      direction TB
      I1["Disable agent identity<br/>new tokens blocked"]:::red
      I2["Disable blueprint<br/>all of that type"]:::red
      I3["Disable agent user"]
      I4["Revoke sessions<br/>close the token gap"]
      I5["CA block, tenant<br/>all agent auth"]
      I6["Remove role assignments<br/>block new agents"]
    end
    subgraph RT["Runtime plane"]
      direction TB
      R1["Delete agent, force<br/>ends active sessions"]:::red
      R2["Delete agent version"]
      R3["Delete conversation"]
      R4["Pause deployment"]
    end
    subgraph NET["Network plane"]
      direction TB
      N1["Cut egress, firewall<br/>deny agent subnet"]:::red
      N2["NSG deny rule"]
      N3["Remove private endpoint"]
    end
    subgraph DATA["Data plane"]
      direction TB
      D1["Rotate / revoke keys"]
      D2["Revoke data connection"]
      D3["Purview DLP block"]
    end
    subgraph PLAT["Platform plane"]
      direction TB
      P1["Disable project"]
      P2["Disable guardrail bypass"]
      P3["Disable public access"]
    end

    classDef red fill:#fdf2f0,stroke:#c0392b,color:#c0392b,font-weight:bold;
```

---

## Diagram 8. Threat actors, threats and STRIDE
Source: SOC pack, section 6. What it shows: the five actor types mapped to the
threats they create and the STRIDE categories. Repudiation underpins all of them,
it is the case for the logging itself.

```mermaid
flowchart LR
    A1["External attacker"] --> T1["Exposure, planted creds, foothold"] --> S1["S Spoofing<br/>E Elevation"]
    A2["Content author<br/>indirect injection"] --> T2["Agent follows planted instructions"] --> S2["T Tampering<br/>E Elevation"]
    A3["Insider / developer"] --> T3["Guardrail off, shadow model, keys"] --> S3["T Tampering<br/>E Elevation"]
    A4["Supply chain"] --> T4["Malicious tool, pipeline abuse"] --> S4["T Tampering<br/>E Elevation"]
    A5["The agent itself<br/>confused deputy"] --> T5["Acts beyond the user's rights"] --> S5["E Elevation"]
    A5 --> T6["Data leak, cross-user access"] --> S6["I Information disclosure"]
    A5 --> T7["Loops, spend, denial of wallet"] --> S7["D Denial of service"]

    R["Repudiation runs across all:<br/>no telemetry means not attributable"]

    classDef actor fill:#fdf2f0,stroke:#c0392b,color:#16222e;
    classDef stride fill:#eef2f6,stroke:#3b5168,color:#16222e;
    class A1,A2,A3,A4,A5 actor;
    class S1,S2,S3,S4,S5,S6,S7 stride;
```

---

### Notes on rendering and editing
- These use `<br/>` for line breaks, which GitHub's mermaid renderer handles.
  Some older viewers prefer `\n`; swap if a line break shows literally.
- Diagram 7 uses `:::red` on the core-kill buttons and a `classDef`. If a viewer
  does not support inline class syntax, move the class to a `class I1,I2,... red;`
  line at the end of the block.
- Colours are set with `classDef`. Remove the `classDef` and `class` lines for a
  plain black-and-white render.
- To export an image from any of these, use the GitHub preview, or VS Code with a
  mermaid extension, or a local mermaid CLI with a browser available.
