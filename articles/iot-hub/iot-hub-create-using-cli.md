---
title: Criar um Hub IoT usando a CLI do Azure (az.py) | Microsoft Docs
description: Como criar um Hub IoT do Azure usando a CLI do Azure 2.0 de plataforma cruzada (az.py).
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/15/2016
ms.author: dobett
translationtype: Human Translation
ms.sourcegitcommit: 5ea7095e12b6194556d3cd0baa43ccfed1e087ee
ms.openlocfilehash: 4f512601cebcfce7bfac47ed440c96fcb2c91b5f
ms.lasthandoff: 02/27/2017


---
# <a name="create-an-iot-hub-using-the-azure-cli-20"></a>Criar um Hub IoT usando a CLI do Azure 2.0

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

## <a name="introduction"></a>Introdução

É possível usar a CLI do Azure 2.0 (az.py) para criar e gerenciar Hubs IoT do Azure de forma programática. Este artigo mostra como usar a CLI do Azure 2.0 (az.py) para criar um Hub IoT.

Você pode concluir a tarefa usando uma das seguintes versões da CLI:

* [CLI do Azure (azure.js)](iot-hub-create-using-cli-nodejs.md) – a CLI para os modelos de implantação clássico e de gerenciamento de recursos.
* CLI do Azure 2.0 (az.py) – a próxima geração da CLI para o modelo de implantação de gerenciamento de recursos, conforme descrito neste artigo.

Para concluir este tutorial, você precisará do seguinte:

* Uma conta ativa do Azure. Se você não tem uma conta, pode criar uma [conta gratuita][lnk-free-trial] em apenas alguns minutos.
* [CLI do Azure 2.0][lnk-CLI-install].

## <a name="sign-in-and-set-your-azure-account"></a>Entre e configure sua conta do Azure

Entre em sua conta do Azure e configure a CLI do Azure para trabalhar com recursos de Hub IoT.

1. Ao prompt de comando, execute o [comando de logon][lnk-login-command]:
    
    ```azurecli
    az login
    ```

    Siga as instruções de autenticação usando o código e entre em sua conta do Azure por meio de um navegador da Web.

2. Se você tiver várias assinaturas do Azure, entrar o Azure lhe dará acesso a todas as contas do Azure associadas às suas credenciais. Use o seguinte [comando para listar as contas do Azure][lnk-az-account-command] disponíveis para você:
    
    ```azurecli
    az account list 
    ```

    Use o comando a seguir para selecionar a assinatura que você deseja usar para executar os comandos e criar seu Hub IoT. Você pode usar a ID ou nome da assinatura da saída do comando anterior:

    ```azurecli
    az account set --subscription {your subscription name or id}
    ```

3. Instalar a CLI do Azure _componente do iot_. Execute o seguinte [comando para adicionar o componente d IoT][lnk-az-addcomponent-command]:
    
    ```azurecli
    az component update --add iot
    ```

4. Registre o provedor de IoT antes de implantar recursos de IoT. Execute o seguinte [comando para registrar o provedor de IoT][lnk-az-register-command]:
    
    ```azurecli
    az provider register -namespace Microsoft.Devices
    ```

## <a name="create-an-iot-hub"></a>Crie um Hub IoT

Use a CLI do Azure para criar um grupo de recursos e, em seguida, adicione um Hub IoT.

1. Quando cria um Hub IoT, você deve criá-lo em um grupo de recursos. Use um grupo de recursos existente ou execute o seguinte [comando para criar um grupo de recursos][lnk-az-resource-command]:
    
    ```azurecli
     az group create --name {your resource group name} --location westus
    ```

    > [!TIP]
    > O exemplo anterior cria o grupo de recursos localizado no Oeste dos EUA. Você pode exibir uma lista dos locais disponíveis executando o comando `az account list-locations -o table`.
    >
    >

2. Execute o seguinte [comando para criar um Hub IoT][lnk-az-iot-command] em seu grupo de recursos:
    
    ```azurecli
    az iot hub create --name {your iot hub name} --resource-group {your resource group name} --sku S1
    ```

> [!NOTE]
> O nome do hub IoT deve ser globalmente exclusivo. O comando anterior cria um Hub IoT com tipo de preço S1, pelo qual você será cobrado. Para saber mais, confira [Preço do Hub IoT do Azure][lnk-iot-pricing].
>
>

## <a name="remove-an-iot-hub"></a>Remover um Hub IoT

Você pode usar a CLI do Azure para [excluir um recurso individual][lnk-az-resource-command], como um Hub IoT ou excluir um grupo de recursos e todos os seus recursos, incluindo os Hubs IoT.

Para excluir um Hub IoT, execute o seguinte comando:

```azurecli
az resource delete --name {your iot hub name} --resource-group {your resource group name} --resource-type Microsoft.Devices/IotHubs
```

Para excluir um grupo de recursos e todos os seus recursos, execute o seguinte comando:

```azurecli
az group delete --name {your resource group name}
```

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre como desenvolver para o Hub IoT, veja os seguintes artigos:

* [Guia do desenvolvedor do Hub IoT][lnk-devguide]

Para explorar melhor as funcionalidades do Hub IoT, consulte:

* [Uso do Portal do Azure para gerenciar o Hub IoT][lnk-portal]

<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-CLI-install]: https://docs.microsoft.com/cli/azure/install-az-cli2
[lnk-login-command]: https://docs.microsoft.com/cli/azure/get-started-with-az-cli2
[lnk-az-account-command]: https://docs.microsoft.com/cli/azure/account
[lnk-az-register-command]: https://docs.microsoft.com/cli/azure/provider
[lnk-az-addcomponent-command]: https://docs.microsoft.com/cli/azure/component
[lnk-az-resource-command]: https://docs.microsoft.com/cli/azure/resource
[lnk-az-iot-command]: https://docs.microsoft.com/cli/azure/iot
[lnk-iot-pricing]: https://azure.microsoft.com/pricing/details/iot-hub/
[lnk-devguide]: iot-hub-devguide.md
[lnk-portal]: iot-hub-create-through-portal.md 

