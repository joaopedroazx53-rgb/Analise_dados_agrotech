# Análise de Dados AgroTech Solutions

Projeto de análise da base de vendas agropecuárias `tb_vendas_agro` (MySQL): limpeza e tratamento dos dados, relatório gerencial e criação de 10 KPIs (indicadores-chave de desempenho) para apoiar as decisões da diretoria.

## 1. Visão geral

### Objetivo

A AgroTech Solutions vai repassar esta análise para outra equipe. O objetivo do trabalho é permitir que essa equipe **entenda a base, execute o script e confira os resultados**. Para isso, o projeto trata a base de vendas, gera um relatório gerencial e transforma os resultados em 10 KPIs.

### Integrantes

- [Nome do integrante 1]
- [Nome do integrante 2]
- [Nome do integrante 3]
- [Nome do integrante 4]

**Disciplina:** [nome da disciplina]

### O que foi feito em cada parte

| Parte | O que foi feito |
|---|---|
| **Preparação da base** | Criação do banco `agropecuaria_db`, da tabela `tb_vendas_agro` e carga de 75 registros fornecidos (com inconsistências a tratar). |
| **Parte 1 — Limpeza e tratamento** | 10 atividades de correção: remoção de duplicatas, de datas futuras e de registros de teste; correção de nomes de produtos e fornecedores, valores unitários, quantidades, estados e categorias; marcação de defensivos de valor unitário acima de R$ 800,00 como indisponíveis. A base passou de 75 para 68 registros. |
| **Parte 2 — Relatório gerencial** | 10 consultas sobre a base tratada: produtos disponíveis, registros por fornecedor e por estado, maiores valores unitários, valor médio por categoria, valor potencial do estoque disponível e outros. |
| **Parte 3 — KPIs** | 10 indicadores (`KPI 01` a `KPI 10`) com objetivo, regra de cálculo, consulta SQL, resultado e interpretação gerencial, mais a escolha dos 3 mais relevantes. |
| **Parte 4 — Documentação** | Este `README.md` e a publicação do projeto no GitHub. |


### Estrutura do repositório

```text
analise_de_dados_agrotech/
├── README.md
├── sql/
│   └── AgroTech_Integrantes.sql      # banco, tabela, carga e Partes 1, 2 e 3
└── slides/
    └── AgroTech_KPIs_Integrantes.pptx  # apresentação dos 10 KPIs
```

> **Cuidado de interpretação:** neste projeto, **valor potencial do estoque** é `quantidade × valor_unitario`. Ele **não é faturamento nem lucro**, pois a base não informa custos nem a quantidade efetivamente vendida em cada operação. Metas e critérios de comparação citados são **sugestões do grupo**, não da empresa.

## 2. Como executar

### Requisitos

- MySQL Server 8.0 ou superior.
- Um cliente SQL: MySQL Workbench ou o cliente de linha de comando `mysql`.
- Usuário com permissão para criar e apagar bancos (`CREATE` e `DROP`).
- O banco `agropecuaria_db` **não precisa existir** antes da execução.

### Arquivo SQL

Todo o projeto está em um único arquivo: **`sql/AgroTech_Integrantes.sql`**. Ele cria o banco, carrega a base, aplica o tratamento e executa as consultas, na ordem abaixo:

| Seção do script | Conteúdo |
|---|---|
| Preparação da base | `DROP DATABASE IF EXISTS`, `CREATE DATABASE`, `CREATE TABLE tb_vendas_agro` e `INSERT` dos 75 registros |
| Parte 1 | Atividades 1 a 10, cada uma com uma consulta de conferência antes da alteração |
| Parte 2 | Atividades 1 a 10 do relatório gerencial |
| Parte 3 | `KPI 01` a `KPI 10`, com nome, objetivo e regra de cálculo nos comentários |


### Passo a passo

**MySQL Workbench**

1. Conecte-se ao servidor MySQL.
2. Abra o arquivo em *File > Open SQL Script* e selecione `sql/AgroTech_Integrantes.sql`.
3. Execute o script inteiro (ícone de raio, ou `Ctrl+Shift+Enter`). O Workbench abre uma aba de resultado para cada `SELECT`.

**Linha de comando**

```bash
mysql -u SEU_USUARIO -p --default-character-set=utf8mb4 < sql/AgroTech_Integrantes.sql
```

