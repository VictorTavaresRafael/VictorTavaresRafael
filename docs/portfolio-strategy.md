# Estratégia de Portfólio

Legenda: **[F]** fato pesquisado · **[H]** hipótese a validar · **[O]** opinião estratégica.

## 1. Decisões

| Papel | Projeto | Status |
|---|---|---|
| Principal | **Job Radar AI** (`radar-vagas`) — matching explicável, OSS self-hosted | Em desenvolvimento |
| Complementar técnico | **Regression Intelligence** → CLI + GitHub Action `blast-radius` (TS/JS) | Após v0.1 do Job Radar |
| Absorvido | **Observability Toolkit** → vira "saúde das fontes" + filas/retries dentro do Job Radar | Não será produto |
| Experimentos | MCP server do Job Radar · Design System Auditor (React) | Backlog |
| Manutenção | SIGA: CI, deploy, README | Semana 0 |
| Vitrine | Portfólio pessoal com estudos de caso | Após MVP do Job Radar |

## 2. Concorrência (resumo)

- **Job Radar [F]:** OSS — JobSync, Job-Ops, OpenApply, Vega AI, FitPilot. Comerciais — Huntr (~US$40/mês), Teal+ (~US$29/mês), Simplify+ (~US$40/mês).
  - Lacuna [O]: score opaco, sem separação obrigatório/desejável com evidência, risco de alucinação no currículo, nicho BR + remoto internacional.
- **Regression Intelligence [F]:** CodeRabbit, Greptile, Qodo Merge, CodeScene; `jest --findRelatedTests`, `nx affected`, Launchable.
  - Lacuna [O]: relatório determinístico de impacto + risco explicável + backtesting (SZZ).
- **Observability [F]:** Uptime Kuma, Gatus, OpenStatus, Healthchecks.io, Checkmate, OneUptime. Mercado saturado.

## 3. Matriz (1–5, total máx. 60)

| Projeto | Total |
|---|---|
| Job Radar AI | 53 |
| Regression Intelligence | 43 |
| Agendamento WhatsApp (micro-SaaS) | 36 |
| MCP server | 34 |
| Design System Auditor | 33 |
| Observability Toolkit | 28 |

## 4. Job Radar AI — especificação do MVP (v0.1)

**Tagline:** seu radar de carreira open source — encontre as vagas certas e entenda *por que* combinam com você.

**Princípios:** humano no controle · zero candidatura automática · zero fato inventado · respeito a ToS (sem scraping de LinkedIn/Indeed/Glassdoor).

### Funcionalidades v0.1
1. Perfil: upload de PDF → perfil estruturado editável (skills com evidência).
2. Ingestão: APIs públicas Greenhouse / Lever / Ashby, RemoteOK/Remotive, colar URL/texto.
3. Normalização + dedup (hash normalizado + similaridade de embedding).
4. Extração estruturada via LLM (schema Zod): obrigatório, desejável, senioridade, modalidade, salário, restrição de país.
5. Score explicável: fórmula determinística ponderada + trechos da vaga e do currículo + gaps.
6. Dashboard com filtros + kanban de candidaturas com histórico.
7. Eval set (~50 vagas rotuladas) + `pnpm eval` no CI (precisão/recall da extração).

### v0.2+
Currículo adaptado com rastreabilidade e diff · carta de apresentação · alertas · painel de saúde das fontes · tendências de skills · MCP server · extensão "clip".

### Arquitetura

```
apps/web     Next.js App Router · shadcn/ui · TanStack Query/Table · Recharts
apps/api     NestJS REST + OpenAPI · auth · domínio
apps/worker  NestJS standalone + BullMQ (ingestão, extração LLM, embeddings, alertas)
packages/    core (scoring puro) · adapters (SourceAdapter) · llm (providers) · ui
infra        Postgres + pgvector · Redis · Docker Compose · GitHub Actions
```

| Tecnologia | Justificativa |
|---|---|
| pnpm + Turborepo | Tipos compartilhados web/api/worker |
| BullMQ + Redis | Agendamento, rate limit de LLM, retry/backoff, DLQ |
| Postgres + pgvector | Dedup e busca semântica sem vector DB extra |
| Prisma | Migrações versionadas |
| Provider LLM abstrato | Claude padrão, Ollama no self-hosted, BYOK |
| Vitest · Testcontainers · Playwright | Unitário no scoring, integração com Postgres real, E2E |
| Vercel + Railway | Deploy web / API, worker, banco, Redis |

### Direção visual
Dark mode padrão, cor de destaque própria, densidade estilo Linear/Attio, ⌘K (Raycast), skeletons, estados vazios com CTA, kanban acessível (dnd-kit), contraste AA.

### Definição de pronto (v1.0)
- Demo pública com dados seed, sem cadastro.
- `docker compose up` em < 5 min.
- CI: lint, typecheck, testes, eval com limiar mínimo, build de imagens.
- README com GIF, diagrama, ADRs e resultados do eval.
- 4+ semanas de uso real na própria busca.

## 5. Regression Intelligence — v0.1

1. `git diff base..head` → arquivos/exports alterados.
2. Grafo de módulos (dependency-cruiser/ts-morph) → dependentes reversos.
3. Mineração git: co-change, churn, histórico de fix/revert.
4. Testes relacionados por grafo; lcov opcional.
5. Score de risco com features nomeadas e pesos visíveis.
6. Saída markdown + GitHub Action comentando no PR.
7. LLM opcional, restrito às evidências do grafo.

Validação: backtesting com SZZ em 3–5 repositórios TS OSS vs. baseline (tamanho do diff).
Referências: Zimmermann et al. (2004) "Mining Version Histories to Guide Software Changes"; Śliwerski, Zimmermann, Zeller (2005) — SZZ.

## 6. Roadmap (~10h/semana)

| Semana | Entrega |
|---|---|
| 0 | Polir SIGA · atualizar README do perfil |
| 1–2 | Monorepo, auth, schema Prisma, design system, landing |
| 3–4 | Adapters + BullMQ, dedup, listagem/filtros |
| 5–6 | Extração LLM, scoring explicável, tela de detalhe |
| 7 | Eval set no CI → post LinkedIn #1 |
| 8 | Kanban, demo pública → release v0.1 |
| 9–10 | Currículo rastreável, alertas, saúde das fontes → v0.2 |
| 11 | MCP server |
| 12 | Portfólio pessoal |
| 13–20 | Regression Intelligence v0.1 + backtesting + artigo |

Regra de corte: sem usuários além do autor até a semana 10 → manter como portfólio, sem SaaS.

## 7. Pontos para entrevista

1. Score explicável e medido (eval set, precisão/recall, regressão de prompt no CI).
2. Idempotência e retry na ingestão (BullMQ, chaves de dedup, DLQ).
3. Prevenção de alucinação no currículo (rastreabilidade de fatos).
4. Custo/latência de LLM (cache por hash, modelo menor, batching).
5. Isolamento multi-tenant, rate limit, chaves BYOK criptografadas.
6. AST/grafo antes de LLM; validação com SZZ.
7. UX: estados vazios, kanban acessível, atalhos.
