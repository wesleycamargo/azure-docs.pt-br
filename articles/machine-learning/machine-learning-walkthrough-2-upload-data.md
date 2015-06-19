<properties 
	pageTitle="Etapa 2: Carregar dados em um teste de aprendizado de máquina | Azure" 
	description="Solução alternativa. Etapa 2: Carregar dados públicos existentes em Studio de aprendizado de máquina do Azure" 
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
	ms.date="01/06/2015" 
	ms.author="garye"/>

Esta é a segunda etapa do passo a passo, [Desenvolvendo uma Solução Preditiva com o AM do Azure][develop]:

[develop]: ../machine-learning-walkthrough-develop-predictive-solution/


1.	[Criar um espaço de trabalho do AM][create-workspace]
2.	**Fazer upload de dados existentes**
3.	[Criar um novo experimento][create-new]
4.	[Treinar e avaliar os modelos][train-models]
5.	[Publicar o serviço Web][publish]
6.	[Acessar o serviço Web][access-ws]

[create-workspace]: ../machine-learning-walkthrough-1-create-ml-workspace/
[carregar dados]: ../machine-learning-walkthrough-2-upload-data/
[create-new]: ../machine-learning-walkthrough-3-create-new-experiment/
[train-models]: ../machine-learning-walkthrough-4-train-and-evaluate-models/
[publish]: ../machine-learning-walkthrough-5-publish-web-service/
[access-ws]: ../machine-learning-walkthrough-6-access-web-service/

----------

# Etapa 2: Carregar dados existentes no experimento de Aprendizado de Máquina do Azure  

Para desenvolver um modelo preditivo para risco de crédito, usaremos o "Conjunto de Dados Statlog (Dados de Crédito Alemão) UCI" do repositório de Aprendizado de Máquina UCI. Você pode encontrar mais informações aqui:  
<a href="http://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data)">http://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data)</a>

Usaremos o arquivo chamado **german.data**. Baixe esse arquivo em sua unidade de disco rígido local.  

Esse conjunto de dados contém linhas de 20 variáveis para 1000 candidatos antigos de crédito. Essas 20 variáveis representam o vetor de recurso do conjunto de dados que fornece características de identificação para cada candidato de crédito. Uma coluna adicional em cada linha representa o risco de crédito do candidato, com 700 candidatos identificados como um risco de crédito baixo e 300 como um alto risco.   

O website UCI fornece uma descrição dos atributos do vetor de recurso que inclui informações financeiras, histórico de crédito, status de emprego e informações pessoais. Para cada candidato, foi dada uma classificação binária indicando se são um risco baixo ou alto de crédito.  

Usaremos estes dados para treinar um modelo analítico preditivo. Quando tivermos concluído, nosso modelo deverá ser capaz de aceitar informações para novas pessoas e prever se são um risco de crédito alto ou baixo.  

Aqui está uma mudança interessante. A descrição do conjunto de dados explica que a classificação incorreta de uma pessoa como um risco de crédito baixo quando, na verdade, são um risco de crédito alto, é 5 vezes mais onerosa à instituição financeira do que a classificação incorreta de um risco de crédito baixo como alto. Uma forma simples de considerar isto em nosso experimento é duplicando (5 vezes) essas entradas que representam alguém com um risco de crédito alto. Então, se o modelo classificar incorretamente um risco de crédito alto como baixo, ele fará essa classificação incorreta 5 vezes, uma para cada duplicado. Isso aumentará o custo deste erro nos resultados de treinamento.  

## Converter o formato do conjunto de dados
O conjunto de dados original usa um formato separado por espaço em branco. O Estúdio AM trabalha melhor com um arquivo separado por vírgulas (CSV), então converteremos o conjunto de dados substituindo espaços por vírgulas.  

Podemos fazer isso usando o comando Windows PowerShell a seguir:   

	cat german.data | %{$_ -replace " ",","} | sc german.csv  

Também podemos fazer isso usando o comando Unix sed:  

	sed 's/ /,/g' german.data > german.csv  

## Fazer upload do conjunto de dados no Estúdio AM

Uma vez que os dados tiverem sido convertidos para o formato CSV, precisaremos fazer upload deles no Estúdio AM.  

1.	No Estúdio AM, clique em **+NOVO** na parte inferior da janela.
2.	Selecione **CONJUNTO DE DADOS**.
3.	Selecione **DO ARQUIVO LOCAL**.
4.	No diálogo **Carregar um novo conjunto de dados**, clique em **Procurar** e localize o arquivo **german.csv** criado.
5.	Insira um nome para o conjunto de dados. Para este exemplo, vamos chamá-lo de "Dados do cartão de crédito alemão UCI".
6.	Para tipo de dados, selecione "Arquivo CSV Genérico Sem Cabeçalho (.nh.csv)".
7.	Inclua uma descrição se desejar.
8.	Clique em **OK**.  

![Upload the dataset][1]  

 
Isso faz upload dos dados em um Módulo de conjunto de dados que podemos usar em um experimento.

**Avançar: [Criar um novo experimento][create-new]**

[1]: ./media/machine-learning-walkthrough-2-upload-data/upload1.png

<!--HONumber=46--> 
 