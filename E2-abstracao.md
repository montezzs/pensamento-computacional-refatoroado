# E2 - Abstração e Identificação do Essencial

**Objetivo:** separar o que é relevante do que é ruído em um problema.

**Ao final deste encontro você terá:** aplicado a abstração em textos, tabelas de dados e código, e mostrado que a mesma informação pode ser essencial ou irrelevante dependendo do objetivo.

## E2.1 Conceitos antes de começar

| Termo | O que é |
| :--- | :--- |
| **Abstração** | Concentrar-se apenas nas informações necessárias para um objetivo, ignorando de propósito os detalhes que não ajudam. |
| **Ruído** | Informação que está presente, mas não ajuda a atingir o objetivo, e só aumenta a complexidade. |
| **Objetivo** | A pergunta que você quer responder. É ele que decide o que é essencial e o que é ruído. |
| **Coluna / variável** | Em uma tabela de dados, cada coluna guarda um tipo de informação (nome, cidade, valor). Escolher colunas é uma forma de abstração. |
| **Função** | Em programação, um bloco de código com nome, que recebe valores (entrada) e devolve um resultado (saída). Quem usa a função não precisa saber como ela funciona por dentro. |

---

## E2.2 O que é abstração

Abstrair não é perder informação: é **escolher** a informação certa para um objetivo. A pergunta que guia a abstração é:

> "O que eu realmente preciso saber para resolver **este** problema?"

Tudo o que não ajuda a responder pode ser deixado de lado, **naquele momento**. Se o objetivo mudar, o que era ruído pode virar essencial.

### Exemplo: comprar um celular

Há dezenas de informações disponíveis: cor, formato da câmera, embalagem, propaganda, processador, memória, armazenamento, bateria.

- Se o objetivo é **desempenho**, o essencial é processador, memória, armazenamento e bateria.
- Se o objetivo é **dar de presente para alguém que gosta de fotografia**, a câmera passa a ser essencial e a cor pode importar.

A lista de informações é a mesma; o objetivo mudou, e com ele o que é essencial.

---

## E2.3 Abstração em textos

Uma página de notícia contém propagandas, menus, comentários, vídeos e links para outras notícias. Se o objetivo é entender o que aconteceu, o essencial é:

- título;
- autor;
- data;
- texto principal.

Essa mesma seleção é a base da coleta automática de dados (E6): um programa de coleta precisa saber exatamente quais partes da página guardar e quais ignorar.

---

## E2.4 Abstração em dados

Considere esta tabela de compras:

| Nome | CPF | Telefone | Produto | Cidade | Cor favorita | Valor da compra |
| :--- | :--- | :--- | :--- | :--- | :--- | ---: |
| Ana | 111.111.111-11 | (11) 9999-0000 | Fone | Campinas | Azul | 120,00 |
| Bruno | 222.222.222-22 | (12) 9888-1111 | Teclado | Guaratinguetá | Verde | 250,00 |
| Carla | 333.333.333-33 | (11) 9777-2222 | Fone | Campinas | Azul | 120,00 |

**Pergunta:** qual cidade gera o maior faturamento?

Só são necessárias as colunas **Cidade** e **Valor da compra**. Nome, CPF, telefone, produto e cor favorita não mudam a resposta.

Duas consequências práticas:

- **Análises mais simples:** menos colunas, menos chance de erro.
- **Proteção de dados pessoais:** CPF, nome e telefone são *dados pessoais* protegidos pela Lei Geral de Proteção de Dados (LGPD). Se eles não são necessários para a pergunta, o correto é não usá-los e, sempre que possível, nem coletá-los. Aqui a abstração também é uma responsabilidade ética e legal.

Em código, com a biblioteca pandas (vista no E4), essa abstração é uma linha:

```python
compras_essencial = compras[["Cidade", "Valor da compra"]]
```

---

## E2.5 Abstração no código

Na programação, abstração significa **esconder os detalhes de como algo funciona** e expor apenas o necessário para usá-lo.

```python
def calcular_media(notas):
    soma = sum(notas)
    return soma / len(notas)


print(calcular_media([7.5, 8.0, 6.0]))  # 7.166...
```

Quem usa `calcular_media` precisa conhecer apenas:

- **Entrada:** uma lista de notas;
- **Saída:** a média das notas.

O cálculo interno fica escondido. Você usa abstrações assim o tempo todo: ao chamar `print()` ou `sum()`, não precisa saber como o Python escreve na tela ou soma os números.

> [!WARNING]
> Abstrair também é decidir o que a função **não** trata. `calcular_media([])` gera um erro de divisão por zero, porque a função assume que sempre haverá pelo menos uma nota. Uma boa abstração deixa claro o que ela espera receber. O tratamento desse tipo de erro é tema do E3.

---

## E2.6 Atividade - Filtro de ruído

**Formato:** individual. **Tempo:** 15 min.

Abaixo está uma ficha de cadastro de um voluntário em um evento, com muitas informações misturadas:

| Campo | Valor |
| :--- | :--- |
| Nome completo | Mariana Souza Lima |
| Apelido | Mari |
| CPF | 444.444.444-44 |
| Data de nascimento | 12/03/2001 |
| Signo | Peixes |
| Telefone | (12) 98888-7777 |
| Cor favorita | Laranja |
| Tamanho de camiseta | M |
| Restrição alimentar | Vegetariana |
| Disponibilidade | Sábado, 8h às 14h |
| Experiência anterior | Recepção em 2 eventos |
| Time de futebol | Corinthians |
| Contato de emergência | (12) 97777-6666 |

