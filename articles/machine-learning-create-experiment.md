<properties 
	pageTitle="Criar um experimento simples no Estúdio de Aprendizado de Máquina do Azure | Azure" 
	description="Como criar uma experiência para treinar e testar um modelo simples no Estúdio de Aprendizado de Máquina do Microsoft Azure" 
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
	ms.date="01/07/2015" 
	ms.author="garye"/>

#Criar um experimento simples no Estúdio de Aprendizado de Máquina do Azure 
 
Um experimento de análise preditiva, em seu núcleo, consiste de componentes para criar um modelo, treinar o modelo e pontuar e testar o modelo.  É possível combiná-los para criar um experimento que pegue os dados, treine um modelo em relação a eles e aplique o modelo a novos dados.  Também é possível adicionar módulos para pré-processar dados e selecionar recursos, dividir dados em conjuntos de treinamento e teste e avaliar ou cruzar a validade da qualidade de seu modelo.  

Neste artigo, usaremos o Estúdio de Aprendizado de Máquina do Azure para desenvolver e iterar em um experimento analítico preditivo simples.  Para abrir o Estúdio de Aprendizado de Máquina, clique neste link:  [https://studio.azureml.net/Home](https://studio.azureml.net/Home).  Para obter mais informações sobre os primeiros passos no Estúdio de Aprendizado de Máquina, consulte [Página Inicial do Estúdio de Aprendizado de Máquina do Microsoft Azure](https://studio.azureml.net/). 
 

##Cinco etapas para criar um experimento 

Aqui estão as cinco etapas básicas que você pode seguir para compilar um experimento no Estúdio de Aprendizado de Máquina para que você possa criar, treinar e pontuar seu modelo:  

- Criar um modelo 
	- [Etapa 1:  Obter dados]
	- [Etapa 2:  Pré-processar dados]
	- [Etapa 3:  Definir recursos]
- Treinar o modelo 
	- [Etapa 4:  Escolher e aplicar um algoritmo de aprendizado]
- Pontuar e testar o modelo 
	- [Etapa 5:  Prever novos dados] 

[Etapa 1:  Obter dados]: #step-1-get-data
[Etapa 2:  Pré-processar dados]: #step-2-preprocess-data
[Etapa 3:  Definir recursos]: #step-3-define-features
[Etapa 4:  Escolher e aplicar um algoritmo de aprendizado]: #step-4-choose-and-apply-a-learning-algorithm
[Etapa 5:  Prever novos dados]: #step-5-predict-over-new-data 

Neste exemplo, vamos examinar a criação de um modelo de regressão que usa dados de exemplo de automóvel.  O objetivo é prever o preço de um automóvel usando diferentes variáveis, como especificações técnicas e de manufatura. 

### Etapa 1:  Obter dados

Há uma série de conjuntos de dados de exemplo incluídos no Estúdio de Aprendizado de Máquina, e você pode importar dados de várias fontes.  Neste exemplo, usaremos o conjunto de dados de exemplo incluído, **Dados de preço de automóvel (Brutos)**, que representa dados de preço de automóvel.

1. Inicie um novo experimento clicando em **+NOVO** na parte inferior da janela do Estúdio de Aprendizado de Máquina, selecione **EXPERIMENTO** e selecione "Experimento em branco".  Selecione o nome de experimento padrão na parte superior da tela e renomeie para algo significativo, por exemplo, **Previsão de preço de automóvel**.

2. À esquerda da tela do experimento está uma paleta de conjuntos de dados e módulos.  Digite **automóvel** na caixa de pesquisa na parte superior desta paleta para localizar o conjunto de dados rotulado **Dados de preço de automóvel (Brutos)**. 

	![Palette search][screen1a]

3. Arraste o conjunto de dados até a tela do experimento. 

	![Dataset][screen1]

Para ver a aparência dos dados, clique na porta de saída na parte inferior do conjunto de dados do automóvel e selecione **Visualizar**.  As variáveis no conjunto de dados aparecem como colunas e cada instância de um automóvel aparece como uma linha.  A coluna à direita (coluna 26 intitulada "preço") é a variável de destino que vamos tentar prever. 

![Dataset visualization][screen1b]

Feche a janela de visualização clicando no "**x**" no canto superior direito.

### Etapa 2:  Pré-processar dados

Um conjunto de dados geralmente requer algum pré-processamento antes de poder ser analisado.  Você talvez tenha observado os valores ausentes presentes nas colunas de várias linhas.  Para analisar os dados, esses valores ausentes precisam ser limpos.  Em nosso caso, vamos remover quaisquer linhas que contém valores ausentes.  Além disso, a coluna **perdas normalizadas** tem uma grande proporção de valores ausentes, portanto, excluiremos totalmente essa coluna do modelo. 

> [AZURE.TIP] Limpar os valores ausentes dos dados de entrada é um pré-requisito para usar a maioria dos módulos. 

Primeiro, removeremos a coluna **perdas normalizadas** e removeremos qualquer linha que possua dados ausentes. 

1. Digite **colunas do projeto** na caixa de pesquisa na parte superior da paleta do módulo para encontrar o módulo **Colunas do Projeto** e arraste-o para a tela do experimento para conectá-lo à porta de saída do conjunto de dados **Dados de preço de automóvel (Brutos)**.  Esse módulo permite selecionar quais colunas de dados desejamos incluir ou excluir no modelo. 

2. Selecione o módulo **Colunas do Projeto** e clique em **Iniciar seletor de coluna** no painel de propriedades. 

	- Certifique-se de **Todas as colunas** esteja selecionado na lista suspensa de filtros, **Começa com**.  Isso instrui as **Colunas do Projeto** a percorrer todas as colunas (exceto aquelas que estamos prestes a excluir). 
	- Na próxima linha, selecione **Excluir** e **nomes de coluna** e clique dentro da caixa de texto.  Uma lista de colunas é exibida.  Selecione **perdas normalizados** e ela será adicionada à caixa de texto. 
	- Clique no botão de marca de seleção (OK) para fechar o seletor de coluna.

    ![Select columns][screen3]
	
	O painel de propriedades para **Colunas do Projeto** indica que passará por todas as colunas do conjunto de dados, exceto **perdas normalizadas**. 

    ![Project Columns properties][screen4]

    > [AZURE.TIP] É possível adicionar um comentário em um módulo ao clicar duas vezes nele e inserir o texto.  Isso pode ajudar a ver rapidamente o que o módulo está fazendo em seu experimento.  Nesse caso, clique duas vezes no módulo **Colunas do Projeto** e digite o comentário "Excluir perdas normalizadas". 

3. Arraste o módulo **Programa de Limpeza de Valores Ausentes** até a tela do experimento e conecte-o ao módulo **Colunas do Projeto**.  No painel de **Propriedades**, selecione **Remover linha inteira** em **Para valores ausentes** para limpar os dados removendo linhas que têm valores ausentes.  Clique duas vezes no módulo e digite o comentário "Remover linhas de valor ausente".

	![Missing Values Scrubber properties][screen4a]

4. Execute o experimento clicando em **EXECUTAR** abaixo da tela do experimento.

Quando o experimento for concluído, todos os módulos terão uma marca de seleção verde para indicar que foram concluídos com sucesso.  Observe também o status **Execução concluída** no canto superior direito.

![First experiment run][screen5]

Tudo o que fizemos no experimento até esse ponto é limpar os dados.  Para exibir o conjunto de dados limpo, clique na porta da saída do módulo **Programa de Limpeza de Valores Ausentes** e selecione **Exibir**.  Observe que a coluna **perdas normalizadas** não está mais incluída e não há valores ausentes.

Agora que os dados estão limpos, estamos prontos para especificar quais recursos usaremos no modelo preditivo.

### Etapa 3:  Definir recursos

No aprendizado de máquina, os  *features* são propriedades mensuráveis individuais de algo em que você está interessado. Em nosso conjunto de dados, cada linha representa um automóvel e cada coluna é um recurso desse automóvel.  Localizar um bom conjunto de recursos para criar um modelo de previsão requer experimentação e conhecimento sobre o problema que você deseja resolver.  Alguns recursos são melhores para prever o destino do que outros.  Além disso, alguns recursos têm uma forte correlação com outros recursos, (por exemplo, city-mpg versus highway-mpg) portanto, eles não adicionarão muitas novas informações no modelo e poderão ser removidos.

Vamos criar um modelo que usa um subconjunto dos recursos em nosso conjunto de dados.  É possível voltar e selecionar diferentes recursos, executar o experimento novamente e ver se você obtém melhores resultados.  Como uma primeira suposição, selecionaremos os recursos (colunas) a seguir com o módulo **Colunas do Projeto**.  Observe que, para treinar o modelo, precisamos incluir o valor  *price* que vamos prever.

	make, body-style, wheel-base, engine-size, horsepower, peak-rpm, highway-mpg, price

1. Arraste outro módulo **Colunas do Projeto** até a tela do experimento e conecte-o ao módulo **Programa de Limpeza de Valores Ausentes**.  Clique duas vezes no módulo e digite "Selecionar recursos de previsão".

2. Clique em **Iniciar seletor de coluna** no painel de **Propriedades**. 

3. No seletor de coluna, selecione **Nenhuma coluna** para **Começar com**, em seguida, selecione **Incluir** e **nomes de coluna** na linha do filtro.  Insira nossa lista de nomes da coluna.  Isso instrui o módulo a percorrer apenas colunas que especificamos.

	> [AZURE.TIP] Como executamos o experimento, as definições de coluna para os dados passaram do conjunto de dados original por meio do módulo **Programa de Limpeza de Valores Ausentes**.  Ao conectar **Colunas do Projeto** ao **Programa de Limpeza de Valores Ausentes**, o módulo **Colunas do Projeto** torna-se ciente das definições de coluna em nossos dados.  Ao clicar na caixa **nomes de colunas**, é exibida uma lista das colunas e você pode selecionar as colunas que deseja adicionar à lista. 

4. Clique no botão de marca de seleção (OK).

![Select columns][screen6]

Isso produzirá o conjunto de dados que será usado no algoritmo de aprendizado nas próximas etapas.  Posteriormente, é possível retornar e tentar novamente com uma seleção diferente de recursos. 

### Etapa 4:  Escolher e aplicar um algoritmo de aprendizado

Agora que os dados estão prontos, criar um modelo de previsão consiste em treinamento e teste. *Classification* e *regression* são dois tipos de técnicas de aprendizado de máquina supervisionado.  A classificação é usada para fazer uma previsão de um conjunto definido de valores, como uma cor (vermelho, azul ou verde).  A regressão é usada para fazer uma previsão de um conjunto contínuo de valores, como a idade de uma pessoa.

Desejamos prever o preço de um automóvel, que pode ser qualquer valor, portanto, usaremos um modelo de regressão.  Neste exemplo, treinaremos um modelo  *linear regression* simples e na próxima etapa o testaremos. 

1. Divida os dados em conjuntos de treinamento e teste.  Selecione e arraste o módulo **Dividir** até a tela do experimento e conecte-o à porta de saída do último módulo **Colunas do Projeto**.  Configure **Fração de linhas no primeiro conjunto de dados de saída** para 0,75.  Desta forma, usaremos 75% dos dados para treinar o modelo e manteremos 25% para teste.

	> [AZURE.TIP] Alterando o parâmetro **Semente aleatória**, é possível produzir amostras aleatórias diferentes para treinamento e teste.  Esse parâmetro controla a alimentação do gerador de número pseudoaleatório.
	
2. Execute o experimento.  Isso permite que os módulos **Colunas do Projeto** e **Divisão** passem pelas definições de coluna para os módulos que incluiremos em seguida.  

3. Para selecionar o algoritmo de aprendizado, expanda a categoria **Aprendizado de Máquina** na paleta do módulo à esquerda da tela e expanda **Inicializar Modelo**.  Isso exibe várias categorias de módulos que podem ser usadas para inicializar um algoritmo de aprendizado. 

	Para este experimento, selecione o módulo **Regressão Linear** na categoria **Regressão** (também é possível localizar o módulo digitando "Regressão Linear" na caixa de pesquisa da paleta) e arraste-o até a tela do experimento.

4. Localize e arraste o módulo **Treinar Modelo** até o experimento.  Conecte-se à porta de entrada à esquerda da saída do módulo **Regressão Linear**.  Conecte-se à porta de entrada à direita dos dados de treinamento (porta esquerda) do módulo **Divisão**.

5. Execute o experimento para passar as definições de coluna para o módulo do **Modelo de Treinamento**.
 
6. Selecione o módulo **Modelo de Treinamento**, clique em **Abrir o seletor de coluna** no painel **Propriedades** e selecione a coluna **preço**.  Este é o valor que nosso modelo vai prever.

	![Select "price" column][screen7]

7. Execute o experimento. 

O resultado é um modelo de regressão treinado que pode ser usado para pontuar novas amostras para fazer previsões. 

![Applying the learning algorithm][screen8]

### Etapa 5:  Prever novos dados 

Agora que treinamos o modelo, podemos usá-lo para pontuar os outros 25% dos nossos dados e ver se nosso modelo funciona bem. 

1. Localize e arraste o módulo do **Modelo de Pontuação** para o experimento da tela e conecte a porta de entrada à esquerda à saída do módulo **Modelo de Treinamento**.  Conecta a porta de entrada direita à saída de dados de teste (porta à direita) do módulo **Divisão**.  

	![Score Model module][screen8a]

2. Para executar o experimento e exiba a saída do módulo **Modelo de Pontuação**, clique duas vezes na porta de saída e selecione **Visualizar**.  A saída mostra os valores previstos para o preço e os valores conhecidos dos dados de teste.  

3. Por fim, para testar a qualidade dos resultados, selecione e arraste o módulo **Avaliar Modelo** para a tela do experimento e conecte a porta de entrada à esquerda à saída do módulo **Modelo de Pontuação**.  (Existem duas portas de entrada porque o módulo **Avaliar Modelo** pode ser usado para comparar dois modelos.)
 
4. Execute o experimento e exiba a saída do módulo **Avaliar Modelo**, clique duas vezes na porta de saída e selecione **Visualizar**.  As estatísticas a seguir são mostradas para nosso modelo:

	- **Erro de Média Absoluta** (MAE):  A média dos erros absolutos (um *error* é a diferença entre o valor previsto e o valor real).
	- **Erro de Valor Eficaz** (RMSE):  A raiz quadrada da média de erros quadrados de previsões feitas no conjunto de dados de teste.
	- **Erro Absoluto Relativo**:  A média de erros absolutos relativos à diferença absoluta entre os valores reais e a média de valores reais.
	- **Erro Quadrado Relativo**:  A média de erros quadrados relativos à diferença quadrada entre os valores reais e a média de todos os valores reais.
	- **Coeficiente de Determinação**:  Também conhecido como o **Valor quadrado R**, esta é uma métrica estatística que indica se o modelo se encaixa bem nos dados.
	
	Para cada estatística de erro, menos é melhor.  Um valor menor indica que as previsões se aproximam mais dos valores reais.  Para **Coeficiente de Determinação**, quanto mais próximo o valor estiver de um (1,0), melhores as previsões.

	![Evaluation results][screen9]

O experimento final deve ficar assim:

![Complete experiment][screen10]

### O que vem a seguir?

Agora que você tem seu experimento configurado, é possível iterar para tentar melhorar o modelo.  Por exemplo, é possível alterar os recursos usados em sua previsão.  Ou é possível modificar as propriedades do algoritmo **Regressão Linear** ou tentar um algoritmo diferente.  Você ainda pode adicionar vários algoritmos ao seu experimento por vez e comparar dois deles usando o módulo **Avaliar Modelo**. 

> [AZURE.TIP] Use o botão **SALVAR COMO** na tela do experimento para copiar qualquer iteração do seu experimento.  Você pode ver todas as iterações do seu experimento clicando em **EXIBIR O HISTÓRICO DE EXECUÇÃO** na tela.  Consulte [Gerenciar iterações do experimento no Estúdio de Aprendizado de Máquina do Azure][runhistory] para obter mais detalhes.

[runhistory]: machine-learning-manage-experiment-iterations.md

Quando estiver satisfeito com seu modelo, será possível publicá-lo como um serviço Web a ser usado para prever preços de automóveis usando novos dados.  Consulte [Publicar um serviço Web de Aprendizado de Máquina do Azure][publicar] para obter mais detalhes.

[publicar]: machine-learning-publish-a-machine-learning-web-service.md

Para obter um passo a passo mais extenso e detalhado sobre a criação, treinamento, pontuação e publicação de um modelo de previsão, consulte [Desenvolver uma solução de previsão usando o Aprendizado de Máquina do Azure ][passo a passo]. 

[passo a passo]: machine-learning-walkthrough-develop-predictive-solution.md

<!-- Images -->
[screen1]:./media/machine-learning-create-experiment/screen1.png
[screen1a]:./media/machine-learning-create-experiment/screen1a.png
[screen1b]:./media/machine-learning-create-experiment/screen1b.png
[screen2]:./media/machine-learning-create-experiment/screen2.png
[screen3]:./media/machine-learning-create-experiment/screen3.png
[screen4]:./media/machine-learning-create-experiment/screen4.png
[screen4a]:./media/machine-learning-create-experiment/screen4a.png
[screen5]:./media/machine-learning-create-experiment/screen5.png
[screen6]:./media/machine-learning-create-experiment/screen6.png
[screen7]:./media/machine-learning-create-experiment/screen7.png
[screen8]:./media/machine-learning-create-experiment/screen8.png
[screen8a]:./media/machine-learning-create-experiment/screen8a.png
[screen9]:./media/machine-learning-create-experiment/screen9.png
[screen10]:./media/machine-learning-create-experiment/screen10.png

<!--HONumber=49-->