### Atenção

- O script começa com `DROP DATABASE IF EXISTS agropecuaria_db;`. Se você já tiver um banco com esse nome, **ele será apagado**.
- O script executa `SET SQL_SAFE_UPDATES = 0;` porque o modo seguro do Workbench bloqueia `UPDATE` e `DELETE` que filtram por colunas sem chave (erro 1175).
- Use `utf8mb4` para que os acentos (Grãos, Rações etc.) sejam exibidos corretamente.

### Como conferir

Depois de executar, a tabela deve ter **68 registros** (`SELECT COUNT(*) FROM tb_vendas_agro;`), sendo 48 disponíveis e 20 indisponíveis. Compare os resultados das seções 4 e 5 deste README com os retornados pelas consultas.

## 3. Dicionário de dados

### Tabela `tb_vendas_agro`

Reúne os registros de vendas de produtos agropecuários (grãos, rações, fertilizantes, defensivos, minerais e sementes) da AgroTech Solutions. **Cada linha representa um registro de um produto**, com seu fornecedor, estado, quantidade, valor unitário, data e situação de estoque. O mesmo produto pode aparecer em mais de uma linha, em datas e estados diferentes.

| Coluna | Tipo de dado | Descrição | Restrições / regras (base tratada) | Exemplo |
|---|---|---|---|---|
| `id` | `INT` | Identificador do registro | `PRIMARY KEY`: único. Vai de 1 a 71, com lacunas porque registros foram removidos na Parte 1 | `1` |
| `produto` | `VARCHAR(100)` | Nome do produto | Nomes padronizados (sem erros de digitação). 50 nomes distintos na base tratada | `Milho Safrinha` |
| `categoria` | `VARCHAR(50)` | Categoria do produto | Apenas 6 valores: Grãos, Rações, Fertilizantes, Defensivos, Minerais, Sementes | `Grãos` |
| `fornecedor` | `VARCHAR(100)` | Empresa fornecedora | Apenas 6 valores: AgroSul, Campo Verde, NutriCampo, AgroMax, BioPlant, Solo Forte | `AgroSul` |
| `estado` | `VARCHAR(50)` | Estado (UF) do registro | Sigla de 2 letras. 8 valores: MT, PR, GO, MS, SP, SC, RS, MG | `PR` |
| `quantidade` | `INT` | Unidades do produto no registro | Inteiro maior que zero. Na base tratada vai de 12 a 340 | `120` |
| `valor_unitario` | `DECIMAL(10,2)` | Valor de uma unidade, em reais (R$) | Maior que zero, com 2 casas decimais. Na base tratada vai de R$ 42,00 a R$ 980,00 | `89.90` |
| `data_venda` | `DATE` | Data do registro (`AAAA-MM-DD`) | Sem datas futuras. Na base tratada vai de 2025-01-10 a 2025-05-17 | `2025-01-10` |
| `estoque` | `BOOLEAN` | Situação do produto para comercialização | `TRUE` (1) = disponível; `FALSE` (0) = indisponível. No MySQL, `BOOLEAN` é um `TINYINT(1)` | `TRUE` |


> Apenas `id` tem restrição declarada na criação da tabela (`PRIMARY KEY`). As demais regras da coluna "Restrições" são resultado do tratamento da Parte 1, e não restrições do banco.

### Como interpretar `quantidade`, `estoque` e `data_venda`

- **`quantidade`**: número de unidades do produto no registro. Nos KPIs, é usada como o **volume em estoque** daquele registro. Ela **não indica a quantidade efetivamente vendida**, pois a base não traz essa informação. Valores negativos ou zero eram inconsistências e foram corrigidos na Parte 1.
- **`estoque`**: indica se o produto está **disponível (`TRUE`) ou indisponível (`FALSE`)** para comercialização. Os KPIs separam os registros por essa coluna. Na Parte 1 (Atividade 10), os defensivos com valor unitário acima de R$ 800,00 foram marcados como indisponíveis.
- **`data_venda`**: data associada ao registro. Na base tratada, todas as datas estão entre 10/01/2025 e 17/05/2025. Datas posteriores a 31/12/2025 (de 2028 a 2099) foram tratadas como inconsistentes e removidas. Como a base não informa a quantidade efetivamente vendida, a data não deve ser usada para calcular faturamento.

