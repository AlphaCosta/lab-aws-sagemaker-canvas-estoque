# SageMaker Canvas – Time Series Forecasting (VENDAS_UNIDADES)

## Objetivo
Construir um modelo de **previsão de séries temporais** no Amazon SageMaker Canvas para prever a demanda diária (**VENDAS_UNIDADES**) por produto (**ID_PRODUTO**) e analisar desempenho e variáveis mais influentes.

---

## Dataset
Dataset tabular com granularidade diária e múltiplas séries (uma por produto).

**Colunas principais**
- `DATA_EVENTO` (date): timestamp diário
- `ID_PRODUTO` (text): identificador do item/série
- `VENDAS_UNIDADES` (numeric): variável alvo (demanda/vendas)

**Features auxiliares (exemplos)**
- `PRECO`, `FLAG_PROMOCAO`, `CATEGORIA`, `DIA_SEMANA`, `MES`
- `ESTOQUE_INICIO_DIA`, `ESTOQUE_FIM_DIA`
- `LEAD_TIME_DIAS`, `ESTOQUE_SEGURANCA`, `PONTO_REPOSICAO`

---

## Preparação de Dados (Data Wrangler)
1. Importação do CSV no Data Wrangler.
2. Ajuste de tipos:
   - `DATA_EVENTO` como **date**
   - `ID_PRODUTO` convertido para **text** (requisito do Canvas para Item ID em time series)
3. Transformações:
   - Remoção de colunas derivadas/decisórias para reduzir vazamento e manter apenas variáveis disponíveis no momento da previsão.

---

## Treinamento do Modelo (Canvas)
Tipo de problema: **Predictive analysis → Time series forecasting**

Configuração:
- Target: `VENDAS_UNIDADES`
- Timestamp: `DATA_EVENTO`
- Item ID: `ID_PRODUTO`
- Horizonte configurado: **14 dias**
- Build executado: **Quick build**

---

## Resultados (Quick build)
Métricas obtidas no Canvas:
- Avg. wQL: **0.171**
- MAPE: **0.243** (~24,3%)
- WAPE: **0.253** (~25,3%)
- RMSE: **3.817**
- MASE: **0.723**

Interpretação:
- Erro percentual médio em torno de ~25% (MAPE/WAPE), adequado como primeira versão de forecast de demanda diária.
- RMSE ~3,8 indica erro típico de aproximadamente 4 unidades na escala do alvo.
- MASE < 1 sugere desempenho melhor que um baseline simples.

---

## Impacto das variáveis (Column impact)
Top 5 variáveis mais relevantes:
1. `ESTOQUE_INICIO_DIA` (14.58%)
2. `ESTOQUE_FIM_DIA` (12.48%)
3. `PRECO` (10.19%)
4. `PONTO_REPOSICAO` (3.16%)
5. `DIA_SEMANA` (3.12%)

Observação:
- O modelo se apoia fortemente em sinais de estoque e preço, além do padrão semanal, para prever a demanda.

---

## Predições (Predict)
Foi iniciado um job de **Batch prediction**, porém o processo não foi concluído devido ao término dos créditos de treinamento/execução da conta AWS (job com status **Failed**).  
Apesar disso, o modelo foi treinado com sucesso (Quick build) e as análises de métricas e impacto de variáveis foram realizadas.

---

## Evidências (prints)

### 1) Métricas do modelo (Analyze)
![Métricas do Quick build](Evidencias/Evidencia_1.png)

### 2) Predição (falha por créditos)
![Predict job failed](Evidencias/Evidencia_2.png)

---

## Próximos passos
- Executar **Standard build** quando houver créditos disponíveis para melhorar acurácia e estabilidade.
- Reexecutar **Batch prediction** para gerar forecasts e exportar resultados.
- Transformar previsão de demanda em recomendação de reposição:
  **Reposição sugerida = (Demanda prevista no lead time) + (Estoque de segurança)**.
