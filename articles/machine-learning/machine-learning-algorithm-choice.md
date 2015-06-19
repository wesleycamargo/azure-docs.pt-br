<properties 
	title="" 
	pageTitle="Como escolher um algoritmo no Aprendizado de Máquina do Azure | Azure" 
	description="Explica como escolher um algoritmo no Aprendizado de Máquina do Azure." 
	services="machine-learning"
	documentationCenter="" 
	authors="bradsev" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/9/2015" 
	ms.author="bradsev" />


# Como escolher um algoritmo no Aprendizado de Máquina do Azure

Este tópico explica alguns aspectos de básicos da abordagem de aprendizado de máquina e descreve, em especial, como selecionar um algoritmo apropriado para analisar um determinado tipo de dado, responder a uma pergunta apresentada, realizar uma tarefa especificada ou fornecer critérios para tomar uma decisão.  Ao usar a o aprendizado de máquina para realizar análises, normalmente ficamos diante de duas perguntas: 

* Qual tipo de análise precisamos para alcançar nossos objetivos com os dados disponíveis? 
* Qual é o algoritmo ou modelo mais apropriado a ser usado para fazer essa análise?

Três tipos de análises e seus casos de uso são discutidos e comparados: 

* classificação 
* regressão 
* clustering

Os vários algoritmos para cada um desses tipos de análise e os módulos que os contêm e que estão disponíveis no Aprendizado de Máquina do Azure também são discutidos. 


<a name="anchor-1"></a>
##**Aprendizado de Máquina**

O Aprendizado de Máquina é uma disciplina que estuda uma classe de algoritmos controlada por dados no sentido em que eles são projetados para aprender com os dados e não impõem um modelo específico e predeterminado para testar em relação aos dados.  A ideia é adquirir conhecimento por indução examinando os padrões em um conjunto de dados em vez de usar o que é normalmente conhecido como o método hipotético-dedutivo em que você tentar adivinhar o modelo apropriado para todo o conjunto de dados primeiro e depois o testa empiricamente.  Esse tipo de aprendizado dos dados vem em duas versões: aprendizado supervisionado e não supervisionado. 

<a name="anchor-2"></a>
##**Aprendizado Supervisionado**  

O aprendizado supervisionado requer que a variável de destino seja bem definida e que recebem um número suficiente de seus valores. 

O aprendizado supervisionado é o tipo de aprendizado que ocorre quando os resultados de saída corretos (ou variáveis de destino) para as instâncias de treinamento que serão entradas são conhecidos.  O objetivo do treinamento de um algoritmo de aprendizado de máquina é encontrar o modelo (ou seja, uma regra ou função) que mapeia as entradas para os valores de saída conhecidos.  Isso equivale a ter um supervisor que pode informar o agente algorítmico se ele está ou não mapeando as entradas corretamente para as saídas.  Depois que o processo de aprendizado for concluído e termos um modelo funcional, ele pode ser aplicado aos novos dados de entrada para prever a saída esperada em que, diferentemente do conjunto de dados de treinamento, o valor de destino não é conhecido previamente.

A natureza da variável de destino determina o tipo de modelo.

![screenshot_of_experiment](./media/machine-learning-algorithm-choice/help9.png)

Há duas categorias de análise que empregam o aprendizado supervisionado: classificação e regressão.  O aprendizado supervisionado é bastante comum em *classification problems* porque o objetivo frequentemente é que o computador tenha um sistema de classificação que criamos.  As respostas normalmente são apenas alguns valores (rótulos) conhecidos, como 'true' ou 'false'.  Algoritmos de classificação aplicam-se aos valores de resposta nominais, não ordinais.  O reconhecimento de dígito é um exemplo comum de aprendizado de classificação.  Normalmente, o aprendizado de classificação é apropriado para qualquer problema em que deduzir uma classificação é útil e a classificação é fácil de determinar.

No aprendizado supervisionado, as variáveis investigadas podem ser divididas em dois grupos: variáveis explicativas (também chamadas de prognóstica) e dependentes (também chamadas de resposta).  O objetivo da análise é especificar uma relação entre as variáveis de explicativas e dependente, como é feito em *regression analysis*. Os valores da variável dependente devem ser conhecidos para uma parte suficientemente grande do conjunto de dados.  Na regressão, a variável de resposta ou saída assume valores contínuos, como km/litro para de um determinado carro, a idade de uma pessoa etc.

