<properties title="Step 4: Train and evaluate the predictive analytic models" pageTitle="Etapa 4: Treinar e avaliar os modelos analíticos preditivos | Azure" description="Step 4: Train, score, and evaluate multiple models in Azure Machine Learning Studio" metaKeywords="" services="machine-learning" solutions="" documentationCenter="" authors="garye" manager="paulettm" editor="cgronlun" videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/20/2014" ms.author="garye" />


Esta é a quarta etapa do passo a passo, [Desenvolvendo uma Solução Preditiva com o AM do Azure][develop]:

[develop]: ../machine-learning-walkthrough-develop-predictive-solution/

1.	[Criar um espaço de trabalho do AM][create-workspace]
2.	[Fazer upload de dados existentes][upload-data]
3.	[Criar um novo experimento][create-new]
4.	**Treinar e avaliar os modelos**
5.	[Publicar o serviço Web][publish]
6.	[Acessar o serviço Web][access-ws]

[create-workspace]: ../machine-learning-walkthrough-1-create-ml-workspace/
[upload-data]: ../machine-learning-walkthrough-2-upload-data/
[create-new]: ../machine-learning-walkthrough-3-create-new-experiment/
[train-models]: ../machine-learning-walkthrough-4-train-and-evaluate-models/
[publish]: ../machine-learning-walkthrough-5-publish-web-service/
[access-ws]: ../machine-learning-walkthrough-6-access-web-service/

----------

# Etapa 4: Treinar e avaliar os modelos analíticos preditivos


Neste teste, usaremos algoritmos diferentes para nosso modelo preditivo. Vamos criar dois tipos diferentes de modelos e comparar os resultados de suas pontuações para decidir qual algoritmo desejamos usar em nosso teste final.  

Existem diversos modelos dentre os quais podemos escolher. Para ver os modelos disponíveis, expanda o nó **Aprendizado de Máquina** na paleta do módulo e expanda **Inicializar Modelo** e os nós abaixo dele. Para os fins deste teste, selecionaremos a máquina de vetor de suporte (SVM) e as árvores de decisão aumentada de duas classes. Usaremos os módulos apropriados para inicializar os algoritmos de aprendizado e os módulos **Treinar modelo** para treinar os modelos.   

##Treinar os modelos
Primeiro, vamos configurar o modelo de árvore de decisão aumentada:  

1.	Localize o módulo **Árvore de decisão aumentada de duas classes** na paleta do módulo e arraste-o para as telas.
2.	Localize o módulo **Treinar modelo**, arraste-o para as telas e depois conecte a saída do módulo da árvore de decisão aumentada à porta de entrada esquerda ("Modelo sem treinamento") do módulo **Treinar modelo**.
3.	Conecte a saída do módulo **Executar script R** esquerdo à porta de entrada direita ("Conjunto de dados") do módulo **Treinar modelo**.

	>Dica - Não precisamos de duas das entradas e uma das saídas do módulo **Executar script R** para este teste, portanto, apenas as deixaremos desconectadas. Isso é comum para alguns módulos.


4.	Selecione o módulo **Treinar modelo**. No painel **Propriedades**, clique em **Iniciar seletor de colunas**, selecione "Incluir" na primeira lista suspensa, selecione "índices de colunas" na segunda lista suspensa e insira "21" no campo de texto (você também pode selecionar "Nome da coluna" e inserir "Risco de crédito"). Isso identifica a coluna 21, o valor do risco de crédito, como a coluna para o modelo prever.


Esta parte do teste se parece um pouco com o seguinte:  

![Training a model][1]
 
Em seguida, configuramos o modelo SVM.  