## 4. Resultados do tratamento e do relatório gerencial

### 4.1 Parte 1 — Principais correções

**Antes e depois da limpeza**

| Indicador da base | Antes (dados brutos) | Depois (base tratada) |
|---|---:|---:|
| Registros | 75 | 68 |
| Registros disponíveis | 58 | 48 |
| Registros indisponíveis | 17 | 20 |
| Estados distintos | 12 | 8 |
| Categorias distintas | 10 | 6 |
| Fornecedores distintos | 11 | 6 |
| Nomes de produto distintos | 57 | 50 |
| Menor e maior `quantidade` | -999 e 99.999 | 12 e 340 |
| Menor e maior `valor_unitario` | R$ -500,00 e R$ 999.999,99 | R$ 42,00 e R$ 980,00 |
| Primeira e última `data_venda` | 2025-01-10 e 2099-01-01 | 2025-01-10 e 2025-05-17 |
| Valor potencial do estoque disponível | R$ 100.000.981.171,39 | R$ 769.784,00 |


O valor potencial do estoque disponível era distorcido pelos registros inconsistentes (por exemplo, um valor unitário de R$ 999.999,99 em um registro de teste com 99.999 unidades). Depois do tratamento, ele passou a refletir apenas dados válidos.

**Correções por atividade**

| Atividade | O que foi corrigido | Registros afetados |
|---|---|---|
| 01 | Remoção de registros duplicados (ids 51, 52 e 53, cópias exatas dos ids 1, 2 e 3 em outra data) | 3 removidos |
| 02 | Nomes de produtos com erro de digitação: `Milhho Safrinha`, `Sojjja Premium`, `Racão Bovina 25kg`, `Fertilizanti NPK`, `Insetcida Power` | 5 corrigidos |
| 03 | Nomes de fornecedores: `Campo Vrede`, `NutriCamp`, `Bio Planti` | 3 corrigidos |
| 04 | Valores unitários inconsistentes: `Soja Exportação` (R$ 1,99 → R$ 160,00), `Fungicida Premium` (R$ 99.999,99 → R$ 950,00), `Calcário Premium` (−R$ 45,00 → R$ 55,50), `Adubo Nitrogenado` (R$ 0,00 → R$ 275,00) | 4 corrigidos |
| 05 | Quantidades inválidas: `Milho Tradicional` (−10 → 170) e `Ração Ovina` (0 → 66) | 2 corrigidos (o `UPDATE` por nome também alcança os ids 37 e 41, que já tinham esses valores) |
| 06 | Estados fora do padrão: `PARANA` → `PR` e `SÃO PAULO` → `SP` | 2 corrigidos |
| 07 | Categorias fora do padrão: `Graos` → `Grãos` e `Racao` → `Rações` | 2 corrigidos |
| 08 | Remoção de registros com data futura (ids 72, 73, 74 e 75, com datas de 2028 a 2099) | 4 removidos |
| 09 | Remoção de registros de teste (`XXXXX`, `TESTE PRODUTO`) | 0 removidos nesta etapa: os ids 74 e 75 já tinham saído na Atividade 08, por terem data futura |
| 10 | Defensivos com valor unitário acima de R$ 800,00 marcados como indisponíveis (ids 25, 30, 44, 49, 63 e 69) | 6 atendem ao filtro; 5 passaram de disponível para indisponível (o id 25 já estava indisponível) |


No total, **7 registros foram removidos** (75 → 68).

### 4.2 Parte 2 — Principais resultados

**Atividade 01 — Produtos disponíveis:** **48** de 68 registros (70,59%) estão disponíveis.

**Atividade 02 — Registros por fornecedor**

| Fornecedor | Registros | % do total |
|---|---:|---:|
| Campo Verde | 16 | 23,53% |
| NutriCampo | 14 | 20,59% |
| AgroSul | 11 | 16,18% |
| Solo Forte | 10 | 14,71% |
| BioPlant | 9 | 13,24% |
| AgroMax | 8 | 11,76% |


O **Campo Verde** (16 registros, 23,53%) e a **NutriCampo** (14 registros, 20,59%) concentram 30 dos 68 registros (44,12%).

**Atividade 03 — 10 maiores valores unitários**