O aprendizado supervisionado também é a técnica mais comum para redes neurais de treinamento e árvores de decisão.  Ambas as técnicas são altamente dependentes das informações fornecidas pelas classificações predeterminadas. 

* No caso de redes neurais, a classificação é usada para determinar o erro da rede e, em seguida, ajustar a rede para minimizá-lo. 
* No caso de árvores de decisão, as classificações são usadas para determinar quais atributos fornecem a maioria das informações que podem ser usadas para resolver o quebra-cabeça de classificação.  

Ambos os exemplos requerem certa "supervisão" no sentido de que eles dependem das classificações predeterminadas fornecidas. 

O reconhecimento de fala usando modelos de Markov ocultos e redes Bayesianas também contam com elementos de supervisão para ajustar os parâmetros para minimizar o erro em determinadas entradas. 

<a name="anchor-3"></a>
##**Aprendizado não Supervisionado**

No aprendizado de máquina, o problema do aprendizado não supervisado é localizar padrões ou estruturas ocultas em dados sem rótulo.  O modelo não é fornecido com os "resultados corretos" para um conjunto de dados de treinamento.  Como os exemplos fornecidos para o aprendiz não têm rótulo, não há nenhum sinal de erro ou de sucesso para avaliar uma solução em potencial.  O objetivo é que o computador aprenda a fazer algo que nós não informamos explicitamente como fazer!  Em situações de aprendizado não supervisado, todas as variáveis são tratadas da mesma maneira.  Não há nenhuma distinção entre as variáveis dependentes e explicativas.  No entanto, ainda há alguns objetivo a alcançar.  Esse objetivo pode ser tão geral quanto a redução de dados ou tão específico quanto localizar clusters. 

O Aprendizado de Máquina do Azure pode executar ambos os aprendizados com ou sem supervisão por meio de **Classificação**, **Clustering** e **Regressão**.

   ![screenshot_of_experiment](./media/machine-learning-algorithm-choice/help2.png)

<a name="anchor-4"></a>
##**Clustering**  
Clustering é um exemplo de aprendizado sem supervisão.  Nsse tipo de aprendizado, o objetivo é encontrar semelhanças nos dados de treinamento e particionar o conjunto de dados em subconjuntos delimitados por essas semelhanças.  A expectativa de que clusters mais significativos sejam descobertos esses procedimentos controlados por dados estão de acordo com nossa classificação intuitiva que geralmente, mas nem sempre, é satisfeita. 

Embora o algoritmo não atribua nomes apropriados para esses clusters, ele pode produzi-los e usá-los para prever as semelhanças esperadas nos novos exemplos, classificando-os em clusters mais apropriados.  Essa é uma abordagem controlada por dados que pode funcionar bem quando há dados suficientes.  Por exemplo, algoritmos de filtragem de informações sociais, como aqueles usados pela Amazon.com recomendar livros, baseiam-se em localizar grupos semelhantes de pessoas e, em seguida, atribuir novos usuários a esses grupos para fazer recomendações.

O algoritmo de clustering disponível no Aprendizado de Máquina do Azure é o clustering de média K.

![screenshot_of_experiment](./media/machine-learning-algorithm-choice/help10.png)

Média K é um dos algoritmos de aprendizado sem supervisão mais simples que resolvem um problema conhecido de clustering.  O algoritmo KMeans agrupa os dados ao tentar separar exemplos em N grupos de variância igual, minimizando um critério conhecido como a inércia ou a soma dos quadrados no cluster.  Esse algoritmo requer que o número de clusters seja especificado.  Ele se adapta bem a grandes números de amostras e foi usado em uma grande variedade de áreas de aplicações em diversos campos diferentes.

O módulo **Clustering de Média K** que implementa o algoritmo de média K retorna um modelo de clustering de média K não treinado que pode ser passado para o módulo **Modelo de Agrupamento de Treinamento** para treinamento.

![screenshot_of_experiment](./media/machine-learning-algorithm-choice/k4.png)

