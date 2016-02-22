<properties
	pageTitle="Etapa 6: acessar o serviço Web do Aprendizado de Máquina | Microsoft Azure"
	description="Etapa 6: desenvolver um passo a passo de solução de previsão: acessar um serviço Web do Aprendizado de Máquina do Azure ativo."
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
	ms.date="02/03/2016"
	ms.author="garye"/>


# Etapa 6 do passo a passo: acessar o serviço Web do Aprendizado de Máquina do Azure

Esta é a última etapa do passo a passo, [Desenvolver uma solução de análise preditiva com o Aprendizado de Máquina do Azure](machine-learning-walkthrough-develop-predictive-solution.md)


1.	[Criar um espaço de trabalho do Aprendizado de Máquina](machine-learning-walkthrough-1-create-ml-workspace.md)
2.	[Carregar dados existentes](machine-learning-walkthrough-2-upload-data.md)
3.	[Criar um novo teste](machine-learning-walkthrough-3-create-new-experiment.md)
4.	[Treinar e avaliar os modelos](machine-learning-walkthrough-4-train-and-evaluate-models.md)
5.	[Implantar o serviço Web](machine-learning-walkthrough-5-publish-web-service.md)
6.	**Acessar o serviço Web**

----------

Na etapa anterior neste passo a passo, implantamos um serviço Web que usa o nosso modelo de previsão de risco de crédito. Agora, os usuários precisam poder enviar dados a ele e receber os resultados.

O serviço Web é um serviço Web do Azure que pode receber e retornar dados usando as APIs REST de uma das duas maneiras:

-	**Solicitação/Resposta** – O usuário envia uma ou mais linhas de dados de crédito para o serviço usando um protocolo HTTP, e o serviço responde com um ou mais conjunto de resultados.
-	**Execução em lote** - o usuário armazena uma ou mais linhas de dados de crédito em um blob do Azure e envia o local do blob ao serviço. O serviço pontua todas as linhas de dados no blob de entrada, armazena os resultados em outro blob e retorna a URL desse contêiner.  

A maneira mais rápida e fácil de acessar o serviço Web é por meio dos modelos de aplicativos Web disponíveis no [Marketplace de Aplicativos Web do Azure](https://azure.microsoft.com/marketplace/web-applications/all/). Esses modelos de aplicativo Web podem compilar um aplicativo Web personalizado que conhece os dados de entrada do seu serviço Web e o que ele retornará. Basta fornecer acesso aos dados e ao serviço Web, para que o modelo faça o resto.

Para obter mais informações sobre como usar modelos de aplicativos da Web, consulte [Consumir um serviço Web de Aprendizado de Máquina do Azure com um modelo de aplicativo Web](machine-learning-consume-web-service-with-web-app-template.md).

Você também pode desenvolver um aplicativo personalizado para acessar o serviço Web usando código inicial fornecido nas linguagens de programação R, C# e Python. Você pode ver todos os detalhes em [Como consumir um serviço Web de Aprendizado de Máquina do Azure que foi publicado por meio de um experimento de Aprendizado de Máquina](machine-learning-consume-web-services.md).

<!---HONumber=AcomDC_0211_2016-->