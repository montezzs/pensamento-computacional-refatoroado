# E3 - Lógica, Estruturas e Armazenamento (Python)

**Objetivo:** usar variáveis, repetições, condições, listas, conjuntos e dicionários para coletar, verificar e guardar dados.

**Ao final deste encontro você terá:** escrito e executado um pequeno programa em Python que coleta registros, descarta os inválidos e repetidos, trata erros e salva o resultado em um banco de dados simples.

## E3.1 Conceitos antes de começar

| Termo | O que é |
| :--- | :--- |
| **Lógica de programação** | Organizar instruções em uma ordem que o computador consiga executar para resolver um problema. É o pilar de *algoritmos* do Pensamento Computacional transformado em código. |
| **Variável** | Um nome que guarda um valor: `idade = 20`. |
| **Estrutura de repetição (loop)** | Instrução que repete um bloco de código: `while` e `for`. |
| **Estrutura condicional** | Instrução que escolhe o que fazer conforme uma condição: `if`, `elif`, `else`. |
| **Estrutura de dados** | Forma de organizar vários valores juntos: lista (`list`), conjunto (`set`) e dicionário (`dict`). |
| **Exceção (erro)** | Um problema que acontece durante a execução, como dividir por zero ou abrir um arquivo inexistente. |
| **JSON** | Formato de texto para guardar dados como pares de chave e valor. É legível por pessoas e por programas. |
| **TinyDB** | Biblioteca Python que funciona como um banco de dados simples, guardando tudo em um arquivo JSON. |

**Como o laboratório usa isso.** O trabalho com dados do projeto é dividido em três etapas, e cada grupo cuida de uma delas:

| Etapa | O que faz | Estruturas mais usadas |
| :--- | :--- | :--- |
| **Coleta** (Grupo 1) | Obtém os dados, página por página | `while`, `yield` |
| **Verificação** (Grupo 2) | Descarta registros inválidos ou fora do escopo | `for`, `if/else`, `continue`, `break` |
| **Armazenamento** (Grupo 3) | Guarda sem duplicar e sem perder dados se algo falhar | `set`, `dict`, `try/except`, TinyDB |

Este encontro segue essa ordem.

---

## E3.2 Preparação do ambiente

Você precisa de Python e de um terminal. A forma mais simples é o **GitHub Codespaces** (um computador na nuvem com editor e terminal, aberto pelo navegador): no seu repositório do GitHub, clique em **Code → Codespaces → Create codespace on main**.

No terminal, crie uma pasta para os exemplos e instale o TinyDB com o **uv** (o gerenciador de pacotes Python do curso):

```bash
curl -LsSf https://astral.sh/uv/install.sh | sh   # instala o uv, se ainda não tiver
uv init pc-e3 && cd pc-e3
uv add tinydb
```

Cada exemplo deste encontro pode ser salvo em um arquivo `.py` e executado com:

```bash
uv run python nome_do_arquivo.py
```

> No Windows sem Codespaces, instale o uv com `powershell -ExecutionPolicy ByPass -c "irm https://astral.sh/uv/install.ps1 | iex"` e use os mesmos comandos.

---

## E3.3 Variáveis e tipos

```python
nome = "Ana"          # texto (str)
idade = 20            # número inteiro (int)
nota = 8.5            # número com casas decimais (float)
aprovada = True       # verdadeiro ou falso (bool)

print(nome, "tem", idade, "anos")
print(type(nota))     # <class 'float'>
```

O tipo importa: `"20" + 1` gera erro, porque não dá para somar texto e número; `int("20") + 1` resulta em `21`.

---

## E3.4 Coleta: `while` e `yield`

### `while` - repetir enquanto uma condição for verdadeira

Use quando **não se sabe de antemão** quantas repetições serão necessárias.

*No cotidiano:* enquanto não encontrar uma vaga no estacionamento, você continua procurando.