Esta figura mostra quais são as opções a serem configuradas ao usar Clustering de Média K.  O método média K localiza um número especificado de clusters para um conjunto de pontos de dados de dimensão D.  Começando com um *initial set of K centroids*, o método usa o algoritmo de Lloyd iterativamente para refinar os locais de centroides . O algoritmo é encerrado quando os centroides estabilizam ou quando um *specified number of iterations* é concluído.
O módulo inicializa uma matriz de K-por-D com os centroides finais que definem os clusters K encontrados nos pontos de dados de N.  O algoritmo também usa um vetor de tamanho N com a atribuição de cada ponto de dados para um dos clusters K.
Se houver um número específico de clusters (K) para localizar, o módulo atribui os pontos de dados K primeiro aos clusters K.
Este módulo também aceita ou gera pontos iniciais para definir sua configuração de cluster inicial.
*Métrica* define o método usado para medir a distância entre um ponto de dados e o centroide.
Cada ponto de dados é atribuído ao cluster que tem o centroide mais próximo ao ponto de dados.  Por padrão, o método utiliza o *Euclidean metric*.  Você pode especificar o *cosine metric* como uma métrica alternativa para o método.
Observe que o método média K só pode encontrar uma configuração de cluster local ideal para um conjunto de dados.  O método poderia localizar uma configuração diferente, talvez seja melhor, em caso de configuração inicial diferente.

<a name="anchor-5"></a>
##**Classificação**  
Na análise de classificação, dividimos os exemplos em classes e usamos um conjunto treinado de dados rotulados anteriormente.  A técnica é usada para prever associação ao grupo para instâncias de dados. 
No Aprendizado de Máquina do Azure, estão disponíveis os seguintes algoritmos de classificação.

![screenshot_of_experiment](./media/machine-learning-algorithm-choice/help3.png)

Os *Two-Class algorithms* são usados para variáveis de resposta binária (sim ou não, 0 ou 1, verdadeiro ou falso, etc) enquanto *Multiclass algorithms* são usados para qualquer variável de resposta nominal que classifica instâncias em mais de duas classes

### Diretrizes para selecionar um algoritmo de classificação
Essa longa lista de algoritmos dá origem a uma série de perguntas: 

* Como saber quais dessas muitos classificadores é o melhor a usar em um determinado conjunto de dados? 
* Há casos em que um deles é uma opção "natural"? 
* Quais são os princípios para escolhê-lo?

Uma abordagem recomendada é testar vários classificadores diferentes, bem como conjuntos de parâmetros diferentes dentro de cada algoritmo e selecionar aquele que melhor usa a validação cruzada.  Aqui estão algumas diretrizes gerais que podem fornecer um local para iniciar esse discussões.  Considere as seguintes questões ao escolher qual algoritmo usar:

**Qual o tamanho dos seus dados de treinamento?**
Se o conjunto de treinamento é pequeno e você pretende treinar um classificador supervisionado, a teoria de aprendizado de máquina diz que você deve inserir a um classificador com classificadores do viés/baixa variação tais como Naive Bayes.  Eles têm uma vantagem sobre classificadores de baixo viés/alta variação como kNN desde que o segundo tende a ser sobreajustado.  Porém, os classificadores de baixo viés/alta variação começam a obter melhores resultados à medida que cresce o conjunto de treinamento (pois apresentam menos erro assintótico), pois classificadores de alta tendência não são potentes o suficiente para fornecer modelos precisos.  Há resultados teóricos e empíricos que indicam que Naive Bayes funciona bem em tais circunstâncias.  Contudo, observe que melhores dados geralmente é melhor do que melhores algoritmos e que criar bons recursos representa uma vantagem significativa.  Se você tiver um grande conjunto de dados, o algoritmo de classificação que você usar pode não importar muito no desempenho de classificação.  Portanto, pode ser melhor escolher o algoritmo com base em  uma boa escalabilidade, velocidade ou facilidade de uso.

**Você precisa treinar gradativamente ou em lotes?** 
Se você precisa atualizar seu classificador com novos dados com frequência (ou se você tem muitos dados), provavelmente desejará usar algoritmos Bayesianos com boa atualização.  Redes neurais e SVM precisam trabalhar nos dados de treinamento em modo de lote.

**Seus dados são exclusivamente categóricos, exclusivamente numéricos ou uma mistura de ambos os tipos?** 
O método Bayesiano funciona melhor com dados categóricos/binomiais.  Árvores de decisão não podem prever valores numéricos.

**Você ou seu público-alvo precisam entender como funciona a classificação?**  Use o método Bayesiano ou Árvores de Decisão, pois eles podem ser facilmente explicados para a maioria das pessoas.  Redes neurais e SVM são "caixas pretas" no sentido que você realmente não consegue ver como eles classificam os dados.

**Em quanto tempo sua classificação precisa ser gerada?** 
SVMs são rápidos quando se trata de classificação, pois precisam apenas determinar qual em lado da "linha" estão seus dados.  Árvores de decisão podem ser lentas, especialmente quando são complexas (por exemplo, muitas ramificações).

