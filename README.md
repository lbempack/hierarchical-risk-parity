# Hierarchical Risk Parity (HRP) vs. Markowitz

## Objetivo

Este projeto investiga a utilização de **clusterização hierárquica na construção de portfólios de ações**, por meio da metodologia **Hierarchical Risk Parity (HRP)** proposta por Marcos López de Prado. Como referência, compara-se o desempenho da carteira HRP com uma carteira construída pela abordagem tradicional de **Markowitz**, na versão de **mínima variância global (Global Minimum Variance — GMV)**.

A pergunta que orienta o estudo é: **como uma abordagem de alocação baseada na estrutura hierárquica das correlações se compara à otimização clássica de mínima variância em um universo de ações brasileiras?**

## Dados e desenho do experimento

- **Mercado:** ações negociadas na B3, identificadas por tickers `.SA`.
- **Universo:** 39 ativos definidos na lista de tickers do notebook.
- **Fonte:** Yahoo Finance, via biblioteca `yfinance`.
- **Frequência:** diária, utilizando retornos percentuais calculados a partir dos preços de fechamento fornecidos pela fonte.
- **Período de coleta:** 01/10/2016 a 01/10/2026 (data final exclusiva na consulta).
- **In-Sample (IS):** observações anteriores a 01/10/2021.
- **Out-of-Sample (OOS):** observações a partir de 01/10/2021.

As duas metodologias recebem a **mesma matriz de covariância estimada no período IS**. Os pesos são definidos uma única vez, antes do período OOS, e mantidos em uma estratégia **buy and hold**, sem rebalanceamento.

## Metodologia

### 1. Hierarchical Risk Parity (HRP)

O procedimento implementado segue três etapas principais:

1. **Clusterização hierárquica:** estimação da matriz de correlação IS, conversão para distâncias por meio de `sqrt((1 - corr) / 2)` e agrupamento pelo método *single linkage*.
2. **Quasi-diagonalização:** reordenação dos ativos conforme as folhas do dendrograma, aproximando ativos correlacionados na matriz.
3. **Recursive bisection:** divisão sucessiva dos grupos e distribuição do capital segundo as variâncias estimadas dos subgrupos, calculadas com pesos inversamente proporcionais às variâncias individuais.

### 2. Markowitz — Global Minimum Variance (GMV)

O portfólio de referência minimiza a variância estimada da carteira, sujeito a:

- Soma dos pesos igual a 100%.
- Pesos entre 0% e 100% por ativo (*long-only*, sem posições vendidas).

A solução é obtida com o algoritmo **SLSQP**, da biblioteca `scipy.optimize`.

### 3. Avaliação fora da amostra

Com os pesos estimados exclusivamente no período IS, as duas carteiras são acompanhadas diariamente no período OOS. São calculadas as seguintes métricas:

- **Retorno acumulado**.
- **CAGR** (taxa composta de crescimento anual).
- **Volatilidade anualizada**, considerando 252 pregões por ano.
- **Índice de Sharpe anualizado**, com taxa livre de risco assumida igual a zero.

## Resultados Out-of-Sample

| Estratégia | Retorno acumulado | CAGR | Volatilidade anualizada | Sharpe (Rf = 0) |
|---|---:|---:|---:|---:|
| **HRP** | **89,15%** | **13,75%** | 14,62% | **0,9543** |
| **Markowitz (GMV)** | 66,92% | 10,91% | **13,82%** | 0,8185 |

Na janela analisada, a carteira **HRP apresentou maior retorno acumulado, maior CAGR e maior índice de Sharpe**. A carteira **GMV, por sua vez, registrou menor volatilidade realizada**.

Os resultados descrevem o desempenho observado **neste universo e nesta janela OOS**, não constituindo evidência de superioridade generalizada de uma metodologia sobre a outra.

## Limitações

- **Sharpe com taxa livre de risco zero:** não foi utilizado o CDI histórico como benchmark de retorno livre de risco.
- **Dependência dos dados:** resultados podem variar conforme atualizações, ajustes e disponibilidade das séries históricas no Yahoo Finance.

## Tecnologias utilizadas

- Python
- `pandas` e `numpy` — tratamento de dados e cálculos matriciais
- `yfinance` — obtenção de cotações
- `scipy` — clusterização hierárquica e otimização
- `matplotlib` e `seaborn` — visualizações
- `tqdm` — acompanhamento da coleta de dados

## Como executar

1. Clone o repositório ou baixe o notebook `hierarchical_risk_parity.ipynb`.
2. Instale as dependências:

   ```bash
   pip install pandas numpy yfinance scipy matplotlib seaborn tqdm
   ```

3. Abra o notebook no Jupyter ou Google Colab e execute as células na ordem apresentada.

> **Nota:** a execução faz consultas ao Yahoo Finance; a disponibilidade de determinados tickers e a revisão de dados históricos podem afetar a reprodução exata dos resultados.

## Referência

López de Prado, M. (2016). **Building Diversified Portfolios that Outperform Out of Sample**. *The Journal of Portfolio Management*, 42(4), 59–69. https://doi.org/10.3905/jpm.2016.42.4.059

---

*Projeto de pesquisa aplicada com finalidade educacional. Os resultados não constituem recomendação de investimento.*
