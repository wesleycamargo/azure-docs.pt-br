<properties title="Create a simple experiment in Azure Machine Learning Studio" pageTitle="Criar um experimento simples no Estúdio de Aprendizado de Máquina do Azure | Azure" description="How to create an experiment to train and test a simple model in Azure Machine Learning Studio" metaKeywords="" services="machine-learning" solutions="" documentationCenter="" authors="garye" manager="paulettm" editor="cgronlun"  videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/22/2014" ms.author="garye" />

#Criar um experimento simples no Estúdio de Aprendizado de Máquina do Azure 
 
Um experimento analítico preditivo, no seu núcleo, consiste em componentes para *criar um modelo*, *treinar o modelo* e *pontuar e testar o modelo*. É possível combiná-los para criar um experimento que pegue os dados, treine um modelo em relação a eles e aplique o modelo a novos dados. Também é possível incluir módulos para pré-processar dados e selecionar recursos, dividir dados em conjuntos de treinamento e teste e avaliar ou cruzar a validade da qualidade de seu modelo.  

Neste artigo, usaremos o Estúdio de Aprendizado de Máquina do Microsoft Azure para desenvolver e iterar em um experimento analítico preditivo simples.

##Cinco etapas para criar um experimento 

As cinco etapas básicas a serem seguidas para criar um experimento no Estúdio AM permitem criar, treinar e pontuar seu modelo:  

- Criar um modelo 
	- [Etapa 1: Obter dados]
	- [Etapa 2: Pré-processar dados]
	- [Etapa 3: Definir recursos]
- Treinar o modelo 
	- [Etapa 4: Escolher e aplicar um algoritmo de aprendizado]
- Pontuar e testar o modelo 
	- [Etapa 5: Prever novos dados] 

[Etapa 1: Obter dados]: #step-1-get-data
[Etapa 2: Pré-processar dados]: #step-2-pre-process-data
[Etapa 3: Definir recursos]: #step-3-define-features
[Etapa 4: Escolha e aplique um algoritmo de aprendizagem]: #step-4-choose-and-apply-a-learning-algorithm
[Etapa 5: Prever com novos dados]: #step-5-predict-over-new-data 

Neste exemplo, explicaremos a criação de um modelo de regressão usando dados de automóveis de amostra. O objetivo é prever o preço de um automóvel usando diferentes variáveis, como especificações técnicas e de manufatura. 

### Etapa 1: Obter dados

Há vários conjuntos de dados de amostra incluídos com o Estúdio AM e é possível importar dados de muitas origens diferentes. Para este exemplo, usaremos o conjunto de dados de amostra incluído, **Dados de preço de automóvel (Brutos)**, que representam dados de preço de automóvel.

1. Inicie um novo experimento clicando em **+NOVO** na parte inferior da janela do Estúdio AM e selecionando **EXPERIMENTO**. Renomeie o experimento de "Sem Título" para algo significativo, como "Previsão de preço de automóvel".

2. À esquerda da tela do experimento está uma paleta de conjuntos de dados e módulos. Digite "automóvel" na caixa de pesquisa na parte superior desta paleta para localizar o conjunto de dados rotulado **Dados de preço de automóvel (Brutos)**. 

	![Palette search][screen1a]

3. Arraste o conjunto de dados até a tela do experimento. 

	![Dataset][screen1]

Para ver a aparência dos dados, clique na porta de saída na parte inferior do conjunto de dados do automóvel e selecione **Visualizar**. As variáveis no conjunto de dados aparecem como colunas e cada instância de um automóvel aparece como uma linha. A coluna mais à direita "preço" (coluna 26) é a variável de destino que tentaremos prever. 

![Dataset visualization][screen1b]

Feche a janela de visualização clicando no "**x**" no canto superior direito.

### Etapa 2: Pré-processar dados

