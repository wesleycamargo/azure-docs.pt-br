---
title: Executar operações no armazenamento de Fila do Azure com o PowerShell | Microsoft Docs
description: Como executar operações no armazenamento de Fila do Azure com o PowerShell
services: storage
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 09/14/2017
ms.author: rogarana
ms.subservice: queues
ms.openlocfilehash: 7f317c061c66a344731172f83e1c85dc5487379d
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/18/2019
ms.locfileid: "58005192"
---
# <a name="perform-azure-queue-storage-operations-with-azure-powershell"></a>Executar operações de armazenamento de Fila do Azure com o Azure PowerShell

O armazenamento de filas do Azure é um serviço para armazenamento de um grande número de mensagens que podem ser acessadas de qualquer lugar do mundo por meio de HTTP ou HTTPS. Para obter informações detalhadas, veja [Introdução às Filas do Azure](storage-queues-introduction.md). Este artigo de instruções abrange operações comuns de armazenamento de Fila. Você aprenderá como:

> [!div class="checklist"]
> * Criar uma fila
> * Recuperar uma fila
> * Adicionar uma mensagem
> * Ler uma mensagem
> * Excluir uma mensagem 
> * Excluir uma fila

Estas instruções exigem o módulo do Azure PowerShell, versão 0.7 ou posterior. Execute `Get-Module -ListAvailable Az` para encontrar a versão. Se você precisa atualizar, consulte [Instalar o módulo do Azure PowerShell](/powershell/azure/install-Az-ps).

Não há nenhum cmdlet do PowerShell para o plano de dados para filas. Para executar os dados de operações do plano, como adicionar uma mensagem, ler uma mensagem e excluir uma mensagem, você deverá usar a biblioteca de cliente de armazenamento do .NET da forma como ela é exposta no PowerShell. Você cria um objeto de mensagem e então pode usar comandos como AddMessage para executar operações nessa mensagem. Este artigo mostra como fazer isso.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre na sua assinatura do Azure com o comando `Connect-AzAccount` e siga as instruções na tela.

```powershell
Connect-AzAccount
```

## <a name="retrieve-list-of-locations"></a>Recuperar a lista de locais

Se você não sabe qual localização deseja usar, você pode listar as localizações disponíveis. Depois que a lista for exibida, encontre o que deseja usar. Este exercício usará **eastus**. Armazene-a na variável **local** para uso futuro.

```powershell
Get-AzLocation | select Location 
$location = "eastus"
```

## <a name="create-resource-group"></a>Criar grupo de recursos

Crie um grupo de recursos com o comando [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). 

Um grupo de recursos do Azure é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados. Armazene o nome do grupo de recursos em uma variável para uso futuro. Neste exemplo, um grupo de recursos denominado *howtoqueuesrg* é criado na região *eastus*.

```powershell
$resourceGroup = "howtoqueuesrg"
New-AzResourceGroup -ResourceGroupName $resourceGroup -Location $location
```

## <a name="create-storage-account"></a>Criar Conta de Armazenamento

Crie uma conta de armazenamento padrão para uso geral com o LRS (armazenamento com redundância local) usando [New-AzStorageAccount](/powershell/module/az.storage/New-azStorageAccount). Obtenha o contexto da conta de armazenamento que define a conta de armazenamento a ser usada. Ao agir em uma conta de armazenamento, você pode referenciar o contexto em vez de fornecer repetidamente as credenciais.

```powershell
$storageAccountName = "howtoqueuestorage"
$storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroup `
  -Name $storageAccountName `
  -Location $location `
  -SkuName Standard_LRS

