# E4 - Análise e Visualização Introdutória

**Objetivo:** ter o primeiro contato com análise de dados, organizando uma tabela com pandas e criando gráficos com Plotly.

**Ao final deste encontro você terá:** carregado uma base de dados, criado colunas novas, respondido perguntas com agrupamentos e filtros, e produzido gráficos interativos, tudo em um notebook no seu repositório.

## E4.1 Conceitos antes de começar

| Termo | O que é |
| :--- | :--- |
| **Análise de dados** | Fazer perguntas a um conjunto de dados e respondê-las com cálculos, tabelas e gráficos. |
| **pandas** | A biblioteca Python mais usada para trabalhar com tabelas de dados. |
| **DataFrame** | A tabela do pandas, com linhas e colunas, como uma planilha que você manipula por código. Cada coluna tem um nome e um tipo. |
| **CSV** | Formato de arquivo de tabela em texto: cada linha é um registro e as colunas são separadas por vírgulas. Abre no Excel e no pandas. |
| **Agrupamento** | Juntar as linhas que têm o mesmo valor em uma coluna (por exemplo, a mesma categoria) e calcular algo para cada grupo, como soma ou média. |
| **Plotly** | Biblioteca de gráficos interativos: dá para passar o mouse, aproximar e esconder partes. |
| **Notebook (Jupyter)** | Arquivo `.ipynb` que mistura blocos de código, resultados e texto. Ideal para análises, porque cada resultado aparece logo abaixo do código. |

---

## E4.2 Onde estamos no fluxo

Nos encontros anteriores vimos como **decompor** um problema de dados (E1), escolher só as informações **essenciais** (E2) e **coletar, verificar e armazenar** registros com Python (E3). Agora vem a última etapa: transformar os dados guardados em respostas.

```
Coletar → Verificar → Armazenar → Analisar (este encontro)
```

---

## E4.3 Preparação do ambiente e da base de dados

### Ambiente

Abra um terminal no **GitHub Codespaces** (no seu repositório: **Code → Codespaces → Create codespace on main**) ou no VS Code do seu computador. Instale as bibliotecas com o **uv**, o gerenciador de pacotes Python do curso:

```bash
curl -LsSf https://astral.sh/uv/install.sh | sh   # instala o uv, se ainda não tiver
uv init pc-e4 && cd pc-e4
uv add pandas plotly nbformat ipykernel
```

- `pandas` e `plotly`: tabelas e gráficos;
- `ipykernel` e `nbformat`: permitem rodar notebooks no VS Code e exibir os gráficos do Plotly dentro deles.

Crie um notebook: no VS Code, **File → New File → Jupyter Notebook**, e salve como `analise.ipynb` dentro da pasta `pc-e4`. No canto superior direito, clique em **Select Kernel** e escolha o Python da pasta `.venv` (o ambiente criado pelo uv). Para isso, as extensões **Python** e **Jupyter** da Microsoft precisam estar instaladas (painel de extensões: `Ctrl+Shift+X`).

### Base de dados

Usaremos uma base de livros de uma loja fictícia. Na pasta `pc-e4`, crie o arquivo `livros.csv` com este conteúdo:

```csv
titulo,categoria,preco,avaliacao,em_estoque,paginas
Introdução aos Dados,Tecnologia,59.90,5,True,320
Python para Iniciantes,Tecnologia,45.50,4,True,280
O Mistério do Farol,Ficção,32.00,3,False,210
Cidades Invisíveis do Sul,Ficção,28.90,4,True,180
História do Brasil Moderno,História,72.00,5,True,450
Revoluções e Impérios,História,65.40,3,False,520
Cozinha de Vó,Culinária,39.90,5,True,160
Pães e Fermentos,Culinária,44.00,2,True,140
Estatística sem Medo,Tecnologia,52.30,4,False,300
A Última Estação,Ficção,35.50,5,True,240
Guerras do Século XX,História,58.00,4,True,390
Doces do Interior,Culinária,29.90,3,False,120
Redes e Grafos,Tecnologia,68.00,3,True,350
O Jardim Secreto de Ana,Ficção,24.90,2,True,150
Diplomacia em Crise,História,49.90,4,True,280
Visualização de Dados,Tecnologia,61.20,5,True,260
```

---

## E4.4 Do arquivo à tabela

Execute cada bloco em uma célula do notebook (`Shift+Enter` executa a célula e passa para a próxima).

```python
import pandas as pd
import plotly.express as px

df = pd.read_csv("livros.csv")
df.head()
```

`pd.read_csv` lê o arquivo e cria o DataFrame `df`. `df.head()` mostra as 5 primeiras linhas: é o primeiro passo de qualquer análise, para conferir se os dados foram lidos corretamente.

