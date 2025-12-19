Astral HV-NGFW - Documento de Referências e Atualizações
Documento Vivo - Versão 1.1 - Última atualização: Novembro 2024
📋 SOBRE ESTE DOCUMENTO
Este documento serve como referência técnica central e registro de atualizações do projeto Astral HV-NGFW. Em vez de publicar múltiplos papers durante o desenvolvimento, utilizamos este documento único e versionado para:

Manter um registro histórico das decisões arquiteturais

Documentar progresso entre commits significativos

Servir como referência para colaboradores e interessados

Evitar fragmentação da documentação técnica

⚠️ AVISO IMPORTANTE: Este é um documento de atualização em desenvolvimento. Todas as ideias, especificações e decisões estão sujeitas a mudanças sem aviso prévio. A arquitetura pode evoluir conforme o desenvolvimento progride.

Convenções:

✅ = Implementado e testado

🔄 = Em desenvolvimento

📋 = Planejado/Backlog

❌ = Cancelado/Substituído

📅 LINHA DO TEMPO DE ATUALIZAÇÕES
Novembro 2024 - Versão 1.1
Mudança crítica: Substituição de SQL Server por PostgreSQL

Motivo: Alinhamento completo com filosofia open source e GPLv3

Impacto: Redução de custos de licenciamento, aumento da portabilidade

Sistema alvo: Fedora 43 (oficial, 2025)

Outubro 2024 - Versão 1.0
Criação do documento de referência central

Consolidação de toda arquitetura e filosofia

Estabelecimento do roadmap oficial

Definição dos 4 princípios não negociáveis

🎯 PRINCÍPIOS FUNDAMENTAIS (IMUTÁVEIS)
1. Determinismo sobre Mágica
✅ Status: Princípio estabelecido
📝 Implementação: Todo código segue padrão intenção→validação→execução→auditoria

2. Auditabilidade sobre Conveniência
✅ Status: Princípio estabelecido
📝 Implementação: Logs estruturados em PostgreSQL, sem operações "silenciosas"

3. Fallback sobre Dependência
✅ Status: Princípio estabelecido
📝 Implementação: Cada componente opcional tem caminho de remoção limpo

4. Autoridade Humana sobre Automação
✅ Status: Princípio estabelecido
📝 Implementação: Nenhuma auto-remediação não autorizada

🏗️ STATUS DE IMPLEMENTAÇÃO POR CAMADA
Camada 1: UI e Orquestração
🔄 Status: Em desenvolvimento inicial
📅 Progresso: 15%
📋 Próximos passos:

Setup Spring Boot + Tailwind

Autenticação básica

Primeiro dashboard

Camada 2: Serviço Python Central
🔄 Status: Estrutura definida
📅 Progresso: 10%
📋 Próximos passos:

Esqueleto dos módulos principais

Comunicação com PostgreSQL

Sistema de logging unificado

Camada 3: Banco de Dados PostgreSQL ✅ ATUALIZADO
📋 Status: Schema projetado para PostgreSQL
📅 Progresso: 20%
📋 Especificações:

Versão: PostgreSQL 18+ (Fedora 43)

Extensões: timescaledb, pg_stat_statements, pgcrypto

Schemas: astral_core (configurações), astral_telemetry (métricas)

Backup: pg_dump + WAL archiving nativo

Camada 4: Network (NetworkManager)
✅ Status: Implementado em POC
📝 Notas: Funcionalidades básicas testadas via Python

Camada 5: Firewall (iptables/nftables)
✅ Status: Implementado em POC
📝 Notas: Regras básicas funcionais, falta integração com UI

Camada 6: Identidade (Samba AD DC)
✅ Status: Implementado em POC
📝 Notas: Domínio básico funcionando, falta automatização completa

Camada 7: DNS Híbrido (Pi-hole + AD)
✅ Status: Implementado em POC
📝 Notas: Funcionando em ambiente isolado

Camada 8: Hypervisor (KVM/libvirt)
📋 Status: Planejado
📅 Progresso: 0%

