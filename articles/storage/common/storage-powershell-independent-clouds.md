---
title: Gerenciamento do Armazenamento nas nuvens independentes do Azure usando o Azure PowerShell | Microsoft Docs
description: "Gerenciamento do armazenamento na nuvem da China, na nuvem do Governo e na nuvem alemã usando o Azure PowerShell"
services: storage
documentationcenter: na
author: robinsh
manager: timlt
ms.assetid: 
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2017
ms.author: robinsh
ms.openlocfilehash: 08e1af929d7ddc30c7dc149f6305ca1ca0bc22ae
ms.sourcegitcommit: 3ab5ea589751d068d3e52db828742ce8ebed4761
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/27/2017
---
# <a name="managing-storage-in-the-azure-independent-clouds-using-powershell"></a>Gerenciamento do Armazenamento nas nuvens independentes do Azure usando o PowerShell

A maioria das pessoas usa a nuvem pública do Azure em suas implantações globais do Azure. Há também algumas implantações independentes do Microsoft Azure por motivos de soberania e assim por diante. Essas implantações independentes são chamadas de “ambientes”. A lista a seguir fornece detalhes sobre as nuvens independentes disponíveis no momento.

* [Nuvem do Azure Governamental](https://azure.microsoft.com/features/gov/)
* [Nuvem do Azure na China operada pela 21Vianet na China](http://www.windowsazure.cn/)
* [Nuvem alemã do Azure](../../germany/germany-welcome.md)

## <a name="using-an-independent-cloud"></a>Utilização de uma nuvem independente 

Para utilizar o Armazenamento do Azure em uma das nuvens independentes, você deve se conectar àquela nuvem em vez de se conectar à nuvem pública do Azure. Para usar uma das nuvens independentes em vez da nuvem pública do Azure:

* Especifique o *ambiente* com o qual deseja se conectar.
* Determine e use as regiões disponíveis.
* Use o sufixo do ponto de extremidade correto, que é diferente do da nuvem pública do Azure.

Estes exemplos exigem a versão 4.4.0 ou posterior do módulo do Azure PowerShell. Em uma janela do PowerShell, execute `Get-Module -ListAvailable AzureRM` para localizar a versão. Se nada for listado ou você precisar atualizar, consulte [Instalar o módulo do Azure PowerShell](/powershell/azure/install-azurerm-ps). 

## <a name="log-in-to-azure"></a>Fazer logon no Azure

Execute o cmdlet [Get-AzureEnvironment](/powershell/module/azure/Get-AzureRmEnvironment) para ver os ambientes do Azure disponíveis:
   
```powershell
Get-AzureRmEnvironment
```

Entre em sua conta que tem acesso à nuvem com a qual você deseja se conectar e defina o ambiente. Este exemplo mostra como entrar em uma conta que usa a Nuvem do Azure Governamental.   

```powershell
Login-AzureRmAccount –Environment AzureUSGovernment
```

Para acessar a Nuvem da China, use o ambiente **AzureChinaCloud**. Para acessar a Nuvem alemã, use **AzureGermanCloud**.

Neste ponto, se precisar da lista de locais para criar uma conta de armazenamento ou outro recurso, você poderá consultar os locais disponíveis para a nuvem selecionada usando [Get-AzureRmLocation](/powershell/module/azurerm.resources/get-azurermlocation).

```powershell
Get-AzureRmLocation | select Location, DisplayName
```

A tabela a seguir mostra os locais retornados para a Nuvem alemã.

|Local | DisplayName |
|----|----|
| germanycentral | Alemanha Central|
| germanynortheast | Nordeste da Alemanha | 


## <a name="endpoint-suffix"></a>Sufixo de ponto de extremidade

O sufixo de ponto de extremidade para cada um desses ambientes é diferente do ponto de extremidade da nuvem pública do Azure. Por exemplo, o sufixo de ponto de extremidade do blob da nuvem pública do Azure é **blob.core.windows.net**. Para a nuvem do governo, o sufixo de ponto de extremidade do blob é **blob.core.usgovcloudapi.net**. 

### <a name="get-endpoint-using-get-azurermenvironment"></a>Obter o ponto de extremidade usando Get-AzureRMEnvironment 

Recupere o sufixo de ponto de extremidade usando [Get-AzureRMEnvironment](/powershell/module/azurerm.profile/get-azurermenvironment). O ponto de extremidade é a propriedade *StorageEndpointSuffix* do ambiente. Os trechos de código a seguir mostram como fazer isso. Todos esses comandos retornam algo como “core.cloudapp.net” ou “core.cloudapi.de”, etc. Acrescente-o ao serviço de armazenamento para acessar esse serviço. Por exemplo, “queue.core.cloudapi.de” acessará o serviço Fila na nuvem alemã.

Este trecho de código recupera todos os ambientes e o sufixo do ponto de extremidade para cada um.

```powershell
Get-AzureRmEnvironment | select Name, StorageEndpointSuffix 
```

Esse comando retorna os seguintes resultados.

| Nome| StorageEndpointSuffix|
|----|----|
|AzureChinaCloud | core.chinacloudapi.cn|
| AzureCloud | core.windows.net |
| AzureGermanCloud | core.cloudapi.de|
| AzureUSGovernment | core.usgov.cloudapi.net |


Para recuperar todas as propriedades para o ambiente especificado, chame **Get-AzureRmEnvironment** e especifique o nome da nuvem. Este trecho de código retorna uma lista de propriedades. Procure por **StorageEndpointSuffix** na lista. O exemplo a seguir é para a nuvem alemã.

```powershell
Get-AzureRmEnvironment -Name AzureGermanCloud 
```

Os resultados são semelhantes ao seguinte:

|Nome da Propriedade|Valor|
|----|----|
| Nome | AzureGermanCloud |
| EnableAdfsAuthentication | Falso |
| ActiveDirectoryServiceEndpointResourceI | http://management.core.cloudapi.de/ |
| GalleryURL | https://gallery.cloudapi.de/ |
| ManagementPortalUrl | https://portal.microsoftazure.de/ | 
| ServiceManagementUrl | https://manage.core.cloudapi.de/ |
| PublishSettingsFileUrl| https://manage.microsoftazure.de/publishsettings/index |
| ResourceManagerUrl | http://management.microsoftazure.de/ |
| SqlDatabaseDnsSuffix | .database.cloudapi.de |
| **StorageEndpointSuffix** | core.cloudapi.de |
| ... | ... | 

Para recuperar apenas a propriedade de sufixo de ponto de extremidade do armazenamento, recupere a nuvem específica e solicite somente aquela propriedade.

```powershell
$environment = Get-AzureRmEnvironment -Name AzureGermanCloud
Write-Host "Storage EndPoint Suffix = " $environment.StorageEndpointSuffix 
```

Isso retornará as seguintes informações.

```
Storage Endpoint Suffix = core.cloudapi.de
```

### <a name="get-endpoint-from-a-storage-account"></a>Obter o ponto de extremidade de uma conta de armazenamento

Você também pode examinar as propriedades de uma conta de armazenamento para recuperar os pontos de extremidade. Isso poderá ser útil se você já estiver usando uma conta de armazenamento em seu script do PowerShell. Desse modo, você poderá recuperar apenas o ponto de extremidade que precisa. 

```powershell
# Get a reference to the storage account.
$resourceGroup = "myexistingresourcegroup"
$storageAccountName = "myexistingstorageaccount"
$storageAccount = Get-AzureRmStorageAccount `
  -ResourceGroupName $resourceGroup `
  -Name $storageAccountName 
  # Output the endpoints.
Write-Host "blob endpoint = " $storageAccount.PrimaryEndPoints.Blob 
Write-Host "file endpoint = " $storageAccount.PrimaryEndPoints.File
Write-Host "queue endpoint = " $storageAccount.PrimaryEndPoints.Queue
Write-Host "table endpoint = " $storageAccount.PrimaryEndPoints.Table
```

Para uma conta de armazenamento na Nuvem do Governo, os itens a seguir serão retornados: 

```
blob endpoint = http://myexistingstorageaccount.blob.core.usgovcloudapi.net/
file endpoint = http://myexistingstorageaccount.file.core.usgovcloudapi.net/
queue endpoint = http://myexistingstorageaccount.queue.core.usgovcloudapi.net/
table endpoint = http://myexistingstorageaccount.table.core.usgovcloudapi.net/
```

## <a name="after-setting-the-environment"></a>Após configurar o ambiente

Daqui em diante, você poderá usar o mesmo PowerShell utilizado para gerenciar suas contas de armazenamento e acessar o plano de dados, conforme descrito no artigo [Usando o Azure PowerShell com o Armazenamento do Azure](storage-powershell-guide-full.md).

## <a name="clean-up-resources"></a>Limpar recursos

Se tiver criado um novo grupo de recursos e uma conta de armazenamento para este exercício, você poderá remover todos os ativos ao remover o grupo de recursos. Isso também exclui todos os recursos contidos no grupo. Nesse caso, ele remove a conta de armazenamento criada e o próprio grupo de recursos.

```powershell
Remove-AzureRmResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>Próximas etapas

* [Persistência de logons de usuário nas sessões do PowerShell](/powershell/azure/context-persistence)
* [Armazenamento do Azure Governamental](../../azure-government/documentation-government-services-storage.md)
* [Guia do Desenvolvedor do Microsoft Azure Governamental](../../azure-government/documentation-government-developer-guide.md)
* [Notas do desenvolvedor para aplicativos do Azure China](https://msdn.microsoft.com/library/azure/dn578439.aspx)
* [Documentação do Azure Alemanha](../../germany/germany-welcome.md)