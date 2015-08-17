<properties
	pageTitle="Criar uma experiência simples no Studio de Aprendizado de Máquina | Microsoft Azure"
	description="Um primeiro tutorial de aprendizado de máquina para criar uma experiência simples para treinar e testar um modelo de regressão linear no Studio de Aprendizado de Máquina do Azure." 
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
	ms.topic="hero-article" 
	ms.date="07/09/2015"
	ms.author="garye"/>

#Tutorial de aprendizado de máquina: Crie sua primeira experiência no Studio de Aprendizado de Máquina do Azure

Neste primeiro tutorial de Aprendizado de Máquina, vamos criar um modelo de regressão linear que prevê o preço de um automóvel com base em variáveis diferentes, como marca e especificações técnicas. Para fazer isso, usaremos o Estúdio de Aprendizado de Máquina do Azure para desenvolver e iterar em um experimento analítico preditivo simples.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

Um experimento de Estúdio de Aprendizado de Máquina consiste em arrastar e soltar componentes em uma tela e conectá-los para *criar um modelo de*, *treinar o modelo* e *pontuar e testar o modelo*. O experimento usa técnicas de modelagem de previsão na forma de módulos do Studio de Aprendizado de Máquina que incluem dados, treinam um modelo em relação a ele e aplicam o modelo a novos dados. Também é possível adicionar módulos para pré-processar dados e selecionar recursos, dividir dados em conjuntos de treinamento e teste e avaliar ou cruzar a validade da qualidade de seu modelo.