| Produto | Categoria | Valor unitário | Estado |
|---|---|---:|:---:|
| Herbicida Platinum | Defensivos | R$ 980,00 | SP |
| Herbicida Platinum | Defensivos | R$ 980,00 | SP |
| Fungicida Premium | Defensivos | R$ 950,00 | MT |
| Fungicida Premium | Defensivos | R$ 950,00 | MT |
| Inseticida Ultra | Defensivos | R$ 890,00 | SP |
| Fungicida Rural | Defensivos | R$ 845,00 | MT |
| Fungicida Max | Defensivos | R$ 799,90 | PR |
| Inseticida Verde | Defensivos | R$ 720,00 | MS |
| Herbicida AgroPlus | Defensivos | R$ 620,00 | GO |
| Inseticida Power | Defensivos | R$ 599,00 | MT |


Os 10 maiores valores unitários são todos de **Defensivos**, o que explica o valor unitário médio muito acima das demais categorias.

**Atividade 04 — Valor unitário médio por categoria**

| Categoria | Valor unitário médio |
|---|---:|
| Defensivos | R$ 739,91 |
| Fertilizantes | R$ 296,74 |
| Grãos | R$ 136,02 |
| Sementes | R$ 117,44 |
| Rações | R$ 113,68 |
| Minerais | R$ 58,17 |


**Atividade 05 — Registros por estado**

| Estado | Registros | % do total |
|:---:|---:|---:|
| MT | 14 | 20,59% |
| PR | 12 | 17,65% |
| GO | 11 | 16,18% |
| MS | 8 | 11,76% |
| SP | 7 | 10,29% |
| SC | 7 | 10,29% |
| RS | 6 | 8,82% |
| MG | 3 | 4,41% |


MT (14), PR (12) e GO (11) somam 37 registros (54,41% do total). MG tem o menor número (3).

**Atividade 06 — Sementes e Grãos disponíveis:** **18 registros** (13 de Grãos e 5 de Sementes).

**Atividade 07 — Quantidade acima de 200 unidades:** **11 registros**, todos em PR (5, calcários), MS (4) e MT (2). O maior é `Calcário Fino`, com 340 unidades (PR).

**Atividade 08 — Valor potencial do estoque disponível:** **R$ 769.784,00**.

**Atividade 09 — Registros indisponíveis por categoria**

| Categoria | Registros indisponíveis |
|---|---:|
| Defensivos | 7 |
| Sementes | 4 |
| Fertilizantes | 3 |
| Grãos | 3 |
| Rações | 2 |
| Minerais | 1 |


**Defensivos** tem o maior número de indisponíveis (7 dos 20), seguida de Sementes (4).

**Atividade 10 — Registros entre 01/03/2025 e 31/05/2025:** **51 registros** (75,00% do total), o que mostra que a base se concentra nos três últimos meses do período.

## 5. Catálogo e resultados dos 10 KPIs

Os 10 KPIs usam a tabela `tb_vendas_agro` já tratada (68 registros) e a mesma identificação (`KPI 01` a `KPI 10`) dos comentários do arquivo SQL. **Valor potencial do estoque** = `quantidade × valor_unitario`.

**Resumo**

| Código | KPI | Resultado principal |
|---|---|---|
| KPI 01 | Taxa de Indisponibilidade em Volume (%) | 23,09% das unidades |
| KPI 02 | Quantidade Total em Estoque Disponível (unidades) | 5.681 unidades |
| KPI 03 | Participação das Categorias no Valor Potencial do Estoque Disponível (R$ e %) | Grãos: 43,91% |
| KPI 04 | Participação do Valor Potencial do Estoque Indisponível (%) | 29,79% (R$ 326.690,50) |
| KPI 05 | Valor Médio por Unidade em Estoque Disponível (R$) | R$ 135,50 por unidade |
| KPI 06 | Valor Potencial do Estoque Disponível por Fornecedor (R$ e %) | Campo Verde: 43,52% |
| KPI 07 | Abrangência de Categorias e Estados por Fornecedor | Campo Verde: 3 categorias |
| KPI 08 | Valor Potencial Médio do Estoque por Registro Disponível, por Estado (R$) | MS: R$ 29.432,00 |
| KPI 09 | Registros de Alto Volume (acima da média de quantidade) | 33,82% dos registros |
| KPI 10 | Participação dos Itens de Maior Valor Unitário no Valor Potencial do Estoque (%) | 27,42% do valor |


