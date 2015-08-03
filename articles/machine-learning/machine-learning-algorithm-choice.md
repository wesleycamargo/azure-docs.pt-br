<properties 
	pageTitle="Como escolher algoritmos de Aprendizado de Máquina | Microsoft Azure" 
	description="Como escolher algoritmos de Aprendizado de Máquina do Azure para aprendizado supervisionado e não supervisionado nos experimentos de agrupamento, classificação ou regressão." 
	services="machine-learning"
	documentationCenter="" 
	authors="garyericson" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/01/2015" 
	ms.author="bradsev;garye" />


# Como escolher algoritmos de Aprendizado de Máquina do Azure para agrupamento, classificação ou regressão

Este tópico explica alguns aspectos básicos da abordagem de Aprendizado de Máquina. Em particular, você aprenderá a escolher algoritmos de Aprendizado de Máquina apropriados para analisar determinados tipos de dados, para responder às perguntas, realizar tarefas especificadas ou fornecer critérios de tomada de decisões.

> [AZURE.TIP]A [Página de Dicas Úteis sobre o Algoritmo de Aprendizado de Máquina do Microsoft Azure](machine-learning-algorithm-cheat-sheet.md) é uma boa referência que acompanha este artigo.

Ao usar a o aprendizado de máquina para realizar análises, normalmente ficamos diante de duas perguntas:

* Qual tipo de análise precisamos para alcançar nossos objetivos com os dados disponíveis? 
* Qual é o algoritmo ou modelo mais apropriado a ser usado para fazer essa análise?

Seus casos de uso são comparados e três tipos de análise de Aprendizado de Máquina são discutidos:

* **Clustering**
* **Classificação** 
* **Regressão** 

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]


<a name="anchor-1"></a>
## Algoritmos de Aprendizado de Máquina aprenderem com dados

O Aprendizado de Máquina é uma disciplina que estuda uma classe de algoritmos projetados para aprender com os dados e não impõe um modelo específico e predeterminado para testar em relação aos dados. A ideia é adquirir conhecimento por indução, examinando os padrões em um conjunto de dados em vez de usar o método *hipotético dedutivo*, no qual você tentar adivinhar o modelo apropriado para todo o conjunto de dados primeiro e, em seguida, testa-o empiricamente.

Os tipos de Aprendizado de Máquina (conhecido também como aprendizado por meio de dados) vêm em duas versões: *aprendizado supervisionado* e *aprendizado não supervisionado*.

<a name="anchor-2"></a>
## Aprendizado Supervisionado  

O aprendizado supervisionado requer que a variável de destino seja bem definida e que receba um número suficiente dos seus valores.

O aprendizado supervisionado é o tipo de Aprendizado de Máquina que ocorre quando os resultados de saída corretos (ou variáveis de destino) para as instâncias de treinamento que serão entradas são conhecidos. O objetivo do treinamento de um algoritmo de Aprendizado de Máquina é encontrar o modelo (ou seja, uma regra ou função) que mapeia as entradas para os valores de saída conhecidos. Isso semelhante a ter um supervisor que pode informar o agente algorítmico se ele está ou não mapeando as entradas corretamente para as saídas. Depois que o processo de aprendizado for concluído e termos um modelo funcional, ele pode ser aplicado aos novos dados de entrada para prever a saída esperada em que, diferentemente do conjunto de dados de treinamento, o valor de destino não é conhecido previamente.

O tipo de modelo é determinado pela natureza da variável de destino.

![Diagrama de aprendizado supervisionado: modele em dados rotulados e use-os para prever os resultados de novos dados.](./media/machine-learning-algorithm-choice/supervised-learning-using-known-data-to-model-solution.png)

Há duas categorias de análise que empregam o aprendizado supervisionado: *classificação* e *regressão*. Aprendizado supervisionado é muito comum em problemas de classificação, pois o objetivo é, muitas vezes, que o computador tenha um sistema de classificação que criamos. As respostas normalmente são apenas alguns valores (etiquetas) conhecidos, como 'true' ou 'false' ou 'high', 'medium' ou 'low'. Algoritmos de classificação se aplicam a valores nominais, não a valores de resposta ordinal. O reconhecimento de dígito é um exemplo comum de aprendizado de classificação. Normalmente, o aprendizado de classificação é apropriado para qualquer problema em que seja útil e fácil determinar a classificação.

