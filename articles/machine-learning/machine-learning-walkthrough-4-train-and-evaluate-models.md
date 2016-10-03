<properties
	pageTitle="Etapa 4: Treinar e avaliar os modelos de análise preditiva | Microsoft Azure"
	description="Etapa 4 - desenvolver um passo a passo de solução de previsão: treinar, pontuar e avaliar vários modelos no Estúdio de Aprendizado de Máquina do Azure."
	services="machine-learning"
	documentationCenter=""
	authors="garyericson"
	manager="jhubbard"
	editor="cgronlun"/>

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/16/2016"
	ms.author="garye"/>


# Etapa 4 do passo a passo: Treinar e avaliar os modelos de análise preditiva

Esta é a quarta etapa do passo a passo, [Desenvolver uma solução de análise preditiva com o Aprendizado de Máquina do Azure](machine-learning-walkthrough-develop-predictive-solution.md)


1.	[Criar um espaço de trabalho do Aprendizado de Máquina](machine-learning-walkthrough-1-create-ml-workspace.md)
2.	[Carregar dados existentes](machine-learning-walkthrough-2-upload-data.md)
3.	[Criar um novo teste](machine-learning-walkthrough-3-create-new-experiment.md)
4.	**Treinar e avaliar os modelos**
5.	[Implantar o serviço Web](machine-learning-walkthrough-5-publish-web-service.md)
6.	[Acessar o serviço Web](machine-learning-walkthrough-6-access-web-service.md)

----------

Uma das vantagens de usar o Estúdio de Aprendizado de Máquina do Azure para criar modelos de aprendizado de máquina é a capacidade de experimentar mais de um tipo de modelo de cada vez em um teste e comparar os resultados. Esse tipo de experimentação ajuda a encontrar a melhor solução para seu problema.

No teste que estamos desenvolvendo neste passo a passo, vamos criar dois tipos diferentes de modelos e comparar os resultados de suas pontuações para decidir qual algoritmo desejamos usar em nosso teste final.

Existem diversos modelos dentre os quais podemos escolher. Para ver os modelos disponíveis, expanda o nó **Aprendizado de Máquina** na paleta do módulo e expanda **Inicializar Modelo** e os nós abaixo dele. Para os fins deste teste, selecionaremos o Computador de vetor de suporte (SVM) e os módulos de Árvores de decisão aumentada de duas classes.

> [AZURE.TIP] Para obter ajuda para decidir qual algoritmo do Aprendizado de Máquina melhor se ajusta ao problema específico que você está tentando resolver, consulte [Como escolher algoritmos de Aprendizado de Máquina do Microsoft Azure](machine-learning-algorithm-choice.md).

## Treinar os modelos

Primeiro, vamos configurar o modelo de árvore de decisão aumentada:

1.	Localize o módulo [Árvore de decisão aumentada de duas classes][two-class-boosted-decision-tree] na paleta do módulo e arraste-o para as telas.
2.	Localize o módulo [Treinar modelo][train-model], arraste-o para as telas e depois conecte a saída do módulo da árvore de decisão aumentada à porta de entrada esquerda ("Modelo sem treinamento") do módulo [Treinar modelo][train-model].
    
    O módulo [Árvore de Decisão Aumentada em Duas Classes][two-class-boosted-decision-tree] inicializa o modelo genérico, e [Treinar Modelo][train-model] usa dados de treinamento para treinar o modelo.
     
3.	Conecte a saída esquerda do módulo [Executar script R][execute-r-script] à porta de entrada direita ("Conjunto de Dados") do módulo [Treinar Modelo][train-model].

	> [AZURE.TIP] Não precisamos de duas das entradas e uma das saídas do módulo [Executar Script R][execute-r-script] para este teste; portanto, apenas as deixaremos desconectadas. Isso é comum para alguns módulos.

4.	Selecione o módulo [Treinar modelo][train-model]. No painel **Propriedades**, clique em **Iniciar seletor de colunas**, selecione **Incluir** na primeira lista suspensa, selecione **nomes de colunas** na segunda lista suspensa e insira "Risco de crédito" no campo de texto (você também pode selecionar **índices da coluna** e inserir "21"). Isso identifica a coluna 21, o valor do risco de crédito, como a coluna para o modelo prever.


Esta parte do teste se parece um pouco com o seguinte:

![Treinando um modelo][1]

Em seguida, configuramos o modelo SVM.