Camada 9: Compatibilidade Citrix CVAD
📋 Status: Definido (não incluído)
📝 Notas: Será apenas interface de configuração se fornecido pelo usuário

Camada 10: Sistema de Pacotes
📋 Status: Em design
📅 Progresso: 2%

Camada 11: Observabilidade
📋 Status: Em design
📅 Progresso: 3%

🔄 ROADMAP DINÂMICO
M0 – Núcleo do Sistema (ATUAL)
Prazo estimado: Nov 2024 - Jan 2025
Progresso: 30%

Tarefas:

Definir arquitetura completa

Estabelecer princípios fundamentais

Decisão de mudança para PostgreSQL ✅

Setup ambiente desenvolvimento Spring Boot

Schema inicial PostgreSQL

Serviço Python base

M1 – Firewall/NAT + NetworkManager
Prazo estimado: Fev 2025 - Mar 2025
Progresso: 0%

M2 – AD + DNS Híbrido
Prazo estimado: Abr 2025 - Mai 2025
Progresso: 0%

[Roadmap continuará sendo atualizado conforme progresso]

🐛 REGISTRO DE DECISÕES ARQUITETURAIS
[Nov 2024] - Decisão: PostgreSQL como banco primário
Contexto: Necessidade de ACID para transações de infraestrutura com licenciamento aberto
Alternativas consideradas: SQL Server, MySQL, SQLite
Decisão anterior: SQL Server pela integração com ecossistema Microsoft
Nova decisão: PostgreSQL por ser open source, maduro e compatível com GPLv3
Razões para mudança:

Alinhamento completo com filosofia de software livre

Eliminação de custos de licenciamento

Maior portabilidade entre ambientes (cloud, on-prem, híbrido)

Comunidade ativa e ecossistema robusto
Consequências:

✅ Zero custos de licenciamento

✅ Compatibilidade total com GPLv3

✅ Portabilidade máxima

🔄 Necessidade de adaptar alguns recursos específicos

[Out 2024] - Decisão: Python como núcleo de execução
Contexto: Necessidade de linguagem para sysadmin com bibliotecas maduras
Alternativas consideradas: Go, Rust, Java
Decisão: Python pela maturidade em automação de sistemas
Consequências: Performance aceitável, fácil prototipagem

[Out 2024] - Decisão: GPLv3 para licenciamento
Contexto: Garantir liberdade do software
Alternativas: Apache 2.0, MIT, AGPL
Decisão: GPLv3 para proteger contra enclausuramento
Consequências: Contribuições devem ser GPLv3 compatíveis

📊 ESPECIFICAÇÕES TÉCNICAS ATUALIZADAS
Ambiente de Desenvolvimento Oficial
Sistema Operacional: Fedora 43 Workstation/Server

Arquitetura: x86_64

Memória Mínima: 4GB RAM

Armazenamento: 25GB mínimo

Stack Tecnológica
text
Backend Principal: Python 3.12+
- Módulos: psycopg2, sqlalchemy, flask (para APIs internas)
- Framework: Custom (não Django/Flask para o core)

UI/Orquestração: Spring Boot 3.2+
- Template Engine: Thymeleaf
- CSS Framework: Tailwind CSS
- Autenticação: Spring Security + Samba AD

Banco de Dados: PostgreSQL 18+
- Extensões: timescaledb, pg_stat_statements, pgcrypto
- Pooling: HikariCP (Spring) / psycopg2.pool (Python)

Rede/Sistema:
- Network Manager: nmcli/dbus (via Python)
- Firewall: iptables/nftables (Python wrapper)
- Virtualização: KVM/libvirt (Python binding)
- Identidade: Samba 4.20+ (AD DC)
Configuração PostgreSQL para Fedora 43
bash
# Instalação mínima
sudo dnf install postgresql-server postgresql-contrib
sudo postgresql-setup --initdb
sudo systemctl enable --now postgresql

# Configuração de acesso (pg_hba.conf)
# host    all    all    127.0.0.1/32    scram-sha-256
📈 PRÓXIMOS MILESTONES
Milestone 1: Primeiro Commit Público
Critérios de conclusão:

UI básica funcionando (Spring Boot)

Serviço Python respondendo

PostgreSQL com schema mínimo

Documentação inicial

Licença GPLv3 aplicada

Data alvo: Janeiro 2025

Milestone 2: MVP Funcional
Critérios:

Fluxo completo UI→Python→PostgreSQL→UI

Firewall básico via UI

Autenticação AD funcionando

3 pacotes básicos instaláveis

Data alvo: Junho 2025

⚠️ LIMITAÇÕES E RESTRIÇÕES CONHECIDAS
Restrições Atuais
Não suporta Docker: Instalação apenas nativa no Fedora/RHEL

Não inclui VDI Citrix: Apenas compatibilidade se fornecido pelo usuário

Requisito mínimo: 4GB RAM para operação básica

Virtualização: Requer suporte de virtualização aninhada para desenvolvimento

Dependências Críticas
PostgreSQL 18+ (Fedora 43 repositórios oficiais)

Python 3.12+ (Fedora 43 padrão)

Samba 4.20+ (para AD DC)

Kernel Linux 6.8+ (Fedora 43 padrão)

🔍 MONITORAMENTO E MÉTRICAS
Métricas do Projeto (a serem coletadas)
Código:

Linhas de código: [A DEFINIR]

Commits: [A DEFINIR]

Branches ativos: [A DEFINIR]

Testes:

Cobertura atual: [A DEFINIR]

Testes automatizados: [A DEFINIR]

Performance Alvo:

Latência de commit: < 100ms

Throughput de telemetria: > 50k eventos/segundo

Tempo de query de auditoria: < 1s (últimos 30 dias)

🤝 MODELO DE COLABORAÇÃO
Para Desenvolvedores
Fork do repositório (quando disponível)

Consulte este documento para contexto arquitetural

Siga os princípios fundamentais em todas as contribuições

Documente decisões na seção correspondente

Para Testadores/Usuários
Reporte issues com cenários de uso claros

Documente workflows desejados

Forneça feedback sobre experiência de usuário

Para Pesquisadores
Este documento pode ser citado como referência

Contate para colaborações em papers acadêmicos

Use em disciplinas de infraestrutura e sistemas

📚 REFERÊNCIAS E LINKS
Documentação Oficial
Repositório principal: [LINK A DEFINIR - GitHub/GitLab]

Wiki do projeto: [LINK A DEFINIR]

Sistema de issues: [LINK A DEFINIR]

Tecnologias Utilizadas
PostgreSQL: https://www.postgresql.org/docs/18/

Spring Boot: https://spring.io/projects/spring-boot

Python: https://docs.python.org/3/

Samba: https://wiki.samba.org/

Fedora: https://docs.fedoraproject.org/

Documentação Relacionada
Monografia original (2005): [REFERÊNCIA - trabalho de 2005 sobre software aberto]

Filosofia de design: Seção 2 deste documento

API Reference: [LINK A DEFINIR - quando disponível]

🏷️ VERSIONAMENTO DESTE DOCUMENTO
Versão	Data	Mudanças Principais	Autor
1.0	Out 2024	Criação inicial do documento	Euripedes Batista
1.1	Nov 2024	Mudança para PostgreSQL, especificação Fedora 43	Euripedes Batista
1.2	[FUTURO]	[A DEFINIR]	[A DEFINIR]
🚨 AVISO FINAL
Este é um documento de atualização em desenvolvimento ativo.

As especificações, arquitetura e decisões documentadas aqui estão sujeitas a mudanças sem aviso prévio. Este documento representa o estado atual do pensamento e desenvolvimento do projeto Astral HV-NGFW, mas não constitui um compromisso final com qualquer implementação específica.

O desenvolvimento de software open source é um processo iterativo e colaborativo. Encorajamos feedback e contribuições, mas pedimos compreensão de que elementos podem ser alterados, refinados ou mesmo abandonados conforme o projeto evolui.

