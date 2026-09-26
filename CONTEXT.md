# Fake ERP

ERP didático para comerciantes/empreiteiros: um catálogo mestre de materiais,
depois quantidade em um depósito e compra.

## Language

Os termos de domínio são em inglês — aqui, no código e na UI. As definições
ficam em português do Brasil. Nenhum sinônimo em português entra no código ou
na UI: o que está em _Evitar_ está proibido.

**Item**:
Um material ou insumo que é comprado e estocado. Existe no catálogo mesmo sem
quantidade alguma.
_Evitar_: Product, produto, catalog item (como entidade)

**Catalog**:
A lista mestre de Items. Não tem saldo.
_Evitar_: Catálogo, estoque, inventory, warehouse

**SKU**:
Um código estável e único no catálogo que identifica um Item. Não muda quando a
quantidade muda.
_Evitar_: id interno como identificador de negócio

**Unit**:
Como um Item é contado (saco, metro, peça). Nesta fatia, texto no próprio Item.
_Evitar_: Unidade, UoM como entidade, tabela de unidade de medida

**Warehouse**:
O depósito onde vive uma quantidade de um Item. A quantidade pertence aqui, não
ao Item.
_Evitar_: estoque no Item, estoque no catálogo

**Stock**:
A quantidade de um Item em um Warehouse. Um Item pode ter Stock em mais de um
Warehouse.
_Evitar_: inventory no Item, saldo no Catalog, estoque

**User**:
Uma pessoa que faz login. Não é Staff.
_Evitar_: account, usuário, login (como entidade)

**Role**:
Administrator ou Operator. Um User tem um Role.
_Evitar_: permission, perfil, group

**Job**:
O local da obra. Não é um Warehouse. Quantidade não pertence aqui.
_Evitar_: obra, canteiro, project, site, Warehouse (como este lugar)

**Movement**:
Um Receipt, Transfer ou Issue que escreve quantidade de Stock. Nada mais
escreve Stock.
_Evitar_: movimento, escrita de Stock na mão

**Supplier**:
Quem fornece um Item. Identificado por um nome único.
_Evitar_: vendor, fornecedor, company

**Purchase**:
Um pedido de um Item, uma quantidade, um Warehouse, de um Supplier. Não escreve
Stock.
_Evitar_: pedido, order, Receipt (como este pedido)

**Receipt**:
Quantidade de um Item entrando em um Warehouse.
_Evitar_: recebimento, entrada, Purchase (como este Movement)

**Transfer**:
Quantidade de um Item movendo-se de um Warehouse para outro.
_Evitar_: transferência, mover entre Jobs

**Issue**:
Quantidade de um Item saindo de um Warehouse para um Job. O Job não detém essa
quantidade.
_Evitar_: baixa, saída, quantidade no Job
