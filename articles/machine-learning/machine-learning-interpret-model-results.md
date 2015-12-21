<properties 
	pageTitle="Como interpretar resultados de modelo no Aprendizado de Máquina | Microsoft Azure" 
	description="Como escolher o parâmetro ideal definido para um algoritmo usando e visualizando saídas do modelo de pontuação." 
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
	ms.date="12/04/2015" 
	ms.author="bradsev" />


# Como interpretar os resultados do modelo no Aprendizado de Máquina do Azure 
 
**Compreendendo e Visualizando a saída do “Modelo de pontuação”** Este tópico explica como visualizar e interpretar os resultados de previsão no Estúdio de Aprendizado de Máquina do Microsoft Azure. Depois de ter treinado um modelo e feito previsões sobre ele ("pontuado o modelo"), você precisa compreender e interpretar o resultado da previsão que obteve.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

Há quatro tipos principais de modelos de aprendizado de máquina no Aprendizado de Máquina do Azure:

* classificação
* clustering
* regressão
* sistemas de recomendação

Os módulos usados fazem previsão desses módulos, chamados de "pontuação", com alguns dados de teste fornecidos, são eles:

* O módulo [Modelo de pontuação][score-model] para classificação e regressão, 
* O módulo [Atribuir aos Clusters][assign-to-clusters] para clustering 
* [Recomendação da Caixa da Pontuação][score-matchbox-recommender] para sistemas de recomendação 
 
Este documento explica como interpretar os resultados de previsão para cada um desses módulos. Para obter uma visão geral desses tipos de modelos, consulte [Como escolher parâmetros para otimizar seus algoritmos no Aprendizado de Máquina do Azure](machine-learning-algorithm-parameters-optimize.md).

Este tópico aborda a interpretação de previsão, mas não a avaliação do modelo. Para obter mais informações sobre como avaliar o seu modelo, consulte [Como avaliar o desempenho do modelo no Aprendizado de Máquina do Azure](machine-learning-evaluate-model-performance.md).

Se você for novo no Aprendizado de Máquina do Azure e precisar de ajuda sobre como criar um teste simples para começar, consulte [Criar um experimento simples no Estúdio de Aprendizado de Máquina do Azure](machine-learning-create-experiment.md) no Estúdio de Aprendizado de Máquina do Azure.

##Classificação
Há duas subcategorias de problemas de classificação:

* problemas com apenas duas classes (classificação de duas classes ou binária) 
* problemas com mais de duas classes (Classificação multiclasse) 

O Aprendizado de Máquina do Azure tem diferentes módulos para lidar com cada um desses tipos de classificação. Mas as maneiras de interpretar os resultados de previsão são muito semelhantes. Vamos falar primeiro sobre os problemas de classificação de duas classes e, em seguida, solucionar problemas de classificação multiclasse.

###Classificação de duas classes
**Teste de exemplo**

