---
title: "Criar sua primeira função no Linux na CLI do Azure (versão prévia) | Microsoft Docs"
description: "Aprenda a criar sua primeira Função do Azure para a execução em uma imagem padrão linux usando a CLI do Azure."
services: functions
keywords: 
author: ggailey777
ms.author: glenga
ms.date: 11/15/2017
ms.topic: quickstart
ms.service: functions
ms.custom: mvc
ms.devlang: azure-cli
manager: cfowler
ms.openlocfilehash: 49931155339660fc7a0a39f5b60dc9443374b8b0
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/01/2018
---
# <a name="create-your-first-function-running-on-linux-using-the-azure-cli-preview"></a>Criar sua primeira função em execução no Linux usando a CLI do Azure (visualização)

Azure Functions permite hospedar funções no Linux em um contêiner de Serviço de Aplicativo do Azure padrão. Você também pode [usar seu próprio contêiner](functions-create-function-linux-custom-image.md). Esta é uma versão prévia da funcionalidade e exige [o tempo de execução Functions 2.0](functions-versions.md), que também está em versão prévia.

Este tópico de início rápido orienta como usar Azure Functions com a CLI do Azure para criar seu primeiro aplicativo de função no Linux hospedado no contêiner de Serviço de Aplicativo padrão. O código de função em si é implantado para uma imagem de um repositório de exemplo do GitHub.    

As etapas a seguir têm suporte em um computador Mac, Windows ou Linux. 

## <a name="prerequisites"></a>pré-requisitos 

Para concluir este início rápido, você precisa de:

+ Uma assinatura ativa do Azure.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se você optar por instalar e usar a CLI localmente, este tópico exigirá a CLI do Azure versão 2.0.21 ou posterior. Execute `az --version` descobrir a versão que você tem. Se você precisa instalar ou atualizar, consulte [Instalar a CLI 2.0 do Azure]( /cli/azure/install-azure-cli). 

[!INCLUDE [functions-create-resource-group](../../includes/functions-create-resource-group.md)]

[!INCLUDE [functions-create-storage-account](../../includes/functions-create-storage-account.md)]

## <a name="create-a-linux-app-service-plan"></a>Criar um Plano do Serviço de Aplicativo do Linux

Para funções de hospedagem de Linux atualmente só tem suporte em um plano de serviço de aplicativo. Ainda não há suporte para a hospedagem de plano de consumo. Confira [comparação de planos de hospedagem do Azure Functions](functions-scale.md) para saber mais sobre planos de hospedagem. 

[!INCLUDE [app-service-plan-no-h](../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

## <a name="create-a-function-app-on-linux"></a>Criar um aplicativo de funções no Linux

Você deve ter um aplicativo de funções para hospedar a execução de suas funções no Linux. O aplicativo de funções fornece um ambiente para execução do seu código de função. Ele permite que você agrupe funções como uma unidade lógica para facilitar o gerenciamento, a implantação e o compartilhamento de recursos. Crie um aplicativo de funções com um plano de serviço de aplicativo do Linux ao usar o comando [az functionapp create](/cli/azure/functionapp#az_functionapp_create). 

No comando a seguir, substitua um nome de aplicativo de funções exclusivo quando você vir o espaço reservado `<app_name>` e o nome da conta de armazenamento por `<storage_name>`. O `<app_name>` é usado como domínio DNS padrão para o aplicativo de funções, portanto, o nome deve ser exclusivo entre todos os aplicativos no Azure. O parâmetro _deployment-source-url_ é um repositório de exemplo no GitHub que contém uma função de gatilho HTTP "Olá, Mundo".

```azurecli-interactive
az functionapp create --name <app_name> --storage-account  <storage_name>  --resource-group myResourceGroup \
--plan myAppServicePlan --deployment-source-url https://github.com/Azure-Samples/functions-quickstart-linux
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

>[!NOTE]  
>Atualmente, o repositório de exemplo inclui dois arquivos de script, [deploy.sh](https://github.com/Azure-Samples/functions-quickstart-linux/blob/master/deploy.sh) e [.deployment](https://github.com/Azure-Samples/functions-quickstart-linux/blob/master/.deployment). O arquivo .deployment informa o processo de implantação para usar deploy.sh como o [script de implantação personalizada](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script). Na versão de visualização atual, os scripts são necessários para implantar o aplicativo de função em uma imagem do Linux.  

[!INCLUDE [functions-test-function-code](../../includes/functions-test-function-code.md)]

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

[!INCLUDE [functions-quickstart-next-steps-cli](../../includes/functions-quickstart-next-steps-cli.md)]