Outros comandos para conhecer a tabela:

```python
df.shape        # (linhas, colunas)
```

```python
df.info()       # nome, tipo e quantidade de valores preenchidos de cada coluna
```

```python
df.describe()   # estatísticas das colunas numéricas: média, mínimo, máximo...
```

> [!TIP]
> **Vindo do E3?** Se você salvou registros com o TinyDB, dá para transformá-los em DataFrame: `from tinydb import TinyDB` e `df = pd.DataFrame(TinyDB("noticias.json").all())`. Uma lista de dicionários sempre vira uma tabela, em que as chaves são as colunas.

---

## E4.5 Selecionar e criar colunas

Selecionar colunas é a abstração do E2 em código:

```python
df["preco"]                     # uma coluna
df[["titulo", "preco"]]         # várias colunas (repare nos colchetes duplos)
```

Criar colunas a partir das existentes:

```python
df["preco_com_desconto"] = (df["preco"] * 0.9).round(2)
df["caro"] = df["preco"] > 50           # coluna booleana: True ou False
df["livro_longo"] = df["paginas"] >= 300
df.head()
```

Uma **coluna booleana** responde "sim ou não" para cada linha. Ela é útil para filtrar e para contar: somar uma coluna booleana conta quantos `True` existem.

```python
df["caro"].sum()     # quantos livros custam mais de 50
```

---

## E4.6 Filtrar linhas e encontrar valores únicos

### Filtros

```python
df[df["categoria"] == "Ficção"]                              # só ficção
df[df["caro"]]                                               # só os caros
df[(df["avaliacao"] >= 4) & (df["em_estoque"])]              # bem avaliados E em estoque
```

Em filtros do pandas, use `&` (e), `|` (ou) e `~` (não), com cada condição entre parênteses. As palavras `and` e `or` do Python não funcionam aqui.

### Valores únicos

```python
df["categoria"].unique()     # array com as categorias, sem repetição
df["categoria"].nunique()    # quantas categorias diferentes existem
set(df["categoria"])         # também funciona: o set do Python (E3) remove repetidos
```

> [!NOTE]
> `set()` é uma estrutura do próprio Python, não um método do pandas. Com DataFrames, prefira `.unique()` ou `.nunique()`, que funcionam direto na coluna e mantêm a ordem em que os valores aparecem.

---

## E4.7 Agrupar com `groupby()`

`groupby` responde perguntas do tipo "**para cada** categoria, quanto...?".

```python
# Preço médio por categoria
df.groupby("categoria")["preco"].mean().round(2)
```

```python
# Quantos livros há em cada categoria
df.groupby("categoria").size()
```

```python
# Vários resumos de uma vez
df.groupby("categoria").agg(
    quantidade=("titulo", "count"),
    preco_medio=("preco", "mean"),
    avaliacao_media=("avaliacao", "mean"),
    em_estoque=("em_estoque", "sum"),
).round(2)
```

Leia o resultado em voz alta: "para cada categoria, a quantidade de livros, o preço médio, a avaliação média e quantos estão em estoque". Se a frase faz sentido, o agrupamento está certo.

---

## E4.8 Gráficos com Plotly

O Plotly Express (`px`) recebe um DataFrame e os nomes das colunas que vão em cada eixo. Para usar um resultado de `groupby`, transforme-o de volta em DataFrame com `.reset_index()`.

### Barras: comparar valores entre grupos

```python
preco_por_categoria = df.groupby("categoria")["preco"].mean().round(2).reset_index()

fig = px.bar(
    preco_por_categoria,
    x="categoria",
    y="preco",
    title="Preço médio por categoria",
    labels={"categoria": "Categoria", "preco": "Preço médio (R$)"},
)
fig.show()
```

### Pizza: participação de cada parte no total

```python
livros_por_categoria = df.groupby("categoria").size().reset_index(name="quantidade")

fig = px.pie(
    livros_por_categoria,
    names="categoria",
    values="quantidade",
    title="Distribuição dos livros por categoria",
)
fig.show()
```

### Dispersão: relação entre duas medidas

```python
fig = px.scatter(
    df,
    x="paginas",
    y="preco",
    color="categoria",
    hover_name="titulo",
    title="Livros mais longos são mais caros?",
)
fig.show()
```

Passe o mouse sobre os pontos: o título do livro aparece. Clique em uma categoria na legenda para escondê-la.

### Qual gráfico escolher

