<properties 
	pageTitle="Gerenciar contas dos Serviços de Mídia do Azure com o PowerShell" 
	description="Saiba como gerenciar contas dos Serviços de Mídia do Azure com cmdlets do PowerShell." 
	authors="Juliako" 
	manager="dwrede" 
	editor="" 
	services="media-services" 
	documentationCenter=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
 	ms.date="02/03/2016"  
	ms.author="juliako"/>


#Gerenciar contas dos Serviços de Mídia do Azure com o PowerShell

> [AZURE.SELECTOR]
- [Portal](media-services-create-account.md)
- [PowerShell](media-services-manage-with-powershell.md)
- [REST](http://msdn.microsoft.com/library/azure/dn194267.aspx)

> [AZURE.NOTE] Para poder criar uma conta de Serviços de Mídia do Azure, você deve ter uma conta do Azure. Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte <a href="http://www.windowsazure.com/pricing/free-trial/?WT.mc_id=A8A8397B5" target="_blank">Avaliação Gratuita do Azure</a>.

##Visão geral 

Este artigo mostra como usar cmdlets do PowerShell para gerenciar contas dos Serviços de Mídia do Azure.

>[AZURE.NOTE]
Para concluir este tutorial, você precisa de uma conta do Azure. Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte <a href="http://www.windowsazure.com/pricing/free-trial/?WT.mc_id=A8A8397B5" target="_blank">Avaliação Gratuita do Azure</a>.

##Instalar cmdlets do PowerShell do Microsoft Azure

Para instalar os últimos cmdlets do Azure PowerShell, consulte [Como instalar e configurar o Azure PowerShell ](../powershell-install-configure.md)

##Selecione Assinatura do Azure

Após instalar e configurar os cmdlets do PowerShell, você deve especificar em qual assinatura deseja trabalhar.

Para obter uma lista de assinaturas disponíveis, execute o seguinte cmdlet:

	PS C:\> Get-AzureSubscription

Depois, selecione um da seguinte forma:

	PS C:\> Select-AzureSubscription "TestSubscription"

 
##Obter nome de conta de armazenamento

Os Serviços de Mídia do Azure usam o Armazenamento do Azure para armazenar conteúdo de mídia. Ao criar uma nova conta dos Serviços de Mídia, você precisa associá-la a uma conta de armazenamento. A conta de armazenamento deve pertencer à mesma assinatura que você planeja usar para sua conta dos Serviços de Mídia.

Neste exemplo, uma conta de armazenamento existente é usada. O cmdlet [Get-AzureStorageAccount](https://msdn.microsoft.com/library/azure/dn495134.aspx) obtém contas de armazenamento na assinatura atual. Obtenha o nome (StorageAccountName) da conta de armazenamento que você deseja associar à sua conta de mídia.

	StorageAccountDescription : 
	AffinityGroup             :
	Location                  : East US
	GeoReplicationEnabled     : True
	GeoPrimaryLocation        : East US
	GeoSecondaryLocation      : West US
	Label                     : storagetest001
	StorageAccountStatus      : Created
	StatusOfPrimary           : Available
	StatusOfSecondary         : Available
	Endpoints                 : {https://storagetest001.blob.core.windows.net/,
	                            https://storagetest001.queue.core.windows.net/,
	                            https://storagetest001.table.core.windows.net/}
	AccountType               : Standard_GRS
	StorageAccountName        : storatetest001
	OperationDescription      : Get-AzureStorageAccount
	OperationId               : e919dd56-7691-96db-8b3c-2ceee891ae5d
	OperationStatus           : Succeeded

##Criar nova conta dos Serviços de Mídia

Para criar uma nova conta dos Serviços de Mídia do Azure, use o novo cmdlet [AzureMediaServicesAccount](https://msdn.microsoft.com/library/azure/dn495286.aspx), fornecendo o nome da conta dos Serviços de Mídia, o local do data center em que ela será criada e o nome da conta de armazenamento.


	PS C:\> New-AzureMediaServicesAccount -Name "amstestaccount001" -StorageAccountName "storagetest001" -Location "East US"

##Obter as contas dos Serviços de Mídia

Após criar uma ou mais contas dos Serviços de Mídia, você pode listar informações usando [Get-AzureMediaServicesAccount](https://msdn.microsoft.com/library/azure/dn495286.aspx)

	
	PS C:\> Get-AzureMediaServicesAccount
	
	AccountId		Name				State
	---------       ----       			 -----
	xxxxxxxxxx      amstestaccount001   Active

Fornecendo o parâmetro Name, você obterá informações mais detalhadas, incluindo chaves de conta.

	PS C:\> Get-AzureMediaServicesAccount -Name amstestaccount001

##Gerar novamente as chaves de acesso dos Serviços de Mídia

Para atualizar a tecla de acesso primária ou secundária dos Serviços de Mídia, use [New-AzureMediaServicesKey](https://msdn.microsoft.com/library/azure/dn495215.aspx). Você precisa fornecer o nome da conta e especificar qual chave deseja gerar novamente (primária ou secundária).

Especifique a opção -Force se não desejar que o PowerShell faça perguntas de confirmação.

	PS C:\> New-AzureMediaServicesKey -Name "amstestaccount001" -KeyType "Primary" -Force

##Remover a conta dos Serviços de Mídia

Quando você estiver pronto para excluir a conta de mídia do Azure, use [Remove-AzureMediaServicesAccount](https://msdn.microsoft.com/library/azure/dn495220.aspx).

	PS C:\> Remove-AzureMediaServicesAccount -Name "amstestaccount001" -Force


##Roteiros de aprendizagem dos Serviços de Mídia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

 

<!---HONumber=AcomDC_0211_2016-->