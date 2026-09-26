# ROADMAP

Estado de entrega do ERP. Cada item é uma fatia entregável: `routes/` +
`services/` + `repositories/`, com a suíte unitária do service e o teste de
integração rota+DB que o `AGENTS.md` exige.

Marque `[x]` assim que o item estiver concluído — é obrigação do `AGENTS.md`.

Termos de domínio em inglês. Escopo e faseamento derivados de
[`docs/01-modulos-erp.md`](docs/01-modulos-erp.md).

**Legenda:** `[ ]` pendente · `[~]` em andamento · `[x]` concluído

---

## Phase 0 — Foundation

Sem isto nada mais roda.

- [ ] Bootstrap do repo: API (`bun` + Fastify) e Web (Vite, `:5173`)
- [ ] `CONTEXT.md` — idioma e glossário de domínio (referenciado pelo `AGENTS.md`, ainda não existe)
- [ ] Container de DI caseiro + `container.clear()`
- [ ] `createServer()` e `GET /health`
- [ ] Middleware de erro: `{ error, statusCode }`
- [ ] SQLite: conexão, migrations, seed de desenvolvimento
- [ ] Harness de teste: `bun:test` + `sinon` + `fastify.inject()`
- [ ] Layout base do Web + roteamento + client HTTP

## Phase 1 — Platform

- [ ] `User` — cadastro, senha com hash, sessão
- [ ] `Auth` — login, logout, refresh, recuperação de senha
- [ ] `Role` e `Permission` — catálogo granular (`purchase_order.approve`)
- [ ] `RoleAssignment` — papel **com escopo** (`user × role × scope`)
- [ ] Guard de autorização nas rotas
- [ ] `ApprovalLimit` — alçada por valor, tipo de item e projeto
- [ ] `Company` / `Branch` — multiempresa desde o início
- [ ] `AuditLog` — trilha imutável em todo documento
- [ ] `ApprovalWorkflow` — motor genérico de aprovação
- [ ] `Notification` — in-app e e-mail
- [ ] `Attachment` — upload e vínculo a documentos

## Phase 2 — Master data

- [ ] `UnitOfMeasure` + conversões
- [ ] `Item` — SKU único (`409` via violação de índice), NCM, grupo, família
- [ ] `Item` — curva ABC, estoque mín/máx, reorder point, lead time
- [ ] `Supplier` — dados fiscais, contatos, condições comerciais
- [ ] `SupplierCertificate` — validade e bloqueio automático ao vencer
- [ ] `Customer` e `Contract`
- [ ] `Employee` — função, custo-hora, encargos
- [ ] `Qualification` — certificações NR com validade
- [ ] `Team` / `Crew`
- [ ] `CostCenter` e plano de contas
- [ ] `Warehouse` — central, canteiro, frente de serviço

## Phase 3 — Projects

- [ ] `Project` — contrato, prazo, responsáveis
- [ ] `WbsNode` — EAP hierárquica (fase → serviço → atividade)
- [ ] `BudgetLine` — orçamento por serviço com composição de insumos
- [ ] `Schedule` — cronograma, dependências, caminho crítico
- [ ] `ProgressMeasurement` — avanço físico por serviço
- [ ] `DailyReport` (RDO) — clima, efetivo, equipamentos, ocorrências, fotos
- [ ] `Amendment` — aditivos de escopo e orçamento
- [ ] Tela de projeto: EAP, orçamento, avanço

## Phase 4 — Inventory

- [ ] `StockBalance` — saldo por item × warehouse
- [ ] `StockMovement` — documento **imutável**, estorno em vez de edição
- [ ] Movimentos: receipt, issue, transfer, return, adjustment, loss
- [ ] `MaterialRequest` — operador solicita → aprovação → almoxarife atende
- [ ] Apropriação obrigatória: `project` + `costCenter` + `wbsNode`
- [ ] Valoração por custo médio ponderado
- [ ] `StockReservation` — empenho para atividade programada
- [ ] `Inventory` — contagem cega, rotativa e geral, aprovação de divergência
- [ ] `Tool` e `Ppe` — entrega e devolução por colaborador (NR-6)
- [ ] Telas: saldo, movimentação, requisição, inventário

## Phase 5 — Procurement

- [ ] `PurchaseRequest` — manual, por reorder point ou por necessidade do projeto
- [ ] Aprovação de PR por alçada
- [ ] `Rfq` / `Quotation` — envio a N fornecedores, prazo de resposta
- [ ] `QuotationComparison` — mapa comparativo, justificativa obrigatória fora do menor preço
- [ ] `PurchaseOrder` — itens, entrega programada, condições, impostos, frete
- [ ] Aprovação de PO por alçada
- [ ] `GoodsReceipt` — conferência a três vias (NF × PO × PR), aceite parcial
- [ ] `Divergence` — tratamento de divergência de recebimento
- [ ] `DeliveryFollowUp` — atrasos e cobrança
- [ ] `Subcontractor` e medição de serviço
- [ ] Telas: PR, cotação, mapa comparativo, PO, recebimento

## Phase 6 — Workforce

- [ ] `HeadcountPlan` — efetivo planejado por projeto e período
- [ ] `Allocation` — employee/team → project → frente → período
- [ ] Detecção de conflito de alocação
- [ ] Bloqueio por qualificação vencida
- [ ] `Timesheet` — apontamento por `wbsNode`, hora normal/extra/noturna/improdutiva
- [ ] `Attendance` — faltas, atestados, férias
- [ ] Custo de mão de obra apropriado ao projeto
- [ ] `Productivity` — horas × quantidade produzida vs. índice orçado
- [ ] Telas: quadro de alocação, apontamento, disponibilidade

## Phase 7 — Cost control

- [ ] `CostEntry` — consolidação de material, mão de obra, equipamento e serviços
- [ ] Rateio de custos indiretos
- [ ] `ActualVsBudget` por `wbsNode`
- [ ] Curva S física × financeira
- [ ] Dashboard por perfil (admin vê empresa, gestor vê seu projeto, operador vê tarefas)
- [ ] Alertas: estoque mínimo, certidão vencida, projeto estourando orçamento

## Phase 8 — Finance

- [ ] `Payable` — originado de recebimento e de medição de subcontratado
- [ ] Aprovação de pagamento por alçada
- [ ] `Receivable` — medições contratuais, faturamento, retenções
- [ ] `BankAccount` e conciliação
- [ ] `CashFlow` projetado
- [ ] `TaxDocument` — entrada de NF-e (XML), validação contra PO
- [ ] Retenções: ISS, INSS, IRRF
- [ ] Exportação contábil

## Phase 9 — Equipment

- [ ] `Equipment` — próprio e alugado
- [ ] `EquipmentAllocation` por projeto
- [ ] `UsageLog` — horímetro e odômetro
- [ ] `Maintenance` — preventiva e corretiva
- [ ] `Refueling`
- [ ] Custo-hora de equipamento apropriado ao projeto

## Phase 10 — Field and integrations

- [ ] App mobile offline-first: requisição, RDO, apontamento, recebimento
- [ ] Sincronização e resolução de conflito
- [ ] Código de barras / QR na movimentação
- [ ] Aprovação por mobile
- [ ] Portal do fornecedor — cotação e envio de NF
- [ ] SSMA — EPI, ASO, treinamentos NR, incidentes, DDS
- [ ] Qualidade — FVS, não conformidades, checklists de liberação
- [ ] Integrações: SEFAZ, bancos (CNAB/PIX), folha, SINAPI

---

## Decisões travadas

Não reabrir sem motivo novo — ver seção 10 de [`docs/01-modulos-erp.md`](docs/01-modulos-erp.md).

| # | Decisão |
|---|---|
| 1 | Valoração de estoque: **custo médio ponderado** |
| 2 | Apropriação de custo na **`wbsNode`**; projeto e centro de custo são derivados |
| 3 | Permissão é **papel com escopo**, nunca papel global |
| 4 | Movimento de estoque e PO aprovada são **imutáveis** — estorna, não edita |
| 5 | **Multiempresa desde o dia 1**, mesmo com uma empresa |
| 6 | Campo é **offline-first**: IDs gerados no cliente |
| 7 | `UnitOfMeasure` e conversão são cidadãos de primeira classe |
