---
title: Criar um Hub IoT usando a CLI| Microsoft Docs
description: Siga este artigo para criar um Hub IoT usando a Interface de linha de comando do Azure.
services: iot-hub
documentationcenter: .net
author: BeatriceOltean
manager: timlt
editor: ''

ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/21/2016
ms.author: boltean

---
# <a name="create-an-iot-hub-using-cli"></a>Criar um Hub IoT usando a CLI
[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

## <a name="introduction"></a>Introdução
Você pode usar a Interface de linha de comando do Azure para criar e gerenciar hubs IoT do Azure de forma programática. Este artigo mostra como usar a CLI do Azure para criar um Hub IoT.

Para concluir este tutorial, você precisará do seguinte:

* Uma conta ativa do Azure. Você pode criar uma [conta de avaliação gratuita do Azure][Ink-free-trial] em apenas alguns minutos.
* [Azure CLI 0.10.4][lnk-CLI-install] ou posterior. Se você já tiver a CLI do Azure, poderá validar a versão atual no prompt de comando com o seguinte comando:
  ```
    azure --version
  ```

> [!NOTE]
> O Azure tem dois modelos de implantação diferentes para criar e trabalhar com recursos: [Gerenciador de Recursos e clássico](../resource-manager-deployment-model.md). A CLI do Azure deve estar no modo Azure Resource Manager:
> 
> ```
> azure config mode arm
> ```
> 
> 

## <a name="set-your-azure-account-and-subscription"></a>Definir sua conta e assinatura do Azure
1. No logon do prompt de comando, entre ao digitar o seguinte comando
   
   ```
    azure login
   ```
   Use o navegador da Web sugerido e o código para autenticar.
2. Se você tiver várias assinaturas do Azure, a conexão ao Azure dará acesso a todas as assinaturas associadas às suas credenciais. Você pode ver as assinaturas, bem como qual delas é a padrão, usando o comando
   
   ```
    azure account list 
   ```

Para definir o contexto de assinatura sob o qual você deseja executar o restante dos comandos, use

```
    azure account set <subscription name>
```

1. Caso você não tenha um grupo de recursos, você pode criar um chamado **exampleResourceGroup** 
   ```
    azure group create -n exampleResourceGroup -l westus
   ```

> [!TIP]
> O artigo [Usar a CLI do Azure para gerenciar recursos do Azure e grupos de recursos][Ink-CLI-arm] fornece mais informações sobre como usar a CLI do Azure para gerenciar recursos do Azure. 
> 
> 

## <a name="create-and-iot-hub"></a>Crie um Hub IoT
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
> Este comando de CLI cria um Hub IoT Standard S1 pelo qual você será cobrado. Você pode excluir o hub IoT **exampleIoTHubName** usando o seguinte comando 
> 
> ```
> azure iothub delete -g exampleResourceGroup -n exampleIoTHubName
> ```
> 
> 
> 

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre como desenvolver para o Hub IoT, consulte o seguinte:

* [SDKs de Hub IoT][lnk-sdks]

Para explorar melhor as funcionalidades do Hub IoT, consulte:

* [Usando o Portal do Azure para gerenciar o Hub IoT][lnk-portal]

<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-azure-portal]: https://portal.azure.com/
[lnk-status]: https://azure.microsoft.com/status/
[lnk-CLI-install]: ../xplat-cli-install.md
[lnk-rest-api]: https://msdn.microsoft.com/library/mt589014.aspx
[lnk-azure-rm-overview]: ../resource-group-overview.md
[lnk-CLI-arm]: ../xplat-cli-azure-resource-manager.md

[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-portal]: iot-hub-create-through-portal.md 



<!--HONumber=Oct16_HO2-->


