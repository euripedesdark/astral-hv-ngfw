Astral HV-NGFW – Detailed GitHub Documentation

## Overview and Goals
Astral HV-NGFW is a free hyperconverged platform that integrates next-generation firewall, identity, hybrid DNS, hypervisor, VDI/RemoteApp, and observability, with a universal and durable core in Python + SQL Server and UI/orchestration in Spring Boot (Java). The goal is to deliver a modular, auditable, and lock-in-free stack, inspired by the pfSense methodology, with network management via NetworkManager and data collection/ML entirely in Python.

## Architecture
### Core Principles
Dual brain: Python Service (execution/ML/collection) + SQL Server (central persistence).

UI and orchestration: Spring Boot + Thymeleaf + Tailwind (SSR), with SSO and RBAC.

Network with NetworkManager: avoid systemd-resolved; clean integration with Bind, Pi-hole, libvirt.

Firewall/NAT pfSense-inspired: default deny policy, staged apply, rollback, dual approval.

Packages/Plugins: pfSense-like model (OpenVPN, IPsec, IDS/IPS, Blocklists, HAProxy, Squid, Watchdog, Time Sync).

Hybrid DNS: AD for internal, Pi-hole for external, with dynamic upstream failover.

Assistive ML: pattern/anomaly detection and rule suggestions with human approval.

Observability: Grafana + ClickMe + ClickMeKeeper querying SQL Server.

### Layered View
Layer 1 – UI/Orchestration: Spring Boot, Thymeleaf, Tailwind; secure endpoints; calls Python.

Layer 2 – Python Service: domain modules; rule execution; ML pipeline; NM/libvirt/Pi-hole/Samba integration.

Layer 3 – SQL Server: configs, intents, commits, inventory, audit, metrics, ML features.

Layer 4 – Network (NM): interfaces, VLANs, bridges, bonds, resolvers; DBus/nmcli via Python.

Layer 5 – Firewall/NAT: iptables/nftables; aliases/objects; NAT outbound/inbound; staged+rollback.

Layer 6 – Identity (Samba AD DC): domain provisioning, OUs, groups, GPO baseline; SSO.

Layer 7 – DNS hybrid: AD internal; Pi-hole external; upstream failover; blocklists.

Layer 8 – Hypervisor (KVM/libvirt): VMs, snapshots, templates; networks integrated to NM.

Layer 9 – VDI/RemoteApp (Citrix CVAD): Delivery Controllers, StoreFront, VDAs; ICA/HDX firewall.

Layer 10 – Packages/Plugins: installable modules with UI, audit, isolated services.

Layer 11 – Observability: domain dashboards and alerts; centralized logs/metrics.

## Network and Firewall Model
### NetworkManager (nm)
Interfaces: creation/editing of connections (ethernet, vlan, bridge, bond).

VLANs/bridges/bonds: profiles per tenant; consistent names (e.g.: vlan100, br-mgmt).

Routes/resolvers: DNS per interface; point to internal AD and external Pi-hole.

Control via Python: nmcli and DBus to apply/read state; inventory saved to SQL.

### Firewall/NAT (pfSense-inspired)
Default policy: deny on all interfaces.

Aliases/objects: networks, hosts, ports and apps reusable in rules.

Order and scope: rules per interface (WAN/LAN/VLANs) with mandatory comments.

SNAT outbound: prefer manual; mappings per VLAN/tenant; audit with reason.

DNAT inbound: publish via HAProxy when possible; health-check before exposure; minimal ports.

Staged apply: diffs and connectivity validation; automatic rollback on failure.

Dual approval: sensitive changes (firewall/NAT/VPN/blocklists).

## Packages and Plugins
### Philosophy
Package model: installable extensions with standardized config and Python core integration.

Isolation: services under systemd or lightweight containers; resource limits; own logs.

Audit and metrics: state and events in SQL; per-package dashboards.

### Package Map
astral-openvpn: servers/clients; certificates; AD integration; session audit; automatic firewall rules.

astral-ipsec: IKEv2/IKEv1; policies/tunnels; NAT-T; templates; peer health-check.

astral-ids-ips: Snort/Suricata; VLAN profiles; updatable rules; suppressions; ML integration.

astral-blocklists: IP/domains; geoblocking; Pi-hole integration; scheduling/rollback.

astral-haproxy: reverse proxy/balancing; TLS offload; health-check; tenant routes.

astral-squid: web proxy/cache/filter; AD auth; usage reports; controlled bypass.