Primeiro, uma pequena explicação sobre o SVM. Árvores de decisão aumentadas funcionam bem com recursos de qualquer tipo. No entanto, como o módulo SVM gera um classificador linear, o modelo que ele gera apresenta o melhor erro de teste quando todos os recursos numéricos possuem a mesma escala. Então, para converter todos os recursos numéricos para a mesma escala, vamos usar uma transformação de "Tanh" (com o módulo [Normalizar Dados][normalize-data]), que transformará nossos números no intervalo [0,1] \(recursos de cadeia de caracteres são convertidos pelo módulo SVM para recursos categóricos e, em seguida, para recursos binários 0/1, portanto, não precisamos transformar manualmente os recursos de cadeia de caracteres). Também não desejamos transformar a coluna Risco de Crédito (coluna 21) – ela é numérica, mas esse é o valor que estamos treinando para o modelo prever, portanto, precisamos deixá-la intacta.

Para configurar o modelo SVM, faça o seguinte:

1.	Localize o módulo [Máquina de vetor de suporte de duas classes][two-class-support-vector-machine] na paleta do módulo e arraste-o para as telas.
2.	Clique com o botão direito do mouse no módulo [Treinar modelo][train-model], selecione **Copiar**, depois clique com o botão direito do mouse na tela e selecione **Colar**. Observe que a cópia do módulo [Treinar modelo][train-model] tem a mesma seleção de coluna que o original.
3.	Conecte a saída do módulo SVM à porta de entrada esquerda ("Modelo não treinado") do módulo [Treinar Modelo][train-model].
4.	Localize o módulo [Normalizar dados][normalize-data] e arraste-o para a tela.
5.	Conecte a entrada desse módulo para a saída à esquerda do módulo [Executar Script R][execute-r-script] esquerdo (observe que a porta de saída de um módulo pode ser conectada a mais de um outro módulo).
6.	Conecte a porta de saída esquerda ("Conjunto de dados transformado") do módulo [Normalizar Dados][normalize-data] à porta de entrada direita ("Conjunto de dados") do segundo módulo [Treinar Modelo][train-model].
7.	No painel **Propriedades** do módulo [Normalizar Dados][normalize-data], selecione **Tanh** para o parâmetro **Método de transformação**.
8.	Clique no **Seletor de coluna de inicialização**, selecione “Sem colunas” para **Começar Com**, selecione **Incluir** na primeira lista suspensa, selecione **tipo de coluna** na segunda lista suspensa e selecione **Numérico** na terceira lista suspensa. Isso especifica que todas as colunas numéricas (e somente as numéricas) serão transformadas.
9.	Clique no sinal de adição (+) à direita desta linha - isso cria uma nova linha de listas suspensas. Selecione **Excluir** na primeira lista suspensa, selecione **nomes de colunas** na segunda lista suspensa, clique no próximo campo de texto e selecione "Risco de crédito" da lista de colunas. Isso especifica que a coluna Risco de crédito deve ser ignorada (precisamos fazer isso porque essa coluna é numérica e, caso contrário, seria transformada).
10.	Clique em **OK**.


Agora o módulo [Normalizar dados][normalize-data] está definido para executar uma transformação Tanh em todas as colunas numéricas, exceto para a coluna Risco de crédito.

Esta parte de nosso teste deve se parecer um pouco com o seguinte:

![Treinando o segundo modelo][2]

## Pontuar e avaliar os modelos

Usaremos os dados de teste que foram separados pelo módulo [Dividir Dados][split] para pontuar nossos modelos treinados. Podemos comparar os resultados dos dois modelos para ver quais geraram os melhores resultados.

1.	Localize o módulo [Modelo de pontuação][score-model] e arraste-o para as telas.
2.	Conecte a porta de entrada esquerda desse módulo ao modelo de árvore de decisão aumentada (ou seja, conecte-a à porta de saída do módulo [Treinar modelo][train-model] que está conectado ao módulo [Árvore de decisão aumentada de duas classes][two-class-boosted-decision-tree]).
3.	Conecte a porta de entrada direita do módulo [Modelo de pontuação][score-model] à saída esquerda do módulo [Executar script R][execute-r-script] direito.

    O módulo [Modelo de Pontuação][score-model] agora pode pegar as informações de crédito dos dados de teste, executá-los por meio do modelo e comparar as previsões que o modelo gera com a coluna de risco de crédito real nos dados de teste.

4.	Copie e cole o módulo [Modelo de pontuação][score-model] para criar uma segunda cópia ou arraste um novo módulo para as telas.
5.	Conecte a porta de entrada esquerda desse módulo ao modelo SVM (ou seja, conecte à porta de saída do módulo [Treinar modelo][train-model] que está conectado ao módulo [Máquina vetor de suporte de duas classes][two-class-support-vector-machine]).
6.	Para o modelo SVM, precisamos fazer a mesma transformação nos dados do teste que fizemos nos dados de treinamento. Portanto, copie e cole o módulo [Normalizar dados][normalize-data] para criar uma segunda cópia e conectá-la à saída esquerda do módulo [Executar Script R][execute-r-script] direito.
7.	Conecte a porta de entrada direita do módulo [Modelo de pontuação][score-model] à saída esquerda do módulo [Normalizar dados][normalize-data].

