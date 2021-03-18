## Genetic Algorithms

# 1. Introdução
Os algoritmos genéticos são projetados para resolver problemas usando os mesmos processos da natureza - eles usam uma combinação de seleção, recombinação e mutação para desenvolver uma solução para um problema.

Vamos começar explicando o conceito desses algoritmos usando o exemplo de algoritmo genético binário mais simples.

# 2. Como funcionam os algoritmos genéticos
Algoritmos genéticos fazem parte da computação evolucionária, que é uma área de inteligência artificial em rápido crescimento.

Um algoritmo começa com um conjunto de soluções (representadas por indivíduos) chamado população. Soluções de uma população são tomadas e usadas para formar uma nova população, pois há uma chance de que a nova população seja melhor do que a antiga.

Os indivíduos escolhidos para formar novas soluções (descendentes) são selecionados de acordo com sua aptidão - quanto mais adequados, mais chances têm de se reproduzir.

# 3. Algoritmo Genético Binário
Vamos dar uma olhada no processo básico de um algoritmo genético simples.

3.1. Inicialização
Na etapa de inicialização, geramos uma População aleatória que serve como uma primeira solução. Primeiro, precisamos decidir o quão grande será a população e qual é a solução final que esperamos:

```
SimpleGeneticAlgorithm.runAlgorithm(50,
  "1011000100000100010000100000100111001000000100000100000000001111");
```

No exemplo acima, o tamanho da população é 50, e a solução correta é representada pela sequência de bits binários que podemos alterar a qualquer momento.

Na próxima etapa, salvaremos nossa solução desejada e criaremos uma população aleatória:

```
setSolution(solution);
Population myPop = new Population(populationSize, true);
```

Agora estamos prontos para executar o loop principal do programa.

### 3.2. Verificação de aptidão
No loop principal do programa, vamos avaliar cada indivíduo pela função de fitness (em palavras simples, quanto melhor o indivíduo, maior o valor da função de fitness):

```
while (myPop.getFittest().getFitness() < getMaxFitness()) {
    System.out.println(
      "Generation: " + generationCount
      + " Correct genes found: " + myPop.getFittest().getFitness());
    
    myPop = evolvePopulation(myPop);
    generationCount++;
}
```

Vamos começar explicando como conseguimos o indivíduo mais apto:

```
public int getFitness(Individual individual) {
    int fitness = 0;
    for (int i = 0; i < individual.getDefaultGeneLength()
      && i < solution.length; i++) {
        if (individual.getSingleGene(i) == solution[i]) {
            fitness++;
        }
    }
    return fitness;
}
```

Como podemos observar, comparamos dois objetos individuais bit a bit. Se não conseguirmos encontrar uma solução perfeita, precisamos prosseguir para a próxima etapa, que é uma evolução da População.

### 3.3. Descendência
Nesta etapa, precisamos criar uma nova População. Primeiro, precisamos selecionar dois objetos individuais pais de uma população, de acordo com sua adequação. Observe que é benéfico permitir que o melhor indivíduo da geração atual passe para a próxima, inalterado. Esta estratégia é chamada de Elitismo:

```
if (elitism) {
    newPopulation.getIndividuals().add(0, pop.getFittest());
    elitismOffset = 1;
} else {
    elitismOffset = 0;
}
```

Para selecionar os dois melhores objetos individuais, vamos aplicar a estratégia de seleção de torneio:

```
private Individual tournamentSelection(Population pop) {
    Population tournament = new Population(tournamentSize, false);
    for (int i = 0; i < tournamentSize; i++) {
        int randomId = (int) (Math.random() * pop.getIndividuals().size());
        tournament.getIndividuals().add(i, pop.getIndividual(randomId));
    }
    Individual fittest = tournament.getFittest();
    return fittest;
}
```

O vencedor de cada torneio (aquele com a melhor condição física) é selecionado para a próxima fase, que é Crossover:

```
private Individual crossover(Individual indiv1, Individual indiv2) {
    Individual newSol = new Individual();
    for (int i = 0; i < newSol.getDefaultGeneLength(); i++) {
        if (Math.random() <= uniformRate) {
            newSol.setSingleGene(i, indiv1.getSingleGene(i));
        } else {
            newSol.setSingleGene(i, indiv2.getSingleGene(i));
        }
    }
    return newSol;
}
```

No cruzamento, trocamos bits de cada indivíduo escolhido em um local escolhido aleatoriamente. Todo o processo é executado dentro do seguinte loop:

```
for (int i = elitismOffset; i < pop.getIndividuals().size(); i++) {
    Individual indiv1 = tournamentSelection(pop);
    Individual indiv2 = tournamentSelection(pop);
    Individual newIndiv = crossover(indiv1, indiv2);
    newPopulation.getIndividuals().add(i, newIndiv);
}
```

Como podemos ver, após o cruzamento, colocamos novos descendentes em uma nova População. Esta etapa é chamada de Aceitação.

Finalmente, podemos realizar uma mutação. A mutação é usada para manter a diversidade genética de uma geração de uma população para a próxima. Usamos o tipo de mutação de inversão de bits, em que bits aleatórios são simplesmente invertidos:

```
private void mutate(Individual indiv) {
    for (int i = 0; i < indiv.getDefaultGeneLength(); i++) {
        if (Math.random() <= mutationRate) {
            byte gene = (byte) Math.round(Math.random());
            indiv.setSingleGene(i, gene);
        }
    }
}
```

Todos os tipos de mutação e crossover são bem descritos neste tutorial.

Em seguida, repetimos as etapas das subseções 3.2 e 3.3, até chegarmos a uma condição de término, por exemplo, a melhor solução.

# 4. Dicas e truques
Para implementar um algoritmo genético eficiente, precisamos ajustar um conjunto de parâmetros. Esta seção deve fornecer algumas recomendações básicas sobre como começar com a maioria dos parâmetros de importação:

- Taxa de crossover - deve ser alta, cerca de 80% -95%;
- Taxa de mutação - deve ser muito baixa, em torno de 0,5% -1%;
- Tamanho da população - um bom tamanho da população é cerca de 20-30, entretanto, para alguns problemas, tamanhos 50-100 são melhores;
- Seleção - a seleção básica da roda de roleta pode ser usada com o conceito de elitismo;
- Tipo de cruzamento e mutação - depende da codificação e do problema.

Observe que as recomendações para ajuste são frequentemente resultados de estudos empíricos sobre algoritmos genéticos e podem variar, com base nos problemas propostos.

# 5. Conclusão
Este tutorial apresenta os fundamentos dos algoritmos genéticos. Você pode aprender sobre algoritmos genéticos sem nenhum conhecimento prévio nesta área, tendo apenas habilidades básicas de programação de computadores.

Observe também que usamos o Lombok para gerar getters e setters.