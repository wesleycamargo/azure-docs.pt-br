---
title: Criar um Hub IoT do Azure usando um cmdlet do PowerShell | Microsoft Docs
description: Como usar um cmdlet do PowerShell para criar um hub IoT.
services: iot-hub
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/23/2017
ms.author: dobett
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: 5c6ab3a8fdacb43546801518b76a15314d0b4b0f
ms.lasthandoff: 03/21/2017


---
# <a name="create-an-iot-hub-using-the-new-azurermiothub-cmdlet"></a>Criar um hub IoT usando o cmdlet New-AzureRmIotHub

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

## <a name="introduction"></a>Introdução

É possível usar os cmdlets do Azure PowerShell para criar e gerenciar hubs IoT do Azure. Este tutorial mostra como criar um hub IoT com o PowerShell.

> [!NOTE]
> O Azure tem dois modelos de implantação diferentes para criar e trabalhar com recursos: [Azure Resource Manager e Classic](../azure-resource-manager/resource-manager-deployment-model.md).  Este artigo aborda o uso do modelo de implantação do Azure Resource Manager.

Para concluir este tutorial, você precisará do seguinte:

* Uma conta ativa do Azure. <br/>Se não tiver uma conta, você poderá criar uma [conta gratuita][lnk-free-trial] em apenas alguns minutos.
* [Azure PowerShell 1.0][lnk-powershell-install] ou posterior.
* [Cmdlets do Azure Resource Manager][lnk-rm-install].

## <a name="connect-to-your-azure-subscription"></a>Conecte-se à sua assinatura do Azure
Em um prompt de comando do PowerShell, digite o seguinte comando para entrar em sua assinatura do Azure:

```powershell
Login-AzureRmAccount
```

## <a name="create-resource-group"></a>Criar grupo de recursos

Você precisa de um grupo de recursos para implantar um hub IoT. É possível usar um grupo de recursos existente ou criar um novo.

Você pode usar o seguinte comando para descobrir as localizações em que é possível implantar um hub IoT:

```powershell
((Get-AzureRmResourceProvider `
  -ProviderNamespace Microsoft.Devices).ResourceTypes `
  | Where-Object ResourceTypeName -eq IoTHubs).Locations
```

Para criar um grupo de recursos para o hub IoT em uma das localizações com suporte no Hub IoT, use o comando a seguir. Este exemplo cria um grupo de recursos chamado **MyIoTRG1** na região **Leste dos EUA**:

```powershell
New-AzureRmResourceGroup -Name MyIoTRG1 -Location "East US"
```

## <a name="create-an-iot-hub"></a>Crie um hub IoT

Para criar um hub IoT no grupo de recursos criado na etapa anterior, use o comando a seguir. Este exemplo cria um hub **S1** chamado **MyTestIoTHub** na região **Leste dos EUA**:

```powershell
New-AzureRmIotHub `
    -ResourceGroupName MyIoTRG1 `
    -Name MyTestIoTHub `
    -SkuName S1 -Units 1 `
    -Location "East US"
```

> [!NOTE]
> O nome do hub IoT deve ser exclusivo.

É possível listar todos os hubs IoT da assinatura usando o seguinte comando:

```powershell
Get-AzureRmIotHub
```

O exemplo anterior adiciona um Hub IoT S1 Standard pelo qual você será cobrado. É possível excluir o hub IoT usando o seguinte comando:

```powershell
Remove-AzureRmIotHub `
    -ResourceGroupName MyIoTRG1 `
    -Name MyTestIoTHub
```

Como alternativa, você pode remover um grupo de recursos e todos os recursos que ele contém usando o seguinte comando:

```powershell
Remove-AzureRmResourceGroup -Name MyIoTRG1
```

## <a name="next-steps"></a>Próximas etapas

Agora que você implantou um hub IoT usando um cmdlet do PowerShell, talvez você deseje explorar ainda mais:

* Descubra outros [cmdlets do PowerShell para trabalhar com o hub IoT][lnk-iothub-cmdlets].
* Leia sobre as funcionalidades da [API REST do provedor de recursos Hub IoT][lnk-rest-api].

Para saber mais sobre como desenvolver para o Hub IoT, veja os seguintes artigos:

* [Introdução ao SDK de C][lnk-c-sdk]
* [SDKs do Azure IoT][lnk-sdks]

Para explorar melhor as funcionalidades do Hub IoT, consulte:

* [Simulando um dispositivo com o SDK do Gateway do IoT][lnk-gateway]

<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-powershell-install]: /powershell/azureps-cmdlets-docs
[lnk-iothub-cmdlets]: /powershell/resourcemanager/azurerm.iothub/v1.3.0/azurerm.iothub
[lnk-rm-install]: /powershell/resourcemanager/
[lnk-rest-api]: https://msdn.microsoft.com/library/mt589014.aspx

[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md

