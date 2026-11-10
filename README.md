# IA Não Supervisionada — Clusterização de Food Delivery

## Integrantes

- Integrante 1: Gustavo Macedo 
- Integrante 2: Isabela Groke

## Objetivo

Identificar grupos de pedidos com características semelhantes de gasto, desconto,
logística e avaliação, usando Machine Learning Não Supervisionado. Cada observação
representa um pedido, não um cliente. O trabalho é exploratório e acadêmico.

## Dataset

- Arquivo: `archive/global_food_delivery_enriched_market_2026.csv`.
- Origem informada: Kaggle. O projeto não contém a URL da página original; adicionar
  o link e conferir a licença antes da publicação no GitHub.
- Base enriquecida com 8.000 pedidos e 26 colunas, incluindo cidade, canal,
  culinária, valores, distâncias, tempos, avaliações e campos derivados.
- Não foram encontrados valores ausentes, linhas duplicadas ou IDs repetidos.
- Inclui entregas por aplicativo, retiradas e consumo no local. Não há coluna de data.
- O processo de coleta e enriquecimento não está documentado nos arquivos disponíveis.
  O dataset original não foi alterado.

## Tecnologias utilizadas

Python, Pandas, NumPy, Matplotlib, Seaborn, Scikit-learn e Jupyter Notebook.

## Algoritmos e features

K-Means como algoritmo principal e AgglomerativeClustering com ligação Ward como comparação.
Ambos utilizam as mesmas cinco features padronizadas com `StandardScaler`:

| Coluna | Motivo |
|---|---|
| `order_value_usd` | Valor econômico do pedido |
| `discount_percentage` | Intensidade relativa da promoção |
| `delivery_distance_km` | Alcance do atendimento |
| `total_fulfillment_time_min` | Tempo total de espera |
| `customer_rating` | Avaliação do pedido |

IDs, classificações existentes e textos explicativos não entram nos modelos.
O canal é usado somente para interpretar os grupos. Distância e tempo têm correlação
de 0,81; mantê-los enfatiza a logística e é uma limitação explícita da análise.

## Etapas do projeto

1. Carregamento e inspeção dos dados.
2. EDA: tipos, estatísticas, ausências e duplicados.
3. Histogramas, boxplots e matriz de correlação.
4. Seleção das features, análise de extremos e preparação.
5. Padronização com StandardScaler.
6. Cotovelo e silhouette para K entre 2 e 10.
7. K-Means e interpretação das médias e tamanhos dos grupos.
8. Visualização com PCA em duas dimensões.
9. Comparação com AgglomerativeClustering.
10. Discussão dos resultados e limitações.

## Resultados da execução

Foram escolhidos **2 clusters**, pela maior silhouette entre os valores testados.
O método do cotovelo é apresentado como apoio visual, sem presumir um ponto inequívoco.
Os modelos foram ajustados nos 8.000 pedidos. As silhouettes foram calculadas na mesma
amostra fixa de 2.000 pedidos, com semente 42, para reduzir o custo de avaliação.

| Algoritmo | Clusters | Silhouette aproximada |
|---|---:|---:|
| K-Means | 2 | 0,3544 |
| AgglomerativeClustering (Ward) | 2 | 0,2592 |

| Perfil do K-Means | Pedidos | Distância média | Tempo total médio | Avaliação média |
|---|---:|---:|---:|---:|
| Menor distância e menor tempo | 6.651 | 1,69 km | 23,96 min | 4,40 |
| Maior distância e maior tempo | 1.349 | 9,77 km | 55,70 min | 3,70 |

O segundo perfil contém exclusivamente pedidos por aplicativo. O primeiro combina
entregas, consumo no local e retiradas. As médias dos valores dos pedidos são próximas
(US$ 30,36 e US$ 29,54): a separação encontrada é principalmente logística.
O PCA em duas dimensões preservou **62,23%** da variância.

K-Means teve a maior silhouette nesta comparação. A diferença de 0,0952 é descritiva;
não foi realizado teste de significância. A partição não comprova segmentos naturais,
causalidade ou representatividade do mercado global. Outras limitações incluem origem
não documentada, mistura de canais, extremos mantidos, avaliação ordinal tratada como
numérica e ausência de análise de estabilidade. Próximos passos: analisar apenas entregas,
reduzir redundância logística e verificar estabilidade com novas sementes e amostras.

## Estrutura

```text
Mini projeto/
├── archive/
│   └── global_food_delivery_enriched_market_2026.csv
├── notebooks/
│   └── clusterizacao_food_delivery.ipynb
├── .gitignore
├── README.md
└── requirements.txt
```

O ambiente local `.venv/` é ignorado pelo Git. O notebook inclui os resultados e gráficos
da execução para facilitar a leitura no GitHub.

## Como executar

Na raiz do projeto, com Python 3.10 ou superior:

```powershell
python -m venv .venv
.\.venv\Scripts\python.exe -m pip install -r requirements.txt
.\.venv\Scripts\python.exe -m jupyter notebook
```

Abra `notebooks/clusterizacao_food_delivery.ipynb`, selecione o kernel do ambiente
e execute todas as células em ordem. O CSV é localizado a partir da raiz ou de `notebooks/`.
A execução completa foi validada com o diretório de trabalho em `notebooks/`.
O Agglomerative usa mais memória que K-Means por trabalhar com distâncias entre pedidos.