Árvores de decisão aumentadas funcionam bem com recursos de qualquer tipo. No entanto, como o módulo SVM gera um classificador linear, o modelo que ele gera apresenta o melhor erro de teste quando todos os recursos numéricos possuem a mesma escala. Para converter todos os recursos numéricos na mesma escala usamos o módulo **Normalizar dados** com uma transformação Tanh, que transforma recursos no intervalo [0,1]. Observe que recursos de cadeia de caracteres são convertidos pelo módulo SVM para recursos categóricos e, em seguida, para recursos binários 0/1, portanto, não precisamos transformar manualmente os recursos de cadeia de caracteres. Também não desejamos transformar a coluna Risco de Crédito (coluna 21) - ela é numérica, mas esse é o valor que estamos treinando para o modelo prever, portanto, precisamos deixá-la intacta.  

1.	Localize o módulo **Máquina de vetor de suporte de duas classes** na paleta do módulo e arraste-o para as telas.
2.	Clique com o botão direito no módulo **Treinar modelo**, selecione **Copiar**, depois clique com o botão direito na tela e selecione **Colar**. Observe que a cópia do módulo **Treinar modelo** tem a mesma seleção de coluna que o original.
3.	Conecte a saída do módulo SVM à porta de entrada esquerda ("Modelo não treinado") do módulo **Treinar Modelo**.
4.	Localize o módulo **Normalizar dados** e arraste para a tela.
5.	Conecte a entrada desse módulo de transformação à saída do módulo **Executar script R**.
6.	Conecte a porta de saída esquerda ("Conjunto de dados transformado") do módulo de transformação à porta de entrada direita ("Conjunto de dados") do módulo **Treinar modelo**.
7.	No painel **Propriedades** do módulo de transformação, selecione "Tanh" para o parâmetro **Método de transformação**.
8.	Clique no **seletor de coluna de inicialização**, selecione "Incluir" na primeira lista suspensa, selecione "tipo de coluna" na segunda lista suspensa e selecione "Numérico" no menu suspenso de terceiro. Isso especifica que todas as colunas numéricas (e somente as numéricas) serão transformadas.
9.	Clique no sinal de mais (+), que cria uma nova linha de listas suspensas. Selecione "Excluir" na primeira lista suspensa, selecione "índices de colunas" na segunda lista suspensa e insira "21" no campo de texto. Isso especifica que a coluna 21 (a coluna de "Risco de crédito") será ignorada.
10.	Clique em **OK**.  


Agora o módulo **Normalizar dados** está definido para executar uma transformação tanh em todas as colunas numéricas, exceto para a coluna Risco de crédito.  

Esta parte de nosso teste deve se parecer um pouco com o seguinte:  

![Training the second model][2]  

##Pontuar e avaliar os modelos
Usaremos os dados de pontuação que foram separados pelo módulo **Divisão** para pontuar nossos modelos treinados. Podemos comparar os resultados dos dois modelos para ver quais geraram os melhores resultados.  

1.	Localize o módulo **Modelo de pontuação** e arraste para a tela.
2.	Conecte a porta de entrada esquerda desse módulo ao modelo de árvore de decisão aumentada (ou seja, conecte-a à porta de saída do módulo **Treinar modelo** que está conectado ao módulo **Árvore de decisão aumentada de duas classes**).
3.	Conecte a porta de entrada direita do módulo **Modelo de pontuação** à saída do módulo **Executar script R** direito. Observe que não há problema nenhum em ter a saída de um módulo indo para diversos lugares.
4.	Copie e cole o módulo **Modelo de pontuação** para criar uma segunda cópia ou arraste um novo módulo para as telas.
5.	Conecte a porta de entrada esquerda desse módulo ao modelo SVM (ou seja, conecte à porta de saída do módulo **Treinar modelo** que está conectado ao módulo **Máquina vetor de suporte de duas classes**).
6.	Para o modelo SVM, precisamos fazer a mesma transformação nos dados do teste que fizemos nos dados de treinamento. Portanto, copie e cole o módulo **Normalizar dados** para criar uma segunda cópia e conectá-la à saída do módulo **Executar** direito.
7.	Conecte a porta de entrada direita do módulo **Modelo de pontuação** à saída do módulo **Normalizar dados**.  

