<properties
	pageTitle="Readaptar um Novo serviço Web usando os cmdlets do PowerShell de Gerenciamento do Machine Learning | Microsoft Azure"
	description="Saiba como readaptar um modelo de forma programática e atualizar o serviço Web para usar o modelo treinado recentemente no Machine Learning do Azure usando os cmdlets do PowerShell de Gerenciamento do Machine Learning."
	services="machine-learning"
	documentationCenter=""
	authors="vDonGlover"
	manager="raymondlaghaeian"
	editor=""/>

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/27/2016"
	ms.author="v-donglo"/>

# Readaptar um Novo serviço Web usando os cmdlets do PowerShell de Gerenciamento do Machine Learning

Quando você readapta um Novo serviço Web, atualiza a definição do serviço Web de previsão para referenciar o novo modelo treinado.

### Pré-requisitos

Você deve ter configurado um Teste de Treinamento e um Experimento de Previsão, como mostrado em Readaptar os modelos do Machine Learning de forma programática. Para obter informações sobre como criar Testes de Treinamento e Previsão, consulte [Readaptar os modelos de Machine Learning de forma programática] \(Readaptar os modelos de Machine Learning de forma programática.md).

Esse processo exige que você tenha instalado os Cmdlets do Machine Learning do Azure. Para obter informações sobre como instalar os cmdlets do Machine Learning, consulte a referência [Cmdlets do Machine Learning do Azure](https://msdn.microsoft.com/library/azure/mt767952.aspx) no MSDN.
 
Copiar as informações a seguir da saída da readaptação:

* BaseLocation
* RelativeLocation

As etapas são:
1.	Entre sua conta do Azure Resource Manager.
2.	Obter a definição do serviço Web
3.	Exportar a Definição do Serviço Web como JSON
4.	Atualize a referência para o blob ilearner no JSON.
5.	Importar o JSON para uma Definição do Serviço Web
6.	Atualizar o serviço Web com a nova Definição do Serviço Web

### Entrar em sua conta do Azure Resource Manager 

Primeiro, você deve entrar em sua conta do Azure no ambiente do PowerShell usando o cmdlet [Add-AzureRmAccount](https://msdn.microsoft.com/library/mt619267.aspx).

### Obter a definição do Serviço Web

Em seguida, obtenha o Serviço Web chamando o cmdlet [Get-AzureRmMlWebService](https://msdn.microsoft.com/library/mt619267.aspx). A Definição do Serviço Web é uma representação interna do modelo treinado do serviço Web e não pode ser modificada diretamente. Verifique se você está recuperando a Definição do Serviço da Web para seu experimento de Previsão, e não seu Teste de Treinamento.

	$wsd = Get-AzureRmMlWebService -Name 'RetrainSamplePre.2016.8.17.0.3.51.237' -ResourceGroupName 'Default-MachineLearning-SouthCentralUS'

Para determinar o nome do grupo de recursos de um serviço Web existente, execute o cmdlet Get-AzureRmMlWebService sem parâmetros para exibir os serviços Web em sua assinatura. Localize o serviço Web e examine sua ID de serviço da Web. O nome do grupo de recursos é o quarto elemento na ID, logo após o elemento *resourceGroups*. No exemplo a seguir, o nome do grupo de recursos é Default-MachineLearning-SouthCentralUS.

	Properties : Microsoft.Azure.Management.MachineLearning.WebServices.Models.WebServicePropertiesForGraph 
	Id : /subscriptions/<subscription ID>/resourceGroups/Default-MachineLearning-SouthCentralUS/providers/Microsoft.MachineLearning/webServices/RetrainSamplePre.2016.8.17.0.3.51.237 
	Name : RetrainSamplePre.2016.8.17.0.3.51.237 
	Location : South Central US 
	Type : Microsoft.MachineLearning/webServices 
	Tags : {} 

Como alternativa, para determinar o nome do grupo de recursos de um serviço Web existente, faça logon no portal de serviços Web do Microsoft Azure Machine Learning. Selecione o serviço Web. O nome do grupo de recursos é o quinto elemento da URL do serviço Web, logo após o elemento *resourceGroups*. No exemplo a seguir, o nome do grupo de recursos é Default-MachineLearning-SouthCentralUS.

	https://services.azureml.net/subscriptions/<subcription ID>/resourceGroups/Default-MachineLearning-SouthCentralUS/providers/Microsoft.MachineLearning/webServices/RetrainSamplePre.2016.8.17.0.3.51.237 


### Exportar a Definição do Serviço Web como JSON

Para modificar a definição para o modelo treinado usar o Modelo Treinado recentemente, primeiro você deve usar o cmdlet [Export-AzureRmMlWebService](https://msdn.microsoft.com/library/azure/mt767935.aspx) para exportá-lo para um arquivo no formato JSON.
  
	Export-AzureRmMlWebService -WebService $wsd -OutputFile "C:\temp\mlservice_export.json"

### Atualize a referência para o blob ilearner no JSON.

Nos ativos, localize o [modelo treinado] e atualize o valor *uri* no nó *locationInfo* com o URI do blob ilearner. O URI é gerado combinando *BaseLocation* e *RelativeLocation* na saída da chamada de readaptação BES.

     "asset3": {
        "name": "Retrain Samp.le [trained model]",
        "type": "Resource",
        "locationInfo": {
          "uri": "https://mltestaccount.blob.core.windows.net/azuremlassetscontainer/baca7bca650f46218633552c0bcbba0e.ilearner"
        },
        "outputPorts": {
          "Results dataset": {
            "type": "Dataset"
          }
        }
      },

### Importar o JSON para uma Definição do Serviço Web

Você deve usar o cmdlet [Import-AzureRmMlWebService](https://msdn.microsoft.com/library/azure/mt767925.aspx) para converter o arquivo JSON modificado novamente em uma Definição do Serviço Web que você pode usar para atualizar o Teste de Previsão.

	$wsd = Import-AzureRmMlWebService -InputFile "C:\temp\mlservice_export.json"


### Atualizar o serviço Web com a nova Definição do Serviço Web

Finalmente, use o cmdlet [Update-AzureRmMlWebService](https://msdn.microsoft.com/library/azure/mt767922.aspx) para atualizar o teste de Previsão.

	Update-AzureRmMlWebService -Name 'RetrainSamplePre.2016.8.17.0.3.51.237' -ResourceGroupName 'Default-MachineLearning-SouthCentralUS'  -ServiceUpdates $wsd 

##Resumo

Usando os cmdlets de gerenciamento do PowerShell de Machine Learning, você pode atualizar o modelo treinado de um Serviço Web de previsão habilitando cenários, como:

* Readaptação de modelo periódico com novos dados.
* A distribuição de um modelo para os clientes com o objetivo de permitir que eles recuperem o modelo usando seus próprios dados.

<!---HONumber=AcomDC_0928_2016-->