**Qual o nível de complexidade apresentado ou exigido pelo problema?** Redes neurais e SVMs podem trabalhar com classificação complexa não linear.

### Vantagens e desvantagens dos algoritmos de classificação
Cada um desses algoritmos de classificação tem algumas vantagens e  algumas desvantagens:

<a name="anchor-5a"></a>
**Vantagens e desvantagens de regressão logística:**   
A análise de regressão logística baseia-se no cálculo da probabilidade do resultado como a proporção da probabilidade de ter o resultado dividido pela probabilidade de não tê-lo.  O modelo logístico  é paramétrico, tendo como vantagem fornecer informações sobre o impacto de cada variável de prognóstico na variável de resposta.  Com interpretações probabilísticas naturais disponíveis (ao contrário de árvores de decisão ou SVMs), podemos facilmente atualizar nosso modelo para receber novos dados.  Há muitas maneiras de regularize nosso modelo e você não precisa se preocupar muito com os recursos que estão sendo relacionados, como faria com Naive Bayes.  A regressão logística é útil se quisermos 

* uma estrutura probabilística que facilita ajustar os limites de classificação para dizer quando não temos certeza ou para obter intervalos de confiança 
* se esperamos receber mais dados de treinamento no futuro que queremos poder incorporar rapidamente ao modelo. 

A regressão logística funciona melhor do que as árvores de decisão para dados de alta dimensão.  Por exemplo, na classificação de texto, você pode ter mais de 100 mil documentos com 500 mil palavras diferentes (recursos).  Uma regra simples como o aprendizado de uma hiperplano linear é melhor, pois as Árvores de Decisão têm muito mais graus de liberdade e estão sujeitas ao sobreajuste.  Você poderia usar a seleção de recursos para usar uma árvore de decisão em dados de texto, mas muitas informações valiosas seriam perdidas para classificação de texto selecionando um subconjunto muito reduzido de recursos.  Quando os modelos são usados com dados altamente dimensionais, é muito fácil para erros de variação crescerem, por isso e modelos simples com viés maior são uma opção melhor. 

Uma desvantagem da regressão logística é que ela é instável quando um prognóstico quase poderia explicar a variável de resposta, porque o coeficiente dessa variável se tornará muito grande.

