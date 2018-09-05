---
title: Criar um Hub IoT do Azure usando um cmdlet do PowerShell | Microsoft Docs
description: Como usar um cmdlet do PowerShell para criar um hub IoT.
author: robinsh
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: robinsh
ms.openlocfilehash: 7ecd35ba33d2860ba052aa27286c69985c2f7dd9
ms.sourcegitcommit: 63613e4c7edf1b1875a2974a29ab2a8ce5d90e3b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/29/2018
ms.locfileid: "43190223"
---
# <a name="create-an-iot-hub-using-the-new-azurermiothub-cmdlet"></a>Criar um hub IoT usando o cmdlet New-AzureRmIotHub

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

## <a name="introduction"></a>Introdução

É possível usar os cmdlets do Azure PowerShell para criar e gerenciar hubs IoT do Azure. Este tutorial mostra como criar um hub IoT com o PowerShell.

Para concluir estas instruções, você precisa de uma assinatura do Azure. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="connect-to-your-azure-subscription"></a>Conecte-se à sua assinatura do Azure

Se você estiver usando o Cloud Shell, você já estará conectado à sua assinatura. Se em vez disso você estiver executando o PowerShell localmente, digite o seguinte comando para entrar em sua assinatura do Azure:

```powershell
# Log into Azure account.
Login-AzureRMAccount
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Você precisa de um grupo de recursos para implantar um hub IoT. É possível usar um grupo de recursos existente ou criar um novo.

Para criar um grupo de recursos para o Hub IoT, use o comando [New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/module/AzureRM.Resources/New-AzureRmResourceGroup). Este exemplo cria um grupo de recursos chamado **MyIoTRG1** na região **Leste dos EUA**:

```azurepowershell-interactive
New-AzureRmResourceGroup -Name MyIoTRG1 -Location "East US"
```

## <a name="create-an-iot-hub"></a>Crie um hub IoT

Para criar um Hub IoT no grupo de recursos criado na etapa anterior, use o comando [New-AzureRmIotHub](https://docs.microsoft.com/powershell/module/AzureRM.IotHub/New-AzureRmIotHub). Este exemplo cria um hub **S1** chamado **MyTestIoTHub** na região **Leste dos EUA**:

```azurepowershell-interactive
New-AzureRmIotHub `
    -ResourceGroupName MyIoTRG1 `
    -Name MyTestIoTHub `
    -SkuName S1 -Units 1 `
    -Location "East US"
```

O nome do Hub IoT deve ser globalmente exclusivo.

[!INCLUDE [iot-hub-pii-note-naming-hub](../../includes/iot-hub-pii-note-naming-hub.md)]

É possível listar todos os hubs IoT da assinatura usando o comando [Get-AzureRmIotHub](https://docs.microsoft.com/powershell/module/AzureRM.IotHub/Get-AzureRmIotHub):

```azurepowershell-interactive
Get-AzureRmIotHub
```

Este exemplo mostra o Hub IoT Standard S1, que você criou na etapa anterior.

É possível excluir o Hub IoT usando o comando [Remove-AzureRmIotHub](https://docs.microsoft.com/powershell/module/azurerm.iothub/remove-azurermiothub):

```azurepowershell-interactive
Remove-AzureRmIotHub `
    -ResourceGroupName MyIoTRG1 `
    -Name MyTestIoTHub
```

Como alternativa, você pode remover um grupo de recursos e todos os recursos que ele contém usando o comando [Remove-AzureRmResourceGroup](https://docs.microsoft.com/powershell/module/AzureRM.Resources/Remove-AzureRmResourceGroup):

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name MyIoTRG1
```

## <a name="next-steps"></a>Próximas etapas

Agora que você implantou um Hub IoT usando um cmdlet do PowerShell, se desejar explorar ainda mais, confira os seguintes artigos:

* [Cmdlets do PowerShell para trabalhar com o Hub IoT](https://docs.microsoft.com/powershell/module/azurerm.iothub/).

* [API REST de provedor de recursos do Hub IoT](https://docs.microsoft.com/rest/api/iothub/iothubresource).

Para saber mais sobre como desenvolver para o Hub IoT, veja os seguintes artigos:

* [Introdução ao SDK C](iot-hub-device-sdk-c-intro.md)

* [SDKs do Azure IoT](iot-hub-devguide-sdks.md)

Para explorar melhor as funcionalidades do Hub IoT, consulte:

* [Implantando o AI em dispositivos de borda com o Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)