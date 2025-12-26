# Astral HV-NGFW – Reference and Updates Document  
Living Document – Version 1.1 – Last updated: November 2024

---

## 📋 ABOUT THIS DOCUMENT

This document serves as the central technical reference and changelog for the **Astral HV-NGFW** project. Instead of publishing multiple papers during development, we maintain this single, versioned document to:

- Preserve a historical record of architectural decisions  
- Document progress between significant commits  
- Serve as a reference for contributors and stakeholders  
- Avoid fragmentation of technical documentation  

> ⚠️ **IMPORTANT NOTICE**: This is a living development document. All ideas, specifications, and decisions are subject to change without notice. The architecture may evolve as development progresses.

### Conventions:
- ✅ = Implemented and tested  
- 🔄 = In development  
- 📋 = Planned / Backlog  
- ❌ = Cancelled / Replaced  

---

## 📅 UPDATE TIMELINE

### **November 2024 – Version 1.1**  
**Critical Change**: Replacement of SQL Server with **PostgreSQL**  
- **Reason**: Full alignment with open-source philosophy and GPLv3  
- **Impact**: Elimination of licensing costs, improved portability  
- **Target System**: **Fedora 43 (official release, 2025)**  

### **October 2024 – Version 1.0**  
- Creation of the central reference document  
- Consolidation of full architecture and philosophy  
- Establishment of the official roadmap  
- Definition of the **4 non-negotiable principles**

---

## 🎯 FUNDAMENTAL PRINCIPLES (IMMUTABLE)

### 1. **Determinism over Magic**  
✅ **Status**: Principle established  
📝 **Implementation**: All code follows the **intent → validation → execution → audit** pattern.

### 2. **Auditability over Convenience**  
✅ **Status**: Principle established  
📝 **Implementation**: Structured logs stored in PostgreSQL; no "silent" operations.

### 3. **Fallback over Dependency**  
✅ **Status**: Principle established  
📝 **Implementation**: Every optional component has a clean removal path.

### 4. **Human Authority over Automation**  
✅ **Status**: Principle established  
📝 **Implementation**: No unauthorized auto-remediation is permitted.

---

## 🏗️ IMPLEMENTATION STATUS BY LAYER

| Layer | Component | Status | Progress | Next Steps |
|------|----------|--------|----------|-----------|
| **1** | UI & Orchestration | 🔄 In early development | 15% | Spring Boot + Tailwind setup, basic auth, first dashboard |
| **2** | Core Python Service | 🔄 Structure defined | 10% | Module skeleton, PostgreSQL integration, unified logging |
| **3** | **PostgreSQL Database** | ✅ **Updated** | 20% | Schema design complete (astral_core, astral_telemetry), extensions: timescaledb, pgcrypto |
| **4** | Network (NetworkManager) | ✅ POC implemented | — | Basic Python-controlled functionality tested |
| **5** | Firewall (iptables/nftables) | ✅ POC implemented | — | Basic rules working; UI integration pending |
| **6** | Identity (Samba AD DC) | ✅ POC implemented | — | Basic domain functional; full automation pending |
| **7** | Hybrid DNS (Pi-hole + AD) | ✅ POC implemented | — | Working in isolated environment |
| **8** | Hypervisor (KVM/libvirt) | 📋 Planned | 0% | — |
| **9** | Citrix CVAD Compatibility | 📋 Defined (not included) | — | Will only provide configuration interface if user supplies Citrix |
| **10** | Package System | 📋 In design | 2% | — |
| **11** | Observability | 📋 In design | 3% | — |

---

## 🔄 DYNAMIC ROADMAP

### **M0 – Core System (CURRENT)**  
**Estimated Timeline**: Nov 2024 – Jan 2025  
**Progress**: 30%  

**Completed Tasks**:  
- Full architecture defined  
- Fundamental principles established  
- PostgreSQL migration decision ✅  
- Spring Boot dev environment setup  
- Initial PostgreSQL schema  
- Base Python service  

### **M1 – Firewall/NAT + NetworkManager**  
**Estimated Timeline**: Feb 2025 – Mar 2025  
**Progress**: 0%  

### **M2 – AD + Hybrid DNS**  
**Estimated Timeline**: Apr 2025 – May 2025  
**Progress**: 0%  

> *(Roadmap will be updated continuously as work progresses)*

---

## 🐛 ARCHITECTURAL DECISION LOG

### **[Nov 2024] Decision: PostgreSQL as Primary Database**  
**Context**: Need for ACID-compliant infrastructure transactions with open licensing.  
**Alternatives Considered**: SQL Server, MySQL, SQLite  
**Previous Decision**: SQL Server (for Microsoft ecosystem integration)  
**New Decision**: **PostgreSQL** (open source, mature, GPLv3-compatible)  

**Reasons for Change**:  
- Full alignment with free software philosophy  
- Zero licensing costs  
- Maximum portability (cloud, on-prem, hybrid)  
- Active community and robust ecosystem  

**Consequences**:  
- ✅ Zero licensing costs  
- ✅ Full GPLv3 compliance  
- ✅ Maximum portability  
- 🔄 Requires adaptation of some proprietary features  

---

### **[Oct 2024] Decision: Python as Core Execution Language**  
**Context**: Need for a sysadmin-friendly language with mature libraries.  
**Alternatives Considered**: Go, Rust, Java  
**Decision**: **Python** — mature ecosystem for system automation  
**Consequences**: Acceptable performance, rapid prototyping  