astral-watchdog: service monitor (AD/DNS/VPN/IDS/StoreFront); auto-restart; maintenance windows.

astral-time-sync: NTP; multiple servers; drift check; log alignment.

## Data Model (SQL Server)
### Core Tables
packages: id, name, version, state, config_json, installed_at, updated_at.

openvpn_sessions/ipsec_sessions: id, user/peer, start/end, bytes, endpoints, failures_json.

ids_events: id, category, severity, action, context_json, suppression_ref, timestamp.

blocklists: id, source, entries_count, impact_score, last_update, history_json.

ad_provision: id, domain, ous_json, groups_json, gpos_json, status, timestamps.

dns_upstreams: id, name, endpoint, priority, health_status, last_check.

dns_events: id, upstream_id, action, reason, timestamp.

vm_templates: id, name, os, image_ref, cpu, ram, storage_profile, networks_json.

vm_inventory: id, template_id, hostname, state, tenant, tags_json.

citrix_catalogs: id, name, type, delivery_group, policy_json, status.

citrix_sessions: id, user, resource, start_time, end_time, metrics_json.

raw_events_net/dns/ad/vpn/ids: id, origin, payload_json, timestamp.

curated_state_host/service/user/net: id, key, state_json, updated_at.

ml_features: entity_key, window, stats_json, updated_at.

ml_suggestions: id, type (firewall/dns/nat/blocklist), payload_json, score, model_version, status, created_at.

ml_anomalies: id, category, features_json, severity, timestamp.

audit_log: id, actor, action, target, payload_hash, timestamp, outcome.

## API Contracts
Spring Boot → Python Service

POST /api/firewall/intents: creates intent. Payload: { action, proto, ports, source_alias, dest_alias, comment }. Response: { intent_id, status }.

POST /api/firewall/commit: staged → health-check → commit/rollback. Payload: { intent_id }. Response: { commit_id, result, diff }.

GET /api/firewall/diff/{id}: returns computed diff.

GET /api/firewall/audit: lists audit.

GET /api/packages: catalog/installed.

POST /api/packages/install: installs/activates package. Payload: { package_name, version, config }.

POST /api/packages/config/{name}: applies config.

GET /api/packages/status/{name}: state/health/versions.

POST /api/nm/network: create/edit connection (ethernet/vlan/bridge/bond). Payload: { type, name, params_json }.

GET /api/nm/inventory: interfaces/routes/resolvers inventory.

POST /api/ad/provision: domain/OUs/groups/GPO baseline.

GET /api/ad/status: AD services state.

POST /api/dns/upstreams: sets priorities/endpoints.

POST /api/dns/healthcheck/run: forces check/switch upstream.

GET /api/dns/events: history.

POST /api/hv/vm/create|snapshot|template: VM/libvirt operations.

GET /api/hv/inventory: VMs/resources.

POST /api/citrix/catalogs: creates catalogs/groups.

GET /api/citrix/sessions: telemetry/latency.

GET /api/ml/suggestions: lists suggestions.

POST /api/ml/suggestions/{id}/approve|deny|modify: decision with audit. 

## Code Organization
### Spring Boot (Java)
src/main/java/com/astral/ controllers/ FirewallController, PackagesController, NetworkController, ADController, DNSController, HVController, CitrixController, MLController. services/ IntentService, AuditService, SecurityService, DataService. config/ SecurityConfig (SSO/RBAC/CSRF/TLS), ThymeleafConfig, DatasourceConfig. models/ DTOs for payloads/responses. src/main/resources/ templates/ Thymeleaf pages (dashboard, firewall, packages, nm, ad, dns, hv, citrix, ml). static/ css/js/images (Tailwind build here). application.yml pointing to SQL Server and configs.

### Python Service
astral/ firewall.py: idempotency, diffs, staged/rollback, aliases/objects. network_nm.py: DBus/nmcli; create connections; inventory; resolvers. ad.py: Samba provisioning; OUs/groups/GPO baseline; status. dns.py: Pi-hole API; upstreams; health-check; events. hv.py: libvirt; templates; VMs; networks; inventory. citrix.py: AD/libvirt integration; publishing; sessions. packages/ openvpn.py, ipsec.py, ids.py, blocklists.py, haproxy.py, squid.py, watchdog.py, timesync.py. ml/ features.py, models.py, suggestions.py (Isolation Forest/LOF/clustering; explainability). db.py: pyodbc connection; persistence utilities. audit.py: audit trails and structured logs. metrics.py: export to SQL and observability. app.py: internal REST/gRPC server; async queues; workers; config.

