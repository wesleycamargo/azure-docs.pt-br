<properties
	pageTitle="Serviços Web do Aprendizado de Máquina do Azure: implantação e consumo | Microsoft Azure"
	description="Recursos para implantar e consumir serviços Web."
	services="machine-learning"
	documentationCenter=""
	authors="vDonGlover"
	manager="raymondl"
	editor=""/>

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/19/2016"
	ms.author="v-donglo"/>

# Serviços Web do Aprendizado de Máquina do Azure: implantação e consumo

O AM do Azure (Aprendizado de Máquina do Azure) permite implantar fluxos de trabalho e modelos de aprendizado de máquina como serviços Web. Esses serviços Web podem ser usados para chamar os modelos AM de aplicativos pela Internet para fazer previsões em tempo real ou no modo de lote. Sendo RESTFul, os serviços Web podem ser chamados de várias plataformas e linguagens de programação, como .NET, Java e aplicativos, como o Excel.

Nas próximas seções, discutiremos as etapas e mostraremos links para código e documentação que ajudam você a começar.

## Implantar

### Usando o Estúdio AM do Azure

O Estúdio de Aprendizado de Máquina e o portal dos Serviços Web do Aprendizado de Máquina do Microsoft Azure permitem implantar e gerenciar um serviço Web sem precisar escrever código.

Os links a seguir fornecem informações gerais sobre o processo de implantar um novo serviço Web:

* Para obter uma visão geral de como implantar um Novo Serviço Web baseado no Azure Resource Manager, confira [Implantar um novo serviço Web](machine-learning-webservice-deploy-a-web-service.md).
* Para ver um passo a passo de como implantar um serviço Web, confira [Implantar um serviço Web do Azure Machine Learning](machine-learning-publish-a-machine-learning-web-service.md).

Para obter uma explicação completa de como criar e implantar um serviço Web, confira [Etapa 1 do passo a passo: Criar um espaço de trabalho do Machine Learning](machine-learning-walkthrough-1-create-ml-workspace.md).

Para obter exemplos específicos de como implantar um serviço Web, confira:

* [Etapa 5 do passo-a-passo: Implantar o serviço Web de Aprendizado de Máquina do Azure](machine-learning-walkthrough-5-publish-web-service.md)
* [Como implantar um serviço Web em várias regiões](machine-learning-how-to-deploy-to-multiple-regions.md)

### Usando APIs do Provedor de Recursos dos serviços Web (APIs do Azure Resource Manager)

O Provedor de Recursos do Azure ML para serviços Web permite a implantação e o gerenciamento dos serviços Web usando chamadas à API REST. Veja os artigos a seguir e o código de exemplo para obter detalhes adicionais.

* Referência ao [Serviço Web do Machine Learning (REST)](https://msdn.microsoft.com/library/azure/mt767538.aspx) no MSDN.


### Usando os cmdlets do PowerShell

O Provedor de Recursos do Azure ML para serviços Web permite a implantação e o gerenciamento dos serviços Web por meio de cmdlets do PowerShell.

Para usar os cmdlets, primeiramente, você deve fazer logon na sua conta do Azure no ambiente do PowerShell usando o cmdlet [Add-AzureRmAccount](https://msdn.microsoft.com/library/mt619267.aspx). Caso não esteja familiarizado com a chamada aos comandos do PowerShell baseados no Resource Manager, confira [Usando o Azure PowerShell com o Azure Resource Manager](../powershell-azure-resource-manager.md#login-to-your-azure-account).

Para exportar seu experimento preditivo, use este [código de exemplo](https://github.com/ritwik20/AzureML-WebServices). Depois de criar o arquivo exe do código, você pode digitar:

	C:<folder>\GetWSD <experiment-url> <workspace-auth-token>

Executar o aplicativo cria um modelo JSON do serviço Web. Para usar o modelo para implantar um serviço Web, você deve adicionar as seguintes informações:

* Nome e chave da conta de armazenamento
	* Você pode obter o nome e a chave da conta de armazenamento no Portal novo ou Clássico do Azure.
* ID do plano de compromisso
	* Você pode obter a ID do plano no portal [Serviços Web do Aprendizado de Máquina do Azure](https://services.azureml.net) efetuando logon e clicando no nome de um plano.

Adicione-a ao modelo JSON como filho do nó *Properties* no mesmo nível do nó *MachineLearningWorkspace*.

Veja o exemplo abaixo:

	"StorageAccount": {
            "name": "YourStorageAccountName",
            "key": "YourStorageAccountKey"
	},
    "CommitmentPlan": {
        "id": "subscriptions/YouSubscriptionID/resourceGroups/YourResourceGroupID/Microsoft.MachineLearning/commitmentPlans/YourPlanName"
    }

Veja os artigos a seguir e o código de exemplo para obter detalhes adicionais.

* Referência aos [cmdlets do Aprendizado de Máquina do Azure](https://msdn.microsoft.com/library/azure/mt767952.aspx) no MSDN
* Exemplo [passo a passo](https://github.com/raymondlaghaeian/azureml-webservices-arm-powershell/blob/master/sample-commands.txt) no GitHub.

## Consumindo os serviços Web

### Na interface de usuário dos Serviços Web do Azure ML (Teste)

Você pode testar seu serviço Web no portal de serviços Web do Azure ML. Isso inclui testes das interfaces RRS e BES.

* [Implantar um novo serviço Web](machine-learning-webservice-deploy-a-web-service.md)
* [Implantar um serviço Web de Aprendizado de Máquina do Azure](machine-learning-publish-a-machine-learning-web-service.md)
* [Etapa 5 do passo-a-passo: Implantar o serviço Web de Aprendizado de Máquina do Azure](machine-learning-walkthrough-5-publish-web-service.md)

### No Excel

Você pode baixar um modelo do Excel que permita consumir facilmente o serviço Web:

* [Consumindo um Serviço Web de Aprendizado de Máquina do Azure do Excel](machine-learning-consuming-from-excel.md)
* [Suplemento do Excel para serviços Web de aprendizado de máquina do Azure](machine-learning-excel-add-in-for-web-services.md)


### Usando um cliente baseado em REST

Os serviços Web do Azure ML são APIs RESTful. Você pode consumir essas APIs em várias plataformas, como .NET, Python, R, Java, etc. A página de consumo do serviço Web no [portal de Serviços Web do Aprendizado de Máquina do Microsoft Azure](https://services.azureml.net) disponibiliza código de exemplo que pode ajudar você a começar.

* [Como consumir um serviço Web de Aprendizado de Máquina do Azure que foi implantado por meio de um teste de Aprendizado de Máquina](machine-learning-consume-web-services.md)

<!---HONumber=AcomDC_0824_2016-->