Para avaliar os dois resultados de pontuação usaremos o módulo **Avaliar modelo**.  

1.	Localize o módulo **Avaliar modelo** e arraste para a tela.
2.	Conecte a porta de entrada esquerda à porta de saída do módulo **Modelo de pontuação** associado ao modelo de árvore de decisão aumentada.
3.	Conecte a porta de entrada direita ao outro módulo de **Modelo de pontuação**.  

O teste deve se parecer como o seguinte:  

![Evaluating both models][3]
 
Clique no botão **EXECUTAR** abaixo das telas para executar o teste. Isso pode levar alguns minutos. Você verá um indicador girando em cada módulo para indicar que está em execução e, depois, uma marca de seleção verde quando o módulo for concluído.   

Quando todos os modelos tiverem uma marca de seleção, a execução do teste estará concluída. Para verificar os resultados, clique com o botão direito do mouse na porta de saída do módulo **Avaliar modelo** e selecione **Visualizar**.  

O módulo **Avaliar modelo** produz um par de curvas e métricas que lhe permitem comparar os resultados dos dois modelos pontuados. Você pode exibir os resultados como curvas ROC (Receiver Operator Characteristic), curvas de Precisão/Repetição, ou curvas de Elevação. Entre os dados adicionais exibidos estão uma matriz de confusão, valores AUC acumulativos e outras métricas. Você pode alterar o valor de limite movendo o controle deslizante para esquerda ou direita e vendo como isso afeta o conjunto de métricas.  

Clique em "Conjunto de dados pontuados" ou "Conjunto de dados pontuados para comparar" para destacar a curva associada e exibir as métricas associadas abaixo. Na legenda das curvas, "Conjunto de dados pontuados" corresponde à porta de entrada esquerda do módulo **Avaliar modelo** - em nosso caso, esse é o modelo de árvore de decisão aumentada. O "Conjunto de dados pontuados para comparar" corresponde à porta de entrada direita - o modelo SVM em nosso caso. Ao clicar em um desses rótulos, destacará a curva desse modelo e exibirá as métricas correspondentes embaixo.  

![ROC curves for models][4]
 
Ao examinar esses valores, você pode decidir qual modelo está mais próximo de fornecer os resultados que você está procurando. Você pode voltar e iterar seu teste alterando os valores nos diferentes modelos.  

>Dica - Toda vez que você executar o teste, um registro dessa iteração será mantido no Histórico de Execução. Você pode exibir essas iterações e retornar a qualquer item clicando em **EXIBIR HISTÓRICO DE EXECUÇÃO** abaixo das telas. Você também pode clicar em **Execução Anterior** no painel **Propriedades** para retornar à iteração imediatamente, precedendo aquela que você abriu.  

Você também pode fazer uma cópia de qualquer iteração de seu teste clicando em **SALVAR COMO** abaixo da tela. Isso faz uma duplicata do teste, criando um novo Histórico de Execução para acompanhar suas iterações dessa versão. A nova cópia é exibida na lista **TESTES** juntamente com o original. Isso pode ser útil para iniciar uma nova ramificação das iterações do teste.  

Como uma ajuda adicional para acompanhar as mudanças que você faz nos parâmetros, é possível adicionar comentários em qualquer módulo nas telas do teste. Basta clicar duas vezes no módulo ou clicar com o botão direito do mouse e selecionar **Editar comentário**. Esses comentários são salvos com as iterações do teste e podem ajudar nas anotações de seu trabalho.


----------

**Avançar: [Publicar o serviço Web][publish]**

[1]: ./media/machine-learning-walkthrough-4-train-and-evaluate-models/train1.png
[2]: ./media/machine-learning-walkthrough-4-train-and-evaluate-models/train2.png
[3]: ./media/machine-learning-walkthrough-4-train-and-evaluate-models/train3.png
[4]: ./media/machine-learning-walkthrough-4-train-and-evaluate-models/train4.png
