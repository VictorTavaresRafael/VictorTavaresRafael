# Estratégia de Portfólio

Legenda: **[F]** fato pesquisado · **[H]** hipótese a validar · **[O]** opinião estratégica.

## 1. Decisões

| Papel | Projeto | Status |
|---|---|---|
| Principal | **Job Radar AI** (`radar-vagas`) — score explicável, rascunhos sem invenção | MVP pronto; próximo: eval |
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

## 4. Job Radar AI (`radar-vagas`): estado atual e próximos passos

### Já implementado (MVP do `SPEC.md`, CI verde: 121 testes, typecheck, lint, build)
- Coletores Greenhouse, Lever, GitHub Issues e Remotive com `fetch` injetável e fixtures.
- Normalização, dedup (empresa + título) com `alt_urls`.
- Filtros eliminatórios por seção (requisito ≠ diferencial) + score determinístico explicado + LLM opcional validado por zod.
- Palavras-chave de ATS ("tem e não escreveu" vs. "não tem").
- Rascunhos com `verifyDraft` anti-invenção.
- Kanban, histórico, métricas, export CSV, alerta Telegram, Basic Auth, deploy Vercel + Supabase + cron no Actions.

### Decisão de arquitetura
**Manter a stack atual** (Next.js 16 + Drizzle + PGlite/Postgres + Actions como scheduler).
Migrar para NestJS + BullMQ + Redis não se justifica: o volume é de duas coletas por dia e não há worker contínuo. Adicionar isso só para o currículo seria complexidade sem motivo, e isso pesa contra em entrevista.

### Backlog proposto (cada item exige aprovação, porque o `SPEC.md` limita o escopo)
1. **Eval do score**: conjunto rotulado de vagas reais (relevante/irrelevante + motivo de eliminação esperado), `npm run eval` com precisão/recall dos filtros e do limiar de score, resultado no README. É o maior diferencial para entrevista.
2. **Coletor Ashby** (API pública de job board).
3. **Saúde das fontes**: tabela `collection_runs` (fonte, duração, itens, erros) + página `/fontes`. Absorve o projeto Observability.
4. **Demo pública** com dados seed e sem senha (modo somente leitura).
5. **MCP server**: consultar vagas, score e pipeline pelo Claude.
6. **Generalizar o perfil** (sair do `config/profile.ts` fixo) apenas se o objetivo for virar OSS para terceiros.

### Definição de pronto (v1.0)
- Eval no CI com limiar mínimo e números no README.
- Demo pública.
- GIF + diagrama no README (o diagrama já existe).
- 4+ semanas de uso real.

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
| 1–2 | radar-vagas: eval do score no CI → post LinkedIn #1 |
| 3 | Coletor Ashby + saúde das fontes |
| 4–5 | Demo pública + GIF → release v1.0 |
| 6 | MCP server |
| 7 | Portfólio pessoal |
| 8–15 | Regression Intelligence v0.1 + backtesting + artigo |

Regra de corte: sem usuários além do autor até a semana 8 → manter como portfólio, sem SaaS.

## 7. Pontos para entrevista

1. Score explicável e medido (eval set, precisão/recall, regressão de prompt no CI).
2. Dedup entre fontes e falhas isoladas por fonte/item (zod item a item).
3. Prevenção de alucinação nos rascunhos (`verifyDraft`).
4. Custo de LLM: vagas eliminadas não chamam o modelo; fallback determinístico.
5. Drizzle + PGlite: mesmo schema em testes offline e produção; salvaguardas de deploy na Vercel.
6. AST/grafo antes de LLM; validação com SZZ.
7. UX: estados vazios, kanban acessível, atalhos.
