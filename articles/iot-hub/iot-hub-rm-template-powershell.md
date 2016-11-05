---
title: Criar um Hub IoT usando um modelo do Resource Manager e do PowerShell | Microsoft Docs
description: Siga este tutorial para começar a usar os modelos do Gerenciador de Recursos para criar um Hub IoT com PowerShell.
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: ''

ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/07/2016
ms.author: dobett

---
# Criar um Hub IoT usando PowerShell
[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

## Introdução
Você pode usar o Gerenciador de Recursos do Azure para criar e gerenciar hubs IoT do Azure de forma programática. Este tutorial mostra como usar um modelo do Resource Manager para criar um Hub IoT com o PowerShell.

> [!NOTE]
> O Azure tem dois modelos de implantação diferentes para criar e trabalhar com recursos: [Gerenciador de Recursos e clássico](../resource-manager-deployment-model.md). Este artigo aborda o uso do modelo de implantação do Gerenciador de Recursos.
> 
> 

Para concluir este tutorial, você precisará do seguinte:

* Uma conta ativa do Azure. <br/>Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Avaliação gratuita do Azure][lnk-free-trial].
* [Microsoft Azure PowerShell 1.0][lnk-powershell-install] ou posterior.

> [!TIP]
> O artigo [Como usar o Azure PowerShell com o Azure Resource Manager][lnk-powershell-arm] fornece mais informações sobre como usar scripts do PowerShell e modelos do Resource Manager para criar recursos do Azure.
> 
> 

## Conecte-se à sua assinatura do Azure
Em um prompt de comando do PowerShell, digite o seguinte comando para entrar em sua assinatura do Azure:

```
Login-AzureRmAccount
```

Você pode usar os comandos a seguir para descobrir onde você pode implantar um Hub IoT e as versões de API com suporte no momento:

```
((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Devices).ResourceTypes | Where-Object ResourceTypeName -eq IoTHubs).Locations
((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Devices).ResourceTypes | Where-Object ResourceTypeName -eq IoTHubs).ApiVersions
```

Crie um grupo de recursos para conter o Hub IoT usando o seguinte comando em um dos locais com suporte para o Hub IoT. Este exemplo cria um grupo de recursos chamado **MyIoTRG1**:

```
New-AzureRmResourceGroup -Name MyIoTRG1 -Location "East US"
```

## Enviar um modelo para criar um hub IoT
Use um modelo JSON para criar um Hub IoT em seu grupo de recursos. Você também pode usar um modelo para fazer alterações em um hub IoT existente.

1. Use um editor de texto para criar um modelo do Resource Manager chamado **template.json** com a seguinte definição de recurso, a fim de criar um novo Hub IoT padrão. Este exemplo adiciona o Hub IoT à região **Leste dos EUA**, cria dois grupos de consumidores (**cg1** e **cg2**) no ponto de extremidade compatível com o Hub de Eventos e usa a versão de API **2016-02-03**. Esse modelo também espera que você passe o nome do Hub IoT como um parâmetro chamado **hubName**. Para obter a lista atual de localizações que dão suporte ao Hub IoT, confira o [Status do Azure][lnk-status].
   
    ```
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
2. Salve o arquivo de modelo em seu computador local. Este exemplo pressupõe que você o salvará em uma pasta chamada **c:\\templates**.
3. Execute o seguinte comando para implantar seu novo Hub IoT, passando o nome de seu Hub IoT como um parâmetro. Neste exemplo, o nome do Hub IoT é **abcmyiothub** (observe que esse nome precisa ser globalmente exclusivo e, por isso, deve incluir seu nome ou suas iniciais):
   
    ```
    New-AzureRmResourceGroupDeployment -ResourceGroupName MyIoTRG1 -TemplateFile C:\templates\template.json -hubName abcmyiothub
    ```
4. A saída exibe as chaves para o Hub IoT criado.
5. Você pode verificar se o seu aplicativo adicionou o novo hub IoT visitando o [portal][lnk-azure-portal] e exibindo sua lista de recursos ou usando o cmdlet **Get-AzureRmResource** do PowerShell.

> [!NOTE]
> Este aplicativo de exemplo adiciona um Hub IoT Standard S1 pelo qual você será cobrado. É possível excluir o hub IoT por meio do [portal][lnk-azure-portal] ou usando o cmdlet **Remove-AzureRmResource**do PowerShell quando tiver terminado.
> 
> 

## Próximas etapas
Agora que você implantou um Hub IoT usando um modelo do Resource Manager com o PowerShell, convém explorar ainda mais:

* Leia sobre as funcionalidades da [API REST do provedor de recursos Hub IoT][lnk-rest-api].
* Leia a [Visão geral do Gerenciador de Recursos do Azure][lnk-azure-rm-overview] para saber mais sobre os recursos do Gerenciador de Recursos do Azure.

Para saber mais sobre como desenvolver para o Hub IoT, consulte o seguinte:

* [Introdução ao SDK de C][lnk-c-sdk]
* [SDKs do Hub IoT][lnk-sdks]

Para explorar melhor as funcionalidades do Hub IoT, consulte:

* [Projetar sua solução][lnk-design]
* [Explorar o gerenciamento de dispositivo usando a interface do usuário de exemplo][lnk-dmui]
* [Simular um dispositivo com o SDK do Gateway][lnk-gateway]
* [Usar o Portal do Azure para gerenciar o Hub IoT][lnk-portal]

<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-azure-portal]: https://portal.azure.com/
[lnk-status]: https://azure.microsoft.com/status/
[lnk-powershell-install]: ../powershell-install-configure.md
[lnk-rest-api]: https://msdn.microsoft.com/library/mt589014.aspx
[lnk-azure-rm-overview]: ../resource-group-overview.md
[lnk-powershell-arm]: ../powershell-azure-resource-manager.md

[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-sdks-summary.md

[lnk-design]: iot-hub-guidance.md
[lnk-dmui]: iot-hub-device-management-ui-sample.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
[lnk-portal]: iot-hub-manage-through-portal.md

<!---HONumber=AcomDC_0907_2016-->