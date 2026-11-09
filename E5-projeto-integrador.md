# E5 - Projeto Final Integrador

**Objetivo:** aplicar decomposição, abstração, lógica e análise de dados em um problema acadêmico real.

**Ao final deste encontro você terá:** estruturado e executado uma análise completa sobre desempenho acadêmico, do problema ao gráfico, e documentado suas decisões em um relatório curto no seu repositório.

## E5.1 Conceitos antes de começar

| Termo | O que é |
| :--- | :--- |
| **Desempenho acadêmico** | Como o estudante vai no curso, medido por notas, frequência, reprovações e progresso. |
| **Evasão** | Quando o estudante abandona o curso antes de concluir. |
| **Risco acadêmico** | Situação em que os indicadores sugerem que o estudante pode reprovar ou evadir, e que merece atenção. |
| **Regra de classificação** | Uma condição lógica que separa os registros em grupos, como "Alerta" e "Regular". |
| **Dados fictícios** | Dados criados por um programa, com aparência de reais, mas que não correspondem a nenhuma pessoa. São usados para praticar sem expor ninguém. |

**Como os encontros anteriores entram aqui:**

| Etapa do projeto | Encontro |
| :--- | :--- |
| Dividir o problema em partes investigáveis | E1 - Decomposição |
| Escolher só os dados necessários | E2 - Abstração |
| Criar e aplicar a regra de risco | E3 - Lógica (`for`, `if/else`) |
| Resumir e visualizar os resultados | E4 - pandas e Plotly |

---

## E5.2 O desafio

> **Pergunta:** quais fatores estão associados ao baixo desempenho acadêmico nos cursos de uma universidade, e quais estudantes deveriam receber acompanhamento?

Não existe uma única resposta certa. O objetivo é usar as ferramentas do Pensamento Computacional para **estruturar** a investigação e **justificar** cada decisão com os dados.

---

## E5.3 Preparação do ambiente e da base de dados

### Ambiente

Abra um terminal no **GitHub Codespaces** (no seu repositório: **Code → Codespaces → Create codespace on main**) ou no VS Code do seu computador, e prepare o projeto com o **uv**, o gerenciador de pacotes Python do curso:

```bash
curl -LsSf https://astral.sh/uv/install.sh | sh   # instala o uv, se ainda não tiver
uv init pc-e5 && cd pc-e5
uv add pandas plotly nbformat ipykernel
```

Crie o notebook `projeto.ipynb` na pasta `pc-e5` (**File → New File → Jupyter Notebook**) e, em **Select Kernel**, escolha o Python da `.venv`. As extensões **Python** e **Jupyter** precisam estar instaladas no VS Code.

### Base de dados

A base é gerada por um script, para que todos tenham exatamente os mesmos dados. Crie o arquivo `gerar_alunos.py` na pasta `pc-e5`:

```python
"""Gera uma base FICTÍCIA de alunos em alunos.csv. Nenhum dado corresponde a pessoas reais."""
import csv
import random

random.seed(42)  # mesma semente = mesmos dados para todo mundo

CURSOS = ["Relações Internacionais", "Direito", "Ciência da Computação", "Economia"]

with open("alunos.csv", "w", newline="", encoding="utf-8") as arquivo:
    escritor = csv.writer(arquivo)
    escritor.writerow([
        "id", "nome", "cpf", "email", "telefone", "curso", "semestre",
        "frequencia", "nota_media", "reprovacoes", "projeto_extensao", "trabalha",
    ])
    for i in range(1, 301):
        curso = random.choice(CURSOS)
        semestre = random.randint(1, 8)
        trabalha = random.random() < 0.4
        projeto = random.random() < 0.3

        frequencia = random.gauss(85, 10) - (8 if trabalha else 0)
        frequencia = round(min(100, max(30, frequencia)), 1)

        nota = random.gauss(6.8, 1.4)
        if frequencia < 75:
            nota -= 1.5
        if projeto:
            nota += 0.5
        if curso == "Ciência da Computação":
            nota -= 0.4
        nota = round(min(10, max(0, nota)), 1)

        reprovacoes = 0
        for _ in range(semestre):
            if random.random() < (0.25 if nota < 5 else 0.05):
                reprovacoes += 1

        escritor.writerow([
            i,
            f"Aluno {i:03d}",
            f"{random.randint(100, 999)}.{random.randint(100, 999)}.{random.randint(100, 999)}-{random.randint(10, 99)}",
            f"aluno{i:03d}@exemplo.edu",
            f"(12) 9{random.randint(1000, 9999)}-{random.randint(1000, 9999)}",
            curso, semestre, frequencia, nota, reprovacoes, projeto, trabalha,
        ])

print("alunos.csv criado com 300 registros fictícios.")
```

