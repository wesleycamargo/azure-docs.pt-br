---
title: Usar o Azure PowerShell para configurar o upload do arquivo | Microsoft Docs
description: Como usar cmdlets do Azure PowerShell para configurar o Hub IoT a fim de habilitar transferências de arquivos de dispositivos conectados. Inclui informações sobre como configurar a conta de armazenamento do Azure de destino.
author: dominicbetts
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/08/2017
ms.author: dobett
ms.openlocfilehash: 9cf13589fb83f100dd024e65dfe9178cb54802f2
ms.sourcegitcommit: 1aedb52f221fb2a6e7ad0b0930b4c74db354a569
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/17/2018
ms.locfileid: "42145666"
---
# <a name="configure-iot-hub-file-uploads-using-powershell"></a>Configurar uploads de arquivo do Hub IoT usando o PowerShell

[!INCLUDE [iot-hub-file-upload-selector](../../includes/iot-hub-file-upload-selector.md)]

Para usar a funcionalidade de [upload de arquivos no Hub IoT](iot-hub-devguide-file-upload.md), você deve primeiro associar uma conta de armazenamento do Azure ao seu hub IoT. Você pode usar uma conta de armazenamento existente ou criar uma nova.

Para concluir este tutorial, você precisará do seguinte:

* Uma conta ativa do Azure. Se você não tem uma conta, pode criar uma [conta gratuita](http://azure.microsoft.com/pricing/free-trial/) em apenas alguns minutos.

* [Cmdlets do PowerShell do Azure](https://docs.microsoft.com/powershell/azure/install-azurerm-ps).

* Um Hub IoT do Azure. Se você não tiver um hub IoT, poderá usar o [cmdlet New-AzureRmIoTHub](https://docs.microsoft.com/powershell/module/azurerm.iothub/new-azurermiothub) para criar um ou usar o portal para [Criar um hub IoT](iot-hub-create-through-portal.md).

* Uma conta de armazenamento do Azure. Se você não tiver uma conta de armazenamento do Azure, você pode usar o [cmdlets do PowerShell do armazenamento do Azure](https://docs.microsoft.com/powershell/module/azurerm.storage/) para criar uma ou usar o portal para [criar uma conta de armazenamento](../storage/common/storage-create-storage-account.md)

## <a name="sign-in-and-set-your-azure-account"></a>Entre e configure sua conta do Azure

Entre na sua conta do Azure e selecione sua assinatura.

1. No prompt do PowerShell, execute o cmdlet **Connect-AzureRmAccount**:

    ```powershell
    Connect-AzureRmAccount
    ```

2. Caso tenha várias assinaturas do Azure, entrar no Azure concede a você acesso a todas as assinaturas do Azure associadas às suas credenciais. Use o comando a seguir para listar as assinaturas do Azure disponíveis para você:

    ```powershell
    Get-AzureRMSubscription
    ```

    Use o comando a seguir para selecionar a assinatura que você deseja usar para executar os comandos para gerenciar seu hub IoT. Você pode usar a ID ou nome da assinatura da saída do comando anterior:

    ```powershell
    Select-AzureRMSubscription `
        -SubscriptionName "{your subscription name}"
    ```

## <a name="retrieve-your-storage-account-details"></a>Recupere os detalhes da sua conta de armazenamento

As etapas a seguir pressupõem que você criou sua conta de armazenamento usando o modelo de implantação do **Resource Manager** e não o modelo de implantação **Clássico**.

Para configurar os uploads de arquivos dos seus dispositivos, você precisa da cadeia de conexão de uma conta de armazenamento do Azure. A conta de armazenamento deve estar nas mesmas região e assinatura que seu Hub IoT. Você também precisa do nome de um contêiner de blob na conta de armazenamento. Use o comando a seguir para recuperar as chaves da conta de armazenamento:

```powershell
Get-AzureRmStorageAccountKey `
  -Name {your storage account name} `
  -ResourceGroupName {your storage account resource group}
```

Anote o valor de chave de conta de armazenamento **key1**. Ele é necessário nas etapas a seguir.

Você pode usar um contêiner de blob existente para os uploads de arquivo ou criar um novo:

* Para listar os contêineres de blob existentes em sua conta de armazenamento, use os seguintes comandos:

    ```powershell
    $ctx = New-AzureStorageContext `
        -StorageAccountName {your storage account name} `
        -StorageAccountKey {your storage account key}
    Get-AzureStorageContainer -Context $ctx
    ```

* Para criar um contêiner de blob em sua conta de armazenamento, use os seguintes comandos:

    ```powershell
    $ctx = New-AzureStorageContext `
        -StorageAccountName {your storage account name} `
        -StorageAccountKey {your storage account key}
    New-AzureStorageContainer `
        -Name {your new container name} `
        -Permission Off `
        -Context $ctx
    ```

## <a name="configure-your-iot-hub"></a>Configurar seu Hub IoT

Agora você pode configurar seu hub IoT para [fazer upload de arquivos para o hub IoT](iot-hub-devguide-file-upload.md) usando os detalhes da sua conta de armazenamento.

A configuração requer os seguintes valores:

* **Contêiner de armazenamento**: um contêiner de blob em uma conta de armazenamento do Azure na assinatura atual do Azure para associar ao Hub IoT. Você recuperou as informações da conta de armazenamento necessárias na seção anterior. O Hub IoT gera automaticamente os URIs de SAS com permissões de gravação para esse contêiner de blob para dispositivos a serem usados ao carregar arquivos.

* **Receber notificações para os arquivos carregados**: habilitar ou desabilitar notificações de upload de arquivo.

* **TTL de SAS**: essa configuração é o tempo de vida dos URIs de SAS retornados para o dispositivo pelo Hub IoT. Defina como uma hora por padrão.

* **TTL de configurações de notificação de arquivo padrão**: o tempo de vida de uma notificação de upload de arquivo antes de sua expiração. Defina como um dia por padrão.

* **Contagem de entrega máxima de notificação de arquivo**: o número de vezes que o Hub IoT tenta entregar uma notificação de carregamento de arquivo. Defina como 10 por padrão.

Use o cmdlet do PowerShell a seguir para configurar o arquivo de configurações de upload em seu Hub IoT:

```powershell
Set-AzureRmIotHub `
    -ResourceGroupName "{your iot hub resource group}" `
    -Name "{your iot hub name}" `
    -FileUploadNotificationTtl "01:00:00" `
    -FileUploadSasUriTtl "01:00:00" `
    -EnableFileUploadNotifications $true `
    -FileUploadStorageConnectionString "DefaultEndpointsProtocol=https;AccountName={your storage account name};AccountKey={your storage account key};EndpointSuffix=core.windows.net" `
    -FileUploadContainerName "{your blob container name}" `
    -FileUploadNotificationMaxDeliveryCount 10
```

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre os recursos de upload de arquivos do Hub IoT, consulte [Upload de arquivos de um dispositivo](iot-hub-devguide-file-upload.md).

Para saber mais sobre o gerenciamento do Hub IoT do Azure, siga estes links:

* [Gerenciamento em massa de dispositivos IoT](iot-hub-bulk-identity-mgmt.md)
* [Métricas do IoT Hub](iot-hub-metrics.md)
* [Monitoramento de operações](iot-hub-operations-monitoring.md)

Para explorar melhor as funcionalidades do Hub IoT, consulte:

* [Guia do desenvolvedor do Hub IoT](iot-hub-devguide.md)
* [Implantar IA em dispositivos de borda com o Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)
* [Proteger sua solução de IoT desde o início](/../iot-fundamentals/iot-security-ground-up.md)