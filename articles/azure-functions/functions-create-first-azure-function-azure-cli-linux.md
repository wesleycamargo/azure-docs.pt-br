---
title: Criar sua primeira função Linux no Azure
description: Aprenda a criar sua primeira função hospedada no Linux no Azure usando o Azure Functions Core Tools e a CLI do Azure.
services: functions
keywords: ''
author: ggailey777
ms.author: glenga
ms.date: 11/28/2018
ms.topic: quickstart
ms.service: azure-functions
ms.custom: mvc
ms.devlang: javascript
manager: jeconnoc
ms.openlocfilehash: 6597e0058176eaa819170a494e4908ab44456360
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/04/2018
ms.locfileid: "52850526"
---
# <a name="create-your-first-function-hosted-on-linux-using-core-tools-and-the-azure-cli-preview"></a>Criar sua primeira função hospedada no Linux usando o Core Tools e a CLI do Azure (versão prévia)

O Azure Functions permite executar seu código em um ambiente Linux [sem servidor](https://azure.microsoft.com/overview/serverless-computing/) sem que seja preciso primeiro criar uma VM ou publicar um aplicativo Web. A hospedagem de Linux encontra-se atualmente em versão prévia e exige [o tempo de execução Functions 2.0](functions-versions.md). Para saber mais sobre as considerações de versão prévia para executar seus aplicativos de funções no Linux, confira [essas Funções no artigo do Linux](https://aka.ms/funclinux).

Este artigo de início rápido explica como usar a CLI do Azure para criar seu primeiro aplicativo de funções em execução no Linux. O código da função é criado localmente e implantado no Azure usando o [Azure Functions Core Tools](functions-run-local.md).

As etapas a seguir têm suporte em um computador Mac, Windows ou Linux. Este artigo mostra como criar funções em JavaScript ou C#. Para saber como criar funções de Python, consulte [Criar sua primeira função de Python usando as Ferramentas do Core e a CLI do Azure (versão prévia)](functions-create-first-function-python.md).

## <a name="prerequisites"></a>Pré-requisitos

Antes de executar este exemplo, você deve ter o seguinte:

+ Instale a [versão do Azure Core Tools 2.x](functions-run-local.md#v2).

+ Instale a [CLI do Azure]( /cli/azure/install-azure-cli). Este artigo requer a CLI do Azure versão 2.0 ou posterior. Execute `az --version` descobrir a versão que você tem. Você também pode usar o [Azure Cloud Shell](https://shell.azure.com/bash).

+ Uma assinatura ativa do Azure.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-the-local-function-app-project"></a>Criar o projeto de aplicativo de funções local

Execute o comando a seguir na linha de comando para criar um projeto de aplicativo de funções na pasta `MyFunctionProj` do diretório local atual. Um repositório GitHub também é criado em `MyFunctionProj`.

```bash
func init MyFunctionProj
```

Quando solicitado, use as teclas de direção para selecionar um tempo de execução do trabalho entre as seguintes opções de linguagem:

+ `dotnet`: cria um projeto de biblioteca de classes do .NET (.csproj).
+ `node`: cria um projeto de JavaScript.
+ `python`: cria um projeto do Python. Para as funções do Python, confira o [Início rápido do Python](functions-create-first-function-python.md).

Quando o comando for executado, você verá algo parecido com a seguinte saída:

```output
Writing .gitignore
Writing host.json
Writing local.settings.json
Initialized empty Git repository in C:/functions/MyFunctionProj/.git/
```

[!INCLUDE [functions-create-function-core-tools](../../includes/functions-create-function-core-tools.md)]

[!INCLUDE [functions-update-function-code](../../includes/functions-update-function-code.md)]

[!INCLUDE [functions-run-function-test-local](../../includes/functions-run-function-test-local.md)]

[!INCLUDE [functions-create-resource-group](../../includes/functions-create-resource-group.md)]

[!INCLUDE [functions-create-storage-account](../../includes/functions-create-storage-account.md)]

## <a name="create-a-linux-app-service-plan"></a>Criar um Plano do Serviço de Aplicativo do Linux

[!INCLUDE [app-service-plan-no-h](../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

## <a name="create-a-linux-function-app-in-azure"></a>Criar um aplicativo de funções Linux no Azure

Você deve ter um aplicativo de funções para hospedar a execução de suas funções no Linux. O aplicativo de funções fornece um ambiente sem servidor para a execução do código da função. Ele permite que você agrupe funções como uma unidade lógica para facilitar o gerenciamento, a implantação e o compartilhamento de recursos. Crie um aplicativo de funções em execução no Linux usando o comando [az functionapp create](/cli/azure/functionapp#az-functionapp-create).

No comando a seguir, use um nome de aplicativo de funções exclusivo quando vir o espaço reservado `<app_name>` e o nome da conta de armazenamento de `<storage_name>`. O `<app_name>` também é o domínio do DNS padrão para o aplicativo de funções. O nome precisa ser exclusivo em todos os aplicativos no Azure. É possível definir o tempo de execução de `<language>` para seu aplicativo de funções, a partir do `dotnet` (C#), `node` (JavaScript) ou `python`.

```azurecli-interactive
az functionapp create --resource-group myResourceGroup --consumption-plan-location westus --os-type Linux \
--name <app_name> --storage-account  <storage_name> --runtime <language>
```

> [!NOTE]
> Se você tiver um grupo de recursos existente denominado `myResourceGroup` com aplicativos do Serviço de Aplicativo que não sejam Linux, use um grupo de recursos diferente. Você não pode hospedar aplicativos Windows e Linux no mesmo grupo de recursos.  

Depois que o aplicativo de função for criado, você verá a seguinte mensagem:

```output
Your serverless Linux function app 'myfunctionapp' has been successfully created.
To active this function app, publish your app content using Azure Functions Core Tools or the Azure portal.
```

Agora é possível publicar seu projeto no novo aplicativo de funções no Azure.

[!INCLUDE [functions-publish-project](../../includes/functions-publish-project.md)]

[!INCLUDE [functions-test-function-code](../../includes/functions-test-function-code.md)]

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

## <a name="next-steps"></a>Próximas etapas

Este artigo mostrou como hospedar seu aplicativo de funções em um contêiner do Serviço de Aplicativo do Azure padrão. Você também pode hospedar as funções no Linux em seu próprio contêiner personalizado.

> [!div class="nextstepaction"]
> [Criar uma função no Linux usando uma imagem personalizada](functions-create-function-linux-custom-image.md)
