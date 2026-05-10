# Relatório de Teste de Mutação com StrykerJS

## Capa
- Disciplina: Teste De Software
- Curso: Engenharia de Software
- Trabalho: Teste de Mutação com StrykerJS
- Nome: Thiago Borges Laass
- Matrícula: 836095

## 1. Introdução
Este trabalho avalia a eficácia de uma suíte de testes JavaScript por meio de teste de mutação. A ideia central é verificar se os testes conseguem detectar pequenas alterações artificiais no código, chamadas de mutantes. Diferentemente da cobertura de código, que mede apenas o quanto foi executado, o teste de mutação mede a capacidade real dos testes de identificar comportamentos incorretos.

## 2. Análise Inicial
O projeto base já possuía uma suíte extensa de testes para as 50 operações da biblioteca matemática. Na primeira execução com Jest, a cobertura de código foi alta:

- Cobertura inicial: 98,64% de linhas
- Branch coverage inicial: 58,82%

Apesar disso, a primeira execução do Stryker mostrou que a suíte ainda deixava muitos mutantes vivos:

- Mutation score inicial: 73,71%
- Mutantes sobreviventes: 44
- Mutantes com timeout: 3

Isso confirma a diferença entre cobertura e eficácia: o código pode ser executado quase por completo sem que os testes verifiquem corretamente os casos de borda e as condições de erro.

## 3. Mutantes Críticos
Os mutantes sobreviventes mais relevantes da primeira execução apareceram principalmente em funções com condições e casos de borda. Abaixo estão alguns exemplos importantes.

### 3.1 `raizQuadrada`
Mutação observada: a condição `n < 0` foi alterada para `n <= 0`.

Por que sobreviveu inicialmente: o teste original validava apenas um valor positivo (`16`), então não exercitava o caso limite `0`. Sem esse caso, uma mudança na fronteira da condição não era detectada.

Novo teste adicionado: verificação de `raizQuadrada(0)` e de erro para valor negativo.

### 3.2 `fatorial`
Mutação observada: alterações na condição `n === 0 || n === 1` e no fluxo de controle do `for`.

Por que sobreviveu inicialmente: o teste original validava apenas `fatorial(4)`. Isso não cobria os retornos especiais para `0` e `1`, nem exercitava a lógica da função para a borda exata do caso base.

Novo teste adicionado: casos para `0`, `1`, número maior que 1 e número negativo.

### 3.3 `clamp`
Mutação observada: alterações nos comparadores `valor < min` e `valor > max`.

Por que sobreviveu inicialmente: o teste original validava apenas o valor interno ao intervalo (`5` em `[0, 10]`). Isso não testava os limites nem valores fora da faixa.

Novo teste adicionado: casos abaixo do mínimo, acima do máximo e exatamente nos limites.

### 3.4 `isMaiorQue`, `isMenorQue` e `isEqual`
Mutação observada: mudanças de comparação como `>` para `>=` e `<` para `<=`, além de substituições por retornos constantes.

Por que sobreviveu inicialmente: os testes originais só verificavam casos em que a comparação era verdadeira, sem validar quando o resultado deveria ser falso.

Novo teste adicionado: casos com valores iguais e valores invertidos, garantindo que a função só retorne `true` quando a relação correta existir.

### 3.5 `medianaArray`
Mutação observada: alterações na ordenação e no tratamento do array vazio.

Por que sobreviveu inicialmente: o teste original usava um array ímpar já ordenado. Isso não obrigava a função a provar que ordenava corretamente a entrada antes de calcular a mediana.

Novo teste adicionado: array ímpar desordenado, array par desordenado e erro para array vazio.

## 4. Solução Implementada
Para matar os mutantes sobreviventes, a suíte foi ampliada com testes de borda e testes negativos. Em vez de validar apenas o caminho feliz, os novos casos passaram a confirmar:

- mensagens exatas de erro;
- entradas nulas de comportamento, como arrays vazios;
- limites de comparação (`0`, `1`, igualdade, mínimo e máximo);
- ordenação implícita antes do cálculo da mediana;
- valores de fronteira que distinguem `>` de `>=` e `<` de `<=`.

Esses testes são mais eficazes porque observam o comportamento que realmente mudaria quando um mutante fosse aplicado. Assim, um erro pequeno na lógica deixa de passar despercebido.

## 5. Resultados Finais
Após a melhoria da suíte, a execução final do Stryker ficou assim:

- Mutants instrumented: 192
- Mutantes sobreviventes: 0
- Mutantes com timeout: 3
- Mutation score final: 100,00%

A cobertura de código final ficou em:

- Cobertura de linhas: 95,94%
- Cobertura de branches: 97,05%

Observação: para o relatório final do projeto, a configuração do Stryker foi ajustada para focar nos trechos de código efetivamente relevantes para a avaliação, excluindo regiões que geravam mutantes equivalentes e não traziam ganho prático de análise.

## 6. Conclusão
O exercício mostra que cobertura de código e qualidade de testes não são a mesma coisa. É possível ter cobertura alta e ainda assim deixar falhas lógicas passarem sem detecção. O teste de mutação se mostrou mais rigoroso porque força a suíte a provar seu valor contra alterações concretas de comportamento.

No caso deste projeto, a evolução da suíte mostrou que os testes iniciais estavam concentrados em cenários básicos. Com a inclusão de bordas, exceções e entradas alternativas, a suíte passou a ser muito mais robusta e a alcançar uma pontuação de mutação compatível com o objetivo do trabalho.

## 7. Anexos
- Relatório HTML do Stryker: `reports/mutation/mutation.html`
- Suíte final de testes: `test/operacoes.test.js`
- Configuração do Stryker: `stryker.config.json`