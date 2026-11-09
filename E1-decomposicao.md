# E1 - Introdução e Decomposição de Problemas

**Objetivo:** entender o que é Pensamento Computacional e aprender a quebrar problemas grandes em partes tratáveis.

**Ao final deste encontro você terá:** conhecido os quatro pilares do Pensamento Computacional, praticado a decomposição em um problema real e registrado o resultado em um arquivo do seu repositório.

## E1.1 Conceitos antes de começar

| Termo | O que é |
| :--- | :--- |
| **Problema** | Qualquer situação em que você quer sair de um estado atual e chegar a um objetivo: organizar uma festa, coletar dados de um site, descobrir por que alunos abandonam um curso. |
| **Pensamento Computacional (PC)** | Um jeito de pensar para resolver problemas de forma organizada, de modo que a solução possa ser executada por uma pessoa ou por um computador. Não exige saber programar. |
| **Pilar** | Cada uma das quatro habilidades que formam o PC: decomposição, reconhecimento de padrões, abstração e algoritmos. |
| **Algoritmo** | Uma sequência finita e ordenada de passos que resolve um problema. Uma receita é um algoritmo; um programa de computador também. |

---

## E1.2 De onde veio a ideia

- **Seymour Papert** (matemático e educador do MIT) criou no fim da década de 1960 a linguagem **Logo**, com a qual crianças programavam uma "tartaruga" que desenhava na tela. Para ele, o computador era uma ferramenta para *pensar*, não só para calcular. Ele usou a expressão "pensamento computacional" em seu livro *Mindstorms* (1980).
- **Jeannette Wing** (cientista da computação) popularizou o termo em um artigo de 2006, defendendo que o Pensamento Computacional é uma habilidade básica para todas as pessoas, ao lado de ler, escrever e fazer contas.

> [!NOTE]
> É comum ver a frase "o Pensamento Computacional foi criado por Papert nos anos 1960". O mais preciso é: Papert lançou as bases com o Logo nos anos 1960, usou o termo em 1980, e Wing o popularizou em 2006.

---

## E1.3 Os quatro pilares

| Pilar | Pergunta que ele responde | Exemplo do dia a dia | Exemplo profissional |
| :--- | :--- | :--- | :--- |
| **Decomposição** | Em que partes menores posso dividir este problema? | Organizar uma festa dividindo em convidados, comida, local e música | Na Medicina, analisar separadamente sintomas, histórico e exames |
| **Reconhecimento de padrões** | O que se repete aqui ou se parece com algo que já resolvi? | Perceber que o trânsito piora em dias de chuva e sair mais cedo | Em um jogo, perceber que todas as fases têm "pular" e escrever esse comando uma única vez |
| **Abstração** | O que é essencial e o que posso ignorar? | Um mapa de metrô mostra estações e linhas, não ruas e prédios | No Direito, filtrar de um depoimento só os fatos com relevância jurídica |
| **Algoritmos** | Em que ordem exata devo fazer cada passo? | Uma receita de bolo | Um protocolo de triagem hospitalar, que define a urgência pelos sinais vitais |

Os pilares não são etapas isoladas: normalmente você decompõe, procura padrões nas partes, abstrai o que importa e, por fim, escreve o algoritmo. O exemplo integrador (E1.7) mostra isso em sequência.

Este encontro aprofunda a **decomposição**. A abstração é tema do E2, e os algoritmos ganham forma de código no E3.

---

## E1.4 Decomposição em detalhe

Decompor é dividir um problema grande em subproblemas menores, até que cada um seja **simples o bastante para ser resolvido diretamente**.

Por que funciona:

- um problema grande paralisa; partes pequenas dão um ponto de partida claro;
- partes diferentes podem ser feitas por pessoas diferentes, ou em momentos diferentes;
- quando algo dá errado, é mais fácil descobrir em qual parte está o erro.

### Como saber se a decomposição está boa

Use estes critérios em cada parte que você criou:

1. **É acionável?** Dá para começar a fazer agora, ou ainda é vago? "Planejar" é vago; "listar as páginas do site que precisam de backup" é acionável.
2. **Tem um resultado verificável?** Você saberia dizer quando a parte terminou?
3. **As partes juntas cobrem o problema todo?** Falta alguma coisa para o objetivo ser atingido?
4. **As partes não se sobrepõem?** Duas partes fazendo a mesma coisa indicam que a divisão precisa de ajuste.

Se uma parte falha no critério 1 ou 2, decomponha-a de novo. Esse processo repetido forma uma **árvore de decomposição**.

### Exemplo: backup diário automático de um site

Uma decomposição genérica como "Planejar → Fazer → Testar" serve para qualquer projeto, por isso não ajuda a resolver *este* problema. Veja a diferença ao aplicar os critérios:

```
Backup diário automático do site
├── 1. Definir o que copiar
│   ├── 1.1 Arquivos do site (páginas, imagens)
│   └── 1.2 Banco de dados
├── 2. Definir para onde copiar
│   ├── 2.1 Escolher o destino (outro servidor, nuvem)
│   └── 2.2 Definir por quantos dias guardar cada cópia
├── 3. Automatizar
│   ├── 3.1 Escrever o script que gera a cópia
│   └── 3.2 Agendar a execução diária
└── 4. Garantir que funciona
    ├── 4.1 Registrar sucesso ou falha de cada execução
    └── 4.2 Testar a restauração de uma cópia
```

Cada folha da árvore é acionável e tem um resultado verificável. A parte 4.2 aparece porque o critério 3 obriga a perguntar "o objetivo é ter cópias ou conseguir *recuperar* o site?".

---

## E1.5 Decomposição em projetos de dados

