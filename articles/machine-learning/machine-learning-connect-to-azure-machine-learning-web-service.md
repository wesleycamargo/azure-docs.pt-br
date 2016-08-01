<properties
	pageTitle="Conectar a um Serviço Web de Aprendizado de Máquina | Microsoft Azure"
	description="Com C# ou Python, conecte a um serviço Web de Aprendizado de Máquina do Azure usando uma chave de autorização."
	services="machine-learning"
	documentationCenter=""
	authors="garyericson"
	manager="paulettm"
	editor="cgronlun" />

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/06/2016" 
	ms.author="garye" />


# Conectar a um Serviço Web de Aprendizado de Máquina do Azure
A experiência do desenvolvedor de Aprendizado de Máquina do Azure é uma API de serviço Web para fazer previsões de dados de entrada em tempo real ou em modo de lote. Você pode usar o Estúdio de Aprendizado de Máquina do Azure para criar previsões e implantar um serviço Web do Aprendizado de Máquina do Azure.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

Para saber como criar e implantar um serviço Web do Aprendizado de Máquina usando o Estúdio de Aprendizado de Máquina:

- Para obter um tutorial sobre como criar um teste no Estúdio de Aprendizado de Máquina, confira a seção [Crie seu primeiro experimento](machine-learning-create-experiment.md).
- Para obter detalhes sobre como implantar um serviço Web, confira a seção [Implantar um serviço Web de Aprendizado de Máquina](machine-learning-publish-a-machine-learning-web-service.md).
- Para obter mais informações sobre o Aprendizado de Máquina em geral, visite o [Centro de Documentação do Aprendizado de Máquina do Azure](https://azure.microsoft.com/documentation/services/machine-learning/).

## Serviço Web de Aprendizado de Máquina do Azure ##

Com o serviço Web de Aprendizado de Máquina do Azure, um aplicativo externo se comunica com um modelo de pontuação do fluxo de trabalho de Aprendizado de Máquina em tempo real. Uma chamada do serviço Web de Aprendizado de Máquina retorna resultados de previsão para um aplicativo externo. Para fazer uma chamada de serviço Web de Aprendizado de Máquina, transmita uma chave de API que é criada quando você implanta uma previsão. O serviço Web de Aprendizado de Máquina baseia-se em REST, uma opção popular de arquitetura para projetos de programação da Web.

O Aprendizado de Máquina do Azure tem dois tipos de serviços:

- Serviço de Solicitação-Resposta (RRS) – Um serviço de baixa latência e altamente escalonável que fornece uma interface para os modelos sem monitoração de estado criados e implantados no Estúdio de Aprendizado de Máquina.
- Serviço de Execução de Lote (BES) – Um serviço assíncrono que pontua um lote de registros de dados.

Para obter mais informações sobre os serviços Web de Aprendizado de Máquina, confira a seção [Implantar um serviço Web de Aprendizado de Máquina](machine-learning-publish-a-machine-learning-web-service.md).

## Obtenha uma chave de autorização de Aprendizado de Máquina do Azure ##

Quando você implanta seu experimento, as chaves de API são geradas para o serviço Web. Onde você recupera as chaves depende de como o experimento foi implantado — como um novo serviço Web ou um serviço Web clássico.

## Serviço Web Clássico ##

 Você pode recuperar uma chave no Estúdio de Aprendizado de Máquina ou portal do Azure.

### Estúdio de Aprendizado de Máquina ###

1. No Estúdio de Aprendizado de Máquina, clique em **SERVIÇOS WEB** à esquerda.
2. Clique em um serviço Web. A **chave de API** está na guia **PAINEL**.

### Portal do Azure ###

1. Clique em **APRENDIZADO DE MÁQUINA** à esquerda.
2. Clique em um espaço de trabalho.
3. Clique em **SERVIÇOS WEB**.
4. Clique em um serviço Web.
5. Clique em um ponto de extremidade. A “CHAVE DE API” está mais abaixo na parte inferior direita.


## Novo serviço Web 

Para recuperar a chave de API, para um novo serviço Web de Aprendizado de Máquina, você deve entrar no portal dos [Serviços Web de Aprendizado de Máquina do Microsoft Azure](https://services.azureml.net/quickstart).

1. No portal de Serviços Web de Aprendizado de Máquina do Azure, clique em **SERVIÇOS Web** no menu superior.
2. Clique no serviço Web para o qual deseja recuperar a chave.
3. No menu superior, clique em **Consumir**.
4. Copie e salve a **Chave Primária**.

## <a id="connect"></a>Conectar-se a um serviço Web de Aprendizado de Máquina

Você pode se conectar a um serviço Web de Aprendizado de Máquina usando qualquer linguagem de programação que dá suporte à resposta e solicitação HTTP. Você pode exibir exemplos em C#, Python e R de uma página de Ajuda do serviço Web de Aprendizado de Máquina.

**Ajuda da API do Aprendizado de Máquina** Uma ajuda de API do Aprendizado de Máquina é criada quando você implanta um serviço Web. Confira [Passo a passo do Aprendizado de Máquina do Azure – Implantar serviço Web](machine-learning-walkthrough-5-publish-web-service.md). A ajuda da API do Aprendizado de Máquina contém detalhes sobre um serviço Web de previsão.

**Para exibir a ajuda da API do Aprendizado de Máquina para um serviço Web clássico** No Estúdio de Aprendizado de Máquina:

1. Clique em **SERVIÇOS WEB**.
2. Clique em um serviço Web.
3. Clique em **Página de ajuda da API** - **SOLICITAÇÃO/RESPOSTA** ou **EXECUÇÃO EM LOTES**

**Para exibir a Ajuda da API do Aprendizado de Máquina para um novo serviço Web** No Portal dos Serviços Web de Aprendizado de Máquina do Azure:

1. Clique em **SERVIÇOS WEB** no menu superior.
2. Clique no serviço Web para o qual deseja recuperar a chave.

Clique em **Consumir** para obter os URIs dos serviços de Solicitação-Resposta e Execução em Lotes, bem como o código de exemplo em C#, R e Python.

Clique em **API do Swagger** para obter a documentação baseada no Swagger para as APIs chamadas dos URIs fornecidos.

### Exemplo de C# ###

Para se conectar a um serviço Web de Aprendizado de Máquina, use um **HttpClient** passando ScoreData. ScoreData contém um FeatureVector, um vetor com n dimensões de recursos numéricos que representa o ScoreData. Autentique no serviço de Aprendizado de Máquina com uma chave de API.

Para se conectar a um serviço Web de Aprendizado de Máquina, o pacote Nuget **Microsoft.AspNet.WebApi.Client** deve ser instalado.

**Instalar o Nuget Microsoft.AspNet.WebApi.Client no Visual Studio**

1. Publique “Baixe o conjunto de dados de Download de UCI: Serviço Web do conjunto de dados da classe Adulto 2”.
2. Clique em **Ferramentas** > **Gerenciador de Pacotes do NuGet** > **Console do Gerenciador de Pacotes**.
2. Escolha **Install-Package Microsoft.AspNet.WebApi.Client**.

**Para executar o exemplo de código**

1. Publique o experimento “Exemplo 1: Baixe o conjunto de dados de UCI: conjunto de dados da classe Adulto 2”, parte da coleção de exemplos de Aprendizado de Máquina.
2. Atribua apiKey com a chave de um serviço Web. Confira a seção acima **Obter uma chave de autorização de Aprendizado de Máquina do Azure**.
3. Atribua serviceUri com o URI de solicitação.


### Exemplo de Python ###

Para se conectar a um serviço Web de Aprendizado de Máquina, use a biblioteca **urllib2** passando ScoreData. ScoreData contém um FeatureVector, um vetor com n dimensões de recursos numéricos que representa o ScoreData. Autentique no serviço de Aprendizado de Máquina com uma chave de API.


**Para executar o exemplo de código**

1. Implante o experimento "Exemplo 1: Baixe o conjunto de dados de UCI: conjunto de dados da classe Adulto 2", parte da coleção de exemplos de Aprendizado de Máquina.
2. Atribua apiKey com a chave de um serviço Web. Confira a seção acima **Obter uma chave de autorização de Aprendizado de Máquina do Azure**.
3. Atribua serviceUri com o URI de solicitação.

<!---HONumber=AcomDC_0720_2016-->