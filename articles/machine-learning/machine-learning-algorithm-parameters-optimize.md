<properties 
	pageTitle="Como escolher parâmetros para otimizar seus algoritmos de Aprendizado de Máquina do Azure | Microsoft Azure" 
	description="Explica como escolher o parâmetro ideal definido por um algoritmo no Aprendizado de Máquina do Azure." 
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
	ms.date="05/27/2016" 
	ms.author="bradsev" />


# Como escolher parâmetros para otimizar seus algoritmos de Aprendizado de Máquina do Azure

Este tópico descreve como escolher o hiperparâmetro certo definido por um algoritmo do Aprendizado de Máquina do Azure. A maioria dos algoritmos de aprendizado de máquina dependem de vários parâmetros. Ao treinar um modelo, é necessário fornecer valores para esses parâmetros. A eficácia do modelo treinado depende dos parâmetros do modelo que escolhemos. O processo de localizar o melhor conjunto de parâmetros é conhecido como seleção de modelo.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

Há várias maneiras de realizar a seleção de modelo. No aprendizado de máquina, a validação cruzada é um dos métodos mais amplamente usados para seleção de modelo e é o mecanismo de seleção de modelo padrão no Aprendizado de Máquina do Azure. Como o Aprendizado de Máquina do Azure dá suporte a R e Python, os usuários sempre podem implementar seu próprio mecanismo de seleção de modelo usando R ou Python.

Há quatro etapas no processo de localizar o melhor conjunto de parâmetros.

1.	**Definir espaço de parâmetro**: para o algoritmo, decidimos primeiro os valores de parâmetro exatos que gostaríamos de considerar. 
2.	**Definir configurações de validação cruzada**: para o conjunto de dados, precisamos decidir como escolher dobras de validação cruzada. 
3.	**Definir métrica**: em seguida, decidimos que métrica deve ser usada para determinar o melhor conjunto de parâmetros, como precisão, erro de raiz quadrada média, precisão, rechamada ou valor f. 
4.	**Treinar, avaliar e comparar**: para cada combinação exclusiva de valores de parâmetro, a validação cruzada é executada e, com base na métrica de erro definida pelo usuário, o modelo com melhor desempenho pode ser escolhido.

O experimento a seguir mostra como isso pode ser obtido no Aprendizado de Máquina do Azure.

![image1](./media/machine-learning-algorithm-parameters-optimize/fig1.png)
 
## Definir espaço de parâmetro
O conjunto de parâmetros pode ser definido na etapa de inicialização de modelo. O painel de parâmetro de todos os algoritmos de aprendizado de máquina tem dois modos de instrutor – **Parâmetro Único** e **Intervalo de Parâmetros**. Precisamos escolher o modo **Intervalo de Parâmetros** (Figura 1). Ele habilita a inserção diversos valores para cada parâmetro: valores separados por vírgula podem ser inseridos na caixa de texto. Como alternativa, **Usar Criador de Intervalo** pode ser usado para definir os pontos mínimo e máximo da grade e o número total de pontos a ser gerado. Por padrão, os valores de parâmetro são gerados em uma escala de linha de comando. Porém, se a caixa **Escala Logarítmica** estiver marcada, os valores são gerados em escala logarítmica (ou seja, a taxa dos pontos adjacentes é constante em vez da diferença). Para parâmetros de inteiro, um intervalo pode ser definido com um hífen "-", por exemplo, "1-10" que significa que todos os inteiros entre 1 e 10 (ambos incluídos) formarão o conjunto de parâmetros. Um modo misto também é suportado, por exemplo, “1-10, 20, 50”. Nesse caso além de números inteiros de 1 a 10, 20 e 50 também serão adicionados ao conjunto de parâmetro.
  
![image2](./media/machine-learning-algorithm-parameters-optimize/fig2.png) ![image3](./media/machine-learning-algorithm-parameters-optimize/fig3.png)

## Definição de dobra de validação cruzada
O módulo [Partição e Exemplo][partition-and-sample] pode ser usado para atribuir dobras aos dados aleatoriamente. Na figura a seguir, vemos um exemplo de configuração para o módulo em que definimos 5 dobras e aleatoriamente atribuímos o número de dobras para as instâncias de amostra.

![image4](./media/machine-learning-algorithm-parameters-optimize/fig4.png)


## Definir Métrica
O módulo [Ajustar Hiperparâmetros do Modelo][tune-model-hyperparameters] dá suporte à escolha empírica do melhor conjunto de parâmetros para um determinado algoritmo e conjunto de dados. O painel de propriedades deste módulo inclui, além de outras informações sobre o treinamento do modelo, a medida a ser usada para determinar o melhor conjunto de parâmetros. Ele tem duas listas suspensas diferentes para algoritmos de classificação e regressão, respectivamente. Se o algoritmo em questão for um algoritmo de classificação, a métrica de regressão é ignorada e vice-versa. Neste exemplo específico, escolhemos **Precisão** como métrica.
 
![image5](./media/machine-learning-algorithm-parameters-optimize/fig5.png)

## Treinar, avaliar e comparar  
O mesmo módulo [Ajustar Hiperparâmetros do Modelo][tune-model-hyperparameters] treina todos os modelos correspondentes ao conjunto de parâmetros, avalia diversas métricas e gera o melhor modelo treinado com base na métrica escolhida pelo usuário. Este módulo tem duas entradas obrigatórias

* o aprendiz não treinado 
* o conjunto de dados 

e um conjunto de dados opcional de entrada. Conectamos o conjunto de dados com informações de dobra à entrada obrigatória do conjunto de dados. Se o conjunto de dados não for atribuído às informações de dobra, uma validação cruzada de 10 dobras será executada automaticamente por padrão. Se a atribuição de dobra não for feita e um conjunto de dados de validação for indicado na porta opcional do conjunto de dados, então, um modo de treinamento de teste escolhido e o primeiro conjunto de dados são usados para treinar o modelo em cada combinação de parâmetros, sendo avaliado no conjunto de dados de validação. A porta de saída à esquerda do módulo mostra métricas diferentes como uma função de valores de parâmetro. A porta de saída à direita fornece o modelo treinado correspondente ao modelo com melhor desempenho de acordo com a métrica escolhida pelo usuário (precisão, neste caso).

![image6](./media/machine-learning-algorithm-parameters-optimize/fig6a.png) ![image7](./media/machine-learning-algorithm-parameters-optimize/fig6b.png)
 
Podemos ver os parâmetros exatos escolhidos visualizando a porta de saída à direita. Esse modelo pode ser usado na pontuação de um conjunto de teste ou em um serviço Web operacionalizado depois de salvá-lo como um modelo treinado.


<!-- Module References -->
[partition-and-sample]: https://msdn.microsoft.com/library/azure/a8726e34-1b3e-4515-b59a-3e4a475654b8/
[tune-model-hyperparameters]: https://msdn.microsoft.com/library/azure/038d91b6-c2f2-42a1-9215-1f2c20ed1b40/
 

<!---HONumber=AcomDC_0608_2016-->