```python
vagas = ["ocupada", "ocupada", "livre", "ocupada"]
posicao = 0

while vagas[posicao] != "livre":
    print("Vaga", posicao, "ocupada, seguindo...")
    posicao += 1

print("Estacionei na vaga", posicao)
```

> [!WARNING]
> Se a condição nunca ficar falsa, o `while` repete para sempre (*loop infinito*). Garanta que algo dentro dele mude a condição. Para interromper um programa travado no terminal, use `Ctrl+C`.

Na coleta, o `while` é usado para percorrer páginas de um site até não haver mais páginas, já que não sabemos quantas existem.

### `yield` - entregar um item de cada vez

Uma função com `yield` é um **gerador**: em vez de montar uma lista inteira e devolvê-la no final, ela entrega cada item assim que ele fica pronto, e pausa até o próximo ser pedido.

*No cotidiano:* na esteira do supermercado, o caixa registra cada produto que chega, sem esperar o carrinho esvaziar.

```python
def coletar_paginas(paginas):
    numero = 0
    while numero < len(paginas):
        print(f"Baixando página {numero + 1}...")
        for registro in paginas[numero]:
            yield registro          # entrega um registro e pausa aqui
        numero += 1


PAGINAS = [
    ["registro A", "registro B"],
    ["registro C"],
]

for registro in coletar_paginas(PAGINAS):
    print("Processando", registro)
```

Saída:

```
Baixando página 1...
Processando registro A
Processando registro B
Baixando página 2...
Processando registro C
```

Repare que o "Processando" começa antes de a página 2 ser baixada. Em uma coleta real, com milhares de páginas, isso economiza memória e permite começar a trabalhar com os dados imediatamente.

---

## E3.5 Verificação: `for`, `if/else`, `continue` e `break`

### `for` - percorrer cada elemento de uma sequência

*No cotidiano:* entregar um convite para cada amigo de uma lista.

```python
amigos = ["Ana", "Bruno", "Carla"]

for amigo in amigos:
    print("Entregando convite para", amigo)
```

### `if / elif / else` - tomar decisões

*No cotidiano:* se estiver chovendo, levar guarda-chuva; senão, óculos de sol.

```python
nota = 6.5

if nota >= 7:
    print("Aprovado")
elif nota >= 5:
    print("Recuperação")
else:
    print("Reprovado")
```

Operadores úteis em condições: `==` (igual), `!=` (diferente), `>`, `<`, `>=`, `<=`, `and`, `or`, `not`, e `in` (está contido).

### `continue` - pular para o próximo elemento

*No cotidiano:* ao corrigir provas, o professor pula as folhas em branco.

```python
provas = [8.0, None, 6.5, None, 9.0]

for nota in provas:
    if nota is None:
        continue            # ignora esta e vai para a próxima
    print("Corrigindo prova com nota", nota)
```

### `break` - interromper a repetição

*No cotidiano:* ao achar o livro na estante, você para de procurar.

```python
estante = ["Dom Casmurro", "O Cortiço", "Iracema", "Senhora"]

for livro in estante:
    if livro == "Iracema":
        print("Encontrei!")
        break               # sai do for imediatamente
    print("Não é", livro)
```

### Combinando tudo

Na verificação, essas estruturas aparecem juntas. Este trecho percorre registros coletados, pula os incompletos e para ao atingir um limite:

```python
registros = [
    {"titulo": "Notícia 1", "data": "2026-11-01"},
    {"titulo": "", "data": "2026-11-02"},          # sem título: inválido
    {"titulo": "Notícia 3", "data": "2026-11-03"},
    {"titulo": "Notícia 4", "data": "2026-11-04"},
]

LIMITE = 2
validos = []

for registro in registros:
    if not registro["titulo"]:
        print("Ignorando registro sem título")
        continue
    validos.append(registro)
    if len(validos) == LIMITE:
        print("Limite atingido")
        break

print(validos)
```

---

## E3.6 Estruturas de dados: `list`, `set` e `dict`

