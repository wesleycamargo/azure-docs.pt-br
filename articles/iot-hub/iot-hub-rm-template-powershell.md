---
title: Criar um Hub IoT do Azure usando um modelo (PowerShell) | Microsoft Docs
description: Como usar um modelo do Azure Resource Manager para criar um Hub IoT com o PowerShell.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/08/2017
ms.openlocfilehash: fcc9af9e614b0a1b7977ba18f3147fddab8b7b7d
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/05/2019
ms.locfileid: "59045041"
---
# <a name="create-an-iot-hub-using-azure-resource-manager-template-powershell"></a>Criar um Hub IoT usando um modelo do Azure Resource Manager (PowerShell)

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

Você pode usar o Gerenciador de Recursos do Azure para criar e gerenciar hubs IoT do Azure de forma programática. Este tutorial mostra como usar um modelo do Azure Resource Manager para criar um Hub IoT com o PowerShell.

> [!NOTE]
> O Azure tem dois modelos de implantação diferentes para criar e trabalhar com recursos: [Azure Resource Manager e clássico](../azure-resource-manager/resource-manager-deployment-model.md). Este artigo aborda o uso do modelo de implantação do Azure Resource Manager.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Para concluir este tutorial, você precisará do seguinte:

* Uma conta ativa do Azure. <br/>Se você não tem uma conta, pode criar uma [conta gratuita][lnk-free-trial] em apenas alguns minutos.
* [Azure PowerShell 1.0][lnk-powershell-install] ou posterior.

> [!TIP]
> O artigo [Como usar o Azure PowerShell com o Azure Resource Manager][lnk-powershell-arm] fornece mais informações sobre como usar o PowerShell e modelos do Azure Resource Manager para criar recursos do Azure.

## <a name="connect-to-your-azure-subscription"></a>Conecte-se à sua assinatura do Azure

Em um prompt de comando do PowerShell, digite o seguinte comando para entrar em sua assinatura do Azure:

```powershell
Connect-AzAccount
```

Caso tenha várias assinaturas do Azure, entrar no Azure concede a você acesso a todas as assinaturas do Azure associadas às suas credenciais. Use o comando a seguir para listar as assinaturas do Azure disponíveis para você:

```powershell
Get-AzSubscription
```

Use o comando a seguir para selecionar a assinatura que você deseja usar para executar os comandos e criar seu Hub IoT. Você pode usar a ID ou nome da assinatura da saída do comando anterior:

```powershell
Select-AzSubscription `
    -SubscriptionName "{your subscription name}"
```

Você pode usar os comandos a seguir para descobrir onde você pode implantar um Hub IoT e as versões de API com suporte no momento:

```powershell
((Get-AzResourceProvider -ProviderNamespace Microsoft.Devices).ResourceTypes | Where-Object ResourceTypeName -eq IoTHubs).Locations
((Get-AzResourceProvider -ProviderNamespace Microsoft.Devices).ResourceTypes | Where-Object ResourceTypeName -eq IoTHubs).ApiVersions
```

Crie um grupo de recursos para conter o Hub IoT usando o seguinte comando em um dos locais com suporte para o Hub IoT. Este exemplo cria um grupo de recursos chamado **MyIoTRG1**:

```powershell
New-AzResourceGroup -Name MyIoTRG1 -Location "East US"
```

## <a name="submit-a-template-to-create-an-iot-hub"></a>Enviar um modelo para criar um hub IoT

Use um modelo JSON para criar um Hub IoT em seu grupo de recursos. Também é possível usar um modelo do Azure Resource Manager para fazer alterações em um hub IoT existente.

1. Use um editor de texto para criar um modelo do Azure Resource Manager chamado **template.json** com a seguinte definição de recurso, a fim de criar um novo Hub IoT padrão. Este exemplo adiciona o Hub IoT à região **Leste dos EUA**, cria dois grupos de consumidores (**cg1** e **cg2**) no ponto de extremidade compatível com o Hub de Eventos e usa a versão de API **2016-02-03**. Esse modelo também espera que você passe o nome do Hub IoT como um parâmetro chamado **hubName**. Para obter a lista atual de localizações que dão suporte ao Hub IoT, confira o [Status do Azure][lnk-status].

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "hubName": {
          "type": "string"
        }
      },
      "resources": [
      {
        "apiVersion": "2016-02-03",
        "type": "Microsoft.Devices/IotHubs",
        "name": "[parameters('hubName')]",
        "location": "East US",
        "sku": {
          "name": "S1",
          "tier": "Standard",
          "capacity": 1
        },
        "properties": {
          "location": "East US"
        }
      },
      {
        "apiVersion": "2016-02-03",
        "type": "Microsoft.Devices/IotHubs/eventhubEndpoints/ConsumerGroups",
        "name": "[concat(parameters('hubName'), '/events/cg1')]",
        "dependsOn": [
          "[concat('Microsoft.Devices/Iothubs/', parameters('hubName'))]"
        ]
      },
      {
        "apiVersion": "2016-02-03",
        "type": "Microsoft.Devices/IotHubs/eventhubEndpoints/ConsumerGroups",
        "name": "[concat(parameters('hubName'), '/events/cg2')]",
        "dependsOn": [
          "[concat('Microsoft.Devices/Iothubs/', parameters('hubName'))]"
        ]
      }
      ],
      "outputs": {
        "hubKeys": {
          "value": "[listKeys(resourceId('Microsoft.Devices/IotHubs', parameters('hubName')), '2016-02-03')]",
          "type": "object"
        }
      }
    }
    ```