$ctx = $storageAccount.Context
```

## <a name="create-a-queue"></a>Criar uma fila

O exemplo a seguir primeiro estabelece uma conexão com o armazenamento do Azure usando o contexto de conta de armazenamento, que inclui o nome da conta de armazenamento e sua chave de acesso primária. Em seguida, ele chama o cmdlet [New-AzStorageQueue](/powershell/module/az.storage/New-AzStorageQueue) para criar uma fila denominada 'queuename'.

```powershell
$queueName = "howtoqueue"
$queue = New-AzStorageQueue –Name $queueName -Context $ctx
```

Para obter informações sobre convenções de nomenclatura do serviço Fila do Azure, consulte [Nomear filas e metadados](https://msdn.microsoft.com/library/azure/dd179349.aspx).

## <a name="retrieve-a-queue"></a>Recuperar uma fila

Você pode consultar e recuperar uma lista de todas as filas em uma conta de armazenamento ou de uma fila específica. Os exemplos a seguir demonstram como recuperar todas as filas na conta de armazenamento e uma fila específica; os dois comandos utilizam o cmdlet [Get-AzStorageQueue](/powershell/module/az.storage/Get-AzStorageQueue).

```powershell
# Retrieve a specific queue
$queue = Get-AzStorageQueue –Name $queueName –Context $ctx
# Show the properties of the queue
$queue

# Retrieve all queues and show their names
Get-AzStorageQueue -Context $ctx | select Name
```

## <a name="add-a-message-to-a-queue"></a>Adicionar uma mensagem a uma fila

As operações que afetam as mensagens reais na fila usam a biblioteca de cliente de armazenamento do .NET como exposta no PowerShell. Para adicionar uma mensagem a uma fila, crie uma nova instância do objeto de mensagem, a classe [Microsoft.WindowsAzure.Storage.Queue.CloudQueueMessage](https://msdn.microsoft.com/library/azure/jj732474.aspx). Em seguida, chame o método [AddMessage](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueue.addmessage.aspx) . Um CloudQueueMessage pode ser criado por meio de uma cadeia de caracteres (em formato UTF-8) ou de uma matriz de bytes.

O exemplo a seguir demonstra como adicionar uma mensagem à sua fila.

```powershell
# Create a new message using a constructor of the CloudQueueMessage class
$queueMessage = New-Object -TypeName Microsoft.WindowsAzure.Storage.Queue.CloudQueueMessage `
  -ArgumentList "This is message 1"
# Add a new message to the queue
$queue.CloudQueue.AddMessageAsync($QueueMessage)

# Add two more messages to the queue 
$queueMessage = New-Object -TypeName Microsoft.WindowsAzure.Storage.Queue.CloudQueueMessage `
  -ArgumentList "This is message 2"
$queue.CloudQueue.AddMessageAsync($QueueMessage)
$queueMessage = New-Object -TypeName Microsoft.WindowsAzure.Storage.Queue.CloudQueueMessage `
  -ArgumentList "This is message 3"
$queue.CloudQueue.AddMessageAsync($QueueMessage)
```

