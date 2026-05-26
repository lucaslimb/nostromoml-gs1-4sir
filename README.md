# NostromoML

## Classificação de Exoplanetas a partir do Telescópio Espacial Kepler (NASA)

> Cognitive Computing - Global Solution - 4SIR

Lucas de Lima Bezerra - RM 98632
Felipe Cortez - RM 551665

Prova com base em Ciência de Dados & Machine Learning, tendo como foco os conteúdos dos labs 1 a 3 de Aprendizado Supervisionado (Classificação)

> **Algoritmo principal:** Random Forest Classifier

---

## Sobre o Projeto

### A Visão

A descoberta de exoplanetas habitáveis é hoje um dos maiores desafios científicos da humanidade — e também um dos maiores gargalos operacionais. O Telescópio Kepler gerou mais de **10.000 candidatos a planetas**, cada um exigindo análise manual de especialistas para ser classificado. Esse processo é lento, caro e não escalável para missões futuras como o TESS e o Roman Space Telescope, que vão gerar volumes de dados ainda maiores.

**A NostromoML nasceu para resolver esse problema.**

Nossa proposta é construir um sistema de triagem inteligente capaz de **pré-classificar automaticamente sinais estelares**, filtrando falsos positivos com alta precisão e priorizando os candidatos mais promissores para análise humana aprofundada. Isso reduz em ordens de magnitude o custo de revisão manual e acelera o pipeline de descoberta científica — criando valor tanto para agências espaciais quanto para institutos de pesquisa privados e empresas de astroturismo baseadas em ciência real.

O modelo treinado neste projeto é a **prova de conceito** dessa tecnologia: com dados históricos do Kepler, demonstramos que uma IA pode classificar sinais estelares com **90,5% de acurácia** — sem nenhum conhecimento prévio da astronomia, apenas aprendendo padrões nos dados físicos.

---

### O Pipeline

Este projeto implementa um pipeline completo de Machine Learning para classificar **sinais estelares detectados pelo Telescópio Espacial Kepler da NASA**, determinando se cada objeto de interesse (`KOI — Kepler Object of Interest`) é:

| Classe | Descrição |
|---|---|
| `CONFIRMED` | Planeta confirmado por análise científica adicional |
| `CANDIDATE` | Candidato a planeta — sinal consistente com trânsito planetário, aguardando confirmação |
| `FALSE POSITIVE` | Falso positivo — sinal que aparenta trânsito planetário mas não é de origem planetária |

O trabalho segue a metodologia apresentada na disciplina de **Cognitive Computing**, aplicando as boas práticas de pré-processamento, treinamento estratificado e avaliação com métricas adequadas para dados multiclasse.

---

## Contexto Científico: O Telescópio Kepler

O **Kepler Space Observatory** é um satélite científico desenvolvido pela NASA, lançado em **março de 2009**, com o objetivo de descobrir planetas fora do Sistema Solar (exoplanetas) ao redor de outras estrelas. O método de detecção utilizado é o **método do trânsito**: o telescópio monitora continuamente a luminosidade de mais de 150.000 estrelas e registra pequenas quedas periódicas de brilho que podem indicar a passagem de um planeta na frente da estrela.

---

## Sobre o Dataset utilizado

