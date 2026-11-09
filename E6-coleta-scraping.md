# E6 — Estratégias de Coleta e Scraping

**Objetivo:** entender como os dados são obtidos automaticamente da web, com e sem programação, e quando usar cada estratégia.

**Ao final deste encontro você terá:** inspecionado um site com as ferramentas do navegador, coletado dados com uma extensão sem código e com Python pelas três estratégias de captura, e salvo o resultado em uma tabela.

---

## Roteiro do encontro

| Bloco | Duração | Conteúdo |
| :--- | :--- | :--- |
| 1. Como a web funciona e ética | 20 min | Conceitos, o fluxo de um robô e cuidados legais (E6.1 a E6.3) |
| 2. Inspeção e no-code | 20 min | Ferramentas do navegador e extensões (E6.4 e E6.5) |
| 3. As três estratégias em código | 40 min | API, metadados e conteúdo da página, páginas dinâmicas (E6.6 a E6.10) |
| 4. Fechamento | 10 min | Escolha da ferramenta, autoavaliação e tarefa (E6.11 e E6.12) |

---

## E6.1 Conceitos antes de começar

| Termo | O que é |
| :--- | :--- |
| **Web scraping (raspagem)** | Usar um programa para acessar páginas da web e extrair informações automaticamente, em vez de copiá-las à mão. |
| **HTML** | A linguagem que descreve a estrutura de uma página: títulos, parágrafos, links, tabelas. É o que o navegador recebe e desenha na tela. |
| **Tag** | Cada elemento do HTML, escrito entre `< >`: `<h1>` (título), `<p>` (parágrafo), `<a>` (link), `<meta>` (metadado). |
| **Requisição (request)** | O pedido que o navegador, ou o seu programa, faz a um servidor para receber uma página ou dados. |
| **Código de status** | O número que o servidor devolve dizendo o resultado do pedido: `200` (deu certo), `404` (não encontrado), `403` (acesso negado), `429` (pedidos demais), `500` (erro no servidor). |
| **API** | Um "canal de dados" que o site oferece para programas, devolvendo informação já organizada, normalmente em JSON, sem o visual da página. |
| **Seletor CSS** | Um endereço que localiza elementos no HTML. `div.quote` significa "toda `div` com a classe `quote`". |
| **Página estática / dinâmica** | Na estática, os dados já vêm no HTML. Na dinâmica, o HTML chega quase vazio e um código JavaScript busca e desenha os dados depois, no navegador. |

---

## E6.2 O fluxo de um robô de coleta

Quase toda coleta segue quatro passos:

```
Acessar a URL → Baixar o conteúdo → Filtrar o que interessa → Salvar
```

Repare que é o mesmo fluxo do E3 (coletar, verificar, armazenar), agora com dados vindos da internet. O passo de **filtrar** é a abstração do E2: de toda a página, guardar só o essencial.

---

## E6.3 Ética e legalidade

Antes de coletar dados de qualquer site, verifique:

1. **Termos de uso.** Muitos sites proíbem a coleta automatizada. Respeite.
2. **`robots.txt`.** Arquivo na raiz do site (ex.: `https://site.com/robots.txt`) que indica quais páginas robôs não devem acessar.
3. **Ritmo.** Faça poucas requisições por segundo e espere entre elas. Um robô rápido demais sobrecarrega o servidor e pode ser bloqueado.
4. **Dados pessoais.** Nomes, e-mails e outras informações de pessoas são protegidos pela Lei Geral de Proteção de Dados (LGPD), mesmo quando estão públicos. Colete apenas o necessário para o objetivo da pesquisa.
5. **Prefira a API.** Se o site oferece uma API oficial, use-a: é mais estável e é o canal que o site escolheu para compartilhar dados.

> [!NOTE]
> Os exemplos deste encontro usam [quotes.toscrape.com](https://quotes.toscrape.com) e [books.toscrape.com](https://books.toscrape.com), sites criados especificamente para praticar raspagem.

---

## E6.4 Inspecionando um site pelo navegador

As **ferramentas de desenvolvedor** (DevTools) do navegador mostram o HTML de qualquer página e todas as requisições que ela faz. Funcionam no Chrome, Edge e Firefox.

1. Abra [quotes.toscrape.com](https://quotes.toscrape.com).
2. Pressione `F12` (ou clique com o botão direito em uma citação → **Inspecionar**).
3. **Aba Elements:** mostra o HTML. Passe o mouse pelas linhas e veja a parte correspondente da página se destacar. Repare que cada citação está dentro de `<div class="quote">`, o texto em `<span class="text">` e o autor em `<small class="author">`.
4. **Aba Network:** mostra as requisições. Ative o filtro **Fetch/XHR** (requisições feitas por JavaScript), recarregue a página e observe. Depois, abra [quotes.toscrape.com/scroll](https://quotes.toscrape.com/scroll), role até o fim e veja aparecerem requisições para `api/quotes?page=2`, `page=3`... Clique em uma delas e abra a aba **Response** (ou **Preview**): são os dados em JSON. Essa é a API que a página usa por trás.

---

## E6.5 Coleta sem código (no-code)

Extensões de navegador permitem coletar dados apontando e clicando. São ótimas para coletas pontuais e para explorar um site antes de escrever código.

| Extensão | Para quê |
| :--- | :--- |
| **Easy Scraper** | Detecta automaticamente listas e tabelas na página e exporta para CSV ou JSON. Bom para coletas rápidas de uma página. |
| **Web Scraper** (webscraper.io) | Você monta visualmente um "mapa" do site (quais páginas visitar e quais elementos coletar em cada uma). Bom para percorrer várias páginas sem programar. |

Ambas estão na Chrome Web Store (também funcionam no Edge).

**Experimente:** instale o Easy Scraper, abra [books.toscrape.com](https://books.toscrape.com), clique no ícone da extensão, escolha a lista de livros detectada e exporte em CSV.

**Limites do no-code:** é difícil repetir a coleta todos os dias de forma automática, tratar erros e combinar com outras etapas de análise. Para isso, usamos código.

---

## E6.6 Preparação do ambiente

Abra um terminal no **GitHub Codespaces** (no seu repositório: **Code → Codespaces → Create codespace on main**) ou no VS Code do seu computador, e prepare o projeto com o **uv**, o gerenciador de pacotes Python do curso:

```bash
curl -LsSf https://astral.sh/uv/install.sh | sh   # instala o uv, se ainda não tiver
uv init pc-e6 && cd pc-e6
uv add httpx beautifulsoup4 pandas playwright
uv run playwright install chromium
```

| Biblioteca | Para quê |
| :--- | :--- |
| `httpx` | Fazer requisições e baixar páginas ou dados de APIs |
| `beautifulsoup4` | Ler o HTML e localizar elementos com seletores |
| `pandas` | Organizar o resultado em tabela e salvar em CSV |
| `playwright` | Controlar um navegador de verdade, para páginas dinâmicas |

> [!NOTE]
> No Codespaces ou no Linux, se o Playwright reclamar de bibliotecas do sistema ausentes, use `uv run playwright install --with-deps chromium`.

Salve cada exemplo em um arquivo `.py` e execute com `uv run python nome_do_arquivo.py`.

---

## E6.7 Primeiro passo: baixar uma página

```python
import httpx

url = "https://quotes.toscrape.com"
resposta = httpx.get(url, timeout=10)

if resposta.status_code == 200:
    print(resposta.text[:500])   # os primeiros 500 caracteres do HTML
else:
    print(f"Erro: {resposta.status_code}")
```

O `timeout=10` faz o programa desistir após 10 segundos sem resposta, em vez de esperar para sempre. O que aparece é o mesmo HTML da aba **Elements**.

---

## E6.8 As três estratégias de captura

Antes de coletar, descubra **onde** os dados estão. Teste nesta ordem, da mais simples e confiável para a mais trabalhosa:

| Estratégia | Onde estão os dados | Quando usar |
| :--- | :--- | :--- |
| **1. API (aba Network)** | Em requisições JSON que a página faz por trás | Sempre que existir: dados já organizados, sem depender do visual |
| **2. Metadados (`<head>`)** | Nas tags `<meta>` do cabeçalho do HTML | Quando basta título, descrição, data ou imagem principal |
| **3. Conteúdo (`<body>`)** | No HTML visível da página | Quando não há API e os metadados não bastam |

### Estratégia 1 — API

A página `/scroll` usa a API `https://quotes.toscrape.com/api/quotes?page=N`, que devolve JSON com as citações e um campo `has_next` dizendo se existe próxima página. Como não sabemos quantas páginas há, usamos um `while` (E3):

```python
import time

import httpx
import pandas as pd

URL_API = "https://quotes.toscrape.com/api/quotes"

citacoes = []
pagina = 1

with httpx.Client(timeout=10) as cliente:
    while True:
        resposta = cliente.get(URL_API, params={"page": pagina})
        resposta.raise_for_status()          # gera erro se o status não for de sucesso
        dados = resposta.json()

        for item in dados["quotes"]:
            citacoes.append({
                "texto": item["text"],
                "autor": item["author"]["name"],
                "tags": ", ".join(item["tags"]),
            })

        print(f"Página {pagina}: {len(dados['quotes'])} citações")
        if not dados["has_next"]:
            break
        pagina += 1
        time.sleep(1)                        # pausa de 1 segundo entre requisições

df = pd.DataFrame(citacoes)
df.to_csv("citacoes_api.csv", index=False)
print(f"{len(df)} citações salvas em citacoes_api.csv")
```

### Estratégia 2 — Metadados

O cabeçalho (`<head>`) do HTML guarda informações sobre a página que não aparecem no visual, como o título da aba e a descrição usada por buscadores e redes sociais.

```python
import httpx
from bs4 import BeautifulSoup

url = "https://books.toscrape.com/catalogue/a-light-in-the-attic_1000/index.html"
html = httpx.get(url, timeout=10).text
sopa = BeautifulSoup(html, "html.parser")

titulo = sopa.title.get_text(strip=True) if sopa.title else None
meta_descricao = sopa.find("meta", attrs={"name": "description"})
descricao = meta_descricao["content"].strip() if meta_descricao else None

print("Título:", titulo)
print("Descrição:", descricao[:200] if descricao else "não encontrada")
```

Sempre verifique se a tag existe (`if meta_descricao`) antes de usá-la: nem toda página tem todos os metadados, e acessar algo inexistente gera erro.

### Estratégia 3 — Conteúdo da página

Usamos os seletores que identificamos na aba **Elements** (E6.4):

```python
import httpx
import pandas as pd
from bs4 import BeautifulSoup

html = httpx.get("https://quotes.toscrape.com", timeout=10).text
sopa = BeautifulSoup(html, "html.parser")

citacoes = []
for bloco in sopa.select("div.quote"):
    citacoes.append({
        "texto": bloco.select_one("span.text").get_text(strip=True),
        "autor": bloco.select_one("small.author").get_text(strip=True),
        "tags": ", ".join(tag.get_text() for tag in bloco.select("a.tag")),
    })

df = pd.DataFrame(citacoes)
print(df.head())
df.to_csv("citacoes_html.csv", index=False)
```

- `select("div.quote")` devolve **todos** os elementos que combinam com o seletor;
- `select_one(...)` devolve **o primeiro**;
- `get_text(strip=True)` extrai o texto sem espaços sobrando.

> [!WARNING]
> Essa estratégia depende da estrutura visual do site. Se o site mudar o nome de uma classe, o seletor para de funcionar. Por isso a API, quando existe, é preferível.

---

## E6.9 Páginas dinâmicas: quando o httpx não basta

Abra [quotes.toscrape.com/js](https://quotes.toscrape.com/js/). Visualmente, é igual à página inicial. Agora tente a estratégia 3 nela:

```python
import httpx
from bs4 import BeautifulSoup

html = httpx.get("https://quotes.toscrape.com/js/", timeout=10).text
sopa = BeautifulSoup(html, "html.parser")
print(len(sopa.select("div.quote")))   # 0
```

O resultado é **0**: o HTML recebido não tem as citações. Elas são desenhadas por JavaScript no navegador, e o `httpx` não executa JavaScript. A solução é controlar um navegador de verdade com o **Playwright**:

```python
from playwright.sync_api import sync_playwright

with sync_playwright() as p:
    navegador = p.chromium.launch()          # abre o navegador sem janela visível
    pagina = navegador.new_page()
    pagina.goto("https://quotes.toscrape.com/js/")
    pagina.wait_for_selector("div.quote")    # espera o JavaScript desenhar as citações

    for bloco in pagina.locator("div.quote").all():
        texto = bloco.locator("span.text").inner_text()
        autor = bloco.locator("small.author").inner_text()
        print(f"{autor}: {texto[:60]}")

    navegador.close()
```

> [!NOTE]
> Execute este exemplo como arquivo `.py`, não em um notebook: a versão "sync" do Playwright não funciona dentro do Jupyter.

O Playwright também clica em botões, preenche formulários e rola a página. O **Selenium** é uma alternativa mais antiga, com o mesmo propósito. Ambos são bem mais lentos que o `httpx`, então use-os só quando necessário, e lembre-se de verificar antes, na aba Network, se a página dinâmica não usa uma API que você possa acessar direto (estratégia 1).

---

## E6.10 Qual ferramenta usar

```
Os dados vêm de uma API (aba Network → Fetch/XHR)?
├── Sim → httpx + JSON (estratégia 1)
└── Não → Os dados estão no HTML baixado pelo httpx?
          ├── Sim → httpx + BeautifulSoup (estratégias 2 e 3)
          └── Não (página dinâmica) → Playwright ou Selenium
```

| Situação | Ferramenta |
| :--- | :--- |
| Coleta pontual, uma página, sem programar | Easy Scraper |
| Várias páginas, sem programar | Web Scraper |
| Dados em API | `httpx` |
| HTML estático | `httpx` + BeautifulSoup |
| Página dinâmica, cliques, formulários | Playwright (ou Selenium) |
| Coletas grandes, muitas páginas, com agendamento | Scrapy (framework completo de raspagem) |

---

## E6.11 Autoavaliação

**1. Na aba Network, você encontrou uma requisição que devolve os dados em JSON. Qual estratégia usar?**

<details>
<summary>Resposta</summary>

A estratégia 1 (API), com `httpx`: os dados já vêm organizados e a coleta não depende do visual da página.
</details>

**2. O `httpx` baixou a página, mas `sopa.select("div.produto")` retornou uma lista vazia, embora você veja os produtos no navegador. O que provavelmente está acontecendo?**

<details>
<summary>Resposta</summary>

A página é dinâmica: os produtos são carregados por JavaScript depois que o HTML chega. Verifique se há uma API na aba Network ou use o Playwright.
</details>

**3. O que significa o código de status 429?**

<details>
<summary>Resposta</summary>

Pedidos demais: o servidor está limitando o seu robô. Diminua o ritmo e aumente as pausas entre requisições.
</details>

**4. Para que serve o arquivo `robots.txt`?**

<details>
<summary>Resposta</summary>

Ele indica quais partes do site robôs não devem acessar. Respeitá-lo faz parte da coleta ética.
</details>

**5. Por que usar `time.sleep()` entre requisições?**

<details>
<summary>Resposta</summary>

Para não sobrecarregar o servidor nem ser bloqueado. Uma coleta responsável faz poucas requisições por segundo.
</details>

---

## Tarefa da semana

1. **No-code:** com o Easy Scraper, exporte a lista de livros da primeira página de [books.toscrape.com](https://books.toscrape.com) em CSV.
2. **Inspeção:** abra a aba Network em [quotes.toscrape.com/scroll](https://quotes.toscrape.com/scroll), role a página e anote a URL da API e os campos de uma citação no JSON.
3. **Código:** escreva `coleta_livros.py`, que colete título e preço dos livros das **3 primeiras páginas** de books.toscrape.com com `httpx` e BeautifulSoup e salve em `livros.csv`.
   - Dica: as páginas seguem o padrão `https://books.toscrape.com/catalogue/page-1.html`, `page-2.html`...; cada livro está em `article.product_pod`, o título no atributo `title` de `h3 a` e o preço em `p.price_color`.
   - Use `time.sleep(1)` entre as páginas e trate o caso de status diferente de 200.
4. **Integração com o E4:** carregue o `livros.csv` em um DataFrame e responda: qual o preço médio dos livros coletados?

Envie os arquivos para o seu repositório em `pensamento-computacional/e6/`, com um `README.md` curto contando o que você anotou na inspeção (item 2).

---

## Checklist do Encontro 6

> [!NOTE]
> No GitHub, as caixas de seleção de um arquivo `.md` não são clicáveis. Edite o arquivo trocando `[ ]` por `[x]`, ou copie a lista para uma **Issue** do seu repositório.

- [ ] Sei explicar o que é web scraping e o fluxo de um robô de coleta
- [ ] Conheço os cuidados éticos e legais (termos, `robots.txt`, ritmo, LGPD)
- [ ] Inspecionei HTML e requisições pelas ferramentas do navegador
- [ ] Coletei dados com uma extensão no-code
- [ ] Coletei dados pelas três estratégias: API, metadados e conteúdo
- [ ] Entendi a diferença entre página estática e dinâmica e usei o Playwright
- [ ] Sei escolher a ferramenta certa para cada situação
- [ ] Fiz a autoavaliação
- [ ] Entreguei a tarefa no meu repositório

## Links de apoio

- [quotes.toscrape.com](https://quotes.toscrape.com) e [books.toscrape.com](https://books.toscrape.com) — sites para praticar
- [Documentação do HTTPX](https://www.python-httpx.org/)
- [Documentação do Beautiful Soup](https://www.crummy.com/software/BeautifulSoup/bs4/doc/)
- [Playwright para Python](https://playwright.dev/python/docs/intro)
- [Ferramentas de desenvolvedor do Chrome — painel Network](https://developer.chrome.com/docs/devtools/network)
