---
title: Criar um aplicativo de funções em um Plano do Serviço de Aplicativo do Azure
description: Saiba como criar um aplicativo de funções que é executado no Linux em um Plano do Serviço de Aplicativo usando a CLI do Azure.
services: functions
keywords: ''
author: ggailey777
ms.author: glenga
ms.date: 11/28/2018
ms.topic: conceptual
ms.service: azure-functions
ms.custom: mvc
ms.devlang: azure-cli
manager: jeconnoc
ms.openlocfilehash: ec7b71c7da19ecefc14696c029e63a074b498ec8
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/04/2019
ms.locfileid: "55696721"
---
# <a name="create-a-function-app-on-linux-in-an-azure-app-service-plan-preview"></a>Criar um aplicativo de funções no Linux em um Plano do Serviço de Aplicativo do Azure (versão prévia)

Azure Functions permite hospedar funções no Linux em um contêiner de Serviço de Aplicativo do Azure padrão. Este artigo explica como usar a CLI do Azure para criar um aplicativo de funções hospedado em Linux no Azure que é executado em um [Plano do Serviço de Aplicativo](functions-scale.md#app-service-plan). Você também pode [usar seu próprio contêiner](functions-create-function-linux-custom-image.md). A hospedagem no Linux está atualmente em versão prévia.

Em um Plano do Serviço de Aplicativo, você é responsável por dimensionar seu aplicativo de funções. Para tirar proveito dos recursos do Azure Functions sem servidor, também é possível hospedar as funções no Linux em um [Plano de consumo](functions-scale.md#consumption-plan).

Você pode seguir as etapas abaixo usando um computador Mac, Windows ou Linux.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este início rápido, você precisa de:

+ Uma assinatura ativa do Azure.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se você optar por instalar e usar a CLI localmente, este tópico exigirá a CLI do Azure versão 2.0.21 ou posterior. Execute `az --version` descobrir a versão que você tem. Se você precisa instalar ou atualizar, consulte [Instalar a CLI 2.0 do Azure]( /cli/azure/install-azure-cli). 

[!INCLUDE [functions-create-resource-group](../../includes/functions-create-resource-group.md)]

[!INCLUDE [functions-create-storage-account](../../includes/functions-create-storage-account.md)]

## <a name="create-a-linux-app-service-plan"></a>Criar um Plano do Serviço de Aplicativo do Linux

[!INCLUDE [app-service-plan-no-h](../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

## <a name="create-a-function-app-on-linux"></a>Criar um aplicativo de funções no Linux

Você deve ter um aplicativo de funções para hospedar a execução de suas funções no Linux. O aplicativo de funções fornece um ambiente para execução do seu código de função. Ele permite que você agrupe funções como uma unidade lógica para facilitar o gerenciamento, a implantação e o compartilhamento de recursos. Crie um aplicativo de funções com um plano de serviço de aplicativo do Linux ao usar o comando [az functionapp create](/cli/azure/functionapp#az-functionapp-create).

No comando a seguir, substitua um nome de aplicativo de funções exclusivo quando você vir o espaço reservado `<app_name>` e o nome da conta de armazenamento por `<storage_name>`. O `<app_name>` é usado como domínio DNS padrão para o aplicativo de funções, portanto, o nome deve ser exclusivo entre todos os aplicativos no Azure. É possível definir o tempo de execução de `<language>` para seu aplicativo de funções, a partir do `dotnet` (C#), `node` (JavaScript) ou `python`.

```azurecli-interactive
az functionapp create --resource-group myResourceGroup --plan myAppServicePlan \
--name <app_name> --storage-account  <storage_name> --runtime <language>
```

Depois que o aplicativo de funções for criado e implantado, a CLI do Azure mostrará informações semelhantes ao exemplo a seguir:

```json
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 1536,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "quickstart.azurewebsites.net",
  "enabled": true,
  "enabledHostNames": [
    "quickstart.azurewebsites.net",
    "quickstart.scm.azurewebsites.net"
  ],
   ....
    // Remaining output has been truncated for readability.
}
```

Por `myAppServicePlan` ser um plano de Linux, a imagem do docker interna é usada para criar o contêiner que executa a função de aplicativo no Linux.

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources-simple.md)]

## <a name="next-steps"></a>Próximas etapas

Este artigo mostra como criar um aplicativo de funções hospedado em Linux no Azure. Agora você pode [implantar um projeto de função](https://docs.microsoft.com/cli/azure/functionapp/deployment/source?view=azure-cli-latest) nesse aplicativo de funções. É possível usar o Azure Functions Core Tools para [criar um projeto de funções](functions-run-local.md) no computador local e implantá-lo em seu novo aplicativo de funções do Linux.  

> [!div class="nextstepaction"] 
> [Codificar e testar o Azure Functions localmente](functions-run-local.md)