### KPI 01 — Taxa de Indisponibilidade em Volume (%)

| Item | Descrição |
|---|---|
| **Código** | KPI 01 |
| **Tema** | Disponibilidade |
| **Objetivo** | Medir qual parcela das unidades cadastradas está em itens indisponíveis para comercialização. |
| **Tabela e colunas** | `tb_vendas_agro`: `quantidade`, `estoque` |
| **Regra de cálculo** | Soma de quantidade dos registros indisponíveis ÷ soma de quantidade de todos os registros × 100. |
| **Filtros** | Numerador: estoque = FALSE. Denominador: todos os registros (sem filtro). |
| **Unidade de medida** | % e unidades |
| **Numerador** | Soma de quantidade com estoque = FALSE |
| **Denominador** | Soma de quantidade de todos os registros |
| **Divisão por zero** | NULLIF(denominador, 0): se o denominador for 0, a consulta devolve NULL em vez de erro. |


**Resultado na base tratada**

| Unidades indisponíveis (numerador) | Unidades totais (denominador) | Taxa de indisponibilidade |
|---:|---:|---:|
| 1.706 | 7.387 | 23,09% |


**Interpretação gerencial:** Cerca de 23% das unidades cadastradas estão em itens indisponíveis, o que reduz o volume que a empresa pode comercializar.

**Decisão apoiada:** Priorizar a reposição desses itens. Uma meta de até 15% seria sugestão do grupo, pois a empresa não forneceu metas.

### KPI 02 — Quantidade Total em Estoque Disponível (unidades)

| Item | Descrição |
|---|---|
| **Código** | KPI 02 |
| **Tema** | Estoque e logística |
| **Objetivo** | Dimensionar o volume físico disponível para comercialização. |
| **Tabela e colunas** | `tb_vendas_agro`: `quantidade`, `estoque` |
| **Regra de cálculo** | Soma de quantidade dos registros disponíveis. |
| **Filtros** | estoque = TRUE (apenas registros disponíveis). |
| **Unidade de medida** | unidades |
| **Divisão por zero** | Não há divisão no cálculo. |


**Resultado na base tratada**

| Quantidade total disponível |
|---|
| 5.681 unidades |


**Interpretação gerencial:** O estoque disponível soma 5.681 unidades, volume que exige espaço e organização.

**Decisão apoiada:** Usar como referência no planejamento de armazenagem.

### KPI 03 — Participação das Categorias no Valor Potencial do Estoque Disponível (R$ e %)

| Item | Descrição |
|---|---|
| **Código** | KPI 03 |
| **Tema** | Valor financeiro do estoque / Categorias |
| **Objetivo** | Comparar as categorias pelo valor potencial do estoque disponível. |
| **Tabela e colunas** | `tb_vendas_agro`: `categoria`, `quantidade`, `valor_unitario`, `estoque` |
| **Regra de cálculo** | Valor potencial do estoque da categoria (quantidade × valor_unitario) ÷ valor potencial do estoque de todos os registros disponíveis × 100. |
| **Filtros** | estoque = TRUE no numerador e no denominador. |
| **Unidade de medida** | R$ e % |
| **Numerador** | Valor potencial do estoque da categoria (disponíveis) |
| **Denominador** | Valor potencial do estoque de todos os registros disponíveis |
| **Divisão por zero** | NULLIF(denominador, 0): se o denominador for 0, a consulta devolve NULL em vez de erro. |


**Resultado na base tratada**

| Categoria | Valor potencial do estoque | Participação |
|---|---:|---:|
| Grãos | R$ 338.018,00 | 43,91% |
| Fertilizantes | R$ 120.755,00 | 15,69% |
| Defensivos | R$ 101.155,00 | 13,14% |
| Rações | R$ 92.089,20 | 11,96% |
| Minerais | R$ 77.968,00 | 10,13% |
| Sementes | R$ 39.798,80 | 5,17% |


**Interpretação gerencial:** Grãos concentra quase metade do valor potencial do estoque disponível, quase o triplo da segunda categoria. Sementes pesa só 5,17%.

