# DS em Produção — Previsão de Vendas Rossmann

Projeto de Ciência de Dados ponta a ponta (CRISP-DM) que prevê as vendas diárias das próximas 6 semanas para cada uma das lojas da rede **Rossmann**, culminando na disponibilização do modelo em produção através de uma API Flask.

## 🎯 Problema de Negócio

A Rossmann é uma das maiores redes de farmácias da Europa. O CFO da empresa precisa reformar todas as lojas e, para planejar o orçamento de cada unidade, solicitou aos gerentes de loja a previsão de vendas para as próximas 6 semanas. Este projeto simula o papel de um Data Scientist contratado para resolver esse problema, entregando:

- Um modelo de Machine Learning capaz de prever as vendas por loja;
- Uma API que expõe o modelo para consumo externo;
- Um bot de Telegram (`api tester`) que consulta a previsão de vendas de uma loja sob demanda.

## 🔁 Metodologia (CRISP-DM)

O projeto está estruturado em notebooks incrementais (`m02` a `m10`), cada um representando um ciclo completo do método CRISP-DM, refinado a cada iteração:

1. **Descrição dos Dados** — dimensão, tipos, tratamento de valores ausentes;
2. **Feature Engineering** — criação de variáveis a partir de um mapa mental de hipóteses de negócio;
3. **Filtragem de Variáveis** — seleção de linhas e colunas relevantes;
4. **Análise Exploratória de Dados (EDA)** — análises univariada, bivariada (validação de hipóteses) e multivariada;
5. **Data Preparation** — normalização, rescaling, encoding e transformações cíclicas (seno/cosseno);
6. **Feature Selection** — seleção de atributos com Boruta;
7. **Machine Learning Modelling** — comparação entre modelos (Average Model, Linear Regression, Lasso, Random Forest, XGBoost) com validação cruzada;
8. **Hyperparameter Fine Tuning** — Random Search no modelo escolhido;
9. **Tradução e Interpretação do Erro** — performance de negócio (impacto em R$) e de máquina (MAE, MAPE, RMSE);
10. **Deploy para Produção** — encapsulamento do pipeline em uma classe Python e exposição via API Flask.

### Mapa mental de hipóteses

As hipóteses de negócio que orientam a etapa de Feature Engineering foram organizadas em três grupos:

**Loja**
- Lojas com maior sortimento deveriam vender mais.
- Lojas com competidores mais próximos deveriam vender menos.
- Lojas com competidores há mais tempo deveriam vender mais.
- Lojas com promoções ativas por mais tempo deveriam vender mais.
- Lojas com mais promoções consecutivas deveriam vender mais.

**Produto**
- (hipóteses relacionadas a sortimento, tipo de loja e competição de produto)

**Tempo**
- Lojas abertas durante o feriado de Natal deveriam vender mais.
- Lojas deveriam vender mais ao longo dos anos.
- Lojas deveriam vender mais no segundo semestre do ano.
- Lojas deveriam vender mais depois do dia 10 de cada mês.
- Lojas deveriam vender menos aos finais de semana.
- Lojas deveriam vender menos durante os feriados escolares.

## 📂 Estrutura do Repositório

```
├── api/
│   ├── handler.py              # API Flask que carrega o modelo e expõe o endpoint de previsão
│   └── rossmann/
│       └── Rossmann.py         # Classe com o pipeline de limpeza, feature engineering e preparação dos dados
├── data/
│   ├── train.csv                # Dados históricos de vendas (treino)
│   ├── test.csv                 # Dados para previsão (teste)
│   └── store.csv                # Dados cadastrais das lojas
├── model/
│   └── model_rossmann.pkl       # Modelo treinado (serializado)
├── parameter/
│   └── *.pkl                    # Scalers/encoders usados na etapa de Data Preparation
├── img/
│   └── MindMapHyphotesis.png    # Mapa mental de hipóteses de negócio
├── m02_v01_store_sales_prediction.ipynb   # ... até
├── m10_v01_store_sales_prediction.ipynb   # Notebooks com a evolução do projeto (CRISP-DM)
└── README.md
```
## 🧠 Modelo Final

Entre os modelos testados (Average Model, Linear Regression, Lasso, Random Forest e XGBoost, todos avaliados com validação cruzada em séries temporais), o **XGBoost Regressor** foi selecionado e teve seus hiperparâmetros ajustados via Random Search, sendo o modelo utilizado em produção (`model/model_rossmann.pkl`).

## 🚀 API em Produção

O pipeline de inferência é encapsulado na classe `Rossmann` (`api/rossmann/Rossmann.py`), que replica as etapas de *data cleaning*, *feature engineering* e *data preparation* aplicadas durante o treinamento, garantindo consistência entre treino e produção. A API (`api/handler.py`), construída com **Flask**, expõe o endpoint:
POST /rossmann/predict

Recebendo um JSON (registro único ou lista de registros) com os dados de uma ou mais lojas e retornando a previsão de vendas para cada uma.

> ⚠️ **Atenção:** atualmente `api/handler.py` e `Rossmann.py` carregam o modelo e os scalers a partir de caminhos absolutos fixos (`/Users/Breno/...`). Para rodar em outra máquina, ajuste esses caminhos (idealmente para caminhos relativos) antes de executar.

### Como executar a API localmente

```bash
# instale as dependências principais
pip install pandas numpy scikit-learn xgboost flask inflection

# ajuste os caminhos absolutos em api/handler.py e api/rossmann/Rossmann.py
# depois rode:
cd api
python handler.py
```

## 🛠️ Tecnologias Utilizadas

- **Python** (pandas, numpy, scikit-learn, xgboost, boruta, seaborn, matplotlib, scipy, inflection)
- **Jupyter Notebook**
- **Flask** (API)
- Bot de Telegram para consulta das previsões (etapa "API Tester")

## 📌 Status do Projeto

Projeto de estudo baseado no dataset [Rossmann Store Sales](https://www.kaggle.com/c/rossmann-store-sales) (Kaggle).

## 👤 Autor

Breno Lobracci
