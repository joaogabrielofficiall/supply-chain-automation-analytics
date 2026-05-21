# 📦 Projeto: Consumo Mensal e Automação de Compras 

![Status](https://img.shields.io/badge/Status-Concluído-success)
![Metodologia](https://img.shields.io/badge/Metodologia-CRISP--DM-blue)
![Ferramentas](https://img.shields.io/badge/Ferramentas-Python%20|%20Power%20BI%20|%20Excel%20|%20DAX-orange)

> **Visão Geral:** Pipeline analítica ponta a ponta desenvolvida para automação de requisições, mitigação de rupturas operacionais e governança de capital imobilizado, transformando o estoque de um centro de custo passivo em um ativo logístico estratégico (S&OP).

Abaixo, apresento a estrutura de diretórios do projeto no VS Code, demonstrando a organização lógica das análises, pastas de dados, dashboards e mídias organizadas pelas etapas da metodologia CRISP-DM:

![Estrutura do Projeto](assets/00.png)

---

## 🧭 Metodologia CRISP-DM

A execução deste projeto seguiu o framework padrão da indústria de dados (CRISP-DM), dividido em 6 etapas claras de desenvolvimento:

### 1️⃣ Etapa 1: Entendimento do Negócio

#### 🎯 Objetivo Estratégico do Negócio
Otimizar a gestão de suprimentos para garantir a disponibilidade máxima de itens vitais na operação, eliminando simultaneamente o capital imobilizado em produtos de baixo giro. O foco é transformar o estoque em um ativo estratégico, financeiramente eficiente e fisicamente acurado.

#### ❓ Principais Dores e Perguntas de Negócio
Para direcionar as ações de planejamento (S&OP), o projeto responde de forma exata a quatro perguntas críticas:
1. **Nível de Serviço e Ruptura:** Qual é a nossa eficiência em manter itens vitais disponíveis e quais correm risco de ruptura imediata?
2. **Saúde Financeira do Estoque:** Quanto capital temos imobilizado em itens sem giro (Classe C) e onde esse capital está preso sem necessidade operacional?
3. **Autonomia de Operação (Cobertura):** Por quanto tempo nossa operação sobrevive com o estoque atual antes de uma nova reposição?
4. **Controle de Desperdício e Orçamento:** O consumo atual está alinhado ao planejado? Como os custos estão distribuídos por unidade e qual o impacto dos solicitantes no orçamento?

#### 🧹 Premissas de Acuracidade (Higiene de Dados)
* **Inventário Prévio:** Antes de qualquer extração de dados, é obrigatório realizar o inventário cíclico/rotativo via coletor no WMS Eship.
* **Garantia de Saldo:** Esse processo garante que a base de "Saldos" usada para os cálculos tenha acuracidade física absoluta, eliminando distorções por erros de registro.

#### ⚠️ Riscos Envolvidos e Plano de Contingência
* **Divergência entre Físico e Sistema:**
  * *Contingência:* Bloqueio de extrações se o inventário rotativo estiver defasado há mais de X dias.
* **Resistência Cultural na Liderança:** * *Contingência:* Tradução dos relatórios em Storytelling com foco no prejuízo financeiro (ROI) para neutralizar barreiras de ego.
* **Desvio Operacional do Analista:** * *Contingência:* Alinhamento de metas atreladas à recuperação de receitas, precificando o custo de oportunidade de parar as análises.

#### 🏆 Critérios de Sucesso (Foco em ROI)
✔️ Redução drástica da falta de itens essenciais na operação logística.  
✔️ Identificação clara e plano de escoamento para itens parados.  
✔️ Aumento da visibilidade sobre o custo de oportunidade do capital imobilizado.  
✔️ Automação do pipeline de dados, eliminando o processamento manual.  

---

### 2️⃣ Etapa 2: Entendimento dos Dados

#### 📂 Coleta de Dados Iniciais (Mapeamento de Fontes)
* **WMS (Eship):** Fonte primária e transacional que armazena os registros de saldos físicos atuais e a movimentação histórica de saídas (expedição).
* **Notion:** Interface de entrada descentralizada utilizada para o registro histórico de demandas, previsões e necessidades pontuais de consumo.

#### 🔄 Descrição e Fluxo Operacional dos Dados
* **Saldos e Inventário (Aba "Saldos/Estoques"):** Fotografia atual da disponibilidade física de cada SKU para monitoramento de rupturas.
* **Ciclo de Pedidos:** Consolidação das necessidades inseridas no Notion, migrando para o processamento no WMS (separação, conferência e despacho).
* **Histórico de Saídas (Aba "Saída"):** Registros transacionais das movimentações finais, determinando a velocidade de consumo real e giro de estoque.

#### 🔬 Verificação da Qualidade dos Dados (Sanity Check)
Execução de rotina de Sanity Check cruzando as baixas registradas na base de "Saída" com o saldo remanescente em estoque. O objetivo é garantir o isolamento de perdas invisíveis (furtos, quebras ou erros de bipe) e blindar o modelo contra dados inconsistentes.

---

### 3️⃣ Etapa 3: Preparação dos Dados

#### 🛠️ Seleção de Dados e Limpeza Técnica (Sanitização)
As extrações brutas do sistema transacional passaram por um processo rigoroso de filtragem:
* **Higiene de Chaves Primárias:** Identificação e exclusão de registros nulos, IDs de teste ou códigos inválidos (ex: `0` ou `@666`).
* **Padronização de Encoding:** Correção de falhas de exportação para evitar erros de caracteres especiais.
* **Tratamento de Granularidade:** Quebra dos logs de data e hora para viabilizar a análise temporal do ciclo mensal.

#### 🔗 Integração de Dados e Engenharia de Recursos
A unificação das bases ocorre na aba "Suprimentos", através de Mesclagem de Consultas (Joins). Foram desenvolvidas colunas calculadas para classificar dinamicamente o status crítico de cada SKU: **Sugestão de Compra**, **Risco de Ruptura**, **Excesso de Estoque** e **Normalidade**.

#### 🎥 Demonstração Prática (ETL e Limpeza)
*Abaixo, vídeos demonstrando o pipeline de limpeza e processamento dos dados:*

<p align="center">
  <video src="assets/03. Preparacao/video1.mp4" width="80%" controls="controls"></video>
  <br><i>Processo de Tratamento de Dados - Parte 1</i>
</p>

<p align="center">
  <video src="assets/03. Preparacao/video2.mp4" width="80%" controls="controls"></video>
  <br><i>Processo de Tratamento de Dados - Parte 2</i>
</p>

---

### 4️⃣ Etapa 4: Modelagem

Nesta etapa, o foco é a construção da inteligência estatística e lógica do projeto através da Lógica Fuzzy Operacional (Excel) e Modelagem Dimensional (DAX/Power BI).

#### 🧮 Lógicas Matemáticas Implementadas (Aba Suprimentos)

**Algoritmo de Status de Compra:**
```excel
=SE(F5 <= (G5/30)*20; "RUPTURA"; SE(F5 < G5; "COMPRA IMEDIATA"; SE(F5 > G5*2; "EXCESSO"; "SAUDÁVEL")))

# Mecanismo de Sugestão de Quantidade Otimizada (Com fator embalagem):

=SE(OU(H5="COMPRA IMEDIATA"; H5="RUPTURA"); SE(ARREDONDAR.PARA.CIMA((MÁXIMO(SE(OU(C5="Copa";C5="Limpeza"); F5*2; F5*4); G5+F5) - F5) / I5; 0)<=0; "---"; ARREDONDAR.PARA.CIMA((MÁXIMO(SE(OU(C5="Copa";C5="Limpeza"); F5*2; F5*4); G5+F5) - F5) / I5; 0)); "---")

# 📊 Modelagem de Medidas Analíticas (DAX)
Métricas agregadas desenvolvidas no Power Pivot / Power BI:

1. Nível de Serviço de Itens Essenciais (OTIF de Disponibilidade)

Nivel_Servico_Essenciais := 
DIVIDE(
    CALCULATE( DISTINCTCOUNT(TBEstoque[SKU]); TBEstoque[ITEM ESSENCIAL] = "SIM"; TBEstoque[ESTOQUE ATUAL] > 0 );
    CALCULATE( DISTINCTCOUNT(TBEstoque[SKU]); TBEstoque[ITEM ESSENCIAL] = "SIM" )
)

2. Capital Imobilizado Crítico (Classe C em Excesso)

Capital_Imobilizado_Critico := 
CALCULATE (
    SUM ( TBEstoque[VALOR TOTAL (R$)] );
    TBEstoque[CURVA ABC] = "C";
    FILTER ( TBEstoque; CALCULATE ( COUNTROWS ( TBSuprimentos ); TBSuprimentos[STATUS COMPRA] = "EXCESSO" ) > 0 )
)

3. Cobertura de Estoque Geral (Dias)

Cobertura_Estoque_Dias := 
DIVIDE(
    SUM(TBEstoque[ESTOQUE ATUAL]);
    DIVIDE(SUM(TBEstoque[CONSUMO MÉDIO MENSAL]); 30)
)

# 🖼️ Galeria de Modelagem e Inteligência Analítica
Confira o desenvolvimento lógico da etapa de modelagem:

# 5️⃣ Etapa 5: Avaliação dos Resultados
O modelo dita agora as diretrizes de ressuprimento do ERP, impactando diretamente os R$ 22 milhões anuais da operação.

# A. Otimização do Capital de Giro vs. Nível de Serviço

Fato: 100% de Nível de Serviço nos itens essenciais; Cobertura Global de Estoque em 91 dias.

Ação de S&OP: Parametrização diferenciada baseada no giro. Copa e Limpeza passaram a operar com 30 dias de cobertura; itens críticos de Expedição com 90 dias. Caixa destravado sem risco fabril.

# B. Auditoria de Requisições e Aderência ao Consumo

Fato: Aderência ao Consumo Planejado registrou apenas 41,6%.

Ação de S&OP: Criação de trava sistêmica no ERP. Requisições que ultrapassarem a média histórica do setor são bloqueadas automaticamente, estancando compras emergenciais por "desespero operacional".

# C. Automação de Gatilhos contra Desabastecimento

Ação de S&OP: O gatilho de "Compra Imediata" agora é um indicador dinâmico. Ele cruza o saldo real disponível com o lead time exato do fornecedor (15 ou 30 dias).

# 🖼️ Evidências de Validação (Testes e Negócio)

# 6️⃣ Etapa 6: Implantação (Deployment)
⚙️ Arquitetura de Execução e Macro Fluxo
O pipeline de dados opera de forma semi-automatizada:

1. O analista exporta o dump do WMS e salva no diretório de rede.

2. O script / Power Query executa o ETL (higienização, joins, cálculos) nativamente.

3. O modelo é atualizado e o painel analítico disponibilizado via SharePoint.

4. Governança: Compradores são obrigatoriamente bloqueados de emitir ordens de compra caso o item conste com Status: Excesso no dashboard, exigindo assinatura da Diretoria.

# 📈 Demonstração de ROI (Retorno sobre o Investimento)
💰 Destravamento de Capital: Recuperação imediata de R$ 17.583,65 imobilizados em estoque morto de Classe C.

⏱️ Eficiência Operacional (FTE): Processo manual de consolidação reduzido de 6 horas semanais para 2 minutos. Aumento de 24h úteis mensais para o time focar em negociações estratégicas.

🛡️ Mitigação de Custos Ocultos: Bloqueio de 58,4% das requisições fora do padrão, extinguindo fretes rodoviários emergenciais (majorados).

🖼️ Painel Final e Ambiente Implantado
Documentação desenvolvida focada em governança corporativa, aplicando inteligência e métricas avançadas na estrutura de suprimentos da organização.