**Decisão apoiada:** Priorizar o controle e a reposição de Grãos, onde uma falta afeta mais o valor do estoque.

### KPI 04 — Participação do Valor Potencial do Estoque Indisponível (%)

| Item | Descrição |
|---|---|
| **Código** | KPI 04 |
| **Tema** | Valor financeiro do estoque / Disponibilidade |
| **Objetivo** | Mostrar quanto do valor potencial do estoque está em itens que não podem ser comercializados. |
| **Tabela e colunas** | `tb_vendas_agro`: `quantidade`, `valor_unitario`, `estoque` |
| **Regra de cálculo** | Valor potencial do estoque dos registros indisponíveis ÷ valor potencial do estoque de todos os registros × 100. |
| **Filtros** | Numerador: estoque = FALSE. Denominador: todos os registros. |
| **Unidade de medida** | R$ e % |
| **Numerador** | Valor potencial do estoque com estoque = FALSE |
| **Denominador** | Valor potencial do estoque de todos os registros |
| **Divisão por zero** | NULLIF(denominador, 0): se o denominador for 0, a consulta devolve NULL em vez de erro. |


**Resultado na base tratada**

| Valor indisponível (numerador) | Valor total (denominador) | Participação |
|---:|---:|---:|
| R$ 326.690,50 | R$ 1.096.474,50 | 29,79% |


**Interpretação gerencial:** Quase 30% do valor potencial do estoque está em itens indisponíveis, mais que os 23,09% em unidades (KPI 01): os itens indisponíveis têm valor unitário acima da média.

**Decisão apoiada:** Priorizar a reposição dos itens indisponíveis de maior valor unitário.

### KPI 05 — Valor Médio por Unidade em Estoque Disponível (R$)

| Item | Descrição |
|---|---|
| **Código** | KPI 05 |
| **Tema** | Valor financeiro do estoque |
| **Objetivo** | Obter o valor unitário médio ponderado pelas quantidades, que reflete melhor o estoque do que a média simples. |
| **Tabela e colunas** | `tb_vendas_agro`: `quantidade`, `valor_unitario`, `estoque` |
| **Regra de cálculo** | Valor potencial do estoque disponível ÷ soma de quantidade disponível. |
| **Filtros** | estoque = TRUE (apenas registros disponíveis). |
| **Unidade de medida** | R$ por unidade |
| **Divisão por zero** | Divisão por SUM(quantidade) protegida com NULLIF(..., 0). |


**Resultado na base tratada**

| Valor médio por unidade disponível |
|---|
| R$ 135,50 |


**Interpretação gerencial:** Em média, cada unidade disponível vale R$ 135,50.

**Decisão apoiada:** Usar como referência para acompanhar mudanças no perfil do estoque ao longo do tempo.

### KPI 06 — Valor Potencial do Estoque Disponível por Fornecedor (R$ e %)

| Item | Descrição |
|---|---|
| **Código** | KPI 06 |
| **Tema** | Fornecedores / Valor financeiro do estoque |
| **Objetivo** | Comparar fornecedores pelo valor potencial do estoque disponível. |
| **Tabela e colunas** | `tb_vendas_agro`: `fornecedor`, `quantidade`, `valor_unitario`, `estoque` |
| **Regra de cálculo** | Valor potencial do estoque do fornecedor ÷ valor potencial do estoque de todos os registros disponíveis × 100. |
| **Filtros** | estoque = TRUE no numerador e no denominador. |
| **Unidade de medida** | R$ e % |
| **Numerador** | Valor potencial do estoque do fornecedor (disponíveis) |
| **Denominador** | Valor potencial do estoque de todos os registros disponíveis |
| **Divisão por zero** | NULLIF(denominador, 0): se o denominador for 0, a consulta devolve NULL em vez de erro. |


**Resultado na base tratada**

| Fornecedor | Valor potencial do estoque | Participação |
|---|---:|---:|
| Campo Verde | R$ 334.995,00 | 43,52% |
| Solo Forte | R$ 140.840,00 | 18,30% |
| NutriCampo | R$ 98.492,20 | 12,79% |
| AgroSul | R$ 69.396,80 | 9,02% |
| BioPlant | R$ 67.782,20 | 8,81% |
| AgroMax | R$ 58.277,80 | 7,57% |