Projetos do laboratório quase sempre se decompõem em quatro grandes partes, que serão estudadas ao longo da trilha:

```
Responder uma pergunta com dados
├── Coletar     → obter os dados (E6)
├── Verificar   → descartar o que está incompleto ou repetido (E3)
├── Armazenar   → guardar de forma organizada (E3)
└── Analisar    → transformar em tabelas e gráficos (E4)
```

O projeto integrador (E5) percorre essas etapas em um único problema.

---

## E1.6 Atividade - Árvore de decomposição

**Formato:** individual (com mais pessoas, cada uma faz a sua e depois comparam). **Tempo:** 20 min.

1. Escolha um problema real: uma tarefa do seu projeto, do seu trabalho ou da sua rotina. Precisa ser algo que você ainda não sabe resolver de uma vez.
2. Escreva o problema no topo.
3. Divida-o em 3 a 5 partes.
4. Aplique os quatro critérios da seção E1.4 a cada parte. Decomponha de novo as que não passarem.
5. Pare quando todas as folhas forem acionáveis.
6. Se houver outra pessoa na turma, troquem as árvores e cada uma aponta uma parte que achou vaga ou uma parte que está faltando.

Você pode desenhar a árvore em um quadro virtual (Miro, por exemplo), no papel, ou em Markdown, como no exemplo da seção E1.4.

---

## E1.7 Exemplo integrador: planejando uma mudança de casa

Veja os quatro pilares trabalhando em sequência:

1. **Decomposição:** "fazer a mudança" vira embalar objetos, contratar transporte, desmontar móveis e organizar a casa nova.
2. **Reconhecimento de padrões:** certos itens se comportam de forma parecida: livros são pesados; copos são leves e frágeis.
3. **Abstração:** em vez de pensar em cada objeto, você cria categorias ("caixa frágil", "caixa pesada") e ignora o que há dentro de cada uma.
4. **Algoritmo:** define a ordem: embalar primeiro o que é pouco usado, carregar as caixas pesadas primeiro (embaixo) e desembalar na ordem inversa.

---

## E1.8 Por que isso importa no mercado de trabalho

O relatório *Future of Jobs* do Fórum Econômico Mundial pesquisa empresas sobre as habilidades mais procuradas. Nas edições de 2023 e 2025, o **pensamento analítico** aparece como a habilidade mais importante para os empregadores, e o pensamento criativo e a resolução de problemas também estão entre as primeiras. Essas habilidades são, em grande parte, o que os quatro pilares treinam.

> [!TIP]
> Os rankings mudam a cada edição. Antes de citar números em um trabalho, consulte a versão mais recente do relatório (link no final).

---

## E1.9 Autoavaliação

Responda mentalmente ou por escrito e depois abra a resposta. Esta seção substitui o quiz único do encontro: como é individual, funciona com qualquer tamanho de turma e pode ser refeita quando quiser.

**1. Qual pilar está sendo usado quando você percebe que "todo relatório mensal tem as mesmas três seções" e cria um modelo?**

<details>
<summary>Resposta</summary>

Reconhecimento de padrões: você identificou algo que se repete e reaproveitou a solução.
</details>

**2. Uma decomposição "Planejar → Executar → Revisar" é boa?**

<details>
<summary>Resposta</summary>

Geralmente não. Ela serve para qualquer problema, por isso não ajuda a resolver nenhum em particular. As partes não são acionáveis nem têm resultado verificável.
</details>

**3. Quando você deve parar de decompor?**

<details>
<summary>Resposta</summary>

Quando cada parte for acionável (dá para começar agora) e tiver um resultado verificável (dá para saber quando terminou).
</details>

**4. Qual a diferença entre abstração e decomposição?**

<details>
<summary>Resposta</summary>

A decomposição divide o problema em partes. A abstração escolhe, dentro do problema ou de cada parte, o que é essencial e o que pode ser ignorado.
</details>

**5. Verdadeiro ou falso: Pensamento Computacional só serve para quem programa.**

<details>
<summary>Resposta</summary>

Falso. É um modo de organizar a resolução de problemas, usado em qualquer área. A programação é uma das formas de executar a solução.
</details>

---

## Tarefa da semana

No seu repositório, crie o arquivo `pensamento-computacional/e1-decomposicao.md` contendo:

1. o problema real que você escolheu;
2. a árvore de decomposição completa, com pelo menos dois níveis;
3. para duas folhas da árvore, uma frase explicando por que ela passa nos critérios de acionável e verificável;
4. pelo menos três informações do problema que podem ser **ignoradas** (abstraídas), com uma frase justificando cada uma.

Use blocos de código ou listas aninhadas do Markdown para a árvore. Registre e envie o arquivo para o GitHub.

---

## Checklist do Encontro 1

- [ ] Sei explicar o que é Pensamento Computacional e sua origem (Papert e Wing)
- [ ] Sei diferenciar os quatro pilares com um exemplo de cada
- [ ] Sei aplicar os quatro critérios para avaliar uma decomposição
- [ ] Montei uma árvore de decomposição com pelo menos dois níveis
- [ ] Entendi como os pilares se conectam no exemplo da mudança
- [ ] Respondi a autoavaliação
- [ ] Entreguei a tarefa no meu repositório

## Links de apoio

- Jeannette Wing, "Computational Thinking" (2006): [cs.cmu.edu/~15110-s13/Wing06-ct.pdf](https://www.cs.cmu.edu/~15110-s13/Wing06-ct.pdf)
- [Fórum Econômico Mundial - The Future of Jobs Report 2025](https://www.weforum.org/publications/the-future-of-jobs-report-2025/)