2. Salve o arquivo de modelo do Azure Resource Manager na máquina local. Este exemplo pressupõe que você o salvará em uma pasta chamada **c:\templates**.

3. Execute o seguinte comando para implantar seu novo Hub IoT, passando o nome de seu Hub IoT como um parâmetro. Neste exemplo, o nome do hub IoT é `abcmyiothub`. O nome do hub IoT deve ser globalmente exclusivo:

    ```powershell
    New-AzResourceGroupDeployment -ResourceGroupName MyIoTRG1 -TemplateFile C:\templates\template.json -hubName abcmyiothub
    ```
   [!INCLUDE [iot-hub-pii-note-naming-hub](../../includes/iot-hub-pii-note-naming-hub.md)]

4. A saída exibe as chaves para o Hub IoT criado.

5. Para verificar seu aplicativo adicionado ao novo hub IoT, visite o [Portal do Azure][lnk-azure-portal] e exiba sua lista de recursos. Como alternativa, use o **Get-AzResource** cmdlet do PowerShell.

> [!NOTE]
> Este aplicativo de exemplo adiciona um Hub IoT Standard S1 pelo qual você será cobrado. Você pode excluir o hub IoT por meio de [portal do Azure] [ lnk-azure-portal] ou usando o **remover AzResource** cmdlet do PowerShell quando tiver terminado.

## <a name="next-steps"></a>Próximas etapas

Agora que você implantou um Hub IoT usando um modelo do Azure Resource Manager com o PowerShell, convém explorar ainda mais:

* Leia sobre as funcionalidades da [API REST do provedor de recursos Hub IoT][lnk-rest-api].
* Leia a [Visão geral do Azure Resource Manager][lnk-azure-rm-overview] para saber mais sobre os recursos do Azure Resource Manager.
* Para obter a sintaxe e as propriedades de JSON a serem usadas em modelos, consulte [Tipos de recurso Microsoft.Devices](/azure/templates/microsoft.devices/iothub-allversions).

Para saber mais sobre como desenvolver para o Hub IoT, veja os seguintes artigos:

* [Introdução ao SDK de C][lnk-c-sdk]
* [SDKs do Azure IoT][lnk-sdks]

Para explorar melhor as funcionalidades do Hub IoT, consulte:

* [Implantação do IA em dispositivos de borda com o Azure IoT Edge][lnk-iotedge]

<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-azure-portal]: https://portal.azure.com/
[lnk-status]: https://azure.microsoft.com/status/
[lnk-powershell-install]: /powershell/azure/install-Az-ps
[lnk-rest-api]: https://docs.microsoft.com/rest/api/iothub/iothubresource
[lnk-azure-rm-overview]: ../azure-resource-manager/resource-group-overview.md
[lnk-powershell-arm]: ../azure-resource-manager/manage-resources-powershell.md

[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
