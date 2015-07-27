<properties 
	pageTitle="Página de dicas úteis de algoritmo de Aprendizado de Máquina | Microsoft Azure" 
	description="Uma página de dicas úteis de algoritmo de Aprendizado de Máquina que pode ser impressa o ajuda a escolher o algoritmo certo para o seu modelo de previsão no Estúdio de Aprendizado de Máquina do Azure."
	services="machine-learning"
	documentationCenter="" 
	authors="brohrer" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/18/2015" 
	ms.author="brohrer;garye" />


# Página de dicas úteis do algoritmo para Aprendizado de Máquina para o Estúdio de Aprendizado de Máquina do Microsoft Azure

A **Página de dicas úteis do algoritmo para Aprendizado de Máquina** ajuda a escolher o melhor algoritmo de Aprendizado de Máquina para suas soluções de análise preditiva na biblioteca de algoritmos do Aprendizado de Máquina do Microsoft Azure. A folha de consulta faz perguntas sobre a natureza dos dados e o problema que você está tentando resolver e sugere um algoritmo como tentativa.

O [Estúdio de Aprendizado de Máquina do Azure](https://studio.azureml.net/) vem com um grande número de algoritmos de aprendizado de máquina para as suas soluções analíticas preditivas. Esses algoritmos entram nas categorias gerais de aprendizado de máquina de ***regressão***, ***classificação***, ***clustering***, e ***detecção de anomalias***, e cada um foi projetado para atender a um tipo diferente de problema de aprendizado de máquina.



## Baixe a página de dicas úteis de algoritmo de Aprendizado de Máquina

Baixe a página de dicas úteis de algoritmo de Aprendizado de Máquina e obtenha ajuda para descobrir como escolher um algoritmo de Aprendizado de Máquina para a sua solução. Para mantê-lo próximo, você pode imprimir os roteiros em tamanho tabloide (11 x 17 polegadas).

**Baixe a folha de consulta daqui: [Folha de consulta de algoritmos do Aprendizado de Máquina do Microsoft Azure](http://download.microsoft.com/download/A/6/1/A613E11E-8F9C-424A-B99D-65344785C288/microsoft-machine-learning-algorithm-cheat-sheet-v2.pdf)**

![Página de dicas úteis de algoritmo de Aprendizado de Máquina: Saiba como escolher um algoritmo de Aprendizado de Máquina.][cheat-sheet]

[cheat-sheet]: ./media/machine-learning-algorithm-cheat-sheet/machine-learning-algorithm-cheat-sheet-microsoft-azure.png



<!-- This is now covered in the first footnote below
[Azure Machine Learning Studio](https://studio.azureml.net/) gives you the flexibility to experiment - 
try one algorithm, and if you're not satisfied with the results, try another.
Here's an example from the [Azure Machine Learning Gallery](http://gallery.azureml.net) of an experiment that tries several algorithms against the same data and compares the results:
[Compare Multi-class Classifiers: Letter recognition](http://gallery.azureml.net/Details/a635502fc98b402a890efe21cec65b92).
-->

## Obter ajuda com algoritmos 

* Para uma discussão mais profunda dos diferentes tipos de algoritmos de aprendizado de máquina e como eles são usados, consulte [Como escolher um algoritmo no Aprendizado de Máquina do Azure](machine-learning-algorithm-choice.md).
* Para obter uma lista por categoria de todos os algoritmos de aprendizado de máquina disponíveis no Estúdio de Aprendizado de Máquina, consulte [Inicializar modelo][initialize-model] na Ajuda de módulo e no algoritmo do Estúdio de Aprendizado de Máquina.
* Para obter uma lista completa de algoritmos e módulos no Estúdio de Aprendizado de Máquina, consulte a [Lista de A-Z de módulos do Estúdio de Aprendizado de Máquina](https://msdn.microsoft.com/library/azure/dn906033.aspx) na Ajuda de módulo e no algoritmo de Estúdio de Aprendizado de Máquina.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## Roteiro para anotações e definições terminológicas para o algoritmo de Aprendizado de Máquina

* As sugestões oferecidas nessa página de dicas úteis de algoritmo são aproximadas às regras de bolso. Algumas podem ser ajustadas e algumas podem ser flagrantemente violadas. Isso serve para sugerir um ponto de partida. Não tenha medo de executar uma comparação entre vários algoritmos em seus dados. Simplesmente não existe substituto para entender os princípios de cada algoritmo e o sistema que gerou seus dados. 

* Cada algoritmo de aprendizado de máquina tem seu próprio estilo ou *tendência indutiva*. Para um problema específico, vários algoritmos podem ser apropriados e um algoritmo pode ser mais adequado do que outros. No entanto, saber antecipadamente qual será a opção mais adequada nem sempre é possível. Em casos como esse, vários algoritmos estão listados juntos na folha de consulta. Uma estratégia apropriada seria um algoritmo e se os resultados ainda não estiverem satisfatórios, tentar os outros. Aqui está um exemplo da [Galeria de Aprendizado de Máquina do Azure](http://gallery.azureml.net/) de um experimento que tenta vários algoritmos nos mesmos dados e os compara aos resultados: [Comparar classificadores multiclasse: reconhecimento de letra](http://gallery.azureml.net/Details/a635502fc98b402a890efe21cec65b92).

* Há três categorias principais de aprendizado de máquina: **aprendizado supervisionado**, **aprendizado sem supervisão**, e **aprendizado de reforço**.

  * Em **aprendizado supervisionado**, cada ponto de dados é rotulado ou associado a uma categoria ou um valor de interesse. Um exemplo de um rótulo categórico é atribuir uma imagem como, por exemplo, um gato ou um cão. Um exemplo de um rótulo de valor é o preço de venda associado a um carro usado. A meta do aprendizado supervisionado é estudar muitos exemplos de rótulos como esses e poder fazer previsões sobre pontos de dados futuros - por exemplo, identificar novas fotos com o animal correto ou atribuir preços de venda precisos a outros carros usados. Este é um tipo popular e útil de aprendizado de máquina. Todos os módulos do Aprendizado de Máquina do Azure são algoritmos de aprendizado supervisionados, exceto [Cluster K-means][k-means-clustering].

  * No **aprendizado sem supervisão**, pontos de dados não têm rótulos associados a eles. Em vez disso, a meta de um algoritmo de aprendizado sem supervisão é organizar os dados de alguma forma ou descrever sua estrutura. Isso pode significar agrupá-los em clusters, como faz o K-means, ou encontrar diferentes maneiras de consultar dados complexos para que eles pareçam mais simples.

  * Em **aprendizado de reforço**, o algoritmo escolhe uma ação em resposta a cada ponto de dados. É uma abordagem comum em robótica, em que o conjunto de leituras do sensor, em um ponto no tempo, é um ponto de dados e o algoritmo deve escolher a próxima ação do robô. Também é um ajuste natural para aplicativos da Internet das Coisas. O algoritmo de aprendizado também recebe um sinal de recompensa pouco tempo depois, indicando se a decisão foi boa. Com base nisso, o algoritmo modifica sua estratégia para alcançar a recompensa mais alta. Atualmente, não há nenhum módulo de algoritmo de reforço de aprendizado no Aprendizado de Máquina do Azure.

* **Métodos bayesianos** fazem a previsão de pontos de dados estatisticamente independentes. Isso significa que a variabilidade não modelada em um ponto de dados não está correlacionada com outros, ou seja, não pode ser prevista. Por exemplo, se os dados que estão sendo registrados são o número de minutos até que o próximo metrô chegue, duas medidas feitas com um dia de diferença são estatisticamente independentes. No entanto, duas medidas feitas com um minuto de diferença não são estatisticamente independentes - o valor de uma é uma alta previsão do valor da outra.

* **Regressão de árvore de decisão aumentada** aproveita a sobreposição de recurso ou interação entre os recursos. Isso significa que, em qualquer ponto de dados específico, o valor de um recurso é, de alguma forma, previsão do valor de outro. Por exemplo, em dados diários de alta/baixa temperatura, saber a baixa temperatura do dia permite que você faça uma estimativa razoável da alta temperatura. As informações contidas nos dois recursos são, de alguma forma, redundantes.

* A classificação de dados em mais de duas categorias pode ser feita usando um classificador inerentemente multiclasse ou pela combinação de um conjunto de classificadores de duas classes em um **conjunto**. Na abordagem de conjunto, há um classificador de duas classes separado para cada classe - cada uma separa os dados em duas categorias: "esta classe" e "não é esta classe". Em seguida, os classificadores votam na atribuição correta do ponto de dados. Isso é o princípio operacional subjacente a [Uma vs todas as multiclasses][one-vs-all-multiclass].

* Vários métodos, incluindo regressão logística e o computador de ponto Bayes assumem **limites de classe linear**, ou seja, os limites entre classes são aproximadamente linhas retas (ou hiperplanos no caso mais geral). Geralmente, essa é uma característica dos dados que você não conhece até depois de tentar separá-los, mas é algo que normalmente pode ser aprendido visualizando com antecedência. Se os limites de classe parecerem muito irregulares, fique com as árvores de decisão, selvas de decisão, computadores de vetor de suporte ou redes neurais.

* Redes neurais podem ser usadas com variáveis categóricas, criando uma **variável fictícia** para cada categoria e configurando-a como 1 em casos em que a categoria se aplica, e 0 em casos em que não se aplica.


<!-- This is how you can add a link to the image in HTML. Don't know how to do this in markdown.
<a href="http://download.microsoft.com/download/A/6/1/A613E11E-8F9C-424A-B99D-65344785C288/microsoft-machine-learning-algorithm-cheat-sheet.pdf">
<img src="C:\Users\garye\azure-content-pr\articles\media\machine-learning-algorithm-cheat-sheet\cheat-sheet-small.png">
</a>
-->

<!-- Module References -->
[initialize-model]: https://msdn.microsoft.com/library/azure/0c67013c-bfbc-428b-87f3-f552d8dd41f6/
[k-means-clustering]: https://msdn.microsoft.com/library/azure/5049a09b-bd90-4c4e-9b46-7c87e3a36810/
[one-vs-all-multiclass]: https://msdn.microsoft.com/library/azure/7191efae-b4b1-4d03-a6f8-7205f87be664/
 

<!---HONumber=July15_HO2-->