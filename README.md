**Astral PLATAFORM & HCI - Full Text Version with Technical Specifications**

**Abstract**

Astral PLATAFORM & HCI is an open, auditable and intent-driven Hyperconverged Infrastructure (HCI) platform that treats networking, security and identity as first-class infrastructure primitives, not auxiliary services.

Astral unifies compute (VMs and containers), network, firewall, identity and DNS into a single converged fabric, governed by a deterministic reconciliation engine and operated through explicit intents, approvals and rollback.

**Technical Implementation Architecture:**
- **Control Plane & Backend**: Java-based stateless API layer with Spring Boot
- **Frontend Interface**: HTML5, vanilla JavaScript, and CSS3 with Tailwind styling (framework-free)
- **Reconciliation Engine**: Python-based deterministic state management
- **Observability Platform**: SigNoz integration with Python collectors querying database directly
- **Configuration Management**: Database-driven architecture with no YAML/JSON configuration files
- **Data Collection**: Direct database-to-telemetry pipeline without intermediate configuration layers

The project is released under the GNU General Public License v3.0 (GPLv3), ensuring long-term freedom, transparency and resistance to vendor lock-in.

**Problem Statement**

Modern infrastructure platforms suffer from recurring structural problems:

Artificial complexity introduced by layered products

Vendor lock-in disguised as "enterprise features"

Expensive certifications used as operational gatekeeping

Opaque control planes with fragile and non-deterministic behavior

Networking, firewalling, identity and compute are usually treated as separate silos, managed by different tools and mental models. This fragmentation increases operational risk, slows down recovery and raises the cognitive load for infrastructure teams.

Astral addresses this by collapsing these silos into a single authoritative control plane, operated directly by infrastructure engineers and fully observable through explicit state and audit logs.

**Design Philosophy**

Astral is built on four non-negotiable principles:

Determinism over magic
Every action must be explainable, reproducible and reversible.

Auditability over convenience
If a change cannot be audited, it is considered unsafe.

Fallback over dependency
Any optional component must be removable without stopping the platform.

Human authority over automation
Automation assists; humans decide.

If a system cannot be operated manually, it is considered unsafe by design.

**What Makes Astral HCI**

Astral is not a hypervisor with add-ons.

It is an infrastructure operating system where:

Network, firewall, identity and DNS form one converged domain

VMs and containers consume the same infrastructure fabric

All changes follow the same intent → reconcile → commit lifecycle

Rollback is mandatory, not optional

Each node is a fully converged HCI node, capable of operating independently or as part of a cluster.

**Core Architecture**

**Control Plane**

Stateless, headless API layer implemented in Java with Spring Boot

RBAC, approvals and full audit trail

Designed for CLI-first and automation-driven operation with optional HTML/JS/CSS frontend

The control plane never hides state behind UI artifacts.

**Reconciliation Engine**

Implemented in Python, the reconciliation engine:

Validates declared intents

Computes desired versus observed state

Produces deterministic execution plans

Applies changes in controlled stages

Enforces automatic rollback on failure

There is no imperative bypass path.

**Persistence & Data**

Astral separates responsibilities clearly:

Relational database (PostgreSQL) for authoritative state, intents, commits and audit

Data lake for curated telemetry and historical analysis

Promotion of data into the data lake is selective and policy-driven.

**Frontend Implementation**

Astral provides an optional web interface built with:
- Pure HTML5 semantic markup
- Vanilla JavaScript (no frameworks)
- CSS3 with Tailwind CSS for styling
- Direct REST API communication with Java backend
- Real-time WebSocket updates for state changes

The frontend is completely optional - all operations can be performed via CLI or API.

**Converged Infrastructure Fabric**

Astral enforces a single logical fabric providing:

Network interfaces, VLANs and routing

Stateful firewall and NAT

Identity-aware access control (AD / SSO)

DNS resolution and policy enforcement

There is no separation between these domains.

If identity is unavailable, access is restricted.
If firewall application fails, changes are rolled back atomically.

**Architect's Note: Integration as Philosophy**

Astral follows an architectural philosophy of integration over replacement.

