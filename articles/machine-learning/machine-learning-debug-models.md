<properties 
	pageTitle="Depurar seu modelo no aprendizado de máquina do Azure | Microsoft Azure" 
	description="Explica como depurar seu modelo no aprendizado de máquina do Azure." 
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
	ms.date="04/29/2015" 
	ms.author="bradsev;garye" />

# Depurar seu modelo no aprendizado de máquina do Azure

Este artigo explica como depurar seus modelos no Aprendizado de Máquina do Microsoft Azure. Especificamente, ele aborda os motivos possíveis do porquê que qualquer um dos dois cenários de falha a seguir pode ser encontrado durante a execução de um modelo:

* o módulo [Modelo de Treinamento][train-model] gera um erro 
* o módulo [Modelo de Pontuação][score-model] produz resultados incorretos 

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## O módulo Modelo de Treinamento gera um erro

![image1](./media/machine-learning-debug-models/train_model-1.png)

O módulo [Modelo de Treinamento][train-model] espera as 2 entradas a seguir:

1. O tipo de modelo de regressão/classificação da coleção de modelos fornecidos pelo aprendizado de máquina do Azure
2. Os dados de treinamento com uma coluna de Rótulo especificada. A coluna Rótulo especifica a variável a ser prevista. O restante das colunas incluídas são consideradas Recursos.

Este módulo gera um erro nos seguintes casos:

1. A coluna de rótulo foi especificada incorretamente porque mais de uma coluna está selecionada como o Rótulo ou um índice de coluna incorreto está selecionado. Por exemplo, o segundo caso é aplicável quando um índice de coluna 30 tiver sido usado com um conjunto de dados de entrada que tinha apenas 25 colunas.

2. O conjunto de dados não contém nenhuma coluna de Recurso. Por exemplo, se o conjunto de dados de entrada tem apenas uma coluna, que é marcada como a coluna Rótulo, não haveria nenhum recurso com o qual compilar o modelo. Nesse caso, o módulo [Modelo de Treinamento][train-model] gerará um erro.

3. O conjunto de dados de entrada (Recursos ou Rótulo) contém Infinito como um valor.


## Módulo Modelo de Pontuação não produz resultados corretos

![image2](./media/machine-learning-debug-models/train_test-2.png)

Em um gráfico típico do treinamento/teste de aprendizado supervisionado, o módulo [Divisão][split] divide o conjunto de dados original em duas partes: a parte que é usada para treinar o modelo e a parte que é reservada para pontuação da mesma forma que o modelo treinado é executado em dados para os quais ele não foi treinado. O modelo treinado, em seguida, é usado para pontuar os dados de teste e, depois, os resultados são avaliados para determinar a precisão do modelo.

O módulo [Modelo de Pontuação][score-model] requer duas entradas:

1. Uma saída de modelo treinado do módulo [Modelo de Treinamento][train-model]
2. Um conjunto de dados de pontuação no qual o modelo não foi treinado.

Isso pode ocorrer mesmo que o teste tenha sido bem-sucedido, o módulo [Modelo de Pontuação][score-model] produz resultados incorretos. Vários cenários podem causar isso:

1. Se o rótulo especificado for categórico e um modelo de regressão for treinado nos dados, uma saída incorreta poderá ser produzida pelo módulo [Modelo de Pontuação][score-model]. Isso ocorre porque a regressão requer uma variável de resposta contínua. Nesse caso, deve ser mais adequado usar um modelo de classificação. 
2. Da mesma forma, se um modelo de classificação for treinado em um conjunto de dados com números de ponto flutuante na coluna Rótulo, ele pode produzir resultados indesejáveis. Isso ocorre porque a classificação exige uma variável de resposta discreta que permite somente valores que variam em um conjunto finito e normalmente um pouco pequeno de classes.
3. Se o conjunto de dados de pontuação não contiver todos os recursos usados para treinar o modelo, o [Modelo de Pontuação][score-model] produzirá um erro.
4. O [Modelo de Pontuação][score-model] não produzirá nenhuma saída correspondente a uma linha no conjunto de dados de pontuação que contenha um valor ausente ou um valor infinito para qualquer um dos seus recursos.
5. O [Modelo de Pontuação][score-model] pode produzir resultados idênticos para todas as linhas no conjunto de dados de pontuação. Isso pode ocorrer, por exemplo, ao tentar fazer a classificação usando Florestas de Decisão se o número mínimo de amostras por nó folha for escolhido como sendo maior que o número de exemplos de treinamento disponível.


<!-- Module References -->
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
 

<!---HONumber=July15_HO4-->