### **[Oct 2024] Decision: GPLv3 Licensing**  
**Context**: Guarantee software freedom and prevent vendor lock-in.  
**Alternatives Considered**: Apache 2.0, MIT, AGPL  
**Decision**: **GPLv3** — strongest protection against proprietary enclosure  
**Consequences**: All contributions must be GPLv3-compatible  

---

## 📊 UPDATED TECHNICAL SPECIFICATIONS

### **Official Development Environment**
- **OS**: Fedora 43 Workstation / Server  
- **Architecture**: x86_64  
- **Minimum RAM**: 4 GB  
- **Storage**: 25 GB minimum  

### **Technology Stack**
```text
Core Backend: Python 3.12+
  - Modules: psycopg2, sqlalchemy, flask (internal APIs)
  - Framework: Custom (no Django/Flask for core)

UI/Orchestration: Spring Boot 3.2+
  - Template Engine: Thymeleaf
  - CSS Framework: Tailwind CSS
  - Authentication: Spring Security + Samba AD

Database: PostgreSQL 18+
  - Extensions: timescaledb, pg_stat_statements, pgcrypto
  - Connection Pooling: HikariCP (Spring) / psycopg2.pool (Python)

System Integration:
  - Network: NetworkManager (nmcli/dbus via Python)
  - Firewall: iptables/nftables (Python wrapper)
  - Virtualization: KVM/libvirt (Python bindings)
  - Identity: Samba 4.20+ (AD Domain Controller)
```

### **PostgreSQL Setup for Fedora 43**
```bash
# Minimal installation
sudo dnf install postgresql-server postgresql-contrib
sudo postgresql-setup --initdb
sudo systemctl enable --now postgresql

# Access configuration (pg_hba.conf)
# host    all    all    127.0.0.1/32    scram-sha-256
```

---

## 📈 UPCOMING MILESTONES

### **Milestone 1: First Public Commit**  
**Target Date**: January 2025  
**Completion Criteria**:  
- Basic working UI (Spring Boot)  
- Python service responding to requests  
- PostgreSQL with minimal schema  
- Initial documentation  
- GPLv3 license applied  

### **Milestone 2: Functional MVP**  
**Target Date**: June 2025  
**Completion Criteria**:  
- End-to-end flow: UI → Python → PostgreSQL → UI  
- Basic firewall configuration via UI  
- Working AD authentication  
- 3 installable core packages  

---

## ⚠️ KNOWN LIMITATIONS AND RESTRICTIONS

### **Current Restrictions**
- ❌ **No Docker support**: Native installation only (Fedora/RHEL)  
- ❌ **No Citrix VDI included**: Only config compatibility if user provides Citrix  
- ⚠️ **Minimum 4 GB RAM** required for basic operation  
- 🔧 **Nested virtualization required** for development environments  

### **Critical Dependencies**
- PostgreSQL 18+ (Fedora 43 official repos)  
- Python 3.12+ (Fedora 43 default)  
- Samba 4.20+ (for AD DC)  
- Linux Kernel 6.8+ (Fedora 43 default)

---

## 🔍 MONITORING AND METRICS

### **Project Metrics (To Be Tracked)**
- **Code**: Lines of code, commits, active branches  
- **Testing**: Test coverage %, automated test count  
- **Performance Targets**:  
  - Commit latency: < 100 ms  
  - Telemetry throughput: > 50k events/sec  
  - Audit query time (last 30 days): < 1 sec  

---

## 🤝 COLLABORATION MODEL

### **For Developers**
- Fork the repository (when public)  
- Consult this document for architectural context  
- Adhere to the four fundamental principles  
- Document decisions in the relevant section  

### **For Testers / Users**
- Report issues with clear use-case scenarios  
- Document desired workflows  
- Provide UX feedback  

### **For Researchers**
- This document may be cited as a reference  
- Contact for academic collaboration  
- Suitable for infrastructure/systems coursework  

---

## 📚 REFERENCES AND LINKS

### **Official Documentation**
- Main Repository: [LINK TBD – GitHub/GitLab]  
- Project Wiki: [LINK TBD]  
- Issue Tracker: [LINK TBD]  

### **Technologies Used**
- PostgreSQL: https://www.postgresql.org/docs/18/  
- Spring Boot: https://spring.io/projects/spring-boot  
- Python: https://docs.python.org/3/  
- Samba: https://wiki.samba.org/  
- Fedora: https://docs.fedoraproject.org/  

### **Related Documentation**
- Original Thesis (2005): [REFERENCE – 2005 work on open-source software]  
- Design Philosophy: Section 2 of this document  
- API Reference: [LINK TBD – when available]

---

## 🏷️ DOCUMENT VERSION HISTORY

| Version | Date       | Key Changes                          | Author               |
|--------|------------|--------------------------------------|----------------------|
| 1.0    | Oct 2024   | Initial document creation            | Euripedes Batista    |
| 1.1    | Nov 2024   | PostgreSQL migration, Fedora 43 spec | Euripedes Batista    |
| 1.2    | [FUTURE]   | [TO BE DEFINED]                      | [TO BE DEFINED]      |

---

## 🚨 FINAL WARNING

This is a **living development document**.  

All specifications, architecture, and documented decisions are **subject to change without notice**. This document reflects the current state of thinking and development for the **Astral HV-NGFW** project, but **does not constitute a final commitment** to any specific implementation.

Open-source software development is an **iterative and collaborative process**. We welcome feedback and contributions, but ask for your understanding that elements may be **modified, refined, or even abandoned** as the project evolves.