Execute:

```bash
uv run python gerar_alunos.py
```

> [!IMPORTANT]
> Os dados são **fictícios**, e o script cria de propósito algumas relações entre as variáveis (por exemplo, quem trabalha tende a ter menos frequência). Em uma análise real, você não sabe de antemão quais relações existem: é justamente isso que a análise investiga.

---

## E5.4 Etapa 1 - Decomposição

Em vez de atacar "desempenho acadêmico" como um bloco único, divida em perguntas menores, cada uma respondível com os dados:

```
Quais fatores se associam ao baixo desempenho?
├── Notas: como a nota média se distribui entre os cursos?
├── Frequência: alunos com menos de 75% têm notas menores?
├── Trajetória: as reprovações aumentam com o semestre?
├── Contexto: quem trabalha tem desempenho diferente?
└── Engajamento: quem participa de extensão tem notas diferentes?
```

**Sua vez:** escreva no notebook (célula de texto) a sua árvore de decomposição. Pode usar esta como base, mas adicione ou retire perguntas e justifique.

---

## E5.5 Etapa 2 - Abstração

Carregue a base e veja quais colunas existem:

```python
import pandas as pd
import plotly.express as px

df = pd.read_csv("alunos.csv")
df.head()
```

Algumas colunas não ajudam a responder nenhuma das perguntas da decomposição: **nome, CPF, e-mail e telefone**. Além de inúteis para a análise, são **dados pessoais** protegidos pela Lei Geral de Proteção de Dados (LGPD). A abstração aqui é também uma obrigação: descarte-as.

```python
COLUNAS_PESSOAIS = ["nome", "cpf", "email", "telefone"]
dados = df.drop(columns=COLUNAS_PESSOAIS)
dados.head()
```

O `id` foi mantido: ele não identifica ninguém sozinho e permite, se necessário, localizar o registro original de forma controlada.

**Sua vez:** liste em uma célula de texto as colunas mantidas e uma frase justificando cada uma.

---

## E5.6 Etapa 3 - Padrões e algoritmo

### A regra de risco

Uma regra simples para classificar um aluno como **"Alerta"**:

> Se a **frequência** for menor que 75% **ou** a **nota média** for menor que 5,0, o aluno está em **Alerta**. Caso contrário, está **Regular**.

O limite de 75% não foi escolhido ao acaso: é a frequência mínima exigida para aprovação na maioria das instituições brasileiras.

### Aplicando com `for` e `if/else` (E3)

```python
alunos = dados.to_dict("records")   # lista de dicionários, um por aluno

for aluno in alunos:
    if aluno["frequencia"] < 75 or aluno["nota_media"] < 5.0:
        aluno["situacao"] = "Alerta"
    else:
        aluno["situacao"] = "Regular"

dados = pd.DataFrame(alunos)
dados["situacao"].value_counts()
```

### A mesma regra no estilo do pandas

O pandas aplica uma condição a todas as linhas de uma vez, sem escrever o `for`. O resultado é o mesmo, e para bases grandes é muito mais rápido:

```python
alerta = (dados["frequencia"] < 75) | (dados["nota_media"] < 5.0)
dados["em_alerta"] = alerta
dados["em_alerta"].sum()   # quantos alunos em alerta
```

Os dois jeitos são o mesmo algoritmo: percorrer cada aluno e testar a condição. O `for` deixa a lógica explícita; a versão do pandas a executa em bloco.

---

## E5.7 Etapa 4 - Análise e gráficos

Responda cada pergunta da decomposição. Os blocos abaixo cobrem algumas; complete as demais.

### Notas por curso

```python
media_por_curso = dados.groupby("curso")["nota_media"].mean().round(2).reset_index()

px.bar(
    media_por_curso.sort_values("nota_media"),
    x="curso",
    y="nota_media",
    title="Nota média por curso",
    labels={"curso": "Curso", "nota_media": "Nota média"},
)
```

### Percentual de alunos em alerta por curso

A média de uma coluna booleana é a proporção de `True`. Multiplicando por 100, vira percentual:

```python
alerta_por_curso = (
    dados.groupby("curso")["em_alerta"].mean().mul(100).round(1).reset_index(name="pct_alerta")
)

px.bar(
    alerta_por_curso.sort_values("pct_alerta"),
    x="curso",
    y="pct_alerta",
    title="Percentual de alunos em alerta por curso",
    labels={"curso": "Curso", "pct_alerta": "% em alerta"},
)
```

> [!TIP]
> Compare **percentuais**, não quantidades absolutas. Um curso com mais alunos terá mais alunos em alerta mesmo que, proporcionalmente, esteja melhor.

### Frequência e nota

```python
px.scatter(
    dados,
    x="frequencia",
    y="nota_media",
    color="situacao",
    hover_data=["id", "curso"],
    title="Frequência x nota média",
)
```

### Trabalha ou não

```python
dados.groupby("trabalha").agg(
    alunos=("id", "count"),
    frequencia_media=("frequencia", "mean"),
    nota_media=("nota_media", "mean"),
    pct_alerta=("em_alerta", "mean"),
).round(2)
```

**Sua vez:** faça as análises de **reprovações por semestre** (dica: `px.line` ou `px.bar` com `groupby("semestre")`) e de **projeto de extensão**.

---

## E5.8 Cuidados com as conclusões

Antes de apresentar, revise sua análise com estas perguntas:

1. **Associação não é causa.** Se quem participa de extensão tem notas maiores, isso não prova que a extensão *causa* notas maiores: talvez alunos com notas altas procurem mais a extensão. Escreva "está associado a", não "causa".
2. **A regra é uma escolha.** Por que 5,0 e não 6,0? Mude os limites e veja quantos alunos mudam de grupo. Se o resultado muda muito, deixe isso claro.
3. **Classificar pessoas tem consequências.** Um rótulo de "Alerta" deve servir para **oferecer apoio**, nunca para punir ou excluir. Quem trabalha aparece mais em alerta: isso pede, por exemplo, horários flexíveis, não discriminação.
4. **Os dados não contam tudo.** Saúde, renda, transporte e motivação não estão na base e certamente influenciam o desempenho. Diga o que ficou de fora.

---

## E5.9 Entrega e apresentação

No seu repositório, em `pensamento-computacional/e5/`, envie:

- `gerar_alunos.py`;
- `projeto.ipynb`, com as saídas limpas (**Clear All Outputs**);
- `relatorio.md`, seguindo este modelo:

```markdown
# Projeto integrador - Desempenho acadêmico

## Pergunta
(a pergunta do desafio, com suas palavras)

## Decomposição
(sua árvore de perguntas)

## Abstração
(colunas mantidas e descartadas, com justificativa)

## Regra de risco
(a regra usada e por que esses limites)

## Resultados
(uma frase por pergunta da decomposição, com o gráfico ou número que a sustenta)

## Limitações
(o que a análise não permite concluir)

## Recomendação
(uma ação concreta que a universidade poderia tomar)
```

Na apresentação (5 a 10 minutos), percorra o relatório. Com mais de uma pessoa, compare as decomposições e as regras: o mesmo problema admite caminhos diferentes, e a comparação mostra o que cada escolha muda.

---

## E5.10 Autoavaliação final

Responda por escrito no final do `relatorio.md`, em uma seção **Reflexão**:

1. Qual pilar do Pensamento Computacional foi mais difícil de aplicar neste projeto? Por quê?
2. Em qual etapa você percebeu mais evolução desde o E1?
3. Onde você pode aplicar decomposição e abstração no seu projeto do laboratório?
4. Que habilidade técnica você quer desenvolver a seguir?

---

## Checklist do Encontro 5

- [ ] Gerei a base de dados e abri o notebook com o kernel da `.venv`
- [ ] Escrevi a árvore de decomposição do problema
- [ ] Descartei as colunas de dados pessoais e justifiquei as mantidas
- [ ] Apliquei a regra de risco com `for/if` e com o pandas
- [ ] Respondi todas as perguntas da decomposição com agrupamentos e gráficos
- [ ] Revisei as conclusões com os cuidados da seção E5.8
- [ ] Entreguei o notebook, o script e o relatório com a reflexão

## Links de apoio

- [pandas - introdução em 10 minutos](https://pandas.pydata.org/docs/user_guide/10min.html)
- [Plotly Express - guia](https://plotly.com/python/plotly-express/)
- [Lei Geral de Proteção de Dados (Lei nº 13.709/2018)](https://www.planalto.gov.br/ccivil_03/_ato2015-2018/2018/lei/l13709.htm)