Um conjunto de dados geralmente requer algum pré-processamento antes de poder ser analisado. Você pode ter notado os valores ausentes presentes nas colunas de várias linhas - para analisar os dados, esses valores ausentes precisam ser limpos. Neste caso, apenas removeremos quaisquer linhas que tiverem valores ausentes. Além disso, a coluna "perdas normalizadas" possui uma proporção muito grande de valores ausentes, portanto, apenas excluiremos essa coluna do modelo. 

>**Dica** - Limpar os valores ausentes dos dados de entrada é um pré-requisito para usar a maioria dos módulos. 

Primeiro, removeremos a coluna "perdas normalizadas" e removeremos qualquer linha que possua dados ausentes. 

1. Arraste o módulo **Colunas do Projeto** até a tela do experimento e conecte-o à porta de saída do conjunto de dados **Dados de preço de automóvel (Brutos)**. Esse módulo permite selecionar quais colunas de dados desejamos incluir ou excluir no modelo. 

2. Selecione o módulo **Colunas do Projeto** e clique em **Iniciar seletor de coluna** no painel de propriedades. 

	- Assegure-se de que **Todas as colunas** estejam selecionadas no filtro suspenso **Começar com**. Isso direciona **Colunas do Projeto** para passar todas as colunas (exceto para aqueles que estamos prestes a excluir). 
	- Na próxima linha, selecione **** e **nomes da coluna** e clique dentro da caixa de texto. Uma lista de colunas é exibida - selecione "perdas normalizadas" e será incluída na caixa de texto. 
	- Clique na marca de seleção do botão **OK** para fechar o seletor de coluna.

    ![Select columns][screen3]
	
	O painel de propriedades para **Colunas do Projeto** indica que passará por todas as colunas do conjunto de dados, exceto "perdas normalizadas". 

    ![Project Columns properties][screen4]

    >**Dica** - É possível incluir um comentário em um módulo ao clicar duas vezes nele e inserir o texto. Isso pode ajudar a ver rapidamente o que o módulo está fazendo em seu experimento. Neste caso, clique duas vezes no módulo **Colunas do Projeto** e insira o comentário "Excluir perdas normalizadas". 

3. Arraste o módulo **Programa de Limpeza de Valores Ausentes** até a tela do experimento e conecte-a ao módulo **Colunas do Projeto**. No painel de propriedades, selecione **Remover linha inteira** em **Para valores ausentes** para limpar os dados removendo linhas que possuem valores ausentes.  Clique duas vezes no módulo e insira o comentário "Remover linhas de valores ausentes".

	![Missing Values Scrubber properties][screen4a]

4. Execute o experimento clicando em **EXECUTAR** abaixo da tela do experimento.

Quando o experimento for concluído, todos os módulos terão uma marca de seleção verde para indicar que foram concluídos com sucesso. Observe também o status "Execução concluída" no canto superior direito.

![First experiment run][screen5]

Tudo que o experimento fez até este momento foi limpar os dados. Para exibir o conjunto de dados limpo, clique na porta da saída do módulo **Programa de Limpeza de Valores Ausentes** e selecione **Exibir**. Observe que a coluna "perdas normalizadas" não está mais incluída e não há valores ausentes.

Agora que os dados estão limpos, estamos prontos para especificar quais recursos usaremos no modelo preditivo.

### Etapa 3: Definir recursos

No aprendizado de máquina, os *recursos* são propriedades mensuráveis individuais de algo em que você está interessado. Em nosso conjunto de dados, cada linha representa um automóvel e cada coluna é um recurso desse automóvel. Encontrar um bom conjunto de recursos para criar um modelo preditivo requer a experimentação e o conhecimento sobre o problema em mãos. Alguns recursos são melhores para prever o destino do que outros. Além disso, alguns recursos têm uma forte correlação com outros recursos, por exemplo, city-mpg versus highway-mpg, portanto, eles não incluirão muitas novas informações no modelo e poderão ser removidos.

Vamos criar um modelo que usa um subconjunto dos recursos em nosso conjunto de dados. É possível voltar e selecionar diferentes recursos, executar o experimento novamente e ver se você obtém melhores resultados. Como uma primeira suposição, selecionaremos os recursos (colunas) a seguir com o módulo **Colunas do Projeto**. Observe que para treinar o modelo, precisamos incluir o valor *preço* que vamos prever.

	marca, estilo do corpo, largura entre eixos, tamanho do motor, potência, rpm de pico, mpg em estrada, preço