Para avaliar os dois resultados de pontuação usaremos o módulo [Avaliar modelo][evaluate-model].

1.	Localize o módulo [Avaliar modelo][evaluate-model] e arraste-o para as telas.
2.	Conecte a porta de entrada esquerda à porta de saída do módulo [Modelo de pontuação][score-model] associado ao modelo de árvore de decisão aumentada.
3.	Conecte a porta de entrada direita ao outro módulo de [Modelo de pontuação][score-model].

Clique no botão **EXECUTAR** abaixo das telas para executar o teste. Isso pode levar alguns minutos. Você verá um indicador girando em cada módulo para indicar que está em execução e, depois, uma marca de seleção verde quando o módulo for concluído. Quando todos os modelos tiverem uma marca de seleção, a execução do teste estará concluída.

O teste deve se parecer como o seguinte:

![Avaliando os dois modelos][3]

Para verificar os resultados, clique na porta de saída do módulo [Avaliar modelo][evaluate-model] e selecione **Visualizar**.

O módulo [Avaliar modelo][evaluate-model] produz um par de curvas e métricas que lhe permitem comparar os resultados dos dois modelos pontuados. Você pode exibir os resultados como curvas ROC (Receiver Operator Characteristic), curvas de Precisão/Repetição, ou curvas de Elevação. Os dados adicionais exibidos incluem uma matriz de confusão, valores cumulativos para a área sob a curva (AUC) e outras métricas. Você pode alterar o valor de limite movendo o controle deslizante para esquerda ou direita e vendo como isso afeta o conjunto de métricas.

À direita do gráfico, clique em **Conjunto de dados pontuados** ou **Conjunto de dados pontuados para comparar** para destacar a curva associada e exibir as métricas associadas abaixo. Na legenda das curvas, "Conjunto de dados pontuados" corresponde à porta de entrada esquerda do módulo [Avaliar modelo][evaluate-model] - em nosso caso, esse é o modelo de árvore de decisão aumentada. O "Conjunto de dados pontuados para comparar" corresponde à porta de entrada direita - o modelo SVM em nosso caso. Ao clicar em um desses rótulos, destacará a curva desse modelo e exibirá as métricas correspondentes embaixo.

![Curvas ROC dos modelos][4]

Ao examinar esses valores, você pode decidir qual modelo está mais próximo de fornecer os resultados que você está procurando. Você pode voltar e iterar seu teste alterando os valores nos diferentes modelos.

> [AZURE.TIP] Toda vez que você executar o teste, um registro dessa iteração será mantido no Histórico de Execução. Você pode exibir essas iterações e retornar a qualquer item clicando em **EXIBIR HISTÓRICO DE EXECUÇÃO** abaixo das telas. Você também pode clicar em **Execução Anterior** no painel **Propriedades** para retornar à iteração imediatamente, precedendo aquela que você abriu. Você pode fazer uma cópia de qualquer iteração de seu teste clicando em **SALVAR COMO** abaixo das telas. Use as propriedades **Resumo** e **Descrição** do teste para manter um registro do que você experimentou em suas iterações de teste.

>  Consulte [Gerenciar iterações do experimento no Estúdio de Aprendizado de Máquina do Azure](machine-learning-manage-experiment-iterations.md) para obter mais detalhes.


----------

**Próximo: [Implantar o serviço Web](machine-learning-walkthrough-5-publish-web-service.md)**

[1]: ./media/machine-learning-walkthrough-4-train-and-evaluate-models/train1.png
[2]: ./media/machine-learning-walkthrough-4-train-and-evaluate-models/train2.png
[3]: ./media/machine-learning-walkthrough-4-train-and-evaluate-models/train3.png
[4]: ./media/machine-learning-walkthrough-4-train-and-evaluate-models/train4.png


<!-- Module References -->
[evaluate-model]: https://msdn.microsoft.com/library/azure/927d65ac-3b50-4694-9903-20f6c1672089/
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[normalize-data]: https://msdn.microsoft.com/library/azure/986df333-6748-4b85-923d-871df70d6aaf/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
[two-class-boosted-decision-tree]: https://msdn.microsoft.com/library/azure/e3c522f8-53d9-4829-8ea4-5c6a6b75330c/
[two-class-support-vector-machine]: https://msdn.microsoft.com/library/azure/12d8479b-74b4-4e67-b8de-d32867380e20/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/

<!---HONumber=AcomDC_0921_2016-->