Digite Estúdio de Aprendizado de Máquina: [https://studio.azureml.net](https://studio.azureml.net) e clique no botão "Iniciar". Você pode escolher o acesso de convidado ou entrar com sua conta da Microsoft.

E para obter mais informações gerais sobre o Estúdio do Aprendizado de Máquina, consulte [O que é o Estúdio de Aprendizado de Máquina?](machine-learning-what-is-ml-studio.md)


##Cinco etapas para criar um experimento

Neste tutorial de Aprendizado de Máquina, você seguirá cinco etapas básicas para criar uma experiência do Studio de Aprendizado de Máquina para criar, treinar e pontuar o seu modelo:

- Criar um modelo
	- [Etapa 1: Obter dados]
	- [Etapa 2: Pré-processar dados]
	- [Etapa 3: Definir recursos]
- Treinar o modelo
	- [Etapa 4: Escolher e aplicar um algoritmo de aprendizado]
- Pontuar e testar o modelo
	- [Etapa 5: Prever novos preços de automóveis]

[Etapa 1: Obter dados]: #step-1-get-data
[Etapa 2: Pré-processar dados]: #step-2-preprocess-data
[Etapa 3: Definir recursos]: #step-3-define-features
[Etapa 4: Escolher e aplicar um algoritmo de aprendizado]: #step-4-choose-and-apply-a-learning-algorithm
[Etapa 5: Prever novos preços de automóveis]: #step-5-predict-new-automobile-prices


## Etapa 1: Obter dados

Há uma série de conjuntos de dados de exemplo incluídos no Estúdio de Aprendizado de Máquina, e você pode importar dados de várias fontes. Para este exemplo, usaremos o conjunto de dados de amostra incluído, **Dados de preço de automóvel (Brutos)**. Esse conjunto de dados inclui entradas para uma série de automóveis individuais, incluindo informações como marca, modelo, especificações técnicas e preço.

1. Inicie um novo experimento clicando em **+NOVO** na parte inferior da janela do Estúdio de Aprendizado de Máquina, selecione **EXPERIMENTO** e selecione “Experimento em branco". Selecione o nome de experimento padrão na parte superior da tela e renomeie para algo significativo, por exemplo, **Previsão de preço de automóvel**.

2. À esquerda da tela do experimento está uma paleta de conjuntos de dados e módulos. Digite **automóvel** na caixa de pesquisa na parte superior desta paleta para localizar o conjunto de dados rotulado **Dados de preço de automóvel (Brutos)**.

	![Pesquisa de paleta][screen1a]

3. Arraste o conjunto de dados até a tela do experimento.

	![Conjunto de dados][screen1]

Para ver a aparência dos dados, clique na porta de saída na parte inferior do conjunto de dados do automóvel e selecione **Visualizar**. As variáveis no conjunto de dados aparecem como colunas e cada instância de um automóvel aparece como uma linha. A coluna à direita (coluna 26 intitulada "preço") é a variável de destino que vamos tentar prever.

![Visualização de conjunto de dados][screen1b]

Feche a janela de visualização clicando no "**x**" no canto superior direito.

## Etapa 2: Pré-processar dados

Um conjunto de dados geralmente requer algum pré-processamento antes de poder ser analisado. Você talvez tenha observado os valores ausentes presentes nas colunas de várias linhas. Os valores ausentes precisam ser limpos para que o modelo possa analisar os dados adequadamente. Em nosso caso, vamos remover quaisquer linhas que contém valores ausentes. Além disso, a coluna **normalized-losses** tem uma grande proporção de valores ausentes, portanto excluiremos totalmente essa coluna do modelo.

> [AZURE.TIP]Limpar os valores ausentes dos dados de entrada é um pré-requisito para usar a maioria dos módulos.

Primeiro, removeremos a coluna **normalized-losses** e removeremos qualquer linha que tenha dados ausentes.

1. Digite **colunas do projeto** na caixa de pesquisa na parte superior da paleta do módulo para encontrar o módulo [Colunas do Projeto][project-columns] e arraste-o para a tela do experimento para conectá-lo à porta de saída do conjunto de dados **Dados de preço de automóvel (Brutos)**. Esse módulo permite selecionar quais colunas de dados desejamos incluir ou excluir no modelo.

2. Selecione o módulo [Colunas do Projeto][project-columns] e clique em **Ativar seletor de coluna** no painel de propriedades.

	- Certifique-se de **Todas as colunas** esteja selecionado na lista suspensa de filtros, **Começa com**. Isso instrui [Colunas do Projeto][project-columns] a passar por todas as colunas (exceto por aquelas que estamos prestes a excluir).
	- Na próxima linha, selecione **Excluir** e **nomes da coluna** e clique dentro da caixa de texto. Uma lista de colunas é exibida. Selecione **normalized-losses** e ela será adicionada à caixa de texto.
	- Clique no botão de marca de seleção (OK) para fechar o seletor de coluna.

    ![Selecionar colunas][screen3]

	O painel de propriedades de **Colunas do Projeto** indica que ele passará por todas as colunas do conjunto de dados exceto por **normalized-losses**.

    ![Propriedades de Colunas do Projeto][screen4]

    > [AZURE.TIP]É possível adicionar um comentário em um módulo ao clicar duas vezes nele e inserir o texto. Isso pode ajudar a ver rapidamente o que o módulo está fazendo em seu experimento. Nesse caso, clique duas vezes no módulo [Colunas do Projeto][project-columns] e digite o comentário “Excluir perdas normalizadas”.

3. Arraste o módulo [Limpar valores ausentes][clean-missing-data] até a tela do experimento e conecte-o ao módulo [Colunas do Projeto][project-columns]. No painel de **Propriedades**, selecione **Remover linha inteira** em **Modo de limpeza** para limpar os dados removendo linhas que têm valores ausentes. Clique duas vezes no módulo e digite o comentário “Remover linhas de valor ausente".

	![Propriedades de Limpar Dados Ausentes][screen4a]

4. Execute o experimento clicando em **EXECUTAR** abaixo da tela do experimento.

Quando o experimento for concluído, todos os módulos terão uma marca de seleção verde para indicar que foram concluídos com sucesso. Observe também o status **Execução concluída** no canto superior direito.

![Primeira execução do experimento][screen5]

Tudo o que fizemos no experimento até esse ponto é limpar os dados. Se desejar exibir o conjunto de dados limpo, clique na porta de saída à esquerda do módulo [Limpar dados ausentes][clean-missing-data] ("conjunto de dados limpo") e selecione **Visualizar**. Observe que a coluna **normalized-losses** não está mais incluída e não há valores ausentes.

Agora que os dados estão limpos, estamos prontos para especificar quais recursos usaremos no modelo preditivo.

## Etapa 3: Definir recursos

No aprendizado de máquina, *recursos* são propriedades individuais mensuráveis de algo em que você está interessado. Em nosso conjunto de dados, cada linha representa um automóvel e cada coluna é um recurso desse automóvel. Localizar um bom conjunto de recursos para criar um modelo de previsão requer experimentação e conhecimento sobre o problema que você deseja resolver. Alguns recursos são melhores para prever o destino do que outros. Além disso, alguns recursos têm uma forte correlação com outros recursos, (por exemplo, city-mpg versus highway-mpg) portanto, eles não adicionarão muitas novas informações no modelo e poderão ser removidos.

Vamos criar um modelo que usa um subconjunto dos recursos em nosso conjunto de dados. É possível voltar e selecionar diferentes recursos, executar o experimento novamente e ver se você obtém melhores resultados. Como uma primeira suposição, selecionaremos os recursos (colunas) a seguir com o módulo [Colunas do Projeto][project-columns]. Observe que para treinar o modelo, precisamos incluir o valor *preço* que vamos prever.

	make, body-style, wheel-base, engine-size, horsepower, peak-rpm, highway-mpg, price

1. Arraste outro módulo [Colunas do Projeto][project-columns] até a tela do experimento e conecte-o à porta de saída à esquerda do módulo [Limpar dados ausentes][clean-missing-data]. Clique duas vezes no módulo e digite “Selecionar recursos de previsão".

2. Clique em **Iniciar seletor de coluna** no painel de **Propriedades**.

3. No seletor de coluna, selecione **Nenhuma coluna** para **Começar com**, em seguida, selecione **Incluir** e **nomes de coluna** na linha do filtro. Insira nossa lista de nomes da coluna. Isso instrui o módulo a percorrer apenas colunas que especificamos.

	> [AZURE.TIP]Como executamos o experimento, as definições de coluna para os dados passaram do conjunto de dados original por meio do módulo [Limpar dados ausentes][clean-missing-data]. Ao conectar [Colunas do Projeto][project-columns] a [Limpar dados ausentes][clean-missing-data], o módulo [Colunas do Projeto][project-columns] torna-se ciente das definições de coluna em nossos dados. Ao clicar na caixa **nomes de colunas**, é exibida uma lista das colunas e você pode selecionar as colunas que deseja adicionar à lista.

4. Clique no botão de marca de seleção (OK).

![Selecionar colunas][screen6]

Isso produzirá o conjunto de dados que será usado no algoritmo de aprendizado nas próximas etapas. Posteriormente, é possível retornar e tentar novamente com uma seleção diferente de recursos.

## Etapa 4: Escolher e aplicar um algoritmo de aprendizado

Agora que os dados estão prontos, construir um modelo preditivo consiste em treinamento e teste. Vamos usar nossos dados para treinar o modelo e depois testar o modelo para ver com que proximidade ele é capaz de prever os preços.

*Classificação* e *regressão* são dois tipos de técnicas de aprendizado de máquina supervisionado. A classificação é usada para fazer uma previsão de um conjunto definido de valores, como uma cor (vermelho, azul ou verde). A regressão é usada para fazer uma previsão de um conjunto contínuo de valores, como a idade de uma pessoa.

Desejamos prever o preço de um automóvel, que pode ser qualquer valor, portanto, usaremos um modelo de regressão. Para este exemplo, treinaremos um modelo simples de *regressão linear* e na próxima etapa o testaremos.

1. Podemos usar nossos dados para teste e treinamento dividindo-os em conjuntos separados de treinamento e de teste. Selecione e arraste o módulo [Dividir][split] até a tela do experimento e conecte-o à porta de saída do último módulo [Colunas do Projeto][project-columns]. Configure **Fração de linhas no primeiro conjunto de dados de saída** para 0,75. Desta forma, usaremos 75% dos dados para treinar o modelo e manteremos 25% para teste.

	> [AZURE.TIP]Alterando o parâmetro **Semente aleatória**, é possível produzir amostras aleatórias diferentes para treinamento e teste. Esse parâmetro controla a alimentação do gerador de número pseudoaleatório.

2. Execute o experimento. Isso permite que os módulos [Colunas do Projeto][project-columns] e [Dividir][split] passem pelas definições de coluna para os módulos que incluiremos em seguida.

3. Para selecionar o algoritmo de aprendizado, expanda a categoria **Aprendizado de Máquina** na paleta do módulo à esquerda da tela e expanda **Inicializar Modelo**. Isso exibe várias categorias de módulos que podem ser usados para inicializar os algoritmos de Aprendizado de Máquina.

	Para este experimento, selecione o módulo [Regressão Linear][linear-regression] na categoria **Regressão** (também é possível localizar o módulo digitando “Regressão Linear” na caixa de pesquisa da paleta) e arraste-o até a tela do experimento.

4. Localize e arraste o módulo [Treinar Modelo][train-model] até o experimento. Conecte-se à porta de entrada à esquerda da saída do módulo [Regressão Linear][linear-regression]. Conecte-se à porta de entrada à direita dos dados de treinamento (porta esquerda) do módulo [Dividir][split].

5. Selecione o módulo [Modelo de Treinamento][train-model], clique em **Ativar seletor de coluna** no painel **Propriedades** e selecione a coluna **preço**. Este é o valor que nosso modelo vai prever.

	![Selecione a coluna “preço"][screen7]

6. Execute o experimento.

O resultado é um modelo de regressão treinado que pode ser usado para pontuar novas amostras para fazer previsões.

![Aplicando o algoritmo de aprendizado de máquina][screen8]

## Etapa 5: Prever novos preços de automóveis

Agora que treinamos o modelo usando 75% de nossos dados, podemos usá-lo para classificar os outros 25% dos dados e ver se nosso modelo funciona bem.

1. Localize e arraste o módulo do [Modelo de Pontuação][score-model] para o experimento da tela e conecte a porta de entrada à esquerda à saída do módulo [Modelo de Treinamento][train-model]. Conecta a porta de entrada direita à saída de dados de teste (porta à direita) do módulo [Dividir][split].  

	![Módulo de Modelo de Pontuação][screen8a]

2. Para executar o experimento e exiba a saída do módulo [Modelo de Pontuação][score-model], clique duas vezes na porta de saída e selecione **Visualizar**. A saída mostra os valores previstos para o preço e os valores conhecidos dos dados de teste.

3. Por fim, para testar a qualidade dos resultados, selecione e arraste o módulo [Modelo de Avaliação][evaluate-model] para a tela do experimento e conecte a porta de entrada à esquerda à saída do módulo [Modelo de Pontuação][score-model]. (Existem duas portas de entrada porque o módulo [Modelo de Avaliação][evaluate-model] pode ser usado para comparar dois modelos.)

4. Execute o experimento.

Execute o experimento e exiba a saída do módulo [Modelo de Avaliação][evaluate-model], clique duas vezes na porta de saída e selecione **Visualizar**. As estatísticas a seguir são mostradas para nosso modelo:

- **Média de erros absolutos** (MAE) - A média de erros absolutos (um *erro* é a diferença entre o valor previsto e o valor real).
- **Raiz quadrada dos erros ao quadrado** (RMSE) - A raiz quadrada da média de erros quadrados de previsões feitas no conjunto de dados de teste.
- **Erro absoluto relativo** - A média de erros absolutos relativos à diferença absoluta entre os valores reais e a média de valores reais.
- **Erro ao quadrado relativo** - A média de erros quadrados relativos à diferença quadrada entre os valores reais e a média de todos os valores reais.
- **Coeficiente de Determinação** - Também conhecido como o **Valor quadrado R**, esta é uma métrica estatística que indica se o modelo se encaixa bem nos dados.

Para cada estatística de erro, menos é melhor. Um valor menor indica que as previsões se aproximam mais dos valores reais. Para **Coeficiente de Determinação**, quanto mais próximo o valor estiver de um (1,0), melhores as previsões.

![Resultados de avaliação][screen9]

O experimento final deve ficar assim:

![Tutorial de Aprendizado de Máquina: concluir o teste de regressão linear que usa técnicas de modelagem de previsão.][screen10]

## O que vem a seguir?

Agora que você concluiu um primeiro tutorial de aprendizado de máquina e o seu experimento foi configurado, você pode iterar para tentar melhorar o modelo. Por exemplo, é possível alterar os recursos usados em sua previsão. Ou é possível modificar as propriedades do algoritmo [Regressão Linear][linear-regression] ou tentar um algoritmo diferente. Ainda é possível adicionar vários algoritmos de Aprendizado de Máquina à sua experiência de aprendizagem e comparar dois usando o módulo [Avaliar modelo][evaluate-model].

> [AZURE.TIP]Use o botão **SALVAR COMO** na tela do experimento para copiar qualquer iteração do seu experimento. Você pode ver todas as iterações do seu experimento clicando em **EXIBIR O HISTÓRICO DE EXECUÇÃO** na tela. Consulte [Gerenciar iterações do experimento no Estúdio de Aprendizado de Máquina do Azure][runhistory] para obter mais detalhes.

[runhistory]: machine-learning-manage-experiment-iterations.md

Quando estiver satisfeito com seu modelo, será possível publicá-lo como um serviço Web a ser usado para prever preços de automóveis usando novos dados. Consulte [Publicar um serviço Web do Aprendizado de Máquina do Azure][publish] para obter mais detalhes.

[publish]: machine-learning-publish-a-machine-learning-web-service.md

Para obter uma explicação detalhada das técnicas de modelagem de previsão para criar, treinar, pontuar e publicar um modelo mais abrangente e detalhada, consulte [Desenvolver uma solução de previsão usando o Aprendizado de Máquina do Azure][walkthrough].

[walkthrough]: machine-learning-walkthrough-develop-predictive-solution.md

<!-- Images -->
[screen1]: ./media/machine-learning-create-experiment/screen1.png
[screen1a]: ./media/machine-learning-create-experiment/screen1a.png
[screen1b]: ./media/machine-learning-create-experiment/screen1b.png
[screen2]: ./media/machine-learning-create-experiment/screen2.png
[screen3]: ./media/machine-learning-create-experiment/screen3.png
[screen4]: ./media/machine-learning-create-experiment/screen4.png
[screen4a]: ./media/machine-learning-create-experiment/screen4a.png
[screen5]: ./media/machine-learning-create-experiment/screen5.png
[screen6]: ./media/machine-learning-create-experiment/screen6.png
[screen7]: ./media/machine-learning-create-experiment/screen7.png
[screen8]: ./media/machine-learning-create-experiment/screen8.png
[screen8a]: ./media/machine-learning-create-experiment/screen8a.png
[screen9]: ./media/machine-learning-create-experiment/screen9.png
[screen10]: ./media/machine-learning-create-experiment/complete-linear-regression-experiment.png


<!-- Module References -->
[evaluate-model]: https://msdn.microsoft.com/library/azure/927d65ac-3b50-4694-9903-20f6c1672089/
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
[clean-missing-data]: https://msdn.microsoft.com/library/azure/d2c5ca2f-7323-41a3-9b7e-da917c99f0c4/
[project-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
 

<!---HONumber=August15_HO6-->