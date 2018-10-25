---
title: Como direcionar para versões de tempo de execução do Azure Functions
description: O Azure Functions é compatível com várias versões do tempo de execução. Saiba como especificar a versão de tempo de execução de um aplicativo de funções hospedado no Azure.
services: functions
documentationcenter: ''
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: conceptual
ms.date: 10/05/2018
ms.author: glenga
ms.openlocfilehash: 6d89746c0a2d4642e5025789d352803195c0a3b9
ms.sourcegitcommit: 55952b90dc3935a8ea8baeaae9692dbb9bedb47f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2018
ms.locfileid: "48886800"
---
# <a name="how-to-target-azure-functions-runtime-versions"></a>Como direcionar para versões de tempo de execução do Azure Functions

Uma execução do aplicativo de funções em uma versão específica do tempo de execução do Azure Functions. Há duas versões principais: [1.x e 2.x](functions-versions.md). Por padrão, os aplicativos de funções criados na versão 2.x do tempo de execução. Este artigo explica como configurar um aplicativo de funções no Azure para executar a versão que você escolher. Para obter informações sobre como configurar um ambiente de desenvolvimento local para uma versão específica, consulte [Código e teste local do Azure Functions](functions-run-local.md).

> [!NOTE]
> Não é possível alterar a versão de tempo de execução de um aplicativo de funções que tenha uma ou mais funções. Você deve usar o portal do Azure para alterar a versão do tempo de execução.

## <a name="automatic-and-manual-version-updates"></a>Atualizações de versão automática e manual

O Functions permite que você direcione uma versão específica do tempo de execução usando a configuração de aplicativo `FUNCTIONS_EXTENSION_VERSION` em um aplicativo de funções. O aplicativo de funções é mantido na versão principal especificada até que você escolha explicitamente mudar para uma nova versão.

Se você especificar apenas a versão principal ("~2" para 2.x ou "~1" para 1.x), o aplicativo de função será atualizado automaticamente para novas versões secundárias do tempo de execução quando elas estiverem disponíveis. Novas versões secundárias não introduzem alterações consideráveis. Se você especificar uma versão secundária (por exemplo, "2.0.12345"), o aplicativo de funções será fixado nessa versão específica até que seja explicitamente alterado.

Quando uma nova versão está disponível publicamente, um prompt no portal oferece a possibilidade de atualizar para essa versão. Depois de mudar para uma nova versão, você sempre poderá usar a configuração de aplicativo `FUNCTIONS_EXTENSION_VERSION` para voltar para uma versão anterior do tempo de execução.

Uma alteração na versão do tempo de execução faz com que seu aplicativo de função reinicie.

Os valores que você pode definir na configuração de aplicativo `FUNCTIONS_EXTENSION_VERSION` para habilitar as atualizações automáticas no momento são "~1" para o tempo de execução 1.x e "~2" para 2.x.

## <a name="view-and-update-the-current-runtime-version"></a>Exibir e atualizar a versão de tempo de execução atual

Use o procedimento a seguir para exibir a versão de tempo de execução usada no momento por um aplicativo de funções.

1. No [portal do Azure](https://portal.azure.com), navegue até o aplicativo de funções.

1. Em **Recursos Configurados**, escolha **Configurações do aplicativo de funções**.

    ![Selecionar as configurações do aplicativo de funções](./media/set-runtime-version/add-update-app-setting.png)

1. Na guia **Configurações do aplicativo de funções**, localize a **Versão de tempo de execução**. Observe a versão de tempo de execução específica e a versão principal solicitada. No exemplo a seguir, a versão é definida como `~2`.

   ![Selecionar as configurações do aplicativo de funções](./media/set-runtime-version/function-app-view-version.png)

1. Para fixar o aplicativo de funções no tempo de execução de versão 1.x, escolha **~1** em **Versão de tempo de execução**. Essa opção estará desabilitada quando houver funções no aplicativo.

1. Se você alterou a versão de tempo de execução, retorne à guia de **Visão geral** e escolha **Restart** para reiniciar o aplicativo.  O aplicativo de funções reinicia a execução no tempo de execução de versão 1.x e os modelos de versão 1.x são usados quando você cria funções.

## <a name="view-and-update-the-runtime-version-using-azure-cli"></a>Exibir e atualizar a versão de tempo de execução usando CLI do Azure

Também é possível exibir e definir o `FUNCTIONS_EXTENSION_VERSION` da CLI do Azure.

>[!NOTE]
>Como outras configurações podem ser afetadas pela versão de tempo de execução, você deverá alterar a versão no portal. Quando você altera as versões de tempo de execução, o portal automaticamente executa outras atualizações necessárias como a versão do Node.js e a pilha de tempo de execução.  

Usando a CLI do Azure, exiba a versão de tempo de execução atual com o comando [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings#set).

```azurecli-interactive
az functionapp config appsettings list --name <function_app> \
--resource-group <my_resource_group>
```

Nesse código, substitua `<function_app>` pelo nome de seu aplicativo de funções. Substitua também `<my_resource_group>` pelo nome do grupo de recursos para seu aplicativo de funções. 

Você verá `FUNCTIONS_EXTENSION_VERSION` na seguinte saída, que foi truncada para maior clareza:

```output
[
  {
    "name": "FUNCTIONS_EXTENSION_VERSION",
    "slotSetting": false,
    "value": "~2"
  },
  {
    "name": "FUNCTIONS_WORKER_RUNTIME",
    "slotSetting": false,
    "value": "dotnet"
  },
  
  ...
  
  {
    "name": "WEBSITE_NODE_DEFAULT_VERSION",
    "slotSetting": false,
    "value": "8.11.1"
  }
]
```

É possível atualizar a configuração `FUNCTIONS_EXTENSION_VERSION` o aplicativo de funções com o comando [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings#set).

```azurecli-interactive
az functionapp config appsettings set --name <function_app> \
--resource-group <my_resource_group> \
--settings FUNCTIONS_EXTENSION_VERSION=<version>
```

Substitua `<function_app>` pelo nome do aplicativo de funções. Além disso, substitua `<my_resource_group>` pelo nome do grupo de recursos para seu aplicativo de funções. Substitua também `<version>` por uma versão de tempo de execução 1.x válida ou `~2` para a versão 2.x.

Execute esse comando a partir do [Azure Cloud Shell](../cloud-shell/overview.md) escolhendo **Experimentar** no exemplo de código anterior. Você também pode usar a [CLI do Azure localmente](/cli/azure/install-azure-cli) para executar esse comando após a execução de [az login](/cli/azure/reference-index#az-login) para entrar.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Direcionar para o tempo de execução 2.0 em seu ambiente de desenvolvimento local](functions-run-local.md)

> [!div class="nextstepaction"]
> [Consulte as notas de versão para versões de tempo de execução](https://github.com/Azure/azure-webjobs-sdk-script/releases)