Se você usar o [Gerenciador de Armazenamento do Azure](https://storageexplorer.com), poderá se conectar à sua conta do Azure e exibir as filas na conta de armazenamento e fazer uma busca detalhada em uma fila para exibir as mensagens na fila. 

## <a name="read-a-message-from-the-queue-then-delete-it"></a>Ler uma mensagem na fila e depois excluí-la

As mensagens são lidas na ordem do primeiro a entrar, primeiro a sair de melhor tentativa. Isso não é garantido. Quando você lê a mensagem da fila, ela se torna invisível para todos os outros processos que estejam olhando para a fila. Isso garante que quando o código não processar uma mensagem devido a falhas de hardware ou de software, outra instância do seu código pode receber a mesma mensagem e tentar novamente.  

Esse **tempo limite de invisibilidade** define por quanto tempo a mensagem permanecerá invisível antes de estar disponível novamente para processamento. O padrão é 30 segundos. 

Seu código remove uma mensagem de uma fila em duas etapas. Quando você chamar o método [Microsoft.WindowsAzure.Storage.Queue.CloudQueue.GetMessage](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueue.getmessage.aspx), receberá a próxima mensagem em uma fila. Uma mensagem retornada de **GetMessage** torna-se invisível para todas as outras mensagens de leitura de código da fila. Para concluir a remoção da mensagem da fila, chame o método [Microsoft.WindowsAzure.Storage.Queue.CloudQueue.DeleteMessage](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueue.deletemessage.aspx). 

No exemplo a seguir, leia as mensagens em três filas e aguarde 10 segundos (o tempo limite de invisibilidade). Em seguida, leia as três mensagens novamente, excluindo as mensagens depois de lê-las ao chamar **DeleteMessage**. Se você tentar ler a fila após a exclusão das mensagens, $queueMessage será retornada como NULL.

```powershell
# Set the amount of time you want to entry to be invisible after read from the queue
# If it is not deleted by the end of this time, it will show up in the queue again
$invisibleTimeout = [System.TimeSpan]::FromSeconds(10)

# Read the message from the queue, then show the contents of the message. Read the other two messages, too.
$queueMessage = $queue.CloudQueue.GetMessageAsync($invisibleTimeout,$null,$null)
$queueMessage.Result
$queueMessage = $queue.CloudQueue.GetMessageAsync($invisibleTimeout,$null,$null)
$queueMessage.Result
$queueMessage = $queue.CloudQueue.GetMessageAsync($invisibleTimeout,$null,$null)
$queueMessage.Result

# After 10 seconds, these messages reappear on the queue. 
# Read them again, but delete each one after reading it.
# Delete the message.
$queueMessage = $queue.CloudQueue.GetMessageAsync($invisibleTimeout,$null,$null)
$queueMessage.Result
$queue.CloudQueue.DeleteMessageAsync($queueMessage.Result.Id,$queueMessage.Result.popReceipt)
$queueMessage = $queue.CloudQueue.GetMessageAsync($invisibleTimeout,$null,$null)
$queueMessage.Result
$queue.CloudQueue.DeleteMessageAsync($queueMessage.Result.Id,$queueMessage.Result.popReceipt)
$queueMessage = $queue.CloudQueue.GetMessageAsync($invisibleTimeout,$null,$null)
$queueMessage.Result
$queue.CloudQueue.DeleteMessageAsync($queueMessage.Result.Id,$queueMessage.Result.popReceipt)
```

## <a name="delete-a-queue"></a>Excluir uma fila

Para excluir uma fila e todas as mensagens contidas nela, chame o cmdlet Remove-AzStorageQueue. O exemplo a seguir mostra como excluir a fila específica usada neste exercício usando o cmdlet Remove-AzStorageQueue.

```powershell
# Delete the queue 
Remove-AzStorageQueue –Name $queueName –Context $ctx
```

## <a name="clean-up-resources"></a>Limpar recursos

Para remover todos os ativos que você criou neste exercício, remova o grupo de recursos. Isso também exclui todos os recursos contidos no grupo. Nesse caso, ele remove a conta de armazenamento criada e o próprio grupo de recursos.

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>Próximas etapas

Neste artigo de instruções, você aprendeu sobre gerenciamento de armazenamento de Fila básico com o PowerShell, incluindo como:

> [!div class="checklist"]
> * Criar uma fila
> * Recuperar uma fila
> * Adicionar uma mensagem
> * Ler a próxima mensagem
> * Excluir uma mensagem 
> * Excluir uma fila

### <a name="microsoft-azure-powershell-storage-cmdlets"></a>Cmdlets de Armazenamento do Microsoft Azure PowerShell

* [Cmdlets do PowerShell do Armazenamento](/powershell/module/az.storage)

### <a name="microsoft-azure-storage-explorer"></a>Gerenciador do Armazenamento do Microsoft Azure

* [O Gerenciador de Armazenamento do Microsoft Azure](../../vs-azure-tools-storage-manage-with-storage-explorer.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json) é um aplicativo autônomo e gratuito da Microsoft que possibilita o trabalho visual com os dados do Armazenamento do Azure no Windows, MacOS e Linux.