**Interpretação gerencial:** O Campo Verde concentra 43,52% do valor potencial do estoque disponível, mais que o 2º e o 3º colocados somados (31,09%). Uma divisão igual entre 6 fornecedores seria 16,67% cada (critério de comparação do grupo).

**Decisão apoiada:** Negociar fornecedores alternativos e criar um plano de contingência.

### KPI 07 — Abrangência de Categorias e Estados por Fornecedor

| Item | Descrição |
|---|---|
| **Código** | KPI 07 |
| **Tema** | Fornecedores |
| **Objetivo** | Comparar o alcance de cada fornecedor (categorias e estados em que aparece) para avaliar o quanto ele é difícil de substituir. |
| **Tabela e colunas** | `tb_vendas_agro`: `fornecedor`, `categoria`, `estado` |
| **Regra de cálculo** | Contagem de categorias distintas e de estados distintos por fornecedor. |
| **Filtros** | Sem filtro (todos os registros). |
| **Unidade de medida** | número de categorias e de estados |
| **Divisão por zero** | Não há divisão no cálculo. |


**Resultado na base tratada**

| Fornecedor | Categorias atendidas | Estados atendidos |
|---|:---:|:---:|
| Campo Verde | 3 | 4 |
| AgroMax | 2 | 5 |
| NutriCampo | 2 | 5 |
| BioPlant | 2 | 4 |
| Solo Forte | 2 | 4 |
| AgroSul | 2 | 3 |


**Interpretação gerencial:** O Campo Verde é o único fornecedor presente em 3 categorias. Junto com o KPI 06, é importante em valor e em variedade, o que dificulta substituí-lo.

**Decisão apoiada:** Buscar alternativas para as categorias em que ele atua.

### KPI 08 — Valor Potencial Médio do Estoque por Registro Disponível, por Estado (R$)

| Item | Descrição |
|---|---|
| **Código** | KPI 08 |
| **Tema** | Categorias e estados / Valor financeiro do estoque |
| **Objetivo** | Comparar os estados pelo valor potencial médio do estoque de cada registro disponível. |
| **Tabela e colunas** | `tb_vendas_agro`: `estado`, `quantidade`, `valor_unitario`, `estoque` |
| **Regra de cálculo** | Média de (quantidade × valor_unitario) dos registros disponíveis de cada estado. |
| **Filtros** | estoque = TRUE (apenas registros disponíveis). |
| **Unidade de medida** | R$ por registro (e número de registros) |
| **Divisão por zero** | Não há divisão no cálculo. |


**Resultado na base tratada**

| Estado | Registros disponíveis | Valor potencial médio por registro |
|:---:|---:|---:|
| MS | 5 | R$ 29.432,00 |
| MT | 11 | R$ 22.623,38 |
| GO | 6 | R$ 17.597,03 |
| PR | 11 | R$ 12.150,92 |
| SP | 4 | R$ 10.392,50 |
| SC | 4 | R$ 9.050,00 |
| MG | 3 | R$ 8.646,33 |
| RS | 4 | R$ 7.703,88 |


**Interpretação gerencial:** Cada registro disponível em MS vale em média cerca de 3,8 vezes o de RS. MS tem só 5 registros disponíveis, então a média é mais sensível a poucos itens.

**Decisão apoiada:** Dar atenção especial à reposição em MS e MT, onde cada registro representa mais valor.

### KPI 09 — Registros de Alto Volume (acima da média de quantidade)

| Item | Descrição |
|---|---|
| **Código** | KPI 09 |
| **Tema** | Estoque e logística |
| **Objetivo** | Identificar registros com volume acima da média geral, que exigem mais espaço e atenção logística. |
| **Tabela e colunas** | `tb_vendas_agro`: `quantidade` |
| **Regra de cálculo** | Registros com quantidade maior que a média de quantidade de todos os registros ÷ total de registros × 100. |
| **Filtros** | Numerador: quantidade > média geral. Denominador: todos os registros. |
| **Unidade de medida** | registros e % |
| **Numerador** | Registros com quantidade acima da média geral |
| **Denominador** | Total de registros |
| **Divisão por zero** | NULLIF(denominador, 0): se o denominador for 0, a consulta devolve NULL em vez de erro. |


**Resultado na base tratada**

