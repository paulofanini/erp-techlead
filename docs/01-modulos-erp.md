# ERP de Construção Civil — Mapa de Módulos

> Levantamento dos módulos necessários para um ERP completo e funcional com
> **estoque/almoxarifado**, **obras**, **alocação de equipes**, **perfis
> administrador/operador** e **suprimentos (procurement)**.

---

## 1. Visão geral

O sistema se organiza em 5 camadas. Nada do núcleo operacional funciona sem a
base, e nada da gestão fecha sem o núcleo.

```
┌─────────────────────────────────────────────────────────────┐
│  E. ANALÍTICO      BI · Dashboards · Curva S · Relatórios   │
├─────────────────────────────────────────────────────────────┤
│  D. FINANCEIRO     Contas a pagar/receber · Custos · Fiscal │
├─────────────────────────────────────────────────────────────┤
│  C. OPERACIONAL    Obras · Almoxarifado · Compras · Equipes │
├─────────────────────────────────────────────────────────────┤
│  B. CADASTROS      Itens · Fornecedores · Pessoas · Centros │
├─────────────────────────────────────────────────────────────┤
│  A. PLATAFORMA     Auth · RBAC · Multiempresa · Auditoria   │
└─────────────────────────────────────────────────────────────┘
```

Regra de dependência: uma camada só pode depender das camadas abaixo dela.

---

## 2. Camada A — Plataforma (fundação obrigatória)

### A1. Identidade e Acesso (IAM)
- Autenticação (e-mail/senha, MFA opcional, SSO futuro).
- Sessões, refresh tokens, revogação.
- Recuperação de senha, primeiro acesso, convite de usuário.

### A2. Autorização / RBAC
Este é o módulo que materializa os perfis **administrador** e **operador**.

Modelo recomendado: **RBAC com escopo** (`papel × escopo`), não papel global.
Um mesmo usuário é *operador* na Obra A e *encarregado* na Obra B.

| Perfil | Escopo típico | Pode |
|---|---|---|
| **Administrador** | Empresa | Tudo: cadastros mestres, aprovação de compras sem limite, fechamento financeiro, gestão de usuários, parametrização |
| **Gestor de Obra** | Obra | Orçamento, cronograma, medições, aprovar requisições até limite, alocar equipe |
| **Comprador** | Empresa | Cotações, pedidos de compra, cadastro de fornecedor |
| **Almoxarife** | Almoxarifado | Entradas, saídas, transferências, inventário |
| **Operador / Apontador** | Obra | Requisitar material, apontar horas/produção, registrar diário de obra |
| **Financeiro** | Empresa | Títulos, pagamentos, conciliação |
| **Consulta** | Obra/Empresa | Somente leitura |

Componentes: papéis, permissões granulares (`compras.pedido.aprovar`),
atribuição `usuário → papel → escopo`, **alçadas de aprovação por valor**,
delegação temporária (férias/afastamento).

### A3. Multiempresa / Multifilial
Empresa → Filial → Obra → Centro de Custo. Isolamento de dados por tenant.

### A4. Auditoria e Log
Trilha imutável de quem/quando/o quê em todo documento (requisição, pedido,
movimento de estoque, medição). Exigência legal e de auditoria interna.

### A5. Núcleo transversal
Numeração de documentos, workflow/aprovações genérico, notificações
(e-mail/push/in-app), anexos e documentos (GED), parametrização por empresa,
jobs agendados, importação/exportação.

---

## 3. Camada B — Cadastros mestres

Sem estes, os módulos operacionais não se sustentam.

### B1. Catálogo de Itens / Insumos
- Código, descrição, NCM, classificação fiscal.
- **Unidade de medida + conversões** (saco ↔ kg, m³ ↔ viagem) — fonte clássica de erro.
- Grupos e famílias, item crítico, item controlado.
- Curva ABC, estoque mínimo/máximo, ponto de pedido, lead time.
- Vínculo com **composições de serviço** (SINAPI/TCPO) — insumo × serviço.
- Rastreabilidade: lote, número de série, validade (relevante para químicos/EPI).