1. Arraste outro módulo **Colunas do Projeto** até a tela do experimento e conecte-o ao módulo **Programa de Limpeza de Valores Ausentes**. Clique duas vezes no módulo e insira "Selecionar recursos para previsão".

2. Clique em **Iniciar seletor de coluna** no painel de propriedades. 

3. No seletor de coluna, selecione **Nenhuma coluna** para **Começar com**, em seguida, selecione **Incluir** e **nomes de coluna** na linha do filtro. Insira nossa lista de nomes da coluna. Isso direciona o módulo para passar apenas pelas colunas que especificarmos.

	>**Dica** - Como executamos o experimento até este ponto, as definições de coluna para nossos dados passaram do conjunto de dados original através do módulo **Programa de Limpeza de Valores Ausentes**. Ao conectar **Colunas do Projeto** ao **Programa de Limpeza de Valores Ausentes**, o módulo **Colunas do Projeto** torna-se ciente das definições de coluna em nossos dados. Ao clicar na caixa de nomes da coluna, uma lista de colunas é exibida e é possível selecionar uma coluna que desejar incluir na lista de cada vez. 

4. Clique em **OK**.

![Select columns][screen6]

Isso produzirá o conjunto de dados que será usado no algoritmo de aprendizado nas próximas etapas. Posteriormente, é possível retornar e tentar novamente com uma seleção diferente de recursos. 

### Etapa 4: Escolher e aplicar um algoritmo de aprendizado

Agora que os dados estão prontos, construir um modelo preditivo consiste em treinamento e teste. *Classificação* e *regressão* são dois tipos de técnicas de aprendizado de máquina supervisionado. A classificação é usada para fazer uma previsão de um conjunto definido de valores, como uma cor (vermelho, azul ou verde). A regressão é usada para fazer uma previsão de um conjunto contínuo de valores, como a idade de uma pessoa.

Desejamos prever o preço de um automóvel, que pode ser qualquer valor, portanto, usaremos um modelo de regressão. Para este exemplo, treinaremos um modelo simples de *regressão linear* e na próxima etapa o testaremos. 

1. Divida os dados em conjuntos de treinamento e teste. Selecione e arraste o módulo **Dividir** até a tela do experimento e conecte-o à porta de saída do último módulo **Colunas do Projeto**. Configure **Fração de linhas no primeiro conjunto de dados de saída** para 0,75. Desta forma, usaremos 75% dos dados para treinar o modelo e manteremos 25% para teste.

	>**Dica** - Ao alterar o parâmetro **Random seed**, é possível produzir amostras aleatórias diferentes para treinamento e teste. Esse parâmetro controla a alimentação do gerador de número pseudoaleatório.
	
2. Execute o experimento. Isso permite que os módulos **Colunas do Projeto** e **Dividir** passem pelas definições de coluna para os módulos que incluiremos em seguida.  

2. Para selecionar o algoritmo de aprendizado, expanda a categoria **Aprendizado de Máquina** na paleta do módulo à esquerda da tela e expanda **Inicializar Modelo**. Isso exibe várias categorias de módulos que podem ser usadas para inicializar um algoritmo de aprendizado. 

	Para este exemplo de experimento, selecione o módulo **Regressão Linear** na categoria **Regressão** (também é possível localizar o módulo digitando "Regressão Linear" na caixa de pesquisa da paleta) e arraste-o até a tela do experimento.

3. Localize e arraste o módulo **Treinar Modelo** até o experimento. Clique em **Ativar seletor de coluna** e selecione a coluna *preço*. Este é o valor que nosso modelo vai prever.

	![Select "price" column][screen7]

4. Conecte a porta de entrada esquerda à saída do módulo **Regressão Linear** e a porta de entrada direita à saída de dados de treinamento (porta esquerda) do módulo **Dividir**.  