**Fonte:** [NASA — Kepler Exoplanet Search Results (Kaggle)](https://www.kaggle.com/datasets/nasa/kepler-exoplanet-search-results/data)  
**Arquivo:** `kepler_data.csv`  
**Licença:** CC0 — Domínio Público  

O dataset é um **registro cumulativo de todos os Kepler Objects of Interest (KOIs)** observados pelo telescópio — aproximadamente 10.000 candidatos a exoplanetas, com medições fotométricas e parâmetros estelares associados.

### Principais Colunas

| Coluna | Descrição |
|---|---|
| `koi_disposition` | **Target (variável alvo):** classificação final do KOI — `CONFIRMED`, `CANDIDATE` ou `FALSE POSITIVE` |
| `koi_pdisposition` | Disposição preliminar interna do projeto Kepler (removida no pré-processamento) |
| `koi_score` | Nível de confiança na disposição (0 a 1) — removida para evitar data leakage |
| `koi_fpflag_nt` | Flag: formato do trânsito não-trapézoidal (indica falso positivo) |
| `koi_fpflag_ss` | Flag: sinal secundário de eclipses |
| `koi_fpflag_co` | Flag: contaminação por objeto central (offsetting) |
| `koi_fpflag_ec` | Flag: contaminação por eclipses de fundo |
| `koi_period` | Período orbital do KOI (dias) |
| `koi_impact` | Parâmetro de impacto do trânsito |
| `koi_duration` | Duração do trânsito (horas) |
| `koi_depth` | Profundidade do trânsito (fração da curva de luz) |
| `koi_prad` | Raio do planeta (em raios terrestres) |
| `koi_teq` | Temperatura de equilíbrio do planeta (K) |
| `koi_insol` | Fluxo de insolação (em relação à Terra) |
| `koi_model_snr` | Razão sinal-ruído do modelo de trânsito |
| `koi_steff` | Temperatura efetiva da estrela hospedeira (K) |
| `koi_slogg` | Gravidade superficial da estrela (log g) |
| `koi_srad` | Raio da estrela hospedeira (em raios solares) |
| `ra` / `dec` | Ascensão reta e declinação (coordenadas celestes) |
| `koi_kepmag` | Magnitude na banda Kepler |

> **Colunas removidas no pré-processamento:** identificadores (`rowid`, `kepid`, `kepoi_name`, `kepler_name`, `koi_tce_delivname`), coluna de score (`koi_score`), disposição preliminar (`koi_pdisposition`) e 22 colunas de incerteza (`_err1` / `_err2`).

---

## Resultados Obtidos

### Dimensões do Dataset

| Etapa | Linhas | Colunas |
|---|---|---|
| Carregamento original | 9.564 | 50 |
| Após remoção de colunas | 9.564 | 21 |
| Após remoção de NaN | **8.945** | **21** |
| Treino | 7.156 | 20 |
| Teste | 1.789 | 20 |

### Distribuição das Classes (dataset limpo)

| Classe | Amostras | % |
|---|---|---|
| FALSE POSITIVE | ~4.706 | ~52,6% |
| CONFIRMED | ~2.143 | ~24,0% |
| CANDIDATE | ~2.096 | ~23,4% |

> O dataset apresenta **desbalanceamento moderado**, com FALSE POSITIVEs sendo a classe majoritária (~52%). O parâmetro `stratify=y` no `train_test_split` garante que essa proporção seja mantida nos conjuntos de treino e teste.

### Métricas de Desempenho

| Classe | Precision | Recall | F1-score | Suporte |
|---|---|---|---|---|
| CANDIDATE | 0.83 | 0.79 | 0.81 | 427 |
| CONFIRMED | 0.82 | 0.82 | 0.82 | 457 |
| FALSE POSITIVE | 0.98 | 1.00 | 0.99 | 905 |
| **Acurácia geral** | | | **0.90** | 1.789 |
| macro avg | 0.88 | 0.87 | 0.87 | 1.789 |
| weighted avg | 0.90 | 0.90 | 0.90 | 1.789 |

### Top 10 Features Mais Importantes

| # | Feature | Importância | Descrição |
|---|---|---|---|
| 1 | `koi_model_snr` | 12.7% | Razão sinal-ruído do modelo de trânsito |
| 2 | `koi_fpflag_co` | 12.6% | Flag de contaminação por objeto central |
| 3 | `koi_fpflag_nt` | 12.3% | Flag de trânsito com formato não-trapézoidal |
| 4 | `koi_fpflag_ss` | 10.7% | Flag de eclipse secundário |
| 5 | `koi_prad` | 7.7% | Raio do planeta (raios terrestres) |
| 6 | `koi_depth` | 5.4% | Profundidade do trânsito |
| 7 | `koi_fpflag_ec` | 4.2% | Flag de eclipse por contaminação de fundo |
| 8 | `koi_impact` | 4.2% | Parâmetro de impacto do trânsito |
| 9 | `koi_period` | 3.8% | Período orbital |
| 10 | `koi_teq` | 3.6% | Temperatura de equilíbrio do planeta |

> As **flags de falso positivo** (`koi_fpflag_*`) e a razão sinal-ruído (`koi_model_snr`) dominam a importância — o que faz sentido fisicamente, pois são os indicadores mais diretos de se um sinal é ou não de origem planetária.

---

## Metodologia

O pipeline segue o fluxo padrão de classificação supervisionada com base nos laboratórios:

| Etapa | Descrição | Ferramenta |
|-------|-----------|------------|
| 1. EDA | Inspeção do dataset: `head()`, `info()`, `describe()`, distribuição de classes | `pandas` |
| 2. Limpeza | Remoção de colunas identificadoras, 22 colunas `_err1`/`_err2` e linhas com NaN | `pandas` |
| 3. Encoding | Conversão do target string em inteiros (ordem alfabética) | `LabelEncoder` |
| 4. Split | Divisão 80/20 estratificada por classe | `train_test_split` |
| 5. Normalização | Padronização média 0, desvio padrão 1 (fit apenas no treino) | `StandardScaler` |
| 6. Treinamento | Ensemble de árvores de decisão com `random_state=42` | `RandomForestClassifier` |
| 7. Avaliação | Acurácia, precision/recall/F1 por classe e matriz de confusão visual | `sklearn.metrics` + `seaborn` |
| 8. Interpretação | Ranking das 10 features mais relevantes para o modelo | `feature_importances_` |

---

## Como Executar

### Pré-requisitos

- Python 3.11+

### Instalação

```bash
# Criar e ativar ambiente virtual
python -m venv .venv
.venv\Scripts\activate        # Windows
# source .venv/bin/activate   # Linux/macOS

# Instalar dependências
pip install -r requirements.txt
```

### Execução

Abra o arquivo `kepler_classificador.ipynb` no VS Code ou Jupyter e execute as células em ordem.

---

## Interpretação dos Resultados

### Por que FALSE POSITIVE tem desempenho tão superior?

A classe `FALSE POSITIVE` atingiu **recall de 100%** pois suas features são altamente discriminativas: as flags `koi_fpflag_*` foram especificamente projetadas pelo time Kepler para identificar assinaturas de fenômenos não-planetários (eclipses de estrelas binárias, variações de fundo, etc.).

### Por que CANDIDATE é a classe mais difícil?

`CANDIDATE` é intrinsecamente uma classe de **incerteza** — são sinais que se parecem com trânsitos planetários mas ainda não possuem dados suficientes para confirmação ou rejeição definitiva. Suas características se sobrepõem tanto com `CONFIRMED` quanto com `FALSE POSITIVE`, resultando em F1-score menor (0.81).

### Por que koi_score foi removido?

Apesar de ser numericamente altamente correlacionado ao target, `koi_score` representa o **resultado da classificação** do próprio sistema Kepler — inclui-lo seria **data leakage**, tornando o modelo artificialmente perfeito sem aprendizado real dos dados físicos.

---

## Conclusão

O modelo **Random Forest** demonstrou ser uma escolha eficaz para a classificação de sinais do telescópio Kepler, atingindo **acurácia de 90,5%** no conjunto de teste com um pipeline reprodutível e bem estruturado.

Os principais achados foram:

- A classe **`FALSE POSITIVE`** é classificada com quase perfeição (F1 = 0.99, recall = 100%), graças às flags de falso positivo (`koi_fpflag_*`) que o próprio time Kepler projetou para identificar fenômenos não-planetários — confirmando que as features do dataset são fisicamente informativas.
- A classe **`CANDIDATE`** é a mais difícil de classificar (F1 = 0.81), o que reflete sua natureza intrínseca de incerteza: são objetos em estado de análise, cujas características se sobrepõem às das outras duas classes.
- A classe **`CONFIRMED`** apresenta desempenho equilibrado (F1 = 0.82), com erros distribuídos simetricamente entre os dois outros grupos.
- As features mais importantes para o modelo são a **razão sinal-ruído** (`koi_model_snr`) e as **flags de falso positivo** — corroborando o que se espera fisicamente do problema.

Como trabalhos futuros, poderiam ser explorados: otimização de hiperparâmetros via `GridSearchCV`, uso de validação cruzada estratificada e comparação com outros algoritmos (XGBoost, SVM, Redes Neurais).

---

## 📚 Referências

- [Kaggle — Kepler Exoplanet Search Results (NASA)](https://www.kaggle.com/datasets/nasa/kepler-exoplanet-search-results/data)
- [NASA Exoplanet Archive — Documentação das colunas](https://exoplanetarchive.ipac.caltech.edu/docs/API_kepcandidate_columns.html)
- [Cognitive Computing — Classificação em ML (Prof. Arnaldo Jr.)](https://arnaldojr.github.io/cognitivecomputing/aulas/IA/lab02/classificacao-ml-completa.html)
- [Cognitive Computing — Classificador Iris](https://arnaldojr.github.io/cognitivecomputing/aulas/IA/lab02/ml-classificador-iris.html)
- [scikit-learn — RandomForestClassifier](https://scikit-learn.org/stable/modules/generated/sklearn.ensemble.RandomForestClassifier.html)
