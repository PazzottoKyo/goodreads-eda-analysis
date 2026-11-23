# Análise exploratória do catálogo Goodreads

![Python](https://img.shields.io/badge/Python-3.10%2B-3776AB?style=flat-square&logo=python&logoColor=white)
![Pandas](https://img.shields.io/badge/Pandas-Data_Wrangling-150458?style=flat-square&logo=pandas&logoColor=white)
![SciPy](https://img.shields.io/badge/SciPy-Hypothesis_Testing-8CAAE6?style=flat-square&logo=scipy&logoColor=white)
![Seaborn](https://img.shields.io/badge/Seaborn-Data_Viz-4C72B0?style=flat-square)

Este projeto realiza uma análise exploratória de dados (EDA) sobre um conjunto de dados contendo **10.000 livros** catalogados na plataforma Goodreads. O objetivo principal é investigar padrões relacionados às avaliações dos leitores, à dinâmica entre popularidade e qualidade percebida, além de diferenças temporais e linguísticas nas publicações.

---

## Sumário

- [Objetivos da análise](#objetivos-da-análise)
- [Dados](#dados)
- [Estrutura do projeto](#estrutura-do-projeto)
- [Pré-processamento e engenharia de atributos](#pré-processamento-e-engenharia-de-atributos)
- [Principais resultados e insights](#principais-resultados-e-insights)
- [Visualizações](#visualizações)
- [Limitações](#limitações)
- [Como executar o projeto](#como-executar-o-projeto)
- [Produção científica (em andamento)](#produção-científica-em-andamento)

---

## Objetivos da análise

1. **Distribuição de notas:** compreender o comportamento geral das avaliações médias (`average_rating`) e identificar padrões de concentração e assimetria nas notas da plataforma.
2. **Impacto temporal:** avaliar se o período histórico de publicação (de obras clássicas pré-1900 a lançamentos do século XXI) está associado a diferenças nas avaliações.
3. **Barreira linguística:** investigar diferenças estatisticamente significativas nas avaliações entre os principais idiomas do catálogo.
4. **Popularidade vs. qualidade:** testar a hipótese de que livros mais populares (maior volume de avaliações e resenhas) tendem a apresentar notas médias diferentes.
5. **Destaques do catálogo:** identificar as obras individuais e os registros de autoria com maior engajamento e volume acumulado de avaliações.

---

## Dados

O conjunto de dados é um recorte de colunas do *Goodbooks-10k*, que reúne livros relativamente populares do Goodreads. O arquivo bruto contém as seguintes colunas:

| Coluna | Descrição |
| --- | --- |
| `id` | Identificador do registro |
| `title` | Título do livro |
| `authors` | Autor(es), conforme registrado no catálogo |
| `original_publication_year` | Ano da primeira publicação |
| `language_code` | Código do idioma da edição |
| `average_rating` | Nota média dos leitores (escala de 1 a 5) |
| `ratings_count` | Quantidade total de avaliações |
| `work_text_reviews_count` | Quantidade de resenhas escritas |

---

## Estrutura do projeto

```text
goodreads-eda-analysis/
│
├── data/
│   ├── raw_data/
│   │   └── books_colunas_relevantes.csv        # Dataset original (10.000 registros)
│   └── processed_data/
│       └── books_clean.csv                     # Dataset tratado e enriquecido (9.979 registros)
│
├── py_notebooks/
│   └── 01_exploratory_data_analysis.ipynb      # Notebook principal com código, testes e visualizações
│
├── charts/                                     # Gráficos exportados em alta resolução (300 DPI)
│   ├── 01_histograma_avaliacoes.png
│   ├── 02_boxplot_periodo.png
│   ├── 03_dispersao_popularidade_resenhas.png
│   ├── 04_boxplot_idioma.png
│   ├── 05_mapa_calor_correlacao.png
│   └── 06_top_livros_autores.png
│
└── README.md                                   # Documentação do projeto
```

---

## Pré-processamento e engenharia de atributos

Durante a preparação dos dados no notebook, foram aplicadas as seguintes transformações:

- **Tratamento de nulos e tipagem:** remoção de 21 registros sem ano de publicação (`original_publication_year`), resultando em **9.979 livros válidos**, com os anos convertidos para valores inteiros, mantendo inclusive as 31 obras históricas da Antiguidade (ano ≤ 0).
- **Padronização de idiomas:** agrupamento de variações regionais do inglês (`eng`, `en`, `en-US`, `en-GB`, `en-CA`) sob o código único `eng` para evitar fragmentação na análise linguística. Registros sem `language_code` (1.079 livros após a limpeza) são classificados como `desconhecido` e **excluídos da comparação entre idiomas**.
- **Categorização temporal:** criação da variável `periodo`, segmentando o acervo em quatro eras:
  - *Clássicos (pré-1900)*
  - *Século XX (1900–1979)*
  - *Contemporâneo (1980–1999)*
  - *Século XXI (2000+)*
- **Transformação logarítmica:** aplicação de `log10(x + 1)` nas variáveis altamente assimétricas (`ratings_count` e `work_text_reviews_count`), gerando as variáveis `log_ratings` e `log_reviews`, utilizadas para facilitar a visualização das distribuições e relações entre os dados.

---

## Principais resultados e insights

### Concentração das avaliações

As notas concentram-se fortemente entre **3,80 e 4,25**, com média global de aproximadamente **4,00** e mediana de **4,02**. A distribuição apresenta leve assimetria negativa (aproximadamente -0,51), indicando maior concentração de avaliações nas faixas superiores.

### Diferenças por período de publicação

O teste não paramétrico de Kruskal-Wallis (H = 93,206; p < 0,0001) identificou diferenças estatisticamente significativas entre os períodos analisados. Livros do **século XX (1900–1979)** apresentam a maior mediana de avaliação, e os **clássicos (pré-1900)** a menor:

| Período | Quantidade | Mediana |
| --- | ---: | ---: |
| Clássicos (pré-1900) | 379 | 3,94 |
| Século XX (1900–1979) | 1.348 | 4,07 |
| Contemporâneo (1980–1999) | 2.064 | 4,04 |
| Século XXI (2000+) | 6.188 | 4,01 |

As comparações *post-hoc* (Mann-Whitney com correção de Bonferroni) mostram diferença significativa em todos os pares de períodos, **exceto** entre o século XX e o contemporâneo (p corrigido = 0,15). A amplitude entre as medianas é pequena (0,13 ponto numa escala de 1 a 5), então a significância estatística, impulsionada pelo tamanho da amostra, tem baixa relevância prática.

### Diferenças por idioma

Entre os cinco idiomas mais frequentes, o teste de Kruskal-Wallis (H = 22,476; p = 0,000161) indicou diferenças estatisticamente significativas nas distribuições das avaliações:

| Idioma | Quantidade | Mediana |
| --- | ---: | ---: |
| Inglês (`eng`) | 8.715 | 4,02 |
| Árabe (`ara`) | 63 | 3,82 |
| Francês (`fre`) | 25 | 3,94 |
| Indonésio (`ind`) | 21 | 4,04 |
| Espanhol (`spa`) | 20 | 4,02 |

Nas comparações *post-hoc* (Bonferroni), **apenas o par inglês × árabe** apresentou diferença significativa (p corrigido ≈ 1,3 × 10⁻⁴). O catálogo é amplamente dominado por obras em inglês (87,3% do total e 97,9% dos livros com idioma conhecido), o que exige cautela na interpretação devido ao desequilíbrio amostral.

### Popularidade não garante nota alta

A correlação de Spearman revelou que o volume de avaliações apresenta uma correlação positiva muito fraca com a nota média (ρ = 0,079), enquanto o volume de resenhas escritas apresenta uma correlação negativa fraca (ρ = -0,149). Os resultados indicam que maior engajamento não implica necessariamente avaliações médias mais elevadas, e não permitem inferir causalidade.

### Concentração de engajamento

Franquias globais dominam o topo do catálogo. O registro de autoria **J.K. Rowling, Mary GrandPré**, considerando o agrupamento conjunto dos nomes presentes na coluna de autoria, lidera o volume acumulado de avaliações, com **cerca de 13,4 milhões de avaliações somadas** em oito registros. Em seguida aparecem Suzanne Collins (≈ 8,6 milhões), Stephenie Meyer (≈ 8,4 milhões) e Stephen King (≈ 6,5 milhões).

---

## Visualizações

Todos os gráficos são gerados pelo notebook e exportados em 300 DPI para a pasta [`charts/`](charts/):

| Gráfico | Arquivo |
| --- | --- |
| Distribuição das avaliações médias | [`01_histograma_avaliacoes.png`](charts/01_histograma_avaliacoes.png) |
| Avaliação média por período | [`02_boxplot_periodo.png`](charts/02_boxplot_periodo.png) |
| Popularidade e resenhas vs. nota (escala log) | [`03_dispersao_popularidade_resenhas.png`](charts/03_dispersao_popularidade_resenhas.png) |
| Avaliação média nos 5 idiomas mais frequentes | [`04_boxplot_idioma.png`](charts/04_boxplot_idioma.png) |
| Matriz de correlação de Spearman | [`05_mapa_calor_correlacao.png`](charts/05_mapa_calor_correlacao.png) |
| Top 10 livros e autores mais avaliados | [`06_top_livros_autores.png`](charts/06_top_livros_autores.png) |

---

## Limitações

- O dataset reúne apenas livros relativamente populares, então os resultados descrevem associações restritas a esse recorte e não ao Goodreads como um todo.
- Não há variáveis como gênero literário ou perfil dos leitores.
- Cerca de 10,8% dos livros não têm idioma informado e ficam fora da análise linguística; entre os demais, os idiomas diferentes do inglês têm poucos registros.
- A coluna de autoria é tratada como texto único, então livros com coautores aparecem como um registro próprio (como `J.K. Rowling, Mary GrandPré`).
- As análises são associativas: não permitem conclusões causais nem sobre volume de vendas.

---

## Como executar o projeto

1. **Clone este repositório:**

   ```bash
   git clone https://github.com/PazzottoKyo/goodreads-eda-analysis.git
   cd goodreads-eda-analysis
   ```

2. **Crie um ambiente virtual e instale as dependências:**

   ```bash
   python -m venv venv
   ```

   No Windows:

   ```bash
   venv\Scripts\activate
   ```

   No Linux ou macOS:

   ```bash
   source venv/bin/activate
   ```

   Instale as bibliotecas necessárias:

   ```bash
   pip install pandas numpy matplotlib seaborn scipy jupyter
   ```

   > O notebook foi testado com Python 3.12, pandas 3.0.6, numpy 2.5.4, matplotlib 3.11.2, seaborn 0.13.2 e scipy 1.18.1.

3. **Execute o Jupyter Notebook:**

   Considerando que o notebook utiliza caminhos relativos ao diretório `py_notebooks`, execute:

   ```bash
   cd py_notebooks
   jupyter notebook 01_exploratory_data_analysis.ipynb
   ```

   Ao executar todas as células, o notebook regenera o arquivo `data/processed_data/books_clean.csv` e os gráficos da pasta `charts/`.

---

## Produção científica (em andamento)

Os resultados estatísticos e visuais consolidados neste repositório servem de base empírica para a redação de um artigo científico em LaTeX (Overleaf), atualmente em fase de elaboração. Assim que concluído, o manuscrito final será disponibilizado diretamente neste repositório.