5. Execute o experimento. 

O resultado é um modelo de regressão treinado que pode ser usado para pontuar novas amostras para fazer previsões. 

![Applying the learning algorithm][screen8]

### Etapa 5: Prever novos dados 

Agora que treinamos o modelo, podemos usá-lo para pontuar os outros 25% de nossos dados e ver se nosso modelo funciona bem. 

1. Localize e arraste o módulo **Modelo de Pontuação** à tela do experimento e conecte a porta de entrada esquerda à saída do módulo **Treinar Modelo** e a porta de entrada direita à saída de dados de teste (porta direita) do módulo **Dividir**.  

	![Score Model module][screen8a]

2. Execute o experimento e exiba a saída do módulo **Modelo de Pontuação** (clique duas vezes na porta de saída e selecione **Visualizar**). A saída mostrará os valores preditivos para preço junto com os valores conhecidos dos dados de teste.  

3. Por fim, para testar a qualidade dos resultados, selecione e arraste o módulo **Avaliar Modelo** até a tela do experimento e conecte a porta de entrada esquerda à porta de saída do módulo **Modelo de Pontuação** (há duas portas de entrada, porque o módulo **Avaliar Modelo** pode ser usado para comparar dois modelos).
 
4. Execute o experimento e exiba a saída do módulo **Avaliar Modelo** (clique duas vezes na porta de saída e selecione **Visualizar**). As estatísticas a seguir são mostradas para nosso modelo.

	- **Média de erros absolutos** (MAE) - A média de erros absolutos (um *erro* é a diferença entre o valor previsto e o valor real).
	- **Root Mean Squared Error** (RMSE) - A raiz quadrada da média de erros quadrados de previsões feitas no conjunto de dados de teste.
	- **Erro absoluto relativo** - A média de erros absolutos relativos à diferença absoluta entre os valores reais e a média de valores reais.
	- **Erro quadrado relativo** - A média de erros quadrados relativos à diferença quadrada entre os valores reais e a média de todos os valores reais.
	- **Coeficiente de Determinação** - Também conhecido como o "Valor quadrado R", esta é uma métrica estatística que indica se o modelo se encaixa bem nos dados.
	
	Para cada uma das estatísticas de erro, menor é melhor - um valor menor indica que as previsões mais próximas correspondem aos valores reais. Para **Coeficiente de Determinação**, quanto mais próximo o valor estiver de um (1,0), melhores as previsões.

	![Evaluation results][screen9]

O experimento final deve ficar assim:

![Complete experiment][screen10]

### O que vem a seguir?

Agora que você tem seu experimento configurado, é possível iterar para tentar melhorar o modelo. Por exemplo, é possível alterar os recursos usados em sua previsão. Ou é possível modificar as propriedades do algoritmo **Regressão Linear** ou tentar um algoritmo diferente. É possível até incluir diversos algoritmos em seu experimento de uma vez e compará-los (dois por vez) usando o módulo **Avaliar Modelo**. 

>**Dica** - Use o botão **SALVAR COMO** abaixo da tela do experimento para fazer uma cópia de qualquer iteração de seu experimento. É possível ver todas as iterações de seu experimento clicando em **EXIBIR HISTÓRICO DE EXECUÇÕES** abaixo da tela. Consulte o tópico de ajuda do Estúdio AM **Visualizando o Histórico de Execuções** para obter mais detalhes.

Quando estiver satisfeito com seu modelo, será possível publicá-lo como um serviço Web a ser usado para prever preços de automóveis usando novos dados. Consulte o tópico de ajuda do Estúdio AM **Publicando Experimentos** para obter mais detalhes.

Para obter um acompanhamento mais extenso e detalhado da criação, treinamento, pontuação e publicação de um modelo preditivo, consulte [Passo a passo: Desenvolver uma solução preditiva com o Aprendizado de Máquina do Azure](http://azure.microsoft.com/pt-br/documentation/articles/machine-learning-walkthrough-develop-predictive-solution/). 


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