Rather than reinventing mature open-source components, Astral unifies them into a coherent, deterministic system. The role of the infrastructure architect is not to replace everything, but to connect proven systems with intent, clarity and control.

**Example: Unified Identity, DNS and Firewall**

In Astral, components such as:

Samba (identity and directory)

Authoritative DNS

DNS filtering

Stateful firewall

are not managed independently. They are expressed as a single declarative intent, reconciled together and audited in a unified log stream.

Failure in any component triggers a full rollback, eliminating partial configuration states.

**Compute & Containers**

Astral supports:

KVM / libvirt virtual machines

OCI-compatible system containers

Both workloads:

Attach to the same network fabric

Obey the same firewall rules

Are governed by identity

Emit identical audit and telemetry

There is no secondary networking or security stack.

**Distributed Storage**

Astral uses DRBD with orchestration for distributed storage:

Synchronous replication

Predictable performance

Deterministic behavior

Storage is managed as an HCI resource and audited like any other infrastructure component.

**Intent Lifecycle**

Every infrastructure change follows the same lifecycle:

Intent creation (no state change)

Authorization and approval

Reconciliation and diff computation

Staging and validation

Commit or automatic rollback

Observation and audit

There is no direct imperative configuration path.

**Observability**

Astral provides unified observability across the platform:

Network and firewall activity

DNS health and failover events

Identity authentication and authorization

VM and container performance

Service and package health

Drift detection is continuous and explicit.

**Observability Implementation:** Astral integrates with SigNoz through Python-based telemetry collectors that query the authoritative PostgreSQL database directly. This database-to-observability pipeline eliminates configuration files, ensuring real-time visibility into the converged fabric state. All metrics, traces, and logs flow from source to SigNoz without intermediate YAML/JSON configuration layers.

**Assistive Intelligence (External Systems)**

Astral may integrate with external assistive intelligence systems.

These systems are not part of Astral, are not required, and hold no authority over infrastructure state.

They may provide:

Anomaly detection

Suggestions and analysis

Observability enrichment

All decisions and changes remain under explicit human control.

**Relationship with CELESTE**

CELESTE is an independent project, developed, licensed and distributed separately.

Astral:

Does not embed CELESTE

Does not bundle CELESTE

Does not depend on CELESTE

Does not delegate authority to CELESTE

Any interaction, if enabled, occurs exclusively through explicit, versioned APIs or data contracts, ensuring:

No license entanglement

No derived work relationship

No runtime dependency

No shared control plane

Astral remains fully functional and fully compliant with its license regardless of the presence or absence of CELESTE.

**Replaceability and Fallback**

All optional integrations are treated as replaceable external systems.

Operators may:

Disable external integrations entirely

Replace them with alternative systems

Integrate Astral with external platforms

Astral continues to operate fully in all cases.

**Licensing**

Astral HCI-NGFW is released under the GNU General Public License v3.0 (GPLv3).

This guarantees:

Freedom to use, study and modify

Mandatory sharing of improvements

Protection against proprietary enclosure

External systems — including independent assistive intelligence projects — are not covered by this license unless explicitly stated in their own repositories.

**Technical Architecture Diagram**

```
+-----------------------------------------------------------+
|         Optional Frontend (HTML/JS/CSS + Tailwind)        |
|         Dashboard • Intent Forms • Audit Viewer           |
+-------------------------------+---------------------------+
                                |
                                v
+-----------------------------------------------------------+
|              Astral Control Plane (Java)                  |
|        API • RBAC • Audit • Approvals • Spring Boot       |
+-------------------------------+---------------------------+
                                |
                                v
+-----------------------------------------------------------------------+
|         Core Reconciliation Engine (Python)                           |
|  Intents • Desired State • Validation • Diff • Apply • Rollback       |
+-----------------------------------------------------------------------+
                                |
                                v
+-----------------------------------------------------------------------+
|         Converged Infrastructure Fabric                               |
|  Network • Firewall • Identity • DNS • Unified Domain                 |
+-----------------------------------------------------------------------+
                                |
                                v
+-----------------------------------------------------------------------+
|         Compute & Containers Runtime (HCI Node Runtime)               |
|      KVM / libvirt • OCI Containers • Unified Fabric                  |
+-----------------------------------------------------------------------+
                                |
                                v
+-----------------------------------------------------------------------+
|         PostgreSQL Database (Authoritative State)                     |
|         Intents • Audit • Configuration • State                       |
+-----------------------------------------------------------------------+
                                |
                                v
+-----------------------------------------------------------------------+
|         Python Observability Collectors → SigNoz                      |
|         Direct Database Queries • No Config Files                     |
+-----------------------------------------------------------------------+

(External systems interact only through explicit APIs)
```

