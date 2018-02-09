---
title: "Como direcionar para versões de tempo de execução do Azure Functions"
description: "O Azure Functions é compatível com várias versões do tempo de execução. Saiba como especificar a versão de tempo de execução de um aplicativo de funções hospedado no Azure."
services: functions
documentationcenter: 
author: ggailey777
manager: cfowler
editor: 
ms.service: functions
ms.workload: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2018
ms.author: glenga
ms.openlocfilehash: c87455f43c6e580c52b3f772a1cbe0c1ac9c9f5b
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/01/2018
---
# <a name="how-to-target-azure-functions-runtime-versions"></a>Como direcionar para versões de tempo de execução do Azure Functions

Uma execução do aplicativo de funções em uma versão específica do tempo de execução do Azure Functions. Há duas versões principais: [1.x e 2.x](functions-versions.md). Este artigo explica como configurar um aplicativo de funções no Azure para executar a versão que você escolher. Para obter informações sobre como configurar um ambiente de desenvolvimento local para uma versão específica, consulte [Código e teste local do Azure Functions](functions-run-local.md).

>[!IMPORTANT]   
> O tempo de execução 2.0 do Azure Functions está em versão prévia e, no momento, nem todos os recursos do Azure Functions têm suporte. Para obter mais informações, consulte [Visão geral de versões de tempo de execução do Azure Functions](functions-versions.md).

## <a name="automatic-and-manual-version-updates"></a>Atualizações de versão automática e manual

O Functions permite que você direcione uma versão específica do tempo de execução usando a configuração de aplicativo `FUNCTIONS_EXTENSION_VERSION` em um aplicativo de funções. O aplicativo de funções é mantido na versão principal especificada até que você escolha explicitamente mudar para uma nova versão.

Se você especificar apenas a versão principal ("~1" para 1.x ou "beta" para 2.x), o aplicativo de função será atualizado automaticamente para novas versões secundárias do tempo de execução quando elas estiverem disponíveis. Novas versões secundárias não introduzem alterações consideráveis. Se você especificar uma versão secundária (por exemplo, "1.0.11360"), o aplicativo de função será mantido nessa versão até você alterá-la explicitamente. 

Quando uma nova versão está disponível publicamente, um prompt no portal oferece a possibilidade de atualizar para essa versão. Depois de mudar para uma nova versão, você sempre poderá usar a configuração de aplicativo `FUNCTIONS_EXTENSION_VERSION` para voltar para uma versão anterior do tempo de execução.

Uma alteração na versão do tempo de execução faz com que seu aplicativo de função reinicie.

Os valores que você pode definir na configuração de aplicativo `FUNCTIONS_EXTENSION_VERSION` para habilitar as atualizações automáticas no momento são "~1" para o tempo de execução 1.x e "beta" para 2.x.

## <a name="view-the-current-runtime-version"></a>Exibir a versão de tempo de execução atual

Use o procedimento a seguir para exibir a versão de tempo de execução usada no momento por um aplicativo de funções. 

1. No [Portal do Azure](https://portal.azure.com), navegue até o aplicativo de funções e, em **Recursos Configurados**, escolha **Configurações do aplicativo de funções**. 

    ![Selecionar as configurações do aplicativo de funções](./media/functions-versions/add-update-app-setting.png)

2. Na guia **Configurações do aplicativo de funções**, localize a **Versão de tempo de execução**. Observe a versão de tempo de execução específica e a versão principal solicitada. No exemplo a seguir, a configuração do aplicativo FUNÇÕES\_EXTENSÃO\_VERSÃO está definida como `~1`.
 
   ![Selecionar as configurações do aplicativo de funções](./media/functions-versions/function-app-view-version.png)

## <a name="target-a-version-using-the-portal"></a>Direcione uma versão usando o portal

Quando você precisar direcionar para uma versão diferente da versão principal atual ou da versão 2.0, defina a configuração de aplicativo `FUNCTIONS_EXTENSION_VERSION`.

1. No [Portal do Azure](https://portal.azure.com), navegue até seu aplicativo de funções e, em **Recursos Configurados**, escolha **Configurações de aplicativo**.

    ![Selecionar as configurações do aplicativo de funções](./media/functions-versions/add-update-app-setting1a.png)

2. Na guia **Configurações de aplicativo**, localize a configuração `FUNCTIONS_EXTENSION_VERSION` e altere o valor para uma versão válida do tempo de execução 1.x, ou `beta` para a versão 2.0. Um til com a versão principal significa usar a versão mais recente da versão principal (por exemplo, "~1"). 

    ![Definir a configuração do aplicativo de funções](./media/functions-versions/add-update-app-setting2.png)

3. Clique em **Salvar** para salvar a atualização da configuração de aplicativo. 

## <a name="target-a-version-using-azure-cli"></a>Direcionar para uma versão usando a CLI do Azure

 Você também pode definir a `FUNCTIONS_EXTENSION_VERSION` na CLI do Azure. Usando a CLI do Azure, atualize a configuração de aplicativo no aplicativo de funções com o comando [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings#set).

```azurecli-interactive
az functionapp config appsettings set --name <function_app> \
--resource-group <my_resource_group> \
--settings FUNCTIONS_EXTENSION_VERSION=<version>
```
Nesse código, substitua `<function_app>` pelo nome de seu aplicativo de funções. Substitua também `<my_resource_group>` pelo nome do grupo de recursos para seu aplicativo de funções. Substitua `<version>` por uma versão válida do tempo de execução 1.x, ou `beta` para a versão 2.0. 

Execute esse comando a partir do [Azure Cloud Shell](../cloud-shell/overview.md) escolhendo **Experimentar** no exemplo de código anterior. Você também pode usar a [CLI do Azure localmente](/cli/azure/install-azure-cli) para executar esse comando após a execução de [az login](/cli/azure#az_login) para entrar.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Direcionar para o tempo de execução 2.0 em seu ambiente de desenvolvimento local](functions-run-local.md)

> [!div class="nextstepaction"]
> [Consulte as notas de versão para versões de tempo de execução](https://github.com/Azure/azure-webjobs-sdk-script/releases)