| Estrutura | Característica | Cotidiano | Código |
| :--- | :--- | :--- | :--- |
| **Lista** (`list`) | Mantém a ordem e aceita repetidos | Lista de compras | `compras = ["arroz", "feijão", "arroz"]` |
| **Conjunto** (`set`) | Não aceita repetidos e não garante ordem | Participantes de um sorteio: quem se inscreve duas vezes aparece uma só | `inscritos = {"Ana", "Bruno", "Ana"}` → `{"Ana", "Bruno"}` |
| **Dicionário** (`dict`) | Associa uma chave a um valor | Agenda telefônica: nome → número | `agenda = {"Ana": "9999-0000"}` |

```python
# Lista: acessar por posição (começa em 0) e adicionar
compras = ["arroz", "feijão"]
compras.append("café")
print(compras[0])            # arroz

# Conjunto: remover repetidos e testar se algo já existe
inscritos = set()
for nome in ["Ana", "Bruno", "Ana", "Carla"]:
    inscritos.add(nome)
print(len(inscritos))        # 3
print("Ana" in inscritos)    # True (e essa verificação é muito rápida)

# Dicionário: acessar e alterar por chave
aluno = {"nome": "Ana", "curso": "Direito", "nota": 8.5}
print(aluno["curso"])        # Direito
aluno["nota"] = 9.0
print(aluno.get("email", "sem e-mail"))   # valor padrão se a chave não existir
```

Um registro coletado costuma ser um **dicionário**, e um conjunto de registros, uma **lista de dicionários**, exatamente como no exemplo de verificação da seção anterior.

---

## E3.7 O plano B: `try`, `except` e `finally`

Nem tudo sai como esperado: a internet cai, o arquivo não existe, o usuário digita texto onde se esperava um número. Sem tratamento, o programa para com um erro.

*No cotidiano:* tentar pagar com cartão e, se a maquininha falhar, pagar por PIX.

Este programa quebra se a pessoa digitar `vinte`:

```python
idade = int(input("Digite sua idade: "))
print("Você tem", idade, "anos")
```

```
ValueError: invalid literal for int() with base 10: 'vinte'
```

Com `try` e `except`, o erro é tratado:

```python
try:
    idade = int(input("Digite sua idade: "))
    print("Você tem", idade, "anos")
except ValueError:
    print("Erro: digite apenas números")
finally:
    print("Fim da leitura")   # executa sempre, com ou sem erro
```

- `try`: o código que pode falhar;
- `except TipoDoErro`: o que fazer se aquele erro acontecer;
- `finally` (opcional): o que fazer sempre, como fechar um arquivo ou registrar que a etapa terminou.

> [!TIP]
> Capture erros específicos (`ValueError`, `FileNotFoundError`). Um `except` sem tipo, ou `except Exception`, esconde erros que você não previu e dificulta descobrir o que deu errado.

---

## E3.8 Armazenamento seguro: verificar antes de guardar

Antes de salvar um registro, verifique se ele já existe. Isso evita duplicatas, que distorcem qualquer análise posterior (uma notícia contada duas vezes vira "duas notícias").

*No cotidiano:* antes de matricular um aluno, a secretaria confere se a matrícula já existe.

```python
ja_salvos = set()
banco = []

for registro in [{"id": 1}, {"id": 2}, {"id": 1}]:
    if registro["id"] in ja_salvos:
        print("Registro", registro["id"], "já existe, ignorando")
        continue
    banco.append(registro)
    ja_salvos.add(registro["id"])

print(banco)   # [{'id': 1}, {'id': 2}]
```

Para isso, cada registro precisa de um **identificador único**: um código, um link ou outra informação que não se repita.

---

## E3.9 JSON e TinyDB

### JSON

Um arquivo JSON guarda dados em texto, com a mesma estrutura de listas e dicionários do Python:

```json
[
  {"id": 1, "titulo": "Notícia 1", "data": "2026-11-01"},
  {"id": 2, "titulo": "Notícia 3", "data": "2026-11-03"}
]
```