Um exemplo de problema de classificação de duas classes é a classificação de flores íris: a tarefa é classificar as flores íris com base em seus recursos. Observe que o conjunto de dados íris fornecido no Aprendizado de Máquina do Azure é um subconjunto do popular [conjunto de dados íris](http://en.wikipedia.org/wiki/Iris_flower_data_set) que contém instâncias de apenas 2 espécies de flores (classes 0 e 1). Há quatro recursos para cada flor (comprimento da sépala, largura da sépala, comprimento da pétala e largura da pétala).

![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/1.png)

Figura 1 Experimento do problema de classificação de duas classes da íris

Foi realizado um experimento para resolver esse problema, conforme mostrado na Figura 1. Um modelo de árvore de decisão aumentado das duas classes foi treinado e pontuado. Agora é possível visualizar os resultados da previsão do módulo [Modelo de pontuação][score-model] clicando na porta de saída do módulo [Modelo de pontuação][score-model] e, em seguida, clicando em **Visualizar** no menu exibido. Isso exibirá os resultados da pontuação conforme mostrado na Figura 2.

![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/1_1.png)

![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/2.png)

Figura 2 Visualizar o resultado do modelo de pontuação na classificação de duas classes

**Interpretação de resultado**

Há seis colunas na tabela de resultados. As quatro colunas à esquerda são os quatro recursos. À direita há duas colunas: Rótulos Pontuados e Probabilidades Pontuadas que são os resultados da previsão. A coluna Probabilidades Pontuadas mostra a probabilidade de uma flor pertencer à classe positiva (classe 1). Por exemplo, o primeiro número 0,028571 na coluna significa que há 0,028571 de probabilidade de a primeira flor pertencer à classe 1. A coluna Rótulos Pontuados mostra a classe prevista para cada flor. Isso se baseia na coluna Probabilidades Pontuadas. Se a Probabilidade Pontuada de uma flor for maior que 0,5, ela é prevista como classe 1, caso contrário, ela será prevista como classe 0.

**Publicação de serviço Web**

Depois de os resultados da previsão serem compreendidos e parecem julgados, a experiência pode ser publicada como um serviço Web para que possamos implantá-la em vários aplicativos e ser chamados para obter previsões de classe em qualquer nova flor de íris. Para o procedimento sobre como alterar um teste de treinamento em um teste de pontuação e publicá-lo como um serviço Web, consulte [Publicar o serviço Web de Aprendizado de Máquina do Azure](machine-learning-walkthrough-5-publish-web-service.md). Esse procedimento fornece uma experiência de pontuação conforme mostrado na Figura 3.

![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/3.png)

Figura 3 Experimento de pontuação de problema de classificação de duas classes da íris

Agora, precisamos definir a entrada e a saída para o serviço Web. Obviamente, a entrada é a porta de entrada certa do [Modelo de pontuação][score-model], que é a entrada de recursos de flor de íris. A escolha da saída depende se estamos interessados na classe prevista (Rótulo pontuado), na probabilidade pontuada ou em ambas. Aqui, supõe-se que estamos interessados em ambas. Para selecionar as colunas de saída desejadas, precisamos usar um módulo de [Colunas do projeto][project-columns]. Clicamos no módulo [Colunas do projeto][project-columns], depois no **Seletor de colunas de inicialização** no painel direito e selecionamos **Rótulos pontuados** e **Probabilidades pontuadas**. Depois de configurarmos a porta de saída do módulo [Colunas do projeto][project-columns] e a executarmos novamente, devemos estar prontos para publicar a pontuação do teste como um serviço Web, clicando no botão **PUBLICAR SERVIÇO WEB** na parte inferior. O teste final se parece com a Figura 4.
 
![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/4.png)

Figura 4 Teste de pontuação final do problema de classificação de duas classes da íris

Depois de executar o serviço Web e inserir alguns valores de recursos de uma instância de teste, o resultado retorna dois números. O primeiro número é o rótulo pontuado, o segundo é a probabilidade pontuada. Com uma probabilidade de 0,9655, essa flor é prevista como classe 1.
 
![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/4_1.png)

![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/5.png)

Figura 5 Resultado do Serviço Web da classificação de duas classes de íris

###Classificação multiclasse
**Teste de exemplo**

Nesse experimento, nós vamos executar uma tarefa de reconhecimento de letra como um exemplo de classificação multiclasse. O classificador tentará prever uma determinada letra (classe), dados alguns valores de atributo manuscritos extraídos das imagens manuscritas. Nos dados de treinamento, há dezesseis recursos extraídos de imagens de letra manuscrita. As 26 letras formam as nossas 26 classes. Um teste foi configurado para treinar um modelo de classificação multiclasse para o reconhecimento de letra e para prever o mesmo recurso definido em um conjunto de dados de teste, conforme mostrado na Figura 6.

![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/5_1.png)
 
![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/6.png)

Figura 6 Teste do problema de classificação multiclasse de reconhecimento de letra

Visualize os resultados do módulo [Modelo de pontuação][score-model] clicando à direita/esquerda na porta de saída do módulo [Modelo de pontuação][score-model] e, em seguida, clicando em **Visualizar**, você deverá ver uma janela como na Figura 7.
 
![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/7.png)

Figura 7 Visualizar o resultado do modelo de pontuação na classificação multiclasses

**Interpretação de resultado**

As colunas dezesseis à esquerda representam os valores do recurso do conjunto de teste. As colunas com nomes Probabilidades Pontuadas para a classe "XX" são como a coluna de Probabilidades Pontuadas no caso de duas classes. Elas mostram a probabilidade de que a entrada correspondente se encaixe em uma determinada classe. Por exemplo, para a primeira entrada, há a probabilidade de 0,003571 que seja um "A", a probabilidade de 0,000451 que seja um "B", e assim por diante. A última coluna, Rótulos Pontuados, é a mesma que Rótulos Pontuados no caso de duas classes. Ela seleciona a classe com a maior probabilidade de pontuação como a classe prevista da entrada correspondente. Por exemplo, para a primeira entrada, o rótulo de pontuação é "F", pois ele tem a maior probabilidade de ser um "F" (0,916995).

**Publicação de serviço Web**

Desta vez, em vez de usar as [Colunas do projeto][project-columns] para selecionar algumas colunas como a saída do nosso serviço Web, gostaríamos de obter o rótulo pontuado para cada entrada e a probabilidade do rótulo pontuado. A lógica básica é encontrar a maior probabilidade dentre todas as probabilidades pontuadas. Para fazer isso, precisamos usar o módulo [Executar Script R][execute-r-script]. O código R é mostrado na Figura 8 e o teste é semelhante à Figura 9.
 
![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/8.png)

Figura 8 Código R para extrair Rótulos Pontuados e as probabilidades associadas dos rótulos
  
![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/9.png)

Figura 9 Teste de classificação final do Problema de Classificação Multiclasse de Reconhecimento de Letra

Após a publicação e a execução do serviço Web e inserindo alguns valores de recurso de entrada, o resultado é semelhante à Figura 10. Essa letra manuscrita, com seus 16 recursos extraídos, é prevista para ser um "T" com probabilidade de 0,9715.
 
![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/9_1.png)

![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/10.png)

Figura 10 Resultado do Serviço Web da classificação de duas classes de íris

##Regressão

Os problemas de regressão são diferentes dos problemas de classificação. Em um problema de classificação, estamos tentando prever classes distintas, como a qual classe uma flor de íris pertence. Mas, em um problema de regressão, estamos tentando prever uma variável contínua, como o preço de um carro, como você pode ver no exemplo a seguir.

**Teste de exemplo**

Usamos a previsão dos preços de automóveis como nosso exemplo de regressão. Estamos tentando prever o preço de um carro com base em seus recursos, incluindo marca, tipo de combustível, tipo de carroceria, tração, etc. O teste é mostrado na Figura 11.
 
![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/11.png)

Figura 11 Teste de problema de regressão de preço de automóveis

Visualizando o módulo [Modelo de Pontuação][score-model], o resultado é semelhante à Figura 12.
 
![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/12.png)

Figura 12 Visualizar resultados de pontuação para o problema de previsão do preço de automóveis

**Interpretação de resultado**

Nesse resultado de pontuação, os Rótulos Pontuados são a coluna de resultado. Os números são o preço previsto para cada carro.

**Publicação de serviço Web**

Podemos publicar o teste de regressão em um serviço Web e chamá-lo para a previsão do preço dos automóveis da mesma maneira como no caso de uso de classificação de duas classes.
 
![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/13.png)

Figura 13 Teste de pontuação do problema de regressão do preço de automóveis

Executando o serviço Web, o resultado é semelhante à Figura 14. O preço previsto para esse carro é 15.085,52.
 
![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/13_1.png)

![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/14.png)

Figura 14 Resultado do serviço Web do problema de regressão do preço de automóveis

##Clustering

**Teste de exemplo**

Vamos usar o conjunto de dados íris novamente para criar um teste de clustering. Aqui, vamos filtrar os rótulos de classe do conjunto de dados para que ele tenha somente recursos e possa ser usado para clustering. Neste caso de uso de íris, vamos especificar o número de clusters como 2 durante o processo de treinamento, o que significa que gostaríamos de armazenar em cluster as flores em duas classes. O teste é mostrado na Figura 15.
 
![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/15.png)

Figura 15 Teste do problema de clustering da íris

O clustering difere da classificação no conjunto de dados de treinamento que não tem rótulos de zero verdadeiro por si só. Em vez disso, estamos interessados em como agrupar as instâncias do conjunto de dados de treinamento em clusters distintos. Durante o processo de treinamento, o modelo rotula as entradas aprendendo as diferenças entre os seus recursos. Depois disso, o modelo treinado pode ser ainda mais usado para classificar entradas futuras. Há duas partes do resultado nas quais estamos interessados dentro de um problema de clustering. A primeira parte é como rotular o conjunto de dados de treinamento, a segunda parte é como classificar um novo conjunto de dados com o modelo treinado.

A primeira parte do resultado pode ser visualizada clicando na porta de saída à esquerda do módulo [Modelo de clustering de treino][train-clustering-model] e clicando em **Visualizar** posteriormente. A janela de visualização é mostrada na Figura 16.
 
![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/16.png)

Figura 16 Visualizar resultados de clustering para o conjunto de dados de treinamento

O resultado da segunda parte, fazendo o clustering de novas entradas com o modelo de clustering treinado, é mostrado na Figura 17.
 
![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/17.png)

Figura 17 Visualizar os resultados de clustering no novo conjunto de dados

**Interpretação de resultado**

Embora os resultados de duas partes provenham de diferentes estágios do teste, eles têm exatamente a mesma aparência e são interpretados da mesma forma. As quatro primeiras colunas são recursos. A última coluna, Atribuições, é o resultado da previsão. As entradas que recebem o mesmo número são previstas como sendo do mesmo cluster, ou seja, elas têm alguma forma de semelhança (usamos a métrica de distância Euclidiana padrão nesse teste). Lembre-se de que especificamos o número de clusters como 2. Para a coluna Atribuições, as entradas são rotuladas como 0 ou 1.

**Publicação de serviço Web**

Podemos publicar o teste de clustering em um serviço Web e chamá-lo para a previsão do clustering da mesma maneira como no caso de uso de classificação de duas classes.
 
![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/18.png)

Figura 18 Teste de pontuação do problema de clustering da íris

Após executar o serviço Web, o resultado é semelhante à Figura 19. É previsto que essa flor esteja no cluster 0.
 
![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/18_1.png)

![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/19.png)

Figura 19 Resultado do Serviço Web da classificação de duas classes de íris

##Sistema de recomendação
**Teste de exemplo**

Para sistemas de recomendação, usaremos o problema de recomendação de restaurante como um exemplo: recomendar restaurantes para clientes com base em seu histórico de classificação. Os dados de entrada consistem em três partes:

* classificações do restaurante feitas pelos clientes 
* dados de recursos do cliente 
* dados de recurso do restaurante 

Há várias coisas que podemos fazer com o módulo integrado [Recomendação de caixa de treino][train-matchbox-recommender] do Aprendizado de Máquina do Azure:

- Prever as classificações de um determinado usuário e item
- Itens recomendados a um determinado usuário
- Localizar usuários relacionados a um determinado usuário
- Localizar itens relacionados a um determinado item

Podemos escolher o que queremos fazer selecionando dentre quatro opções no menu **Tipo de previsão de recomendação** no painel à direita. Aqui, percorreremos todos os quatro cenários. Um teste típico de Aprendizado de Máquina do Azure para o Sistema de recomendação é semelhante à Figura 20. Para obter detalhes sobre como usar esses módulos de sistema de recomendações, consulte as páginas de Ajuda [Recomendação da caixa de treino][train-matchbox-recommender] e [Recomendação da caixa de pontuação][score-matchbox-recommender].
 
![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/19_1.png)

![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/20.png)

Figura 20 Teste do sistema de recomendação

**Interpretação de resultado**

*Prever as classificações de um determinado usuário e item*

Selecionando a Previsão de Classificação no menu **Tipo de previsão de recomendação**, podemos pedir que o sistema de recomendação preveja a classificação de um determinado usuário e o item. A visualização da saída [Recomendação da caixa de pontuação][score-matchbox-recommender] é semelhante à Figura 21.
 
![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/21.png)

Figura 21 Visualizar os resultados da pontuação do sistema de recomendação - Previsão de classificação

Há três colunas. As duas primeiras colunas são pares de item de usuário fornecidos pelos dados de entrada. A terceira coluna é a classificação prevista de um usuário para um determinado item. Por exemplo, na primeira linha, é previsto que o cliente U1048 classifique o restaurante 135026 como 2.

*Itens recomendados a um determinado usuário*

Selecionando **Recomendação de item** no menu **Tipo de previsão de recomendação**, podemos pedir que o sistema de recomendação recomende itens a um determinado usuário. Há um parâmetro a mais que precisamos escolher nesse cenário: a Seleção de item recomendado. A opção **De itens de classificação (para a avaliação de modelo)** é usada principalmente para a avaliação do modelo durante o processo de treinamento. Nesse estágio da previsão, podemos escolher **De todos os itens**. A visualização da saída [Recomendação da caixa de pontuação][score-matchbox-recommender] é semelhante à Figura 22.
 
![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/22.png)

Figura 22 Visualizar os resultados da pontuação do sistema de recomendação - Recomendação de item

Há seis colunas. A primeira coluna representa as IDs de usuário fornecidas para itens recomendados, enviadas pelos dados de entrada. As cinco colunas restantes representam os itens recomendados para o usuário, em ordem decrescente em termos de relevância. Por exemplo, na primeira linha, o restaurante mais recomendado para o cliente U1048 é o 134986, seguido pelo 135018, 134975, 135021, e 132862.

*Localizar usuários relacionados a um determinado usuário*

Selecionando Usuários relacionados no menu “Tipo de previsão de recomendação”, podemos pedir que o sistema de recomendação encontre usuários relacionados a um determinado usuário. Os usuários relacionados são os usuários que têm preferências semelhantes. Há um parâmetro a mais que precisamos escolher nesse cenário: a Seleção de usuário relacionado. A opção “De usuários que classificaram itens (para a avaliação de modelo)” é usada principalmente para a avaliação do modelo durante o processo de treinamento. Escolhemos "De todos os usuários" para este estágio da previsão. A visualização da saída [Recomendação da caixa de pontuação][score-matchbox-recommender] é semelhante à Figura 23.
 
![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/23.png)

Figura 23 Visualizar os resultados da pontuação do sistema de recomendação - Usuários relacionados

Há seis colunas. Na primeira coluna são fornecidas as IDs de usuário para localizar usuários relacionados, fornecidos pelos dados de entrada. As cinco colunas restantes armazenam os usuários relacionados previstos do usuário, em ordem decrescente em termos de relevância. Por exemplo, na primeira linha, o cliente mais relevante para o cliente U1048 é o U1051, seguido pelo U1066, U1044, U1017 e U1072.

**Localizar itens relacionados a um determinado item**

Selecionando **Itens relacionados** no menu **Tipo de previsão de recomendação**, podemos pedir que o sistema de recomendação encontre usuários relacionados a um determinado usuário. Itens relacionados são os itens com maior probabilidade de interessarem ao mesmo usuário. Há um parâmetro a mais que precisamos escolher nesse cenário: a Seleção de item relacionado. A opção **De itens de classificação (para a avaliação de modelo)** é usada principalmente para a avaliação do modelo durante o processo de treinamento. Escolhemos **De todos os itens** para este estágio da previsão. A visualização da saída [Recomendação da caixa de pontuação][score-matchbox-recommender] é semelhante à Figura 24.
 
![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/24.png)

Figura 24 Visualizar os resultados da pontuação do sistema de recomendação - Itens relacionados

Há seis colunas. A primeira coluna representa as IDs de um determinado item para encontrar itens relacionados, fornecidas pelos dados de entrada. As outras cinco colunas armazenam os itens relacionados previstos do item, em ordem decrescente em termos de relevância. Por exemplo, na primeira linha, o item mais relevante para o item 135026 é o 135074, seguido pelo 135035, 132875, 135055 e 134992. Publicação de serviço Web

O processo de publicação desses testes como serviços Web para obter previsões é semelhante para cada um dos quatro cenários. Aqui, veremos o segundo cenário, recomendamos itens para um determinado usuário, como um exemplo. Você pode seguir o mesmo procedimento com os outros três.

Salvando o sistema de recomendação treinado como um modelo treinado, filtrando os dados de entrada para uma coluna de ID de usuário único, conforme solicitado, podemos ligar o teste como na Figura 25 e publicá-lo como um serviço Web.

![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/25.png)
 
Figura 25 Experimento de pontuação do problema de recomendação de restaurante

Executando o serviço Web, o resultado é semelhante à Figura 14. Os cinco restaurantes recomendados para o usuário U1048 são 134986, 135018, 134975, 135021 e 132862.
 
![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/25_1.png)

![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/26.png)

Figura 26 Resultado do serviço Web de Problema de recomendação do restaurante


<!-- Module References -->
[assign-to-clusters]: https://msdn.microsoft.com/library/azure/eed3ee76-e8aa-46e6-907c-9ca767f5c114/
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[project-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[score-matchbox-recommender]: https://msdn.microsoft.com/library/azure/55544522-9a10-44bd-884f-9a91a9cec2cd/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[train-clustering-model]: https://msdn.microsoft.com/library/azure/bb43c744-f7fa-41d0-ae67-74ae75da3ffd/
[train-matchbox-recommender]: https://msdn.microsoft.com/library/azure/fa4aa69d-2f1c-4ba4-ad5f-90ea3a515b4c/
 

<!---HONumber=AcomDC_1210_2015-->