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
ms.date: 11/21/2017
ms.author: glenga
ms.openlocfilehash: 588437af80ecf60b7c4b24dbf6bccc67fc33da7a
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/29/2017
---
# <a name="how-to-target-azure-functions-runtime-versions"></a>Como direcionar para versões de tempo de execução do Azure Functions

Uma execução do aplicativo de funções em uma versão específica do tempo de execução do Azure Functions. Há duas versões principais: 1.x e 2.x. Este artigo explica como escolher qual versão principal a ser usada e como configurar um aplicativo de funções no Azure para executar a versão que você escolher. Para obter informações sobre como configurar um ambiente de desenvolvimento local para uma versão específica, consulte [Código e teste local do Azure Functions](functions-run-local.md).

## <a name="differences-between-runtime-1x-and-2x"></a>Diferenças entre o tempo de execução 1.x e 2.x

> [!IMPORTANT] 
> O tempo de execução 1.x é a única versão aprovada para uso em produção.

| Tempo de execução | Status |
|---------|---------|
|1.x|Disponível de forma geral (GA)|
|2. x|Visualização|

As seções a seguir explicam as diferenças no suporte de desenvolvimento de linguagens, associações e multiplataforma.

### <a name="languages"></a>Linguagens

A tabela a seguir indica quais linguagens de programação têm suporte em cada versão de tempo de execução.

[!INCLUDE [functions-supported-languages](../../includes/functions-supported-languages.md)]

Para obter mais informações, consulte [Linguagens com suporte](supported-languages.md).

### <a name="bindings"></a>Associações 

O Tempo de execução 2.x permite criar [extensões de associação](https://github.com/Azure/azure-webjobs-sdk-extensions/wiki/Binding-Extensions-Overview) personalizadas. Associações internas que usam esse modelo de extensibilidade estão disponíveis apenas no 2.x; entre as primeiras delas, estão as [associações do Microsoft Graph](functions-bindings-microsoft-graph.md).

[!INCLUDE [Full bindings table](../../includes/functions-bindings.md)]

Para obter informações sobre o suporte a associações e outras lacunas funcionais no 2.x, consulte [Problemas conhecidos do Runtime 2.0](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Azure-Functions-runtime-2.0-known-issues).

### <a name="cross-platform-development"></a>Desenvolvimento multiplataforma

O tempo de execução 1.x é compatível com o desenvolvimento de função apenas no portal ou no Windows. com o 2.x, você pode desenvolver e executar o Azure Functions em Linux ou macOS.

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

## <a name="target-the-version-20-runtime"></a>Direcione a versão 2.0 do tempo de execução

>[!IMPORTANT]   
> O tempo de execução 2.0 do Azure Functions está em versão prévia e, no momento, nem todos os recursos do Azure Functions têm suporte. Para obter mais informações, consulte [Diferenças entre o tempo de execução 1.x e 2.x](#differences-between-runtime-1x-and-2x) anteriormente neste artigo.

Você pode mudar um aplicativo de funções para a versão prévia da versão 2.0 do tempo de execução no portal do Azure. Na guia **Configurações do aplicativo de funções**, escolha **beta** em **Versão de tempo de execução**.  

![Selecionar as configurações do aplicativo de funções](./media/functions-versions/function-app-view-version.png)

Essa configuração é equivalente à definição da configuração de aplicativo `FUNCTIONS_EXTENSION_VERSION` como `beta`. Escolha o botão **~1** para voltar à versão principal com suporte público atual. Você também pode usar a CLI do Azure para atualizar essa configuração de aplicativo. 

## <a name="target-a-version-using-the-portal"></a>Direcione uma versão usando o portal

Quando você precisar direcionar para uma versão diferente da versão principal atual ou da versão 2.0, defina a configuração de aplicativo `FUNCTIONS_EXTENSION_VERSION`.

1. No [Portal do Azure](https://portal.azure.com), navegue até seu aplicativo de funções e, em **Recursos Configurados**, escolha **Configurações de aplicativo**.

    ![Selecionar as configurações do aplicativo de funções](./media/functions-versions/add-update-app-setting1a.png)

2. Na guia **Configurações de aplicativo**, localize a configuração `FUNCTIONS_EXTENSION_VERSION` e altere o valor para uma versão válida do tempo de execução 1.x, ou `beta` para a versão 2.0. 

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