Ler e escrever JSON em Python:

```python
import json

dados = [{"id": 1, "titulo": "Notícia 1"}]

with open("dados.json", "w", encoding="utf-8") as arquivo:
    json.dump(dados, arquivo, ensure_ascii=False, indent=2)

with open("dados.json", encoding="utf-8") as arquivo:
    carregados = json.load(arquivo)

print(carregados)
```

### TinyDB

O TinyDB usa um arquivo JSON como banco de dados e oferece buscas prontas, sem que você precise ler e reescrever o arquivo inteiro à mão. É indicado para projetos pequenos; para muitos dados ou muitos acessos simultâneos, usa-se um banco de dados completo.

```python
from tinydb import TinyDB, Query

db = TinyDB("banco.json")
Registro = Query()

db.insert({"id": 1, "titulo": "Notícia 1"})

print(db.contains(Registro.id == 1))          # True
print(db.search(Registro.titulo == "Notícia 1"))
print(len(db))                                 # quantos registros existem
```

---

## E3.10 O programa completo

Este programa junta todas as etapas: **coleta** com `while` e `yield`, **verificação** com `for`, `if`, `continue` e `break`, e **armazenamento** sem duplicatas e com tratamento de erros. Salve como `pipeline.py` e execute com `uv run python pipeline.py`.

```python
from tinydb import TinyDB, Query

# Simula as páginas de um site. Na vida real, cada página viria da internet (E6).
PAGINAS = [
    [
        {"id": 1, "titulo": "Chuva forte no litoral", "categoria": "clima"},
        {"id": 2, "titulo": "", "categoria": "política"},              # sem título
        {"id": 3, "titulo": "Nova linha de ônibus", "categoria": "cidade"},
    ],
    [
        {"id": 3, "titulo": "Nova linha de ônibus", "categoria": "cidade"},  # repetido
        {"id": 4, "titulo": "Feira de ciências", "categoria": "educação"},
        {"id": 5, "titulo": "Previsão do fim de semana", "categoria": "clima"},
    ],
]

LIMITE = 10


def coletar(paginas):
    """Coleta: entrega um registro de cada vez, página por página."""
    numero = 0
    while numero < len(paginas):
        for registro in paginas[numero]:
            yield registro
        numero += 1


def salvar(db, registro):
    """Armazenamento: só insere se o id ainda não existir."""
    Registro = Query()
    if db.contains(Registro.id == registro["id"]):
        return False
    db.insert(registro)
    return True


def main():
    db = TinyDB("noticias.json")
    salvos = 0
    try:
        for registro in coletar(PAGINAS):
            # Verificação
            if not registro["titulo"]:
                print(f"[descartado] id {registro['id']}: sem título")
                continue
            if salvar(db, registro):
                salvos += 1
                print(f"[salvo] id {registro['id']}: {registro['titulo']}")
            else:
                print(f"[repetido] id {registro['id']}")
            if salvos >= LIMITE:
                print("Limite atingido")
                break
    except KeyError as erro:
        print("Registro sem o campo esperado:", erro)
    finally:
        print(f"Total no banco: {len(db)} registros")
        db.close()


if __name__ == "__main__":
    main()
```

Rode o programa **duas vezes**. Na segunda, todos os registros aparecem como `[repetido]`, porque já estão no `noticias.json`. Abra esse arquivo no editor para ver como o TinyDB guarda os dados.

---

## E3.11 Atividade - Lendo um script

**Formato:** individual ou em dupla. **Tempo:** 15 min.

Leia o código abaixo **sem executá-lo** e responda às perguntas.

```python
emails = ["ana@exemplo.com", "bruno-sem-arroba", "carla@exemplo.com", "ana@exemplo.com", "fim", "davi@exemplo.com"]

validos = set()

for email in emails:
    if email == "fim":
        break
    if "@" not in email:
        continue
    validos.add(email)

print(sorted(validos))
```