Para cada objetivo abaixo, liste os campos **essenciais** e justifique os descartes:

1. Montar a escala de turnos dos voluntários.
2. Encomendar as camisetas e as refeições do evento.
3. Ligar para alguém em caso de acidente durante o evento.

<details>
<summary>Uma resposta possível</summary>

1. **Escala:** nome (ou apelido), disponibilidade e experiência anterior. O resto não muda quem trabalha em qual turno.
2. **Camisetas e refeições:** nome, tamanho de camiseta e restrição alimentar.
3. **Emergência:** nome, telefone e contato de emergência.

Campos como signo, cor favorita e time de futebol não são essenciais para nenhum dos três objetivos. Pela LGPD, o ideal seria nem coletá-los.
</details>

---

## E2.7 Desafio - Organizando um festival

**Formato:** individual, resolvendo os três objetivos (com mais pessoas, cada uma pode começar por um objetivo diferente e depois todas comparam). **Tempo:** 25 min.

Uma equipe organiza um festival e tem estas informações:

nome dos artistas · cor dos banners · orçamento · previsão do tempo · quantidade de voluntários · tamanho do palco · horário das apresentações · patrocinadores · marca das cadeiras · quantidade de banheiros · lista de equipamentos · decoração · mapa do evento · fornecedores · estacionamento · número de seguidores nas redes sociais · cardápio da praça de alimentação

Para cada objetivo, responda: quais informações são essenciais, quais podem ser ignoradas, e por quê.

1. **Programação:** definir a ordem e o horário das apresentações.
2. **Orçamento:** saber quanto dinheiro entra e quanto sai.
3. **Infraestrutura:** preparar o espaço físico e a segurança.

Depois, responda a estas perguntas de reflexão:

- Qual informação foi essencial para **todos** os objetivos? E qual não foi essencial para **nenhum**?
- Escolha uma informação que você descartou em um objetivo e manteve em outro. O que mudou?
- O orçamento precisa da *cor* dos banners ou do *preço* dos banners? O que isso diz sobre abstração?

<details>
<summary>Pontos para conferir</summary>

- A **lista de equipamentos** tende a ser essencial para os três objetivos.
- O **número de seguidores** dificilmente é essencial para algum deles (mas seria para um objetivo de divulgação).
- A **previsão do tempo** é essencial para programação e infraestrutura, mas não para o orçamento, a menos que você precise alugar coberturas.
- O orçamento precisa do **preço** de cada item, não de suas características. A abstração pode transformar uma informação (a cor) em outra mais útil (o custo).
</details>

---

## E2.8 Autoavaliação

**1. Em uma notícia online, para entender o fato, o que é ruído: título, data, banner de propaganda ou autor?**

<details>
<summary>Resposta</summary>

O banner de propaganda.
</details>

**2. Uma informação descartada em uma análise está "errada" ou "inútil"?**

<details>
<summary>Resposta</summary>

Não. Ela só não é útil para aquele objetivo. Com outro objetivo, pode ser essencial.
</details>

**3. Para descobrir o produto mais vendido em uma loja, quais colunas de uma tabela de vendas são essenciais: Produto, CPF do cliente, Quantidade, Endereço?**

<details>
<summary>Resposta</summary>

Produto e Quantidade.
</details>

**4. Por que a abstração ajuda a cumprir a LGPD?**

<details>
<summary>Resposta</summary>

Porque leva a usar e coletar apenas os dados necessários para o objetivo, evitando tratar dados pessoais sem necessidade.
</details>

**5. Ao usar a função `sum()` do Python, você precisa saber como ela soma os números?**

<details>
<summary>Resposta</summary>

Não. Basta saber a entrada (uma sequência de números) e a saída (a soma). Isso é abstração no código.
</details>

---

## Tarefa da semana

Escolha uma base de dados, planilha ou site que você usa com frequência no projeto. No seu repositório, crie `pensamento-computacional/e2-abstracao.md` contendo:

1. o objetivo que você quer atingir com aquela fonte (em forma de pergunta);
2. três informações essenciais para esse objetivo;
3. três informações que são ruído, com uma frase explicando cada uma;
4. se a fonte tiver dados pessoais, quais deles você realmente precisaria usar.

---

## Checklist do Encontro 2

- [ ] Sei explicar o que é abstração e por que ela depende do objetivo
- [ ] Diferencio informação essencial de ruído em textos, tabelas e código
- [ ] Sei relacionar abstração e proteção de dados pessoais (LGPD)
- [ ] Fiz a atividade do filtro de ruído
- [ ] Resolvi os três objetivos do desafio do festival e as perguntas de reflexão
- [ ] Respondi a autoavaliação
- [ ] Entreguei a tarefa no meu repositório

## Links de apoio

- [Lei Geral de Proteção de Dados (Lei nº 13.709/2018)](https://www.planalto.gov.br/ccivil_03/_ato2015-2018/2018/lei/l13709.htm)
- [Funções em Python - tutorial oficial (em português)](https://docs.python.org/pt-br/3/tutorial/controlflow.html#defining-functions)
