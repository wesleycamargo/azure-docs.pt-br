<properties 
	pageTitle="Etapa 2: carregar dados em um experimento de Aprendizado de Máquina | Microsoft Azure" 
	description="Etapa 2 - desenvolver um passo a passo de solução de previsão: carregamento armazenado de dados públicos no Estúdio de Aprendizado de Máquina do Azure." 
	services="machine-learning" 
	documentationCenter="" 
	authors="garyericson" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="machine-learning" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/10/2015" 
	ms.author="garye"/>


# Etapa 2 do passo a passo: carregar dados existentes no experimento de Aprendizado de Máquina do Azure

Esta é a segunda etapa do passo a passo, [Desenvolvendo uma Solução Preditiva com o Azure ML](machine-learning-walkthrough-develop-predictive-solution.md)


1.	[Criar um espaço de trabalho do Aprendizado de Máquina](machine-learning-walkthrough-1-create-ml-workspace.md)
2.	**Carregar dados existentes**
3.	[Criar um novo teste](machine-learning-walkthrough-3-create-new-experiment.md)
4.	[Treinar e avaliar os modelos](machine-learning-walkthrough-4-train-and-evaluate-models.md)
5.	[Publicar o serviço Web](machine-learning-walkthrough-5-publish-web-service.md)
6.	[Acessar o serviço Web](machine-learning-walkthrough-6-access-web-service.md)

----------

Para desenvolver um modelo preditivo para risco de crédito, usaremos o “Conjunto de Dados Statlog (Dados de Crédito Alemão) UCI” do repositório de Aprendizado de Máquina UCI. Você pode encontrá-lo aqui: <a href="http://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data)">http://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data)</a>

Usaremos o arquivo chamado **german.data**. Baixe esse arquivo em sua unidade de disco rígido local.

Esse conjunto de dados contém linhas de 20 variáveis para 1000 candidatos antigos de crédito. Essas 20 variáveis representam o vetor de recurso do conjunto de dados que fornece características de identificação para cada candidato de crédito. Uma coluna adicional em cada linha representa o risco de crédito do candidato, com 700 candidatos identificados como um risco de crédito baixo e 300 como um alto risco.

O website UCI fornece uma descrição dos atributos do vetor de recurso que inclui informações financeiras, histórico de crédito, status de emprego e informações pessoais. Para cada candidato, foi dada uma classificação binária indicando se são um risco baixo ou alto de crédito.

Usaremos estes dados para treinar um modelo analítico preditivo. Quando tivermos concluído, nosso modelo deverá ser capaz de aceitar informações para novas pessoas e prever se são um risco de crédito alto ou baixo.

Aqui está uma mudança interessante. A descrição do conjunto de dados explica que a classificação incorreta de uma pessoa como um risco de crédito baixo quando, na verdade, são um risco de crédito alto, é 5 vezes mais onerosa à instituição financeira do que a classificação incorreta de um risco de crédito baixo como alto. Uma forma simples de considerar isto em nosso experimento é duplicando (5 vezes) essas entradas que representam alguém com um risco de crédito alto. Então, se o modelo classificar incorretamente um risco de crédito alto como baixo, ele fará essa classificação incorreta 5 vezes, uma para cada duplicado. Isso aumentará o custo deste erro nos resultados de treinamento.

##Converter o formato do conjunto de dados
O conjunto de dados original usa um formato separado por espaço em branco. O Estúdio de Aprendizado de Máquina trabalha melhor com um arquivo CSV (de valores separados por vírgula). Então, converteremos o conjunto de dados substituindo espaços por vírgulas.

Podemos fazer isso usando o seguinte comando do Windows PowerShell:

	cat german.data | %{$_ -replace " ",","} | sc german.csv  

Também podemos fazer isso usando o comando Unix sed:

	sed 's/ /,/g' german.data > german.csv  

##Carregar o conjunto de dados para o Estúdio de Aprendizado de Máquina

Depois que os dados tiverem sido convertidos no formato CSV, precisaremos carregá-los no Estúdio de Aprendizado de Máquina.

1.	Entre no Estúdio de Aprendizado de Máquina ([https://studio.azureml.net](https://studio.azureml.net)) usando a conta da Microsoft que você especificou como o proprietário do espaço de trabalho e clique na guia **Estúdio** na parte superior.
2.	Clique em **+NOVO** na parte inferior da janela.
3.	Selecione **CONJUNTO DE DADOS**.
4.	Selecione **DO ARQUIVO LOCAL**.
5.	No diálogo **Fazer upload de um novo conjunto de dados**, clique em **Pesquisar** e localize o arquivo **german.csv** criado.
6.	Insira um nome para o conjunto de dados. Para este exemplo, vamos chamá-lo de "Dados do cartão de crédito alemão UCI".
7.	Para tipo de dados, selecione **Arquivo CSV genérico sem cabeçalho (.nh.csv)**.
8.	Inclua uma descrição se desejar.
9.	Clique em **OK**.  

![Carregar o conjunto de dados][1]

 
Isso carrega os dados em um módulo de conjunto de dados que podemos usar em um experimento.

Para obter mais informações sobre como importar vários tipos de dados para um experimento, confira [Importar seus dados de treinamento para o Estúdio de Aprendizado de Máquina do Azure](machine-learning-import-data.md).

**A seguir: [criar um novo teste](machine-learning-walkthrough-3-create-new-experiment.md)**

[1]: ./media/machine-learning-walkthrough-2-upload-data/upload1.png
 

<!---HONumber=July15_HO3-->