1. Onde há repetição? Qual tipo?
2. Onde há tomada de decisão?
3. Qual estrutura de dados guarda o resultado, e por que ela foi escolhida?
4. O que o programa imprime?
5. Qual é o objetivo deste trecho, em uma frase?

<details>
<summary>Respostas</summary>

1. No `for`, que percorre a lista `emails`.
2. Nos dois `if`: um encerra a repetição ao encontrar `"fim"`, o outro pula endereços sem `@`.
3. Um conjunto (`set`), porque ele descarta automaticamente o e-mail repetido `ana@exemplo.com`.
4. `['ana@exemplo.com', 'carla@exemplo.com']`. O `davi@exemplo.com` não entra porque vem depois de `"fim"`, e `bruno-sem-arroba` é pulado.
5. Extrair os e-mails válidos e únicos de uma lista, até um marcador de fim.
</details>

Depois, execute o código e confira. Por fim, altere-o para que, em vez de parar no `"fim"`, ele apenas ignore essa palavra.

---

## E3.12 Autoavaliação

**1. Você quer repetir uma ação até o usuário digitar "sair", sem saber quantas vezes isso vai levar. `for` ou `while`?**

<details>
<summary>Resposta</summary>

`while`, porque o número de repetições não é conhecido de antemão.
</details>

**2. Qual a diferença entre `continue` e `break`?**

<details>
<summary>Resposta</summary>

`continue` pula apenas o elemento atual e segue para o próximo; `break` encerra a repetição inteira.
</details>

**3. Qual estrutura você usaria para guardar os links já visitados por um robô de coleta, de modo a nunca visitar o mesmo duas vezes?**

<details>
<summary>Resposta</summary>

Um conjunto (`set`): não guarda repetidos e verifica muito rápido se um item já existe.
</details>

**4. O que o `finally` garante?**

<details>
<summary>Resposta</summary>

Que o bloco seja executado sempre, tenha ocorrido erro ou não. É usado para fechar arquivos e conexões ou registrar o fim de uma etapa.
</details>

**5. Por que usar `yield` em vez de devolver uma lista com tudo no final?**

<details>
<summary>Resposta</summary>

Porque o gerador entrega cada item assim que ele está pronto, sem precisar guardar todos na memória, e o processamento pode começar antes de a coleta terminar.
</details>

---

## Tarefa da semana

Modifique o `pipeline.py` da seção E3.10:

1. adicione à verificação uma regra que descarte as notícias da categoria `"política"`;
2. guarde em um conjunto as categorias encontradas e imprima-as no final;
3. conte quantos registros foram descartados por cada motivo (sem título, categoria, repetido) usando um dicionário, e imprima esse resumo no final.

Envie o arquivo modificado para o seu repositório em `pensamento-computacional/e3/pipeline.py`. Não envie o `noticias.json`: ele é gerado pelo programa.

---

## Checklist do Encontro 3

- [ ] Preparei o ambiente e executei um script com `uv run`
- [ ] Sei usar `while` e explicar o que faz o `yield`
- [ ] Sei usar `for`, `if/elif/else`, `continue` e `break`, inclusive combinados
- [ ] Sei quando usar lista, conjunto e dicionário
- [ ] Sei tratar erros com `try/except/finally`
- [ ] Salvei e consultei dados com JSON e TinyDB
- [ ] Executei o programa completo e entendi cada etapa
- [ ] Fiz a leitura de script e a autoavaliação
- [ ] Entreguei a tarefa no meu repositório

## Links de apoio

- [Tutorial oficial do Python em português - controle de fluxo](https://docs.python.org/pt-br/3/tutorial/controlflow.html)
- [Tutorial oficial - estruturas de dados](https://docs.python.org/pt-br/3/tutorial/datastructures.html)
- [Tutorial oficial - erros e exceções](https://docs.python.org/pt-br/3/tutorial/errors.html)
- [Documentação do TinyDB](https://tinydb.readthedocs.io/)
