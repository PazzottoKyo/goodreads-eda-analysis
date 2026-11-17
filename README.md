# Análise Exploratória do Catálogo Goodreads

![Python](https://img.shields.io/badge/Python-3.10%2B-3776AB?style=flat-square&logo=python&logoColor=white)
![Pandas](https://img.shields.io/badge/Pandas-Data_Wrangling-150458?style=flat-square&logo=pandas&logoColor=white)
![SciPy](https://img.shields.io/badge/SciPy-Hypothesis_Testing-8CAAE6?style=flat-square&logo=scipy&logoColor=white)
![Seaborn](https://img.shields.io/badge/Seaborn-Data_Viz-4C72B0?style=flat-square)

Este projeto realiza uma Análise Exploratória de Dados (EDA) aprofundada sobre um conjunto de dados contendo **10.000 livros** catalogados na plataforma Goodreads. O objetivo principal é investigar os fatores que influenciam as avaliações dos leitores, a dinâmica entre popularidade e qualidade percebida, além de padrões temporais e linguísticos nas publicações.

---

## 🎯 Objetivos da Análise

1. **Distribuição de Notas:** Compreender o comportamento geral das avaliações médias (`average_rating`) e identificar vieses de pontuação na plataforma.
2. **Impacto Temporal:** Avaliar se o período histórico de publicação (de obras clássicas pré-1900 a lançamentos do século XXI) afeta a percepção de qualidade.
3. **Barreira Linguística:** Investigar diferenças significativas nas avaliações entre os principais idiomas do catálogo.
4. **Popularidade vs. Qualidade:** Testar a hipótese de que livros mais populares (maior volume de avaliações e resenhas) tendem a receber notas mais altas ou mais baixas.
5. **Destaques do Catálogo:** Identificar as obras individuais e os autores com maior engajamento e volume acumulado de avaliações.

---

## 🗂️ Estrutura do Projeto

```text
goodreads-eda-analysis/
│
├── data/
│   ├── raw_data/
│   │   └── books_colunas_relevantes.csv   # Dataset original (10.000 registros)
│   └── processed_data/
│       └── books_clean.csv                # Dataset tratado e enriquecido (9.979 registros)
│
├── notebooks/
│   └── analise_exploratoria_goodreads.ipynb # Notebook principal com código, testes e visualizações
│
├── charts/                                # Gráficos exportados em alta resolução (300 DPI)
│   ├── 01_histograma_avaliacoes.png
│   ├── 02_boxplot_periodo.png
│   ├── 03_avaliacao_idiomas.png
│   ├── 04_popularidade_vs_nota.png
│   ├── 05_mapa_calor_correlacao.png
│   └── 06_top_livros_autores.png
│
└── README.md                              # Documentação do projeto
```

---

## 🛠️ Tecnologias e Bibliotecas Utilizadas

- **Linguagem:** Python 3.x
- **Manipulação e Limpeza de Dados:** `pandas`, `numpy`
- **Visualização de Dados:** `matplotlib`, `seaborn`
- **Estatística e Testes de Hipótese:** `scipy.stats` (Teste de Kruskal-Wallis e Correlação de Spearman)

---

## 🧹 Etapas de Pré-processamento e Engenharia de Atributos

Durante a preparação dos dados no notebook, foram aplicadas as seguintes transformações:

- **Tratamento de Nulos e Tipagem:** Remoção de 21 registros sem ano de publicação (`original_publication_year`), resultando em **9.979 livros válidos** convertidos para valores inteiros, mantendo inclusive obras históricas da Antiguidade (ano $\le 0$).
- **Padronização de Idiomas:** Agrupamento de variações regionais do inglês (`eng`, `en-US`, `en-GB`, `en-CA`) sob o código único `eng` para evitar fragmentação na análise linguística.
- **Categorização Temporal:** Criação da variável `periodo_publicacao`, segmentando o acervo em quatro eras:
  * *Clássicos (Pré-1900)*
  * *Século XX (1900–1979)*
  * *Contemporâneo (1980–1999)*
  * *Século XXI (2000+)*
- **Transformação Logarítmica:** Aplicação de $\log_{10}(x + 1)$ nas variáveis altamente assimétricas (`ratings_count` e `work_text_reviews_count`) para estabilizar a variância e viabilizar análises de correlação e dispersão.

---

## 📊 Principais Resultados e Insights

- **Viés Positivo nas Avaliações:** As notas concentram-se fortemente entre **3,80 e 4,25**, com média global de **4,00** e mediana de **4,02** (distribuição aproximadamente simétrica, com leve assimetria negativa de $-0,66$).
- **O Efeito da Era de Publicação:** O teste não-paramétrico de Kruskal-Wallis ($H = 93,206$, $p < 0,0001$) confirmou diferenças estatisticamente significativas entre os períodos. Livros do **Século XX (1900–1979)** lideram com a maior mediana de avaliação (**4,07**), seguidos pelos **Contemporâneos (4,04)** e **Século XXI (4,01)**, enquanto os **Clássicos (Pré-1900)** apresentam a menor mediana do catálogo (**3,94**), refletindo leituras obrigatórias e maior rigor crítico.
- **Diferenças por Idioma:** Entre os 5 idiomas mais frequentes (Inglês, Espanhol, Francês, Alemão e Árabe), o teste de Kruskal-Wallis ($H = 22,476$, $p = 0,0002$) indicou variações significativas na distribuição das notas, embora o catálogo seja amplamente dominado por obras em inglês.
- **Popularidade Não Garante Nota Alta:** A correlação de Spearman revelou que o volume de avaliações tem correlação positiva desprezível com a nota média ($\rho = 0,079$), enquanto o volume de resenhas escritas apresenta uma **leve correlação negativa** ($\rho = -0,149$) — indicando que livros muito debatidos atraem opiniões mais polarizadas e críticas.
- **Concentração de Engajamento:** Franquias globais dominam o topo do catálogo, com **J.K. Rowling** liderando isoladamente o volume acumulado de avaliações (ultrapassando a marca de 13 milhões de notas somadas entre suas obras no dataset).

---

## 🚀 Como Executar o Projeto

1. **Clone este repositório:**
   ```bash
   git clone [https://github.com/seu-usuario/goodreads-eda-analysis.git](https://github.com/seu-usuario/goodreads-eda-analysis.git)
   cd goodreads-eda-analysis
   ```

2. **Crie um ambiente virtual e instale as dependências:**
   ```bash
   python -m venv venv
   source venv/bin/activate  # No Windows use: venv\Scripts\activate
   pip install pandas numpy matplotlib seaborn scipy jupyter
   ```

3. **Execute o Jupyter Notebook:**
   ```bash
   jupyter notebook notebooks/analise_exploratoria_goodreads.ipynb
   ```

---

## 📄 Produção Científica (Em andamento)

Os resultados estatísticos e visuais consolidados neste repositório servem de base empírica para a redação de um artigo científico em LaTeX (Overleaf), atualmente em fase de elaboração. Assim que concluído, o manuscrito final será disponibilizado diretamente neste repositório.
