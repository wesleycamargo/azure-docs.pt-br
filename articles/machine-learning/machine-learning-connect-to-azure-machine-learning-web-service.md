---
title: "Conectar a um Serviço Web do Machine Learning | Microsoft Docs"
description: "Com C# ou Python, conecte a um serviço Web do Azure Machine Learning usando uma chave de autorização."
services: machine-learning
documentationcenter: 
author: garyericson
manager: jhubbard
editor: cgronlun
ms.assetid: 59b07bab-b60f-48c4-a385-a162e50ec7c2
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/06/2017
ms.author: garye
translationtype: Human Translation
ms.sourcegitcommit: 52153526fb5b127823316b86fa05c0528151e18f
ms.openlocfilehash: 2dfcdf2207d1437a917c493075e3245bd58381ac


---
# <a name="connect-to-an-azure-machine-learning-web-service"></a>Conectar a um Serviço Web de Aprendizado de Máquina do Azure
A experiência do desenvolvedor do Azure Machine Learning é uma API de serviço Web para fazer previsões de dados de entrada em tempo real ou em modo de lote. Você pode usar o Azure Machine Learning Studio para criar previsões e implantar um serviço Web do Azure Machine Learning.

[!INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

Para saber como criar e implantar um serviço Web do Azure Machine Learning usando o Machine Learning Studio:

* Para obter um tutorial sobre como criar um teste no Estúdio de Aprendizado de Máquina, confira a seção [Crie seu primeiro experimento](machine-learning-create-experiment.md).
* Para obter detalhes sobre como implantar um serviço Web, confira [Implantar um serviço Web do Machine Learning](machine-learning-publish-a-machine-learning-web-service.md).
* Para obter mais informações sobre o Aprendizado de Máquina em geral, visite o [Centro de Documentação do Aprendizado de Máquina do Azure](https://azure.microsoft.com/documentation/services/machine-learning/).

## <a name="azure-machine-learning-web-service"></a>Serviço Web do Azure Machine Learning
Com o serviço Web do Azure Machine Learning, um aplicativo externo se comunica com um modelo de pontuação do fluxo de trabalho do Machine Learning em tempo real. Uma chamada do serviço Web do Machine Learning retorna resultados de previsão para um aplicativo externo. Para fazer uma chamada de serviço Web do Machine Learning, transmita uma chave de API que é criada quando você implanta uma previsão. O serviço Web do Machine Learning baseia-se em REST, uma opção popular de arquitetura para projetos de programação da Web.

O Aprendizado de Máquina do Azure tem dois tipos de serviços:

* Serviço de Solicitação-Resposta (RRS) – Um serviço de baixa latência e altamente escalonável que fornece uma interface para os modelos sem monitoração de estado criados e implantados no Estúdio de Aprendizado de Máquina.
* Serviço de Execução de Lote (BES) – Um serviço assíncrono que pontua um lote de registros de dados.

Para obter mais informações sobre os serviços Web do Machine Learning, confira [Implantar um serviço Web do Machine Learning](machine-learning-publish-a-machine-learning-web-service.md).

## <a name="get-an-azure-machine-learning-authorization-key"></a>Obtenha uma chave de autorização de Aprendizado de Máquina do Azure
Quando você implanta seu experimento, as chaves de API são geradas para o serviço Web. Você pode recuperar as chaves de vários locais.

### <a name="from-the-microsoft-azure-machine-learning-web-services-portal"></a>Por meio do portal de serviços Web do Microsoft Azure Machine Learning
Entre no portal [Serviços Web do Microsoft Azure Machine Learning](https://services.azureml.net).

Para recuperar a chave de API para um novo serviço Web do Machine Learning:

1. No portal de serviços Web do Azure Machine Learning, clique em **Serviços Web** no menu superior.
2. Clique no serviço Web para o qual deseja recuperar a chave.
3. No menu superior, clique em **Consumir**.
4. Copie e salve a **Chave Primária**.

Para recuperar a chave de API para um serviço Web clássico do Machine Learning:

1. No portal de serviços Web do Azure Machine Learning, clique em **Serviços Web Clássicos** no menu superior.
2. Clique no serviço Web com o qual está trabalhando.
3. Clique no ponto de extremidade para o qual deseja recuperar a chave.
4. No menu superior, clique em **Consumir**.
5. Copie e salve a **Chave Primária**.

### <a name="classic-web-service"></a>Serviço Web Clássico
 Você também pode recuperar uma chave para um serviço Web Clássico por meio do Machine Learning Studio ou do Portal Clássico do Azure.

#### <a name="machine-learning-studio"></a>Machine Learning Studio
1. No Estúdio de Aprendizado de Máquina, clique em **SERVIÇOS WEB** à esquerda.
2. Clique em um serviço Web. A **Chave de API** está na guia **PAINEL**.

#### <a name="azure-classic-portal"></a>portal clássico do Azure
1. Clique em **APRENDIZADO DE MÁQUINA** à esquerda.
2. Clique no espaço de trabalho no qual o serviço Web está localizado.
3. Clique em **SERVIÇOS WEB**.
4. Clique em um serviço Web.
5. Clique em um ponto de extremidade. A “CHAVE DE API” está mais abaixo na parte inferior direita.

## <a name="a-idconnectaconnect-to-a-machine-learning-web-service"></a><a id="connect"></a>Conectar-se a um serviço Web do Machine Learning
Você pode se conectar a um serviço Web do Machine Learning usando qualquer linguagem de programação que dá suporte à resposta e à solicitação HTTP. Você pode exibir exemplos em C#, Python e R de uma página de ajuda do serviço Web do Machine Learning.

**Ajuda da API do Machine Learning** Uma ajuda de API do Machine Learning é criada quando você implanta um serviço Web. Confira [Passo a passo do Aprendizado de Máquina do Azure – Implantar serviço Web](machine-learning-walkthrough-5-publish-web-service.md).
A ajuda da API do Machine Learning contém detalhes sobre um serviço Web de previsão.

1. Clique no serviço Web com o qual está trabalhando.
2. Clique no ponto de extremidade para o qual deseja exibir uma página de ajuda da API.
3. No menu superior, clique em **Consumir**.
4. Clique na **página de ajuda da API** nos pontos de extremidade Solicitação de Resposta ou Execução em Lote.

**Como exibir a ajuda da API do Machine Learning para um novo serviço Web**

No portal de serviços Web do Azure Machine Learning:

1. Clique em **SERVIÇOS WEB** no menu superior.
2. Clique no serviço Web para o qual deseja recuperar a chave.

Clique em **Consumir** para obter os URIs dos serviços de Solicitação-Resposta e Execução em Lotes, bem como o código de exemplo em C#, R e Python.

Clique em **API do Swagger** para obter a documentação baseada no Swagger para as APIs chamadas dos URIs fornecidos.

### <a name="c-sample"></a>Exemplo de C#
Para se conectar a um serviço Web do Machine Learning, use um **HttpClient** passando ScoreData. ScoreData contém um FeatureVector, um vetor com n dimensões de recursos numéricos que representa o ScoreData. Autentique no serviço de Aprendizado de Máquina com uma chave de API.

Para se conectar a um serviço Web do Machine Learning, o pacote Nuget **Microsoft.AspNet.WebApi.Client** deve ser instalado.

**Instalar o Nuget Microsoft.AspNet.WebApi.Client no Visual Studio**

1. Publique “Baixe o conjunto de dados de Download de UCI: Serviço Web do conjunto de dados da classe Adulto 2”.
2. Clique em **Ferramentas** > **Gerenciador de Pacotes do NuGet** > **Console do Gerenciador de Pacotes**.
3. Escolha **Install-Package Microsoft.AspNet.WebApi.Client**.

**Para executar o exemplo de código**

1. Publique o experimento “Exemplo 1: Baixe o conjunto de dados de UCI: conjunto de dados da classe Adulto 2”, parte da coleção de exemplos de Aprendizado de Máquina.
2. Atribua apiKey com a chave de um serviço Web. Confira a seção acima **Obter uma chave de autorização de Aprendizado de Máquina do Azure** .
3. Atribua serviceUri com o URI de solicitação.

### <a name="python-sample"></a>Exemplo de Python
Para se conectar a um serviço Web do Machine Learning, use a biblioteca **urllib2** passando ScoreData. ScoreData contém um FeatureVector, um vetor com n dimensões de recursos numéricos que representa o ScoreData. Autentique no serviço de Aprendizado de Máquina com uma chave de API.

**Para executar o exemplo de código**

1. Implante o experimento "Exemplo 1: Baixe o conjunto de dados de UCI: conjunto de dados da classe Adulto 2", parte da coleção de exemplos de Aprendizado de Máquina.
2. Atribua apiKey com a chave de um serviço Web. Consulte a seção **Obter uma chave de autorização do Azure Machine Learning** perto do início deste artigo.
3. Atribua serviceUri com o URI de solicitação.




<!--HONumber=Jan17_HO2-->


