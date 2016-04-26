<properties
	pageTitle="Criar um Hub IoT usando um modelo ARM e PowerShell | Microsoft Azure"
	description="Siga este tutorial para começar a usar os modelos do Gerenciador de Recursos para criar um Hub IoT com PowerShell."
	services="iot-hub"
	documentationCenter=".net"
	authors="dominicbetts"
	manager="timlt"
	editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="multiple"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="04/07/2016"
     ms.author="dobett"/>

# Criar um Hub IoT usando PowerShell

[AZURE.INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

## Introdução

Você pode usar o ARM (Gerenciador de Recursos do Azure) para criar e gerenciar Hubs IoT do Azure de forma programática. Este tutorial mostra como usar um modelo do gerenciador de recursos para criar um Hub IoT com o PowerShell.

> [AZURE.NOTE] O Azure tem dois modelos de implantação diferentes para criar e trabalhar com recursos: [Gerenciador de Recursos e clássico](../resource-manager-deployment-model.md). Este artigo aborda o uso do modelo de implantação do Gerenciador de Recursos.

Para concluir este tutorial, você precisará do seguinte:

- Uma conta ativa do Azure. <br/>Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Avaliação gratuita do Azure][lnk-free-trial].
- [Microsoft Azure PowerShell 1.0][lnk-powershell-install] ou posterior.

> [AZURE.TIP] O artigo [Como usar o Azure PowerShell com o Gerenciador de Recursos do Azure][lnk-powershell-arm] fornece mais informações sobre como usar scripts do PowerShell e modelos de ARM para criar recursos do Azure.

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

Use um modelo JSON para criar um novo hub IoT em seu grupo de recursos. Você também pode usar um modelo para fazer alterações em um hub IoT existente.

1. Use um editor de texto para criar um modelo ARM chamado **template.json** com a seguinte definição de recurso a fim de criar um novo Hub IoT padrão. Este exemplo adiciona o Hub IoT à região **Leste dos EUA**, cria dois grupos de consumidores (**cg1** e **cg2**) no ponto de extremidade compatível com o Hub de Eventos e usa a versão de API **2016-02-03**. Esse modelo também espera que você passe o nome do Hub IoT como um parâmetro chamado **hubName**.

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

2. Salve o arquivo de modelo em seu computador local. Este exemplo pressupõe que você irá salvá-lo em uma pasta chamada **c:\\templates**.

3. Execute o seguinte comando para implantar seu novo Hub IoT, passando o nome de seu Hub IoT como um parâmetro. Neste exemplo, o nome do Hub IoT é **myiothub** (observe que esse nome precisa ser globalmente exclusivo).

    ```
    New-AzureRmResourceGroupDeployment -ResourceGroupName MyIoTRG1 -TemplateFile C:\templates\template.json -hubName myiothub
    ```

4. A saída exibe as chaves para o Hub IoT criado.

5. Você pode verificar se o seu aplicativo adicionou o novo hub IoT visitando o [portal][lnk-azure-portal] e exibindo sua lista de recursos ou usando o cmdlet **Get-AzureRmResource** do PowerShell.

> [AZURE.NOTE] Este aplicativo de exemplo adiciona um IoT Hub de padrão S1 pelo qual você será cobrado. É possível excluir o hub IoT por meio do [portal][lnk-azure-portal] ou usando o cmdlet **Remove-AzureRmResource**do PowerShell quando tiver terminado.

## Próximas etapas

Agora que você implantou um Hub IoT usando um modelo ARM com PowerShell, convém explorar ainda mais:

- Leia sobre os recursos da [API REST do Provedor de Recursos do Hub IoT][lnk-rest-api].
- Leia a [Visão geral do Gerenciador de Recursos do Azure][lnk-azure-rm-overview] para saber mais sobre os recursos do Gerenciador de Recursos do Azure.

<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-azure-portal]: https://portal.azure.com/
[lnk-powershell-install]: ../powershell-install-configure.md
[lnk-rest-api]: https://msdn.microsoft.com/library/mt589014.aspx
[lnk-azure-rm-overview]: ../resource-group-overview.md
[lnk-powershell-arm]: ../powershell-azure-resource-manager.md

<!---HONumber=AcomDC_0413_2016-->