| Pergunta | Gráfico |
| :--- | :--- |
| Qual grupo tem mais (ou menos)? | Barras |
| Quanto cada parte representa do total? | Pizza, **somente** com poucas categorias (até 5) |
| Duas medidas andam juntas? | Dispersão |
| Como algo mudou ao longo do tempo? | Linhas (`px.line`) |

> [!WARNING]
> Gráficos de pizza ficam ilegíveis com muitas fatias ou com fatias de tamanho parecido, porque é difícil comparar ângulos. Na dúvida, use barras: elas mostram a mesma informação com mais precisão.

### Salvar um gráfico

```python
fig.write_html("grafico.html")   # arquivo interativo, abre em qualquer navegador
```

---

## E4.9 Atividade - Perguntas para a base

**Formato:** individual ou em dupla. **Tempo:** 20 min.

Responda cada pergunta com código no notebook e escreva a resposta em uma célula de texto (Markdown) logo abaixo:

1. Qual categoria tem a maior avaliação média?
2. Quantos livros estão fora de estoque, e de quais categorias?
3. Qual o livro mais caro de cada categoria?
4. Faça um gráfico de barras com a quantidade de livros em estoque e fora de estoque por categoria.

<details>
<summary>Dicas</summary>

1. `df.groupby("categoria")["avaliacao"].mean().sort_values(ascending=False)`
2. `df[~df["em_estoque"]]`
3. `df.loc[df.groupby("categoria")["preco"].idxmax()]` (o `idxmax` devolve a linha do maior valor de cada grupo)
4. Agrupe por duas colunas: `df.groupby(["categoria", "em_estoque"]).size().reset_index(name="quantidade")`, e use `px.bar(..., x="categoria", y="quantidade", color="em_estoque", barmode="group")`.
</details>

---

## E4.10 Autoavaliação

**1. O que `df.head()` mostra e por que usá-lo primeiro?**

<details>
<summary>Resposta</summary>

As primeiras linhas da tabela. Serve para conferir se os dados foram lidos corretamente antes de qualquer análise.
</details>

**2. Como filtrar os livros de Tecnologia com preço abaixo de 60?**

<details>
<summary>Resposta</summary>

`df[(df["categoria"] == "Tecnologia") & (df["preco"] < 60)]`
</details>

**3. Que pergunta este código responde: `df.groupby("categoria")["paginas"].mean()`?**

<details>
<summary>Resposta</summary>

"Qual é o número médio de páginas dos livros de cada categoria?"
</details>

**4. Você quer mostrar o total de vendas de 12 vendedores. Barras ou pizza?**

<details>
<summary>Resposta</summary>

Barras. Com 12 fatias, a pizza fica difícil de ler e de comparar.
</details>

**5. Qual a diferença entre `df["categoria"].unique()` e `df["categoria"].nunique()`?**

<details>
<summary>Resposta</summary>

`unique()` devolve os valores diferentes; `nunique()` devolve quantos valores diferentes existem.
</details>

---

## Tarefa da semana

No notebook `analise.ipynb`:

1. crie uma coluna booleana que indique se o título do livro contém uma palavra à sua escolha (dica: `df["titulo"].str.contains("Dados", case=False)`);
2. formule uma pergunta nova sobre a base, escreva-a em uma célula de texto e responda-a com filtros ou agrupamentos;
3. crie um gráfico que ajude a comunicar a resposta e explique em uma frase por que escolheu aquele tipo de gráfico.

Antes de enviar, limpe as saídas das células (botão **Clear All Outputs** no topo do notebook) para não guardar resultados pesados no histórico do Git. Envie `analise.ipynb` e `livros.csv` para o seu repositório em `pensamento-computacional/e4/`.

---

## Checklist do Encontro 4

- [ ] Preparei o ambiente e abri um notebook com o kernel da `.venv`
- [ ] Carreguei um CSV em um DataFrame e inspecionei com `head`, `info` e `describe`
- [ ] Criei colunas novas, incluindo uma booleana
- [ ] Filtrei linhas com condições combinadas
- [ ] Encontrei valores únicos com `unique` e `nunique`
- [ ] Respondi perguntas com `groupby`
- [ ] Criei gráficos de barras, pizza e dispersão com Plotly
- [ ] Sei escolher o tipo de gráfico para cada pergunta
- [ ] Fiz a atividade e a autoavaliação
- [ ] Entreguei a tarefa no meu repositório

## Links de apoio

- [pandas - introdução em 10 minutos](https://pandas.pydata.org/docs/user_guide/10min.html)
- [pandas - agrupamentos (`groupby`)](https://pandas.pydata.org/docs/user_guide/groupby.html)
- [Plotly Express - guia](https://plotly.com/python/plotly-express/)