## Security and Governance
### Controls
SSO and RBAC: AD authentication; Viewer/Operator/Admin roles; group mapping. CSRF/TLS: form protection; secure communication; strict headers. Dual approval: firewall/NAT/VPN/blocklists; mandatory justification. Staged + health-check: controlled application; connectivity tests; automatic rollback. Full audit: who/when/action/target/result; payload hash; defined retention. Secrets: env vars in dev; vault/secret manager in prod; periodic rotation. Privacy: collect only operational metadata; optional anonymization in aggregate reports.

## Observability
### Dashboards
Firewall: connections by port/IP, commit time, recent diffs. Network (NM): interfaces/VLANs/bridges, traffic per interface, resolvers/latency. AD: logons, failures, latency, service health. DNS: upstream latency, switch events, blocks. HV: CPU/RAM/IO per VM, storage latency, snapshots. Citrix: sessions, ICA/HDX latency, login failures, VDA usage. Packages: state/version, failures, throughput, alerts. ML: suggestions by period, acceptance/rejection rate, top anomalies.

### Alerts
Firewall drift: desired state divergence. Unstable DNS upstream: flaps/high latency. AD down: critical service failure. Insufficient VDIs: resource saturation. Critical anomaly (ML): traffic off baseline, repeated login failures.

## ML Pipeline
### Features
Network: connections by port/protocol, source/dest, times (hour/day/week), p50/p95 latency. Users/AD: login attempts per window, active groups, admin actions. DNS: upstream latency, failure rate, blocked domains. VPN/IDS: sessions, bytes, events by severity/category.

### Models
Clustering: identify traffic/app profiles. Anomaly: Isolation Forest/LOF for out-of-pattern events. Recommendation: classification based on approval history.

### Governance
Suggestions: intents with score/confidence and explainability (key features). Human approval: mandatory; dual for sensitive rules. Model audit: version, dataset, params; scheduled re-training.

## Deployment
### Environments
Dev: .env; verbose logs; synthetic data. Staging: realistic fake data; integration tests; package smoke-tests. Prod: secrets vault; full RBAC; immutable audit; tested backups/restore.

### Resilience
Staged + rollback: all network/firewall/DNS/VPN changes. Watchdog: critical services auto-restart; maintenance windows. Backups: DB, package configs, VM images, GPOs; restore runbooks. Isolation: services under systemd/containers; CPU/memory limits; separate logs.

## Setup Quickstart
### Requirements
Java (JDK): 17+ (you have 25 LTS). Maven: dependency manager. Spring Boot CLI: global install (SDKMAN). Python 3.10+: recommended virtual env. SQL Server: accessible instance. Node.js/NPM: for Tailwind per project.

### Initial Steps
Install Spring Boot CLI globally: curl SDKMAN; sdk install springboot; spring --version. Create base project: spring init --dependencies=web,thymeleaf astral-ui. Run UI: cd astral-ui && ./mvnw spring-boot:run. Create Python Service: python -m venv venv && source venv/bin/activate. Structure modules per "Code organization". Connect SQL Server: configure datasource in UI and pyodbc in Python. Add Tailwind (local, per project): npm init -y && npm install -D tailwindcss postcss autoprefixer && npx tailwindcss init.

## Coding Standards and Conventions
NM interface names: wan0, lan0, vlan{ID}, br-{role}. Aliases: NET_TENANT_100, HOST_DC1, PORT_HTTPS, APP_CITRIX. Mandatory comments: every firewall/NAT rule with reason/context. JSON payloads: consistent key/value; type validation; schema versioning. Structured logs: level, event, entity, result, duration. Tests: unit/integration; smoke for packages; staged apply in Staging; auto health-check.

## Roadmap
M0 – Core: UI SSR + Tailwind; Python Service; SQL Server; SSO/CSRF/RBAC; audit. M1 – Firewall/NAT + NM: iptables/nftables with staged/rollback; NM for interfaces/VLANs/resolvers. M2 – AD + DNS Hybrid: Samba provisioning; Pi-hole dynamic failover. M3 – Base Packages: OpenVPN, IPsec, IDS/IPS, Blocklists (pfSense-inspired). M4 – HV + Citrix: libvirt templates/inventory; Citrix CVAD publishing/monitoring. M5 – Observability: Grafana + ClickMe + ClickMeKeeper; dashboards/alerts. M6 – ML v1: basic features; anomalies; human-approved suggestions. M7 – Advanced Packages: HAProxy, Squid, Watchdog, Time Sync. M8 – ML v2 + Integrations: feedback loop; OpenTelemetry exporters (SigNoz/Prometheus/Elastic).