<a name="anchor-5b"></a>
**Vantagens e desvantagens das Árvores de Decisão:**   
[Árvores de Decisão](http://research.microsoft.com/pubs/155552/decisionForests_MSR_TR_2011_114.pdf) são fáceis de interpretar e explicar.  Elas lidar facilmente com interações de recursos e são não paramétrica, assim você não precisa se preocupar com exceções ou se os dados são separáveis linearmente (por exemplo, árvores de decisão podem facilmente tratar de casos em que onde a classe A está no inferior de algum recurso x, a classe B em nível intermediário do recurso x e A novamente no topo).  Árvores de decisão geram saídas como regras juntamente com métricas como Suporte, Confiança e Comparação. 

Uma desvantagem é que as árvores de decisão não dão suporte ao treinamento online, então você precisará recompilar sua árvore quando novos exemplos chegarem.  Outra desvantagem é que elas sofrem sobreajuste com facilidade, mas é aí que métodos de ensemble como florestas aleatórias (ou árvores aprimoradas) podem ajudar.  Além disso, florestas aleatórias costumam ser o ideal para muitos problemas na classificação.  Normalmente elas apresentam resultados ligeiramente melhores SVMs: elas são rápidas e escaláveis, e você não precisa se preocupar sobre como ajustar vários parâmetros como faria com SVMs.


<a name="anchor-5c"></a>
**Vantagens e desvantagens de SVMs:**   
SVMs (Máquinas de Vetor de Suporte) são eficazes em espaços altamente dimensionais.  Mesmo nos casos em que o número de dimensões é maior que o número de amostras, ele ainda é eficaz.  No entanto, se o número de recursos for muito maior do que o número de amostras, o método provavelmente demonstrará desempenho insatisfatório.  Ele também faz uso eficiente de memória porque usa um subconjunto dos pontos de treinamento na função de decisão (conhecida como vetores de suporte).  É muito versátil: funções de Kernel diferentes podem ser especificadas para a função de decisão.  Os kernels comuns são fornecidos, mas também é possível especificar kernels personalizados.  A função de kernel é usada para transformar exemplos de treinamento pouco dimensionais em dimensões maiores, o que é útil para problemas de separabilidade linear. 

No entanto, SVMs não fornecem estimativas de probabilidade diretamente.  Isso é calculado usando uma cara validação cruzada quíntupla.  Com a alta precisão, garantias teóricas sólidas em relação a sobreajuste e um kernel apropriado, elas podem trabalhar bem mesmo se os dados não forem separáveis linearmente no espaço de recurso base.  São especialmente populares em problemas de classificação de texto em que espaços altamente dimensionais são a norma.  Ao contrário de florestas, SVMs foram criadas como classificadores de duas classes, embora recentemente tenham sido adaptadas para trabalhar com várias classes.  Podemos usar um treinamento como "um contra o resto" para criar um classificador multiclasse, o que pode não ser o ideal.  Como SVMs somente podem trabalhar com saídas de duas classes (ou seja, uma variável de saída categórica com variedade 2), com N classes, aprende N SVMs (por exemplo, SVM 1 aprende "Output==1" versus "Output != 1", SVM 2 aprende "Output==2" versus "Output != 2", ..., SVM N aprende "Output==N" versus "Output != N").  Em seguida, para prever a saída para uma nova entrada, ele apenas prevê com cada SVM e descobre qual coloca a previsão mais adiante na região positiva.

<a name="anchor-5d"></a>
**Vantagens e desvantagens de Naive Bayes:**   
[Naive Bayes](http://www.aaai.org/Papers/FLAIRS/2004/Flairs04-097.pdf) (NB) são uma opção popular de classificadores para problemas de classificação.  Elas supõem que os recursos são independentes, pois é o que compõe a técnica 'naive'.  Se a suposição de independência condicional de NB provar-se verdadeira, um classificador Naive Bayes convergirá mais rapidamente do que modelos discriminadores como a regressão logística.  Portanto, nesses casos, você precisa de menos dados de treinamento. 

Mesmo que a suposição de NB não se comprove, um classificador NB normalmente ainda faz um excelente trabalho na prática.  Na verdade Bayes não é bom apenas quando os recursos são independentes, mas também quando as dependências dos recursos uns dos outros são similares entre eles.  Portanto, NB é uma boa opção se quiser algo com desempenho fácil e rápido.

Sua principal desvantagem é que ele não pode aprender as interações entre os recursos (por exemplo, não é possível aprender que, embora você adore filmes com Brad Pitt e Tom Cruise, você odeia filmes em que eles estão juntos).


<a name="anchor-5e"></a>
**Um contra todos:**  
Um contra todos é uma estratégia para reduzir o problema da classificação multiclasse para um conjunto de vários problemas de classificação binária.  A estratégia envolve treinar um classificador único por classe com os exemplos dessa classe como exemplos positivos e todos os outros exemplos como negativos.  Essa estratégia requer classificadores para produzir uma pontuação de confiança com valor real para a sua decisão, em vez de apenas um rótulo de classe base.  Rótulos de classes discretas sozinhos podem resultar em ambiguidades, em que várias classes são previstas para um exemplo simples.	


<a name="anchor-6"></a>
##**Regressão**  
Na análise de regressão, podemos prever novos valores com base na inferência passada.  Os novos valores para uma variável dependente são calculados com base no valor de um ou mais atributos medidos.  Os vários algoritmos de regressão disponíveis no Aprendizado de Máquina do Azure são:

![screenshot_of_experiment](./media/machine-learning-algorithm-choice/help4.png)

Dependendo do caso de uso e dos dados em mãos, escolhemos um algoritmo/módulo em vez de outro.  Abaixo descrevemos alguns algoritmos de regressão e seus casos de uso principais.

<a name="anchor-6b"></a>
**Regressão Linear Bayesiana**                      
A regressão linear Bayesiana é uma abordagem de regressão linear em que a análise estatística é realizada no contexto de inferência Bayesiana.  Resultados explícitos estão disponíveis para as distribuições de probabilidade posteriores dos parâmetros do modelo quando o modelo de regressão tem erros distribuídos normais e uma determinada forma de distribuição anterior pode ser considerada.

<a name="anchor-6f"></a>
**Regressão de Árvore de Decisão Aprimorada**  
A regressão calcula uma relação entre o prognóstico e as variáveis de resposta.  A estrutura de árvore de regressão é semelhante a uma árvore de classificação.  Nós terminais são valores de função previstos (modelo).  Os valores previstos são limitados aos valores em nós terminais.  Algumas das vantagens de usar árvores de decisão são: 

* é fácil interpretar as regras de decisão 
* eles são não paramétricas, por isso é fácil incorporar uma gama de camadas de dados numéricos ou categóricos e não é necessário selecionar dados de treinamento unimodais 
* elas são robustas em relação a exceções nos dados de treinamento 
* a classificação é rápida depois que as regras são desenvolvidas 

Algumas desvantagem do uso de árvores de decisão é que elas tendem a sobreajustar os dados de treinamento, podendo gerar resultados fracos quando aplicadas ao conjunto de dados completo e não é possível prever além dos limites mínimos e máximo da variável de resposta nos dados de treinamento.

<a name="anchor-6g"></a>
**Regressão de Floresta de Decisão**  
Florestas de decisão pode ser usadas para aplicativos de classificação (variáveis categóricas) ou regressão (variáveis contínuas).  Florestas de regressão podem ser usadas para a regressão não linear de variáveis dependentes se fornecidas entradas independentes.  Entradas e saídas podem ser multidimensionais.  Florestas de regressão não são usadas tanto quanto seus equivalentes de classificação.  A principal diferença é que o rótulo de saída das florestas de decisão a ser associado a dados de entrada, portanto os rótulos de treinamento, deve ser contínuo.  Consequentemente, a função objetiva deve ser devidamente adaptada.  Florestas de regressão têm várias das vantagens das florestas de classificação, tais como eficiência e flexibilidade.

<a name="anchor-6a"></a>
**Regressão Linear**  
A regressão linear é amplamente usada para modelar a relação entre uma variável dependente escalar Y e uma ou mais variáveis explicativas denotadas X. Ela pode ser, e geralmente é, aplicada à previsão, antecipação ou redução.  Ela pode ser usada de acordo com um modelo de previsão para um conjunto de dados observado de valores de Y e X.  A regressão linear pressupõe que a estrutura subjacente de Y é uma combinação linear das variáveis X.  Se um valor adicional de X é recebido sem seu valor y, o modelo de regressão linear ajustada pode ser usado para prever esse valor de Y.  Modelos de regressão linear normalmente são ajustados usando a abordagem dos quadrados mínimos, mas também existem outras opções para medir qual é a melhor.

<a name="anchor-6c"></a>
**Regressão de Rede Neural**  
Redes neurais são uma ferramenta útil de estatística de regressão não paramétrica.  A regressão não paramétrica resolve o problema de tentar ajustar um modelo a uma variável Y em um conjunto de variáveis explicativas possíveis X1; :  :  :  ;XP e onde a relação entre X e Y pode ser mais complicada do que uma relação linear simples.

<a name="anchor-6d"></a>
**Regressão Ordinal**   
A regressão ordinal é um tipo de análise de regressão usada para modelar ou prever uma variável dependente ordinal.  Para variáveis dependentes ordinais, você pode classificar os valores, mas a distância real entre categorias é desconhecida.  Somente a ordenação relativa entre valores diferentes é significativa.  Como os rótulos ou valores de destino têm uma ordem ou classificação natural, qualquer coluna numérica pode ser usada como um destino ordinal.  A ordem natural dos números é usada para classificá-los.  Doenças são graduadas em escalas de menos grave para mais graves.  Entrevistados de pesquisas escolhem respostas sobre escalas desde concordo totalmente até discordo totalmente.  Os alunos são classificados em escala de A a F. Essencialmente, porém, a regressão ordinal é uma extensão de regressão logística que se baseia no modelo de possibilidades proporcional.


<a name="anchor-6e"></a>
**Regressão Poisson**  
A regressão Poisson é frequentemente usada para modelar dados de contagem.  A regressão Poisson presume que a variável de resposta tem uma distribuição Poisson.  Dados com distribuição de Poisson têm valores inteiros intrínsecos (discretos e positivo), o que faz sentido para dados de contagem.  Considerando um conjunto de dados de treinamento, a regressão Poisson tenta encontrar os valores ideais maximizando a probabilidade logarítmica dos parâmetros fornecidos, dadas as entradas.  A probabilidade dos parâmetros é a probabilidade de que os dados de treinamento foram amostrados de uma distribuição com esses parâmetros.  Por exemplo, a regressão Poisson seria útil para: 

* Modelar o número de gripes associadas a voos de avião 
* Estimar o número de chamadas relacionadas a um evento ou uma promoção 
* Criando tabelas de contingência



<!--HONumber=49--> 