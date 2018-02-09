---
title: Encaminhar eventos de Armazenamento de Blobs do Azure para um ponto de extremidade da Web personalizado - PowerShell | Microsoft Docs
description: Use a Grade de Eventos do Azure para assinar eventos de Armazenamento de Blobs.
services: storage,event-grid
keywords: 
author: david-stanford
ms.author: dastanfo
ms.date: 01/30/2018
ms.topic: article
ms.service: storage
ms.openlocfilehash: 329a79511b810159244b5530a49a5916440d2046
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/01/2018
---
# <a name="route-blob-storage-events-to-a-custom-web-endpoint-with-powershell"></a>Encaminhar eventos de armazenamento de Blobs para um ponto de extremidade da Web personalizado com PowerShell

A Grade de Eventos do Azure é um serviço de eventos para a nuvem. Neste artigo, você usa Azure PowerShell para assinar eventos de Armazenamento de Blobs, acionar um evento e exibir o resultado. 

Normalmente, você envia eventos para um ponto de extremidade que responde ao evento, como um webhook ou uma Função do Azure. Para simplificar o exemplo mostrado neste artigo, os eventos são enviados para uma URL que apenas coleta as mensagens. Você cria essa URL usando ferramentas de terceiros do [RequestBin](https://requestb.in/) ou [Hookbin](https://hookbin.com/).

> [!NOTE]
> **RequestBin** e **Hookbin** não servem para um uso de alta taxa de transferência. O uso dessas ferramentas é meramente demonstrativo. Se você efetuar push de mais de um evento por vez, talvez não veja todos os eventos na ferramenta.

Quando você concluir as etapas descritas neste artigo, verá que os dados do evento foi enviados para um ponto de extremidade.

![Dados de evento](./media/storage-blob-event-quickstart/request-result.png)

## <a name="setup"></a>Configuração

Este artigo requer que você esteja executando a versão mais recente do Azure PowerShell. Se você precisa instalar ou atualizar, confira [Instalar e configurar o Azure PowerShell](/powershell/azure/install-azurerm-ps).

## <a name="log-in-to-azure"></a>Fazer logon no Azure

Faça logon na sua assinatura do Azure com o comando `Login-AzureRmAccount` e siga as instruções na tela para fazer a autenticação.

```powershell
Login-AzureRmAccount
```

> [!NOTE]
> A disponibilidade para eventos de Armazenamento está vinculada à [disponibilidade](../../event-grid/overview.md) da Grade de Eventos e estará disponível em outras regiões, como a Grade de Eventos.

Este exemplo usa **westus2** e armazena a seleção em uma variável para uso.

```powershell
$location = "westus2"
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Os tópicos de Grade de Eventos são recursos do Azure e devem ser colocados em um grupo de recursos do Azure. O grupo de recursos do Azure é uma coleção lógica na qual os recursos do Azure são implantados e gerenciados.

Crie um grupo de recursos com o comando [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup).

O exemplo a seguir cria um grupo de recursos chamado **gridResourceGroup** no local **westus2**.  

```powershell
$resourceGroup = "gridResourceGroup"
New-AzureRmResourceGroup -Name $resourceGroup -Location $location
```

## <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

Para usar eventos de armazenamento de Blobs, você precisa de uma [conta de armazenamento Blob](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#blob-storage-accounts) ou uma [conta de armazenamento de uso geral v2](../common/storage-account-options.md#general-purpose-v2). As contas de **uso geral v2 (GPv2)** são contas de armazenamento que fornecem suporte a todos os recursos de todos os serviços de armazenamento, incluindo Blobs, Arquivos, Filas e Tabelas. Uma **conta de armazenamento Blob** é uma conta de armazenamento especializada para armazenar dados não estruturados como blobs (objetos) no Armazenamento do Microsoft Azure. As contas de armazenamento de Blobs são como contas de armazenamento de finalidade geral existentes e compartilham todos os excelentes recursos de durabilidade, disponibilidade, escalabilidade e desempenho que você usa atualmente, incluindo 100% de consistência de API para blobs de bloco e blobs de acréscimo. Para aplicativos que exigem apenas o armazenamento de blobs em bloco ou acréscimo, recomendamos o uso de contas de Armazenamento de Blobs.  

Criar uma conta de armazenamento de Blob com replicação LRS usando [New-AzureRmStorageAccount](/powershell/module/azurerm.storage/New-AzureRmStorageAccount) e, em seguida, recupere o contexto da conta de armazenamento que define a conta de armazenamento a ser usada. Ao agir em uma conta de armazenamento, você pode referenciar o contexto em vez de fornecer repetidamente as credenciais. Esse exemplo cria uma conta de armazenamento chamada **gridstorage** com LRS (armazenamento com redundância local) e criptografia de blob (habilitada por padrão). 

> [!NOTE]
> Nomes de conta de armazenamento estão em um espaço de nome global, você precisa adicionar alguns caracteres aleatórios ao nome fornecido no script.

```powershell
$storageName = "gridstorage"
$storageAccount = New-AzureRmStorageAccount -ResourceGroupName $resourceGroup `
  -Name $storageName `
  -Location $location `
  -SkuName Standard_LRS `
  -Kind BlobStorage `
  -AccessTier Hot

$ctx = $storageAccount.Context
```

## <a name="create-a-message-endpoint"></a>Criar um ponto de extremidade de mensagem

Antes de assinar o tópico, vamos criar o ponto de extremidade para a mensagem do evento. Em vez de escrever código para responder ao evento, vamos criar um ponto de extremidade que coleta as mensagens, para que você possa exibi-las. RequestBin e Hookbin são ferramentas de terceiros que permitem criar um ponto de extremidade e exibir solicitações que são enviadas a ele. Acesse [RequestBin](https://requestb.in/) e clique em **Criar um RequestBin** ou acesse [Hookbin](https://hookbin.com/) e clique em **Cria novo ponto de extremidade**. Copie a URL do bin e substitua `<bin URL>` no script a seguir.

```powershell
$binEndPoint = "<bin URL>"
```

## <a name="subscribe-to-your-storage-account"></a>Assinar a sua conta de armazenamento

Assine um tópico para indicar à Grade de Eventos quais eventos você deseja acompanhar. O exemplo a seguir assina a conta de armazenamento que você criou e transmite a URL do RequestBin ou Hookbin como o ponto de extremidade para notificação de eventos. 

```powershell
$storageId = (Get-AzureRmStorageAccount -ResourceGroupName $resourceGroup -AccountName $storageName).Id
New-AzureRmEventGridSubscription `
  -EventSubscriptionName gridBlobQuickStart `
  -Endpoint $binEndPoint `
  -ResourceId $storageId
```

## <a name="trigger-an-event-from-blob-storage"></a>Acionar um evento do Armazenamento de Blobs

Agora, vamos disparar um evento para ver como a Grade de Eventos distribui a mensagem para o ponto de extremidade. Primeiro, vamos criar um contêiner e um objeto. Em seguida, vamos carregar o objeto no contêiner.

```powershell
$containerName = "gridcontainer"
New-AzureStorageContainer -Name $containerName -Context $ctx

echo $null >> gridTestFile.txt

Set-AzureStorageBlobContent -File gridTestFile.txt -Container $containerName -Context $ctx -Blob gridTestFile.txt
```

Você disparou o evento e a Grade de Eventos enviou a mensagem para o ponto de extremidade configurado durante a assinatura. Navegue até a URL do ponto de extremidade que você criou anteriormente. Ou clique em Atualizar no navegador. Você verá o evento que acabou de ser enviado. 

```json
[{
  "topic": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myrg/providers/Microsoft.Storage/storageAccounts/myblobstorageaccount",
  "subject": "/blobServices/default/containers/gridcontainer/blobs/gridTestFile.txt",
  "eventType": "Microsoft.Storage.BlobCreated",
  "eventTime": "2017-08-16T20:33:51.0595757Z",
  "id": "4d96b1d4-0001-00b3-58ce-16568c064fab",
  "data": {
    "api": "PutBlockList",
    "clientRequestId": "Azure-Storage-PowerShell-d65ca2e2-a168-4155-b7a4-2c925c18902f",
    "requestId": "4d96b1d4-0001-00b3-58ce-16568c000000",
    "eTag": "0x8D4E4E61AE038AD",
    "contentType": "application/octet-stream",
    "contentLength": 0,
    "blobType": "BlockBlob",
    "url": "https://myblobstorageaccount.blob.core.windows.net/gridcontainer/gridTestFile.txt",
    "sequencer": "00000000000000EB0000000000046199",
    "storageDiagnostics": {
      "batchId": "dffea416-b46e-4613-ac19-0371c0c5e352"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]

```

## <a name="clean-up-resources"></a>Limpar recursos
Se você planeja continuar a trabalhar com essa assinatura de evento e conta de armazenamento, não limpe os recursos criados neste artigo. Caso contrário, use os comandos a seguir para excluir os recursos criados por você neste artigo.

```powershell
Remove-AzureRmResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>Próximas etapas

Agora que sabe como criar tópicos e assinaturas de evento, saiba mais sobre os Eventos de Armazenamento de Blobs e o que a Grade de Eventos pode fazer por você:

- [Reagindo a eventos de Armazenamento de Blobs](storage-blob-event-overview.md)
- [Sobre a Grade de Eventos](../../event-grid/overview.md)
