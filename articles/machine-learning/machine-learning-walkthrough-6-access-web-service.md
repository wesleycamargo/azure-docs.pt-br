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
	ms.date="09/08/2015"
	ms.author="garye"/>


# Etapa 6 do passo a passo: acessar o serviço Web do Aprendizado de Máquina do Azure

Esta é a última etapa do passo a passo, [Desenvolvendo uma Solução Preditiva com o AM do Azure](machine-learning-walkthrough-develop-predictive-solution.md)


1.	[Criar um espaço de trabalho do Aprendizado de Máquina](machine-learning-walkthrough-1-create-ml-workspace.md)
2.	[Carregar dados existentes](machine-learning-walkthrough-2-upload-data.md)
3.	[Criar um novo teste](machine-learning-walkthrough-3-create-new-experiment.md)
4.	[Treinar e avaliar os modelos](machine-learning-walkthrough-4-train-and-evaluate-models.md)
5.	[Implantar o serviço Web](machine-learning-walkthrough-5-publish-web-service.md)
6.	**Acessar o serviço Web**

----------

Para que um serviço Web seja útil, os usuários precisam poder enviar dados a ele e receber os resultados. O serviço Web é um serviço Web do Azure que pode receber e retornar dados de uma das duas maneiras:

-	**Solicitação/Resposta** – O usuário envia uma ou mais linhas de dados de crédito para o serviço usando um protocolo HTTP, e o serviço responde com um conjunto de resultados.
-	**Execução em lote** - o usuário envia ao serviço a URL de um blob do Azure que contém uma ou mais linhas de dados de crédito. O serviço armazena os resultados em outro blob e retorna a URL desse contêiner.  

Na guia **PAINEL** para o serviço Web, há dois links para informações que ajudarão um desenvolvedor a escrever código para acessar esse serviço. Clique no link **Página de ajuda de AP**I na linha de **SOLICITAR/RESPONDER** e uma página será aberta contendo exemplo de código para usar o protocolo de solicitar/responder do serviço. Do mesmo modo, o link na linha **EXECUÇÃO EM LOTE** fornece um exemplo de código para fazer uma solicitação em lote para o serviço.

A página de ajuda de API inclui exemplos de linguagens de programação R, C# e Python.

Para obter mais informações sobre como acessar e usar o serviço Web, consulte [Como consumir um serviço Web de aprendizado de máquina do Azure que foi publicado de um experimento de aprendizado de máquina](machine-learning-consume-web-services.md).

<!---HONumber=Sept15_HO2-->