No aprendizado supervisionado, as variáveis investigadas podem ser divididas em dois grupos: variáveis explicativas (também chamadas de prognóstica) e dependentes (também chamadas de resposta). O objetivo da análise é definir uma relação entre as variáveis explicativas e as variáveis dependentes, como é feito na *análise de regressão*. Os valores da variável dependente devem ser conhecidos por uma parte suficientemente grande do conjunto de dados. Na regressão, a variável de resposta ou saída assume valores contínuos, como km/litro para de um determinado carro, a idade de uma pessoa etc.

O aprendizado supervisionado também é a técnica mais comum para redes neurais de treinamento e árvores de decisão:

> Ambas as técnicas são altamente dependentes das informações fornecidas pelas classificações predeterminadas. No caso de redes neurais, a classificação é usada para determinar o erro da rede e, em seguida, ajustar a rede para minimizar esse erro e, em árvores de decisão, as classificações são usadas para determinar quais atributos fornecem a maioria das informações que podem ser usadas para resolver o quebra-cabeça da classificação... ambos os exemplos prosperam tendo alguma "supervisão" na forma de classificações predeterminadas.

>  O reconhecimento de fala usando modelos de Markov ocultos e redes Bayesian depende de alguns elementos de supervisão também para ajustar os parâmetros a fim de minimizar, como de costume, o erro nas entradas determinadas. [[Aprendizado de Máquina, Parte II: Aprendizado Supervisionado e não Supervisionado](http://www.aihorizon.com/essays/generalai/supervised_unsupervised_machine_learning.htm), [AI Horizon](http://www.aihorizon.com/)]


<a name="anchor-3"></a>
##Aprendizado não Supervisionado

No Aprendizado de Máquina, o problema do aprendizado não supervisionado é determinar se existem padrões ou uma estrutura oculta em dados sem rótulo. O modelo não é fornecido com os "resultados corretos" para um conjunto de dados de treinamento. Como são fornecidos ao aluno exemplos sem rótulo, não há comentários (seja de erro ou elogio) para avaliar uma solução em potencial. O objetivo é fazer com que o computador aprenda a fazer algo, embora nós não informemos a ele explicitamente como realizar essa tarefa. Em situações de aprendizado não supervisado, todas as variáveis são tratadas da mesma maneira. Não há nenhuma distinção entre variáveis explicativas e variáveis dependentes. No entanto, ainda há alguns objetivos a serem alcançados, o que pode ser um objetivo geral, como a redução de dados, ou uma meta mais específica, como localizar clusters.

No Aprendizado de Máquina do Azure, podemos executar o aprendizado supervisionado e não supervisionado por **Clustering**, **Classificação** e **Regressão**.

   ![screenshot_of_experiment](./media/machine-learning-algorithm-choice/help2.png)

<a name="anchor-4"></a>
##Clustering
Clustering é um exemplo de aprendizado sem supervisão. Nsse tipo de aprendizado, o objetivo é encontrar semelhanças nos dados de treinamento e particionar o conjunto de dados em subconjuntos delimitados por essas semelhanças. A expectativa de que clusters mais significativos sejam descobertos esses procedimentos controlados por dados estão de acordo com nossa classificação intuitiva que geralmente, mas nem sempre, é satisfeita.

Embora o algoritmo de clustering não atribua nomes apropriados a esses clusters, ele pode produzi-los e usá-los para prever as semelhanças esperadas nos novos exemplos, classificando-os em clusters mais apropriados. Essa abordagem orientada a dados pode funcionar bem quando há dados suficientes disponíveis. Por exemplo, algoritmos de filtragem de informações sociais, como aqueles usados pela Amazon.com recomendar livros, baseiam-se em localizar grupos semelhantes de pessoas e, em seguida, atribuir novos usuários a esses grupos para fazer recomendações.

O algoritmo de clustering disponível no Aprendizado de Máquina do Azure é o [Clustering de média K][k-means-clustering].

![Experimento de algoritmo de clustering K-Means: captura de tela](./media/machine-learning-algorithm-choice/k-means-clustering-algorithm-menu.png)

Média K é um dos algoritmos de clustering sem supervisão mais simples que resolvem problemas conhecidos de clustering. O algoritmo K-Means armazena os dados em cluster ao tentar separar exemplos em grupos N de variância igual, minimizando o critério de "inércia" ou "soma dos quadrados no cluster". Esse algoritmo requer que o número de clusters seja especificado. K-Means pode ser dimensionado para um grande número de amostras e tem sido usado em uma grande variedade de áreas de aplicativos em muitos campos diferentes.

O módulo de algoritmo [Clustering de K-Means][k-means-clustering] retorna um modelo de clustering de K-Means não treinado que pode ser passado para o módulo [Modelo de Clustering de Treinamento][train-clustering-model] para treinamento.

![screenshot_of_experiment](./media/machine-learning-algorithm-choice/k4.png)

Esta figura mostra que há opções a serem configuradas ao usar Clustering de Média K. O método média K localiza um número especificado de clusters para um conjunto de pontos de dados de dimensão D. Começando com um *conjunto inicial de centroides K*, o método usa o algoritmo de Lloyd para refinar iterativamente os locais de centroides. O algoritmo é encerrado quando os centroides estabilizam ou quando um *número especificado de iterações* é concluído. O módulo inicializa uma matriz de K-por-D com os centroides finais que definem os clusters K encontrados nos pontos de dados de N. O algoritmo também usa um vetor de tamanho N com a atribuição de cada ponto de dados para um dos clusters K. Se houver um número específico de clusters (K) para localizar, o módulo atribui os pontos de dados K primeiro aos clusters K.


Este módulo também aceita ou gera pontos iniciais para definir sua configuração de cluster inicial. *Métrica* define o método usado para medir a distância entre um ponto de dados e o centroide. Cada ponto de dados é atribuído ao cluster que tem o centroide mais próximo ao ponto de dados. Por padrão, o método utiliza a *Métrica Euclidiana*. Você pode especificar a *métrica cosseno* como uma métrica alternativa para o método. Observe que o método média K só pode encontrar uma configuração de cluster local ideal para um conjunto de dados. O método poderia localizar uma configuração diferente, talvez seja melhor, em caso de configuração inicial diferente.

<a name="anchor-5"></a>
##Classificação 
Na análise de classificação, dividimos os exemplos em classes e usamos um conjunto treinado de dados rotulados anteriormente. A técnica é usada para prever associação ao grupo para instâncias de dados. No Aprendizado de Máquina do Azure, estão disponíveis os seguintes algoritmos de classificação.

![screenshot_of_experiment](./media/machine-learning-algorithm-choice/help3.png)

Os *Algoritmos de Classe Dois* são usados para variáveis de resposta binária (sim ou não, 0 ou 1, verdadeiro ou falso, etc.) enquanto os *Algoritmos Multiclasse* são usados para qualquer variável de resposta nominal que classifique instâncias em mais de duas classes

### Diretrizes para selecionar um algoritmo de classificação
Essa longa lista de algoritmos dá origem a uma série de perguntas:

* Como saber quais desses muitos classificadores é o melhor a usar em um determinado conjunto de dados? 
* Há casos em que um classificador é uma opção "natural"? 
* Quais são os princípios para fazer a escolha?

Uma abordagem recomendada é testar vários classificadores diferentes, bem como conjuntos de parâmetros diferentes dentro de cada algoritmo e selecionar aquele que melhor usa a validação cruzada.

> [AZURE.TIP] [Azure Machine Learning Studio](https://studio.azureml.net/) permite que você experimente vários algoritmos lado a lado com os mesmos dados e compare os resultados. Aqui está um exemplo ao [Galeria de Aprendizado de Máquina do Azure](http://gallery.azureml.net/): [Comparar classificadores multiclasses: reconhecimento de letra](http://gallery.azureml.net/Details/a635502fc98b402a890efe21cec65b92).

Aqui estão algumas diretrizes gerais que podem fornecer um local para iniciar esse discussões. Considere as seguintes questões ao escolher qual algoritmo usar: [Tópico sugerido por [Escolhendo um classificador de Aprendizado de Máquina](http://blog.echen.me/2011/04/27/choosing-a-machine-learning-classifier/), Edwin Chen]

**Qual o tamanho dos seus dados de treinamento?** Se o conjunto de treinamento for pequeno e você pretende treinar um classificador supervisionado, a teoria de Aprendizado de Máquina diz que você deve inserir a um classificador de alto viés/baixa variação, como Bayesiana simples. Eles têm uma vantagem sobre classificadores de baixo viés/alta variação como kNN desde que o segundo tende a ser sobreajustado. Porém, os classificadores de baixo viés/alta variação são mais apropriados se você tiver um conjunto de treinamento maior, porque eles têm um erro assintótico menor; nesses casos, uma classificação de alto viés não é tão eficiente para fornecer um modelo preciso. Há resultados teóricos e empíricos que indicam que Naive Bayes funciona bem em tais circunstâncias. Mas observe que ter dados melhores e bons recursos geralmente pode fornecer uma vantagem maior do que ter um algoritmo melhor. Além disso, se você tiver um desempenho de classificação de conjunto de dados muito grande não poderá ser muito afetado pelo algoritmo que usa; então, nesse caso, é melhor escolher o algoritmo com base em itens como sua escalabilidade, velocidade ou facilidade de uso.

**Você precisa treinar gradativamente ou em lotes?** Se você tiver muitos dados ou se os seus dados são atualizados com frequência, você provavelmente desejará usar algoritmos Bayesianos, que também atualizam. Redes neurais e SVMs precisam trabalhar nos dados de treinamento em modo de lote.

**Seus dados são exclusivamente categóricos, exclusivamente numéricos ou uma mistura de ambos os tipos?** O método Bayesiano funciona melhor com dados categóricos/binomiais. Árvores de decisão não podem prever valores numéricos.

**Você ou seu público-alvo precisam entender como funciona a classificação?** Árvores de decisão ou Bayesiano são explicados mais facilmente. É muito mais difícil ver ou explicar como as redes neurais e SVMs classificam os dados.

**Em quanto tempo a sua classificação precisa ser gerada?** As árvores de decisão podem ser lentas quando a árvore é complexa. SVMs, por outro lado, classificam mais rapidamente, pois precisam determinar de qual lado da "linha" os seus dados estão disponíveis.

**Qual o nível de complexidade que o problema apresenta ou requer?** Redes neurais e SVMs podem manipular uma classificação complexa não linear.

### Vantagens e desvantagens dos algoritmos de classificação
Cada um desses algoritmos de classificação tem algumas vantagens e algumas desvantagens:

<a name="anchor-5a"></a> **Advantages and Disadvantages of Logistic Regression:** "Logistic regression analysis is based on calculating the odds of the outcome as the ratio of the probability of having the outcome divided by the probability of not having it." [[Logistic Regression and Linear Discriminant Analyses in Evaluating Factors Associated with Asthma Prevalence among 10- to 12-Years-Old Children: Divergence and Similarity of the Two Statistical Methods](http://www.hindawi.com/journals/ijpedi/2009/952042/), George Antonogeorgos, et al (International Journal of Pediatrics, 2009) ID do artigo: 952042]
 
O modelo logístico é paramétrico, tendo como vantagem fornecer informações sobre o impacto de cada variável de prognóstico na variável de resposta.


Com interpretações probabilísticas naturais disponíveis (ao contrário das árvores de decisão ou SVMs), você pode atualizar facilmente o seu modelo para incorporar novos dados. Há muitas maneiras de regularizar o seu modelo e, diferentemente de Bayesiana simples, você não precisa se preocupar muito com seus recursos serem correlacionados. A Regressão logística é útil se você quiser:

* uma estrutura probabilística para ajustar os limites de classificação
* incorporar rapidamente os dados de treinamento adicional  

A regressão logística funciona melhor do que as árvores de decisão para dados de alta dimensão. Por exemplo, na classificação de texto, você pode ter mais de 100 mil documentos com 500 mil palavras diferentes (recursos). Uma regra simples como o aprendizado de um hiperplano linear é melhor, pois as Árvores de Decisão têm muito mais graus de liberdade e estão sujeitas ao sobreajuste. Você poderia usar a seleção de recursos para usar uma árvore de decisão em dados de texto, mas muitas informações valiosas seriam perdidas para classificação de texto ao selecionar um subconjunto muito reduzido de recursos. Quando os modelos de aprendizagem são usados com dados dimensionais altos, é muito fácil haver um aumento dos erros com base em variação. Nesse caso, é melhor usar modelos simples com viés maior.

Uma desvantagem da regressão logística é que ela é instável quando um prognóstico quase poderia explicar a variável de resposta, porque o coeficiente dessa variável se tornará muito grande.

<a name="anchor-5b"></a> **Vantagens e desvantagens das árvores de decisão:** [árvores de decisão](http://research.microsoft.com/pubs/155552/decisionForests_MSR_TR_2011_114.pdf) são fáceis de interpretar e explicar.

> [Árvores de decisão] lidam facilmente com interações de recursos e não são paramétricas, assim você não precisa se preocupar com exceções ou se os dados são separáveis linearmente (por exemplo, árvores de decisão podem facilmente tratar de casos em que a classe A está no final de algum recurso x, a classe B está em nível intermediário do recurso x e A, novamente, no topo). Uma desvantagem é que não dão suporte ao aprendizado online; você precisará recompilar sua árvore quando vierem novos exemplos. Outra desvantagem é que elas sofrem sobreajuste com facilidade, mas é aí que entram os métodos de ensemble como florestas aleatórias (ou árvores aprimoradas). Além disso, as florestas aleatórias costumam ser o vencedor para muitos problemas de classificação (geralmente um pouco além das SVMs, creio eu), elas são rápidas e escalonáveis, e você não precisa se preocupar sobre como ajustar vários parâmetros, como é feito com SVMs. [[Escolhendo um Classificador de Aprendizado de Máquina](http://blog.echen.me/2011/04/27/choosing-a-machine-learning-classifier/), Edwin Chen]

As Árvores de decisão geram saídas como regras juntamente com métricas como *Suporte*, *Confiança* e *Comparação*.


<a name="anchor-5c"></a> **Vantagens e desvantagens das SVMs:** Máquinas de Vetor de Suporte (SVMs) são eficazes em espaços dimensionais altos. Mesmo nos casos em que o número de dimensões é maior que o número de amostras, ele ainda é eficaz. No entanto, o método é provavelmente fraco em desempenho se o número de recursos for muito maior do que o número de amostras. Ele também faz uso eficiente de memória porque usa um subconjunto dos pontos de treinamento na função de decisão (conhecida como vetores de suporte). É muito versátil: diferentes funções de Kernel, comuns e personalizadas, podem ser especificadas para a função de decisão. A função de Kernel é usada para transformar exemplos de treinamento pouco dimensionais em dimensões maiores, o que é útil para problemas de separabilidade linear.

No entanto, SVMs não fornecem estimativas de probabilidade diretamente. Isso é calculado usando uma cara validação cruzada quíntupla.

>[Com] alta precisão, garantias teóricas interessantes em relação a superajuste e com um Kernel apropriado, é possível funcionar bem mesmo se os seus dados não forem linearmente separáveis no espaço de recurso base. [SVMs são] especialmente populares em problemas de classificação de texto em que os espaços dimensionais muito altos são a norma. [[Escolhendo um classificador de Aprendizado de Máquina](http://blog.echen.me/2011/04/27/choosing-a-machine-learning-classifier/), Edwin Chen]

Ao contrário de florestas, SVMs foram criadas como classificadores de duas classes, embora recentemente tenham sido adaptadas para trabalhar com várias classes. Podemos usar um treinamento como “um contra o resto” para criar um classificador multiclasse, o que pode não ser o ideal. Como SVMs somente podem trabalhar com saídas de duas classes (ou seja, uma variável de saída categórica com variedade 2), com N classes, aprende N SVMs (por exemplo, SVM 1 aprende “Output==1” versus “Output != 1”, SVM 2 aprende “Output==2” versus “Output != 2”, ..., SVM N aprende “Output==N” versus “Output != N”). Em seguida, para prever a saída para uma nova entrada, ele apenas prevê com cada SVM e, em seguida, descobre que cada uma coloca a previsão o mais distante possível na região positiva. [[Máquinas de Vetor de Suporte](http://www.astro.caltech.edu/~george/aybi199/AMooreTutorials/svm.ppt), Andrew W. Moore (Carnegie Mellon University 2001)]

<a name="anchor-5d"></a> **Vantagens e desvantagens de Bayesiana simples: os classificadores de** [Bayesiano Simples (NB)](http://www.aaai.org/Papers/FLAIRS/2004/Flairs04-097.pdf) são uma opção popular para problemas de classificação. Eles supõem que os recursos são independentes, e isso é o que torna a técnica “simples”.

> Se a suposição de independência condicional de NB provar-se verdadeira, um classificador Bayesiano Simples convergirá mais rapidamente do que modelos discriminadores como a regressão logística, então você precisará de menos dados de treinamento. E mesmo que a suposição de NB não se comprove, um classificador NB normalmente ainda faz um excelente trabalho na prática. Sua principal desvantagem é que ele não pode aprender as interações entre os recursos (por exemplo, não é possível aprender que, embora você adore filmes com Brad Pitt e Tom Cruise, você odeia filmes em que eles estejam juntos). [[Escolhendo um Classificador de Aprendizado de Máquina](http://blog.echen.me/2011/04/27/choosing-a-machine-learning-classifier/), Edwin Chen]


<a name="anchor-5e"></a> **Um contra todos:** Um contra todos é uma estratégia para reduzir o problema da classificação multiclasse para um conjunto de vários problemas de classificação binária. Essa estratégia envolve treinar um classificador único por classe com os exemplos dessa classe como exemplos positivos e todos os outros exemplos como negativos. Essa estratégia requer os classificadores de base para produzir uma pontuação de confiança com valor real para a sua decisão, em vez de apenas uma etiqueta de classe; rótulos de classes discretas sozinhos podem gerar ambiguidades, em que várias classes estão previstas para obter um exemplo simples. [[Classificação multiclasses](http://en.wikipedia.org/wiki/Multiclass_classification) (Wikipédia 2006)]


<a name="anchor-6"></a>
##Regressão
 
Na análise de regressão, podemos prever novos valores com base na inferência passada. Os novos valores para uma variável dependente são calculados com base no valor de um ou mais atributos medidos. Os vários algoritmos de regressão disponíveis no Aprendizado de Máquina do Azure são:

![screenshot_of_experiment](./media/machine-learning-algorithm-choice/help4.png)

Dependendo do caso de uso e dos dados em mãos, escolhemos um algoritmo em vez de outro. Abaixo descrevemos alguns algoritmos de regressão e seus casos de uso principais.

<a name="anchor-6b"></a> **[Regressão Linear Bayesiana][bayesian-linear-regression]** A regressão linear Bayesiana é uma abordagem de regressão linear em que a análise estatística é realizada no contexto de inferência Bayesiana. Resultados explícitos estão disponíveis para as distribuições de probabilidade posteriores dos parâmetros do modelo quando o modelo de regressão tem erros normais distribuídos e uma determinada forma de distribuição anterior pode ser considerada. [[Regressão Linear](http://en.wikipedia.org/wiki/Bayesian_linear_regression) ([Wikipédia](http://en.wikipedia.org))]

<a name="anchor-6f"></a> **[Regressão da árvore de decisão aumentada][boosted-decision-tree-regression]** a regressão da árvore de decisão aumentada calcula uma relação entre preditor e variáveis de resposta. A estrutura de árvore de regressão é semelhante a uma árvore de classificação. Nós terminais são valores de função previstos (modelo). Os valores previstos são limitados aos valores em nós terminais. Algumas das vantagens de usar árvores de decisão são:

* é fácil interpretar as regras de decisão 
* elas são não paramétricas, por isso é fácil usar um intervalo de camadas de dados numéricos ou categóricos, e os dados de treinamento unimodal não são necessários
* elas são robustas em relação a exceções nos dados de treinamento 
* a classificação pode ser feita rapidamente depois que as regras são desenvolvidas 

No entanto, existem algumas desvantagens ao uso de árvores de decisão:

* elas tendem a causar um sobreajuste de dados de treinamento, gerando resultados fracos quando aplicadas ao conjunto de dados completo
* não é possível prever além dos limites mínimo e máximo da variável de resposta nos dados de treinamento


<a name="anchor-6g"></a> **[Regressão da Floresta de Decisão][decision-forest-regression]** Florestas de decisão podem ser usadas para aplicativos de classificação (variáveis categóricas) ou de regressão (variáveis contínuas). As Florestas de regressão podem ser usadas para a regressão não linear de variáveis dependentes se fornecidas entradas independentes, e as entradas e saídas podem ser multidimensionais. Florestas de regressão não são usadas tanto quanto seus equivalentes de classificação. A principal diferença é que o rótulo de saída das florestas de decisão a ser associado a dados de entrada, portanto os rótulos de treinamento, deve ser contínuo. Isso significa que a função objetiva deve ser devidamente adaptada. As Florestas de regressão têm muitas das vantagens das florestas de classificação, tais como eficiência e flexibilidade.

<a name="anchor-6a"></a> **[Regressão linear][linear-regression]** A regressão linear é amplamente usada para modelar a relação entre uma variável dependente escalar Y e uma ou mais variáveis explicativas denotadas X. Ela pode ser, e geralmente é, aplicada à previsão, antecipação ou redução. Ela pode ser usada de acordo com um modelo de previsão para um conjunto de dados observado de valores de Y e X. A regressão linear pressupõe que a estrutura subjacente de Y é uma combinação linear das variáveis X. Se um valor adicional de X é recebido sem seu valor y, o modelo de regressão linear ajustada pode ser usado para prever esse valor de Y. Modelos de regressão linear normalmente são ajustados usando a abordagem dos quadrados mínimos, mas também existem outras opções para medir qual é a melhor. [[Regressão linear](http://en.wikipedia.org/wiki/Bayesian_linear_regression) ([Wikipédia](http://en.wikipedia.org))]

<a name="anchor-6c"></a> **[Regressão de Redes Neurais][neural-network-regression]** Redes neurais são uma ferramenta útil de estatística de regressão não paramétrica. A Regressão não paramétrica resolve o problema tentando ajustar um modelo para uma variável Y em um conjunto de variáveis explicativas possíveis X1; : : : ; Xp e em que a relação entre X e Y pode ser mais complicada do que uma relação linear simples. [[Uma estrutura para Regressão não paramétrica usando redes neurais](http://ftp.isds.duke.edu/WorkingPapers/00-32.pdf), Herbert K. H. Lee (ISDS, pela Duke University)]

<a name="anchor-6d"></a> **[Regressão ordinal][ordinal-regression]** A regressão ordinal é um tipo de análise de regressão usada para modelar ou prever uma variável dependente ordinal. Para variáveis dependentes ordinais, você pode classificar os valores, mas a distância real entre categorias é desconhecida. Somente a ordenação relativa entre valores diferentes é significativa. Como os rótulos ou valores de destino têm uma ordem ou classificação natural, qualquer coluna numérica pode ser usada como um destino ordinal. A ordem natural dos números é usada para classificá-los. Por exemplo, doenças graduadas em uma escala de menos grave a mais grave; respondentes que escolhem respostas desde “Concordo Plenamente” até “Discordo Totalmente”; os alunos graduados em uma escala de A a F. Basicamente, embora a regressão ordinal seja uma extensão de regressão logística e se baseie no modelo de *possibilidades proporcionais*.


<a name="anchor-6e"></a> **[Regressão Poisson][poisson-regression]** A regressão Poisson é frequentemente usada para modelar dados de contagem. A regressão Poisson presume que a variável de resposta tem uma distribuição Poisson. Dados com distribuição de Poisson têm valores inteiros intrínsecos (discretos e positivo), o que faz sentido para dados de contagem. Considerando um conjunto de dados de treinamento, a regressão Poisson tenta encontrar os valores ideais maximizando a probabilidade logarítmica dos parâmetros fornecidos, dadas as entradas. A probabilidade dos parâmetros é a probabilidade de que os dados de treinamento foram amostrados de uma distribuição com esses parâmetros. Por exemplo, a regressão Poisson seria útil para:

* Modelar o número de gripes associadas a voos de avião 
* Estimar o número de chamadas relacionadas a um evento ou uma promoção 
* Criando tabelas de contingência

## Referências

Para obter uma lista completa com todos os algoritmos de aprendizado de máquina disponíveis no Estúdio de Aprendizado de Máquina, consulte [Inicializar modelo](https://msdn.microsoft.com/library/azure/0c67013c-bfbc-428b-87f3-f552d8dd41f6/) na [Ajuda de módulo e algoritmo do Estúdio de Aprendizado de Máquina](https://msdn.microsoft.com/library/azure/dn905974.aspx).

Você pode encontrar informações mais detalhadas sobre todos os tipos de algoritmos de aprendizado de máquina nas seguintes referências, muitos dos quais foram usados na criação deste artigo.

* [Escolhendo uma Classificador de Aprendizado de Máquina](http://blog.echen.me/2011/04/27/choosing-a-machine-learning-classifier/), Edwin Chen.

* [Decision Forests for Classification, Regression, Density Estimation, Manifold Learning and Semi-Supervised Learning](http://research.microsoft.com/pubs/155552/decisionForests_MSR_TR_2011_114.pdf), A. Criminisi1, J. Shotton2 e E. Konukoglu (Microsoft Research, 2011) - relatório técnico TR-2011-114.

* [Uma estrutura para Regressão não paramétrica usando redes neurais](http://ftp.isds.duke.edu/WorkingPapers/00-32.pdf), Herbert K. H. Lee (ISDS, pela Duke University).

* Manual de estatísticas computacionais: conceitos e métodos editados por James E. Gentle, Wolfgang Karl Härdle, Yuichi Mori (Springer-Verlag Berlin Heidelberg New York, 2004).

* [Logistic Regression and Linear Discriminant Analyses in Evaluating Factors Associated with Asthma Prevalence among 10- to 12-Years-Old Children: Divergence and Similarity of the Two Statistical Methods](http://www.hindawi.com/journals/ijpedi/2009/952042/), George Antonogeorgos, Demosthenes B. Panagiotakos, Kostas N. Priftis e Anastasia Tzonou (International Journal of Pediatrics, 2009) - ID do artigo 952042.

* [The Optimality of Naive Bayes](http://www.aaai.org/Papers/FLAIRS/2004/Flairs04-097.pdf) (University of New Brunswick 2004) Harry Zhang.

* [Support Vector Machines](http://www.astro.caltech.edu/~george/aybi199/AMooreTutorials/svm.ppt), Andrew W. Moore (Carnegie Mellon University 2001).

* [Aprendizado de Máquina, Parte II: Aprendizado supervisionado e não supervisionado](http://www.aihorizon.com/essays/generalai/supervised_unsupervised_machine_learning.htm), [AI Horizon](http://www.aihorizon.com/).

* [Quais são as vantagens da regressão logística em árvores de decisão?](http://www.quora.com/What-are-the-advantages-of-logistic-regression-over-decision-trees) ([Quora](http://www.quora.com/)).

* [Qual é a diferença entre o aprendizado supervisionado e o aprendizado não supervisionado?](http://stackoverflow.com/questions/1832076/what-is-the-difference-between-supervised-learning-and-unsupervised-learning) ([Stackoverflow](http://stackoverflow.com/)).

* [Quando escolher qual classificador de Aprendizado de Máquina?](http://stackoverflow.com/questions/2595176/when-to-choose-which-machine-learning-classifier) ([Stackoverflow](http://stackoverflow.com/)).

* [Wikipédia](http://en.wikipedia.org):
	* [Regressão linear Bayesiana](http://en.wikipedia.org/wiki/Bayesian_linear_regression)
	* [Regressão linear](http://en.wikipedia.org/wiki/Linear_regression)
	* [Classificação multiclasses](http://en.wikipedia.org/wiki/Multiclass_classification)
	* [Aprendizado não supervisionado](http://en.wikipedia.org/wiki/Unsupervised_learning)

Veja também:

* [Página de dicas úteis sobre algoritmos de Aprendizado de Máquina do Microsoft Azure](machine-learning-algorithm-cheat-sheet.md) (Microsoft).

* [Escolhendo o avaliador certo](http://scikit-learn.org/stable/tutorial/machine_learning_map/) ([scikit-learn](http://scikit-learn.org/stable/index.html)).


<!-- Module References -->
[k-means-clustering]: https://msdn.microsoft.com/library/azure/5049a09b-bd90-4c4e-9b46-7c87e3a36810/
[train-clustering-model]: https://msdn.microsoft.com/library/azure/bb43c744-f7fa-41d0-ae67-74ae75da3ffd/
[bayesian-linear-regression]: https://msdn.microsoft.com/library/azure/ee12de50-2b34-4145-aec0-23e0485da308/
[boosted-decision-tree-regression]: https://msdn.microsoft.com/library/azure/0207d252-6c41-4c77-84c3-73bdf1ac5960/
[decision-forest-regression]: https://msdn.microsoft.com/library/azure/562988b2-e740-4e3a-8131-358391bad755/
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
[neural-network-regression]: https://msdn.microsoft.com/library/azure/d7ee222c-669f-4200-a576-a761a9c1a928/
[ordinal-regression]: https://msdn.microsoft.com/library/azure/ffb557f8-dc7f-44bd-8fd0-b25666dd23f1/
[poisson-regression]: https://msdn.microsoft.com/library/azure/80e21b9d-3827-40d8-b733-b53148becbc2/

 

<!---HONumber=July15_HO4-->