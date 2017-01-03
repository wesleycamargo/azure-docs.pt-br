---
title: Criar um Hub IoT usando a CLI do Azure | Microsoft Docs
description: Siga este artigo para criar um Hub IoT usando a Interface de linha de comando do Azure.
services: iot-hub
documentationcenter: .net
author: BeatriceOltean
manager: timlt
editor: 
ms.assetid: 46a17831-650c-41d9-b228-445c5bb423d3
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/21/2016
ms.author: boltean
translationtype: Human Translation
ms.sourcegitcommit: 00746fa67292fa6858980e364c88921d60b29460
ms.openlocfilehash: 637cdc4763cd0caa9f75e25015c0c12e9db629cb


---
# <a name="create-an-iot-hub-using-azure-cli"></a>Criar um Hub IoT usando a CLI do Azure
[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

## <a name="introduction"></a>Introdução
Você pode usar a Interface de linha de comando do Azure para criar e gerenciar hubs IoT do Azure de forma programática. Este artigo mostra como usar a CLI do Azure para criar um Hub IoT.

Para concluir este tutorial, você precisará do seguinte:

* Uma conta ativa do Azure. Se não tiver uma, você poderá criar uma [conta gratuita][lnk-free-trial] em apenas alguns minutos.
* [CLI do Azure 0.10.4][lnk-CLI-install] ou posterior. Se você já tiver a CLI do Azure, você pode validar a versão atual no prompt de comando com o seguinte comando:
  ```
    azure --version
  ```

> [!NOTE]
> O Azure tem dois modelos de implantação diferentes para criar e trabalhar com recursos: [Azure Resource Manager e clássico](../azure-resource-manager/resource-manager-deployment-model.md). A CLI do Azure deve estar no modo Gerenciador de Recursos do Azure:
> 
> ```
> azure config mode arm
> ```
> 
> 

## <a name="set-your-azure-account-and-subscription"></a>Definir sua conta e assinatura do Azure
1. No prompt de comando, entre ao digitar o seguinte comando
   
   ```
    azure login
   ```
   Use o navegador sugerido e o código para autenticar.
2. Se você tiver várias assinaturas do Azure, a conexão ao Azure dará acesso a todas as assinaturas do Azure associadas às suas credenciais. Você pode visualizar as assinaturas do Azure, bem como qual delas é a padrão, usando o comando
   
   ```
    azure account list 
   ```

   Para definir o contexto de assinatura sob o qual você deseja executar o restante dos comandos, use

   ```
    azure account set <subscription name>
   ```

3. Caso você não tenha um grupo de recursos, você pode criar um chamado **exampleResourceGroup** 
   ```
    azure group create -n exampleResourceGroup -l westus
   ```

> [!TIP]
> O artigo [Use the Azure CLI to manage Azure resources and resource groups][lnk-CLI-arm] (Usar a CLI do Azure para gerenciar recursos e grupos de recursos do Azure) fornece mais informações sobre como usar a CLI do Azure para gerenciar recursos do Azure. 
> 
> 

## <a name="create-an-iot-hub"></a>Crie um Hub IoT
Parâmetros requeridos:

```
 azure iothub create -g <resource-group> -n <name> -l <location> -s <sku-name> -u <units>  
    - <resourceGroup> The resource group name (case insensitive alphanumeric, underscore and hyphen, 1-64 length)
    - <name> (The name of the IoT hub to be created. The format is case insensitive alphanumeric, underscore and hyphen, 3-50 length )
    - <location> (The location (azure region/datacenter) where the IoT hub will be provisioned.
    - <sku-name> (The name of the sku, one of: [F1, S1, S2, S3] etc. For the latest full list refer to the pricing page for IoT Hub.
    - <units> (The number of provisioned units. Range : F1 [1-1] : S1, S2 [1-200] : S3 [1-10]. IoT Hub units are based on your total message count and the number of devices you want to connect.)
```
Para ver todos os parâmetros disponíveis para a criação, você pode usar o comando de ajuda no prompt de comando

```
    azure iothub create -h 
```
Exemplo rápido:

 Para criar um Hub IoT chamado **exampleIoTHubName** no grupo de recursos **exampleResourceGroup**, execute o seguinte comando

```
    azure iothub create -g exampleResourceGroup -n exampleIoTHubName -l westus -k s1 -u 1
```

> [!NOTE]
> Este comando da CLI do Azure cria um Hub IoT Standard S1 pelo qual você será cobrado. Você pode excluir o hub IoT **exampleIoTHubName** usando o seguinte comando 
> 
> ```
> azure iothub delete -g exampleResourceGroup -n exampleIoTHubName
> ```
> 
> 
> 

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre como desenvolver para o Hub IoT, consulte o seguinte:

* [SDKs do IoT][lnk-sdks]

Para explorar melhor as funcionalidades do Hub IoT, consulte:

* [Usar o Portal do Azure para gerenciar o Hub IoT][lnk-portal]

<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-azure-portal]: https://portal.azure.com/
[lnk-status]: https://azure.microsoft.com/status/
[lnk-CLI-install]: ../xplat-cli-install.md
[lnk-rest-api]: https://msdn.microsoft.com/library/mt589014.aspx
[lnk-CLI-arm]: ../azure-resource-manager/xplat-cli-azure-resource-manager.md

[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-portal]: iot-hub-create-through-portal.md 



<!--HONumber=Nov16_HO5-->