### B2. Fornecedores
Dados cadastrais e fiscais, contatos, condições comerciais, certidões e
validades (CND, FGTS, INSS) com bloqueio automático quando vencidas,
qualificação/homologação, avaliação de desempenho (prazo, qualidade, preço).

### B3. Clientes / Contratantes
Contratos, aditivos, medições contratuais, retenções.

### B4. Pessoas e Equipes
Colaboradores próprios e terceiros, **funções/cargos e custo-hora**,
qualificações e certificações (NR-35, NR-10, NR-18) com validade,
equipes/turmas, escalas e turnos, empreiteiros e seus vínculos.

### B5. Estruturas organizacionais
Plano de contas, centros de custo, **EAP/WBS** da obra, naturezas de despesa,
locais de estoque (almoxarifado central, canteiro, frente de serviço).

---

## 4. Camada C — Núcleo operacional

### C1. Obras / Projetos
O módulo agregador — tudo se pendura na obra.

- Cadastro da obra: contrato, prazo, cliente, responsáveis, ART/RRT.
- **EAP** hierárquica (fases → serviços → atividades).
- **Orçamento** por serviço com composições de insumo (previsto).
- **Cronograma físico** com dependências, caminho crítico, Curva S.
- **Medição de avanço físico** por serviço (% executado).
- **Diário de Obra (RDO)**: clima, efetivo presente, equipamentos, ocorrências,
  fotos. Obrigatório contratualmente na maioria dos contratos.
- Aditivos e revisões de escopo/orçamento.
- **Realizado × Previsto**: custo, prazo e físico. É a razão de existir do ERP.

### C2. Almoxarifado / Estoque
- **Locais de estoque multinível**: central, canteiro por obra, frente de serviço,
  estoque de terceiro (em poder de / em nosso poder).
- **Movimentos**: entrada por NF, entrada por transferência, devolução de fornecedor,
  saída por requisição, devolução ao estoque, transferência entre locais, ajuste de
  inventário, perda/quebra.
- **Requisição de Material (RM)**: operador solicita → gestor aprova → almoxarife
  separa e entrega. Toda saída é apropriada a **obra + centro de custo + serviço da EAP**.
- **Valoração**: custo médio ponderado (recomendado) ou PEPS. Define o custo real da obra.
- **Reserva/empenho** de material para atividade programada.
- **Inventário** rotativo e geral, com contagem cega e aprovação de divergência.
- **Recebimento**: conferência quantitativa e qualitativa, aceite parcial,
  divergência contra o pedido de compra.
- Código de barras / QR para movimentação em campo.
- Controle de **ferramentas e EPI** por colaborador (entrega, devolução, ficha
  assinada — exigência da NR-6).

### C3. Suprimentos / Compras (Procurement)
Fluxo completo, do pedido ao pagamento:

```
Requisição de Compra (RC)
  → Aprovação por alçada
  → Cotação / RFQ a N fornecedores
  → Mapa comparativo (preço, prazo, condição, frete)
  → Pedido de Compra (PC) com aprovação
  → Recebimento (NF × PC × RC → conferência a três vias)
  → Contas a Pagar
```

Componentes:
- **Requisição de Compra** originada de: falta de estoque (ponto de pedido),
  necessidade de obra (orçamento × cronograma), ou manual.
- **Cotação**: envio a fornecedores, portal do fornecedor (opcional), prazo de resposta.
- **Mapa comparativo** com critérios de escolha e justificativa obrigatória quando
  não escolhe o menor preço.
- **Pedido de Compra** com itens, entrega programada (parcial/total), condições,
  impostos, frete, local de entrega (canteiro específico).
- **Alçadas de aprovação** por valor, por tipo de item, por obra.
- **Contratos de fornecimento** e registro de preços (ata), preço por vigência.
- **Follow-up** de entrega, atrasos, cobrança.
- **Contratação de serviços/empreiteiros**: medição de serviço, retenções,
  documentação de subcontratado.

### C4. Alocação de Staff / Mão de Obra
- **Planejamento de efetivo** por obra/período derivado do cronograma.
- **Alocação** de colaborador/equipe → obra → frente de serviço → período.
- **Detecção de conflito** (mesma pessoa em duas obras no mesmo dia).
- **Apontamento de horas** por atividade da EAP (normal, extra, adicional noturno,
  improdutiva por chuva).
- **Ponto/frequência**, faltas, atestados, férias — integrado ou importado do RH.
- **Custo de mão de obra apropriado à obra** (hora × custo-hora + encargos).
- **Produtividade**: horas gastas × quantidade produzida vs. índice orçado.
- **Disponibilidade**: quem está livre, quem está com certificação vencida (bloqueio).
- Terceiros: efetivo de empreiteiro, controle de documentação e liberação de acesso.

### C5. Equipamentos e Frota (fortemente recomendado)
Ativos próprios e alugados, alocação à obra, horímetro/odômetro, manutenção
preventiva e corretiva, custo por hora de equipamento apropriado à obra,
abastecimento. Em obra pesada, é o segundo maior custo depois de mão de obra.

---

## 5. Camada D — Financeiro e fiscal

### D1. Contas a Pagar
Títulos originados do recebimento de compra e de medição de empreiteiro,
aprovação de pagamento por alçada, programação de caixa, baixa e conciliação.

### D2. Contas a Receber
Medições contratuais, faturamento, retenções contratuais, inadimplência.

### D3. Tesouraria e Fluxo de Caixa
Contas bancárias, conciliação, fluxo projetado (compromissos de PC + folha +
medições a receber).

### D4. Controle de Custos da Obra
O módulo que fecha o ciclo. Consolida:
material (saída de estoque) + mão de obra (apontamento) + equipamento (horas) +
serviços de terceiros (medições) + rateios indiretos → **custo real por serviço
da EAP**, comparado ao orçado.

### D5. Fiscal
Entrada de NF-e (XML), validação contra pedido, escrituração, impostos
retidos (ISS, INSS, IRRF sobre serviços). No Brasil, não é opcional.

### D6. Contabilidade (ou integração)
Integração contábil a partir dos eventos operacionais. Muitas empresas usam
sistema contábil externo — prever **exportação** em vez de reimplementar.

---

## 6. Camada E — Analítico

Dashboard por perfil (administrador vê empresa; gestor vê sua obra; operador vê
suas tarefas), Curva S física × financeira, realizado × orçado por EAP,
giro e cobertura de estoque, curva ABC, desempenho de fornecedor, produtividade
de equipe, alertas (estoque mínimo, certidão vencida, obra estourando orçamento),
relatórios exportáveis e construtor de relatórios.

---

## 7. Módulos de apoio

| Módulo | Por quê |
|---|---|
| **App mobile / offline** | Canteiro tem sinal ruim. Requisição, RDO, apontamento e recebimento precisam funcionar offline com sincronização. |
| **Portal do fornecedor** | Cotação e NF sem digitação interna. |
| **SSMA / Segurança do Trabalho** | Entrega de EPI, ASO, treinamentos NR, incidentes, DDS. Obrigação legal. |
| **Qualidade** | FVS (ficha de verificação de serviço), não conformidades, checklists de liberação. |
| **Integrações** | NF-e/SEFAZ, bancos (CNAB/PIX), folha de pagamento, contabilidade, SINAPI. |
| **Notificações e aprovações mobile** | Alçada só funciona se o aprovador aprovar de onde estiver. |

---

## 8. Fluxos que cruzam módulos (os que definem a arquitetura)

**Fluxo 1 — Necessidade até o custo**
```
Orçamento da obra (C1)
 → Programação de necessidade (C1+C2)
 → Requisição de Compra (C3)      [operador cria, gestor aprova — A2]
 → Cotação e Pedido (C3)
 → Recebimento (C2+C3)            [entra no estoque, gera título — D1]
 → Requisição de Material (C2)    [operador pede, almoxarife entrega]
 → Saída apropriada à EAP (C2)    [vira custo — D4]
 → Realizado × Orçado (E)
```

**Fluxo 2 — Efetivo até o custo de mão de obra**
```
Cronograma (C1) → Planejamento de efetivo (C4) → Alocação (C4)
 → Apontamento por atividade (C4) → Custo-hora (B4) → Custo da obra (D4)
```

**Fluxo 3 — Aprovação (atravessa tudo)**
```
Documento criado por operador → regra de alçada (A2) → aprovador notificado (A5)
 → aprova/rejeita com justificativa → auditoria (A4) → documento efetivado
```

Esses três fluxos devem ser desenhados **antes** de qualquer tela.

---

## 9. Mínimo viável x completo

### MVP — o menor conjunto que já é útil (não dá para cortar mais)
1. A1 Auth · A2 RBAC (admin/gestor/almoxarife/operador) · A4 Auditoria
2. B1 Itens · B2 Fornecedores · B4 Pessoas · B5 Centros de custo
3. C1 Obras (cadastro + EAP + orçamento simples)
4. C2 Almoxarifado (entrada, saída por requisição, transferência, saldo, inventário)
5. C3 Compras (RC → cotação → PC → recebimento, com alçada)
6. C4 Alocação + apontamento de horas
7. E Dashboards básicos + realizado × orçado

### Fase 2
Financeiro (D1–D4), fiscal (D5), equipamentos (C5), RDO completo, app mobile offline.

### Fase 3
BI avançado, portal do fornecedor, SSMA, qualidade, integrações contábeis,
contratos de fornecimento/registro de preços, previsão de demanda.

---

## 10. Decisões de arquitetura que precisam ser tomadas cedo

1. **Valoração de estoque**: custo médio ou PEPS — afeta todo o custo da obra e é
   caro de trocar depois.
2. **Granularidade da apropriação**: custo vai para a obra, para o centro de custo
   ou para o serviço da EAP? Recomendado: EAP, com os outros derivados.
3. **Modelo de permissão**: papel global vs. papel com escopo. Escopo é mais
   trabalhoso no início e insubstituível depois.
4. **Documentos imutáveis**: movimento de estoque e pedido aprovado não se editam —
   estornam. Isso muda o modelo de dados.
5. **Multiempresa desde o dia 1**, mesmo com uma empresa só. Retrofit de tenant é doloroso.
6. **Offline-first no campo**: decide se o domínio precisa de IDs gerados no cliente
   e resolução de conflito.
7. **Unidade de medida e conversão** como cidadão de primeira classe no catálogo.

---

## 11. Resumo — checklist de módulos

**Plataforma:** Auth · RBAC com escopo e alçadas · Multiempresa · Auditoria ·
Workflow · Notificações · Anexos/GED · Parametrização

**Cadastros:** Itens/insumos · Unidades e conversões · Fornecedores ·
Clientes/contratos · Pessoas e funções · Equipes · Plano de contas ·
Centros de custo · EAP · Locais de estoque

**Operacional:** Obras · EAP · Orçamento · Cronograma · Medição física · RDO ·
Almoxarifado · Requisição de material · Inventário · Recebimento ·
Ferramentas e EPI · Requisição de compra · Cotação · Mapa comparativo ·
Pedido de compra · Follow-up · Contratos de fornecimento · Empreiteiros ·
Planejamento de efetivo · Alocação · Apontamento de horas · Produtividade ·
Equipamentos e frota

**Financeiro:** Contas a pagar · Contas a receber · Tesouraria · Fluxo de caixa ·
Custos da obra · Fiscal/NF-e · Integração contábil

**Analítico:** Dashboards por perfil · Curva S · Realizado × orçado ·
Indicadores de estoque, fornecedor e produtividade · Alertas · Relatórios

**Apoio:** Mobile offline · Portal do fornecedor · SSMA · Qualidade · Integrações