## Contribution Guide
Issues: clearly describe domain (firewall/NM/DNS/AD/HV/Citrix/Packages/ML), expected behavior, logs. PRs: include tests, docs, DB migration notes when needed. Style: follow conventions; keep useful/short comments; no logic duplication. Security reviews: required for firewall/NAT/VPN/blocklists/SSO/RBAC changes. Release notes: list changes, DB migrations, operational impacts, rollback steps.

## Example Snippets
### NM – Create VLAN and Set DNS per Interface
```bash
# VLAN 100 on eth0
nmcli con add type vlan ifname vlan100 dev eth0 id 100
# Bridge for VLAN
nmcli con add type bridge ifname br-tenant100
nmcli con add type bridge-slave ifname vlan100 master br-tenant100
# DNS resolvers (internal AD + external Pi-hole)
nmcli con mod br-tenant100 ipv4.dns "10.0.0.10,10.0.0.11"
nmcli con mod br-tenant100 ipv4.ignore-auto-dns yes
nmcli con up br-tenant100
```

### Python – Apply Firewall Rule with Staged/Rollback
```python
# firewall.py (excerpt)
def apply_intent(intent):
    diff = compute_diff(intent)
    stage_result = stage_rules(diff)
    if not health_check(stage_result):
        rollback(stage_result)
        persist_commit(intent['id'], diff, result='failed')
        return {'status': 'failed'}
    commit_result = commit_rules(diff)
    persist_commit(intent['id'], diff, result='success')
    return {'status': 'success', 'diff': diff}
```

### Spring Boot – Firewall Intent Endpoint
```java
// FirewallController.java (excerpt)
@PostMapping("/api/firewall/intents")
public ResponseEntity<IntentResponse> createIntent(@RequestBody FirewallIntentDto dto) {
    var intentId = intentService.register(dto); // writes to SQL
    pythonExecutor.call("/firewall/apply", dto); // async call
    return ResponseEntity.ok(new IntentResponse(intentId, "queued"));
}
```

## License
Recommended model: Apache-2.0 or MIT to facilitate adoption/contributions. Warnings: respect licenses of used packages (Samba, Pi-hole, libvirt, Snort/Suricata, HAProxy, Squid).

license GPLv3 Perfect, Eurípedes 👌. Astral HV-NGFW will be published under GNU General Public License v3.0 (GPLv3). This choice ensures:

🔓 Freedom to use: anyone can use the software for any purpose.

🛠️ Freedom to modify: code can be studied, changed, adapted.

📤 Distribution: modified versions can be distributed, always under GPLv3.

⚖️ Anti-lock-in: ensures improvements/forks remain free.

🛡️ Compatibility: protects against improper use in proprietary software without releasing code.

### README.md Snippet (GitHub)
```markdown
## License

This project is licensed under the GNU General Public License v3.0 (GPLv3).

You are free to use, modify, and distribute this software under the terms of the GPLv3.
See the [LICENSE](LICENSE) file for full details.

---

### Summary of GPLv3 terms:
- Freedom to run the program for any purpose.
- Freedom to study how the program works and change it.
- Freedom to redistribute copies.
- Freedom to distribute modified versions under the same license.
```

[1](https://grafana.com/pt-br/events/observabilitycon-on-the-road/sao-paulo/observabilidade-full-stack-analise-mais-rapida-da-causa-raiz-com-o-grafo-de-conhecimento-da-grafana-cloud/)
[2](https://www.youtube.com/watch?v=ORIWCa6Bl7M)
[3](https://grafana.com/pt-br/go/webinar/performance-testing-and-observability-with-grafana-cloud-latam/)
[4](https://www.youtube.com/watch?v=9MbuFScUvLo)
[5](https://blog.updev.dev.br/posts/grafana-a-plataforma-aberta-para-monitoramento-e-observabilidade)
[6](https://www.opservices.com.br/ferramentas-de-observabilidade/)
[7](https://pt.linkedin.com/pulse/principais-solu%C3%A7%C3%B5es-de-monitoramento-e-suas-anderson-vieira-machado-vzugf)
[8](https://edsoncelio.dev/posts/grafana-overview/)
[9](https://elven.works/observabilidade/)
[10](https://www.reddit.com/r/PFSENSE/comments/fsf7f7/my_pfsense_monitor_dashboard_in_grafana/?tl=pt-br)