| Quantidade média | Registros acima da média (numerador) | Total de registros (denominador) | Percentual |
|---:|---:|---:|---:|
| 108,63 | 23 | 68 | 33,82% |


**Interpretação gerencial:** Um terço dos registros (23 de 68) está acima da média de 108,63 unidades.

**Decisão apoiada:** Dar atenção especial à armazenagem desses itens.

### KPI 10 — Participação dos Itens de Maior Valor Unitário no Valor Potencial do Estoque (%)

| Item | Descrição |
|---|---|
| **Código** | KPI 10 |
| **Tema** | Valor financeiro do estoque |
| **Objetivo** | Verificar quanto do valor potencial do estoque disponível está concentrado nos itens com valor unitário acima da média. |
| **Tabela e colunas** | `tb_vendas_agro`: `quantidade`, `valor_unitario`, `estoque` |
| **Regra de cálculo** | Valor potencial do estoque dos registros com valor_unitario acima da média dos disponíveis ÷ valor potencial do estoque de todos os disponíveis × 100. |
| **Filtros** | estoque = TRUE no numerador e no denominador; valor_unitario > média de valor_unitario dos disponíveis. |
| **Unidade de medida** | R$ e % |
| **Numerador** | Valor potencial do estoque dos itens com valor unitário acima da média (disponíveis) |
| **Denominador** | Valor potencial do estoque de todos os registros disponíveis |
| **Divisão por zero** | NULLIF(denominador, 0): se o denominador for 0, a consulta devolve NULL em vez de erro. |


**Resultado na base tratada**

| Valor unitário médio dos disponíveis | Registros acima da média | Valor potencial do estoque desses itens (numerador) | Percentual sobre os disponíveis |
|---:|---:|---:|---:|
| R$ 202,24 | 12 | R$ 211.110,00 | 27,42% |


**Interpretação gerencial:** Os 12 itens com valor unitário acima da média respondem por 27,42% do valor potencial do estoque. Como têm quantidades baixas, a maior parte do valor está em itens de menor valor unitário e maior volume.

**Decisão apoiada:** Não focar o controle só nos itens caros: o volume também pesa no valor do estoque.

## 6. Conclusão gerencial

Os três KPIs mais relevantes para a diretoria são o **KPI 06**, o **KPI 04** e o **KPI 03**. Juntos mostram de quem a empresa depende, quanto valor está parado e onde o impacto financeiro é maior.

| KPI | Resultado | Por que é relevante | Decisão que pode apoiar |
|---|---|---|---|
| **KPI 06** — Valor potencial do estoque disponível por fornecedor | Campo Verde: 43,52% (R$ 334.995,00) | Quase metade do valor potencial do estoque disponível depende de um único fornecedor, e o KPI 07 mostra que ele é o único presente em 3 categorias. | Negociar fornecedores alternativos e criar um plano de contingência. |
| **KPI 04** — Participação do valor potencial do estoque indisponível | 29,79% (R$ 326.690,50 de R$ 1.096.474,50) | O percentual em valor é maior que o de unidades (KPI 01: 23,09%), o que indica que os itens em falta têm valor unitário acima da média. | Priorizar a reposição dos itens indisponíveis de maior valor. |
| **KPI 03** — Participação das categorias no valor potencial do estoque disponível | Grãos: 43,91%; Sementes: 5,17% | Mostra onde o controle de estoque tem maior impacto financeiro. | Priorizar o controle e a reposição de Grãos. |


### Recomendações para a diretoria

1. **Reduzir a dependência do Campo Verde:** negociar fornecedores alternativos e definir um plano de contingência (KPI 06 e KPI 07).
2. **Repor primeiro os itens indisponíveis de maior valor:** atacar os 29,79% do valor potencial do estoque parado, começando por Defensivos e Sementes, que têm mais registros indisponíveis (Parte 2, Atividade 09).
3. **Reforçar o controle de Grãos e a atenção a MS e MT:** Grãos pesa 43,91% do valor e cada registro em MS e MT vale mais (KPI 03 e KPI 08).
4. **Planejar a armazenagem pelo volume:** usar as 5.681 unidades disponíveis e os registros de alto volume como base de espaço (KPI 02 e KPI 09).

> Metas e critérios de comparação citados neste documento são **sugestões do grupo**. A empresa não forneceu metas.