**Operational Model**

Astral is designed to be operated without a graphical interface.

Primary interaction methods:

CLI tools

Declarative intent files

API-driven automation

Optional HTML/JS/CSS web interface

This is intentional.

Astral assumes operators understand infrastructure and require clarity, predictability and control, not abstraction layers that hide state.

**Failure Modes & Degraded Operation**

Astral is designed to degrade safely.

If optional components fail:

Control plane API unavailable → no changes applied, runtime continues

Reconciliation engine stopped → last committed state remains enforced

Database unavailable → configuration frozen, workloads continue

External systems unavailable → suggestions and enrichment disabled

Frontend unavailable → CLI and API remain fully operational

Manual operation is always possible.

**Scope**

Astral explicitly focuses on core infrastructure:

Compute (VMs and system containers)

Network, firewall and routing

Identity and DNS

Storage replication

Audit and observability

Astral does not aim to be:

A PaaS

A Kubernetes distribution

A cloud provider abstraction layer

Those systems may run on Astral, but are not part of Astral itself.

**Non-Goals**

To preserve stability and clarity, Astral explicitly avoids:

Hidden automation

Self-modifying infrastructure

Auto-remediation without approval

UI-driven configuration state

YAML/JSON configuration files

If a feature reduces determinism, it is rejected.

**Project Status**

Astral HCI-NGFW is in active early development.

Current focus:

Control plane contracts (Java Spring Boot implementation)

Intent schema definition

Reconciliation engine foundations (Python implementation)

Deterministic networking and firewall enforcement

HTML/JS/CSS frontend with Tailwind styling

SigNoz integration with Python database collectors

Database-driven configuration architecture

APIs, schemas and internal structures are expected to evolve.

**Repository Structure (Implementation View)**

astral/
├── control-plane-java/        # Java Spring Boot API, RBAC, approvals, audit
├── reconciler-python/         # Python reconciliation engine
├── frontend-html/             # HTML + JavaScript + CSS + Tailwind
├── fabric-integrations/       # Network, firewall, identity, DNS integration
├── observability-python/      # Python collectors for SigNoz
├── storage-orchestration/     # DRBD orchestration
├── runtime-bindings/          # KVM and container runtime integration
├── database-schemas/          # PostgreSQL schema and migrations
├── cli-tools/                 # Command-line interface tools
├── api-contracts/             # API specifications and contracts
├── intent-examples/           # Example intent declarations
├── deployment-scripts/        # Deployment and installation
├── docs/                      # Architecture and intent documentation
└── tests/                     # Integration and unit tests

**Implementation Notes**

1. **No Configuration Files**: Astral uses the database as the single source of truth for all configuration, eliminating YAML/JSON configuration files
2. **Direct Observability**: Python collectors query PostgreSQL directly, streaming metrics to SigNoz without configuration layers
3. **Framework-Free Frontend**: Pure HTML5, vanilla JavaScript, and CSS3 with Tailwind for styling
4. **Java Backend**: Spring Boot-based control plane with REST API and WebSocket support
5. **Python Core**: Deterministic reconciliation engine in Python

**Final Statement**

Astral HCI-NGFW is not built to chase trends.

It is built to:

Be understood

Be audited

Be operated under pressure

Survive component failure

Remain free and defensible

Operate without configuration files

Provide deterministic observability

Astral is infrastructure for engineers who value control over convenience, determinism over magic, and auditability over abstraction.
