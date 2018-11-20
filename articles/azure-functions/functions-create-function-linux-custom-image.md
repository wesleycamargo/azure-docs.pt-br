---
title: Criar uma função no Linux usando uma imagem personalizada (versão prévia) | Microsoft Docs
description: Saiba como criar Azure Functions em execução em uma imagem personalizada do Linux.
services: functions
keywords: ''
author: ggailey777
ms.author: glenga
ms.date: 10/19/2018
ms.topic: tutorial
ms.service: azure-functions
ms.custom: mvc
ms.devlang: azure-cli
manager: jeconnoc
ms.openlocfilehash: de15d1f8c268e80ac1659c53a141ec39cc6d3cb8
ms.sourcegitcommit: 6b7c8b44361e87d18dba8af2da306666c41b9396
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/12/2018
ms.locfileid: "51564802"
---
# <a name="create-a-function-on-linux-using-a-custom-image-preview"></a>Criar uma função no Linux usando uma imagem personalizada (versão prévia)

O Azure Functions permite hospedar suas funções no Linux em seu próprio contêiner personalizado. Você também pode [hospedar em um contêiner padrão do Serviço de Aplicativo do Azure](functions-create-first-azure-function-azure-cli-linux.md). Esta funcionalidade encontra-se atualmente em versão prévia e exige [o tempo de execução Functions 2.0](functions-versions.md).

Neste tutorial, você aprenderá a implantar suas funções no Azure como uma imagem personalizada do Docker. Esse padrão será útil quando você precisar personalizar a imagem interna de contêiner do Serviço de Aplicativo. Convém usar uma imagem personalizada quando suas funções precisarem de uma versão de idioma específico, ou exigirem uma configuração ou dependência específica que não é fornecida na imagem interna.

Este tutorial explica como usar o Azure Functions Core Tools para criar uma função em uma imagem personalizada do Linux. Publique essa imagem em um aplicativo de funções no Azure, que foi criado usando a CLI do Azure.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Crie um aplicativo de funções e o Dockerfile usando o Core Tools.
> * Compile uma imagem personalizada usando o Docker.
> * Publique uma imagem personalizada em um registro de contêiner.
> * Criar uma conta do Armazenamento do Azure.
> * Crie um Plano do Serviço de Aplicativo do Linux.
> * Implante um aplicativo de funções do Hub do Docker.
> * Adicione configurações de aplicativo ao aplicativo de funções.

As etapas a seguir têm suporte em um computador Mac, Windows ou Linux.  

## <a name="prerequisites"></a>Pré-requisitos

Antes de executar este exemplo, você deve ter o seguinte:

* Instale a [versão do Azure Core Tools 2.x](functions-run-local.md#v2).

* Instale a [CLI do Azure]( /cli/azure/install-azure-cli). Este artigo requer a CLI do Azure versão 2.0 ou posterior. Execute `az --version` descobrir a versão que você tem.  
Você também pode usar o [Azure Cloud Shell](https://shell.azure.com/bash).

* Uma assinatura ativa do Azure.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-the-local-function-app-project"></a>Criar o projeto de aplicativo de funções local

Execute o comando a seguir na linha de comando para criar um projeto de aplicativo de funções na pasta `MyFunctionProj` do diretório local atual.

```bash
func init MyFunctionProj --docker
```

Quando você inclui a opção `--docker`, um dockerfile é gerada para o projeto. Esse arquivo é usado para criar um contêiner personalizado no qual executar o projeto. A imagem base usada depende da linguagem do tempo de execução do trabalho escolhida.  

Quando solicitado, escolha um tempo de execução do trabalho com as seguintes linguagens:

* `dotnet`: cria um projeto de biblioteca de classes do .NET (.csproj).
* `node`: cria um projeto de JavaScript.

Quando o comando for executado, você verá algo parecido com a seguinte saída:

```output
Writing .gitignore
Writing host.json
Writing local.settings.json
Writing Dockerfile
```

Use o comando a seguir para navegar até a nova pasta do projeto `MyFunctionProj`.

```bash
cd MyFunctionProj
```

[!INCLUDE [functions-create-function-core-tools](../../includes/functions-create-function-core-tools.md)]

[!INCLUDE [functions-update-function-code](../../includes/functions-update-function-code.md)]

[!INCLUDE [functions-run-function-test-local](../../includes/functions-run-function-test-local.md)]

## <a name="build-the-image-from-the-docker-file"></a>Criar a imagem com base no arquivo do Docker

Examine o _Dockerfile_ na pasta raiz do projeto. Este arquivo descreve o ambiente necessário para executar o aplicativo de funções no Linux. O exemplo a seguir é um Dockerfile que cria um contêiner que executa um aplicativo de funções no tempo de execução do trabalho em JavaScript (Node.js): 

```docker
FROM mcr.microsoft.com/azure-functions/node:2.0

ENV AzureWebJobsScriptRoot=/home/site/wwwroot
COPY . /home/site/wwwroot
```

> [!NOTE]
> Ao hospedar uma imagem em um registro de contêiner privado, você deve adicionar as configurações de conexão ao aplicativo de funções usando variáveis **ENV** no Dockerfile. Como este tutorial não pode garantir o seu uso de um registro privado, as configurações de conexão são [adicionadas após a implantação usando a CLI do Azure](#configure-the-function-app) como uma prática recomendada de segurança.

### <a name="run-the-build-command"></a>Execute o comando `build`
Na pasta raiz, execute o comando [docker build](https://docs.docker.com/engine/reference/commandline/build/) e forneça um nome, `mydockerimage`, e uma marca, `v1.0.0`. Substitua `<docker-id>` pela ID da conta do Hub do Docker. Esse comando compila a imagem do Docker para o contêiner.

```bash
docker build --tag <docker-id>/mydockerimage:v1.0.0 .
```

Quando o comando for executado, você verá algo parecido com a seguinte saída, que nesse caso é para um tempo de execução do trabalho em JavaScript:

```bash
Sending build context to Docker daemon  17.41kB
Step 1/3 : FROM mcr.microsoft.com/azure-functions/node:2.0
2.0: Pulling from azure-functions/node
802b00ed6f79: Pull complete
44580ea7a636: Pull complete
73eebe8d57f9: Pull complete
3d82a67477c2: Pull complete
8bd51cd50290: Pull complete
7bd755353966: Pull complete
Digest: sha256:480e969821e9befe7c61dda353f63298f2c4b109e13032df5518e92540ea1d08
Status: Downloaded newer image for mcr.microsoft.com/azure-functions/node:2.0
 ---> 7c71671b838f
Step 2/3 : ENV AzureWebJobsScriptRoot=/home/site/wwwroot
 ---> Running in ed1e5809f0b7
Removing intermediate container ed1e5809f0b7
 ---> 39d9c341368a
Step 3/3 : COPY . /home/site/wwwroot
 ---> 5e196215935a
Successfully built 5e196215935a
Successfully tagged <docker-id>/mydockerimage:v1.0.0
```

### <a name="test-the-image-locally"></a>Teste a imagem localmente
Verifique se a imagem criada funciona executando a imagem do Docker em um contêiner local. Execute o comando [docker run](https://docs.docker.com/engine/reference/commandline/run/) e envie o nome e a marcação da imagem para ele. Lembre-se de especificar a porta usando o argumento `-p`.

```bash
docker run -p 8080:80 -it <docker-ID>/mydockerimage:v1.0.0
```

Com a imagem personalizada em execução em um contêiner local do Docker, verifique se o aplicativo de funções e o contêiner estão funcionando corretamente navegando até <http://localhost:8080>.

![Teste o aplicativo de funções localmente.](./media/functions-create-function-linux-custom-image/run-image-local-success.png)

Opcionalmente, você pode testar a função novamente, desta vez no contêiner local, usando a seguinte URL:

`http://localhost:8080/api/myhttptrigger?name=<yourname>`

Depois de verificar o aplicativo de funções no contêiner, interrompa a execução. Agora, você pode enviar por push a imagem personalizada à sua conta do Hub do Docker.

## <a name="push-the-custom-image-to-docker-hub"></a>Envie por push a imagem personalizada ao Hub do Docker

Um Registro é um aplicativo que hospeda imagens e fornece a imagem de serviços e serviços de contêiner. Para compartilhar sua imagem, você deve enviá-la por push para um registro. O Hub do Docker é um Registro para imagens do Docker que permite hospedar seus próprios repositórios públicos ou particulares.

Antes de enviar uma imagem por push, você deve entrar no Hub do Docker usando o comando [docker login](https://docs.docker.com/engine/reference/commandline/login/). Substitua `<docker-id>` pelo nome de sua conta e digite sua senha no console do prompt. Para obter outras opções de senha de Hub do Docker, confira a [documentação de comandos de logon do docker](https://docs.docker.com/engine/reference/commandline/login/).

```bash
docker login --username <docker-id>
```

Uma mensagem “Logon com êxito” confirma que você está conectado. Depois de conectado, envie a imagem por push para o Hub do Docker usando o comando [docker push](https://docs.docker.com/engine/reference/commandline/push/).

```bash
docker push <docker-id>/mydockerimage:v1.0.0
```

Verifique se o push foi bem-sucedido examinando o resultado do comando.

```bash
The push refers to a repository [docker.io/<docker-id>/mydockerimage:v1.0.0]
24d81eb139bf: Pushed
fd9e998161c9: Mounted from <docker-id>/mydockerimage
e7796c35add2: Mounted from <docker-id>/mydockerimage
ae9a05b85848: Mounted from <docker-id>/mydockerimage
45c86e20670d: Mounted from <docker-id>/mydockerimage
v1.0.0: digest: sha256:be080d80770df71234eb893fbe4d... size: 1796
```

Agora, você pode usar essa imagem como a origem de implantação para um novo aplicativo de funções no Azure.

[!INCLUDE [functions-create-resource-group](../../includes/functions-create-resource-group.md)]

[!INCLUDE [functions-create-storage-account](../../includes/functions-create-storage-account.md)]

## <a name="create-a-linux-app-service-plan"></a>Criar um Plano do Serviço de Aplicativo do Linux

No momento, não há suporte para a hospedagem no Linux do Functions em planos de consumo. Você precisa hospedar aplicativos de contêiner do Linux em um Plano do Serviço de Aplicativo do Linux. Para saber mais sobre hospedagem, confira [Comparação de planos de hospedagem do Azure Functions](functions-scale.md).

[!INCLUDE [app-service-plan-no-h](../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

## <a name="create-and-deploy-the-custom-image"></a>Criar e implantar a imagem personalizada

O aplicativo de funções hospeda a execução de suas funções. Crie um aplicativo de funções a partir de uma imagem do Hub do Docker usando o comando [az functionapp create](/cli/azure/functionapp#az-functionapp-create).

No comando a seguir, substitua um nome de aplicativo de funções exclusivo quando você vir o espaço reservado `<app_name>` e o nome da conta de armazenamento por `<storage_name>`. O `<app_name>` é usado como domínio DNS padrão para o aplicativo de funções, portanto, o nome deve ser exclusivo entre todos os aplicativos no Azure. Como antes, `<docker-id>` é o nome de conta do Docker.

```azurecli-interactive
az functionapp create --name <app_name> --storage-account  <storage_name>  --resource-group myResourceGroup \
--plan myAppServicePlan --deployment-container-image-name <docker-id>/mydockerimage:v1.0.0
```

Depois que o aplicativo de funções for criado, a CLI do Azure mostrará informações semelhantes ao exemplo a seguir:

```json
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
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

O parâmetro _deployment-container-image-name_ indica a imagem hospedada no Hub do Docker a ser usada para criar o aplicativo de funções.

## <a name="configure-the-function-app"></a>Configurar o aplicativo de funções

A função precisa da cadeia de conexão para se conectar à conta de armazenamento padrão. Quando você estiver publicando sua imagem personalizada em uma conta de contêiner privado, defina essas configurações de aplicativo como variáveis de ambiente no Dockerfile usando a [instrução ENV](https://docs.docker.com/engine/reference/builder/#env), ou algo semelhante.

Nesse caso, `<storage_account>` é o nome da conta de armazenamento criada. Obtenha a cadeia de conexão com o comando [az storage account show-connection-string](/cli/azure/storage/account#show-connection-string). Adicione essas configurações de aplicativo ao aplicativo de funções com o comando [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set).

```azurecli-interactive
storageConnectionString=$(az storage account show-connection-string \
--resource-group myResourceGroup --name <storage_account> \
--query connectionString --output tsv)

az functionapp config appsettings set --name <function_app> \
--resource-group myResourceGroup \
--settings AzureWebJobsDashboard=$storageConnectionString \
AzureWebJobsStorage=$storageConnectionString
```

> [!NOTE]
> Se o contêiner for privado, você precisaria definir as seguintes configurações de aplicativo também  
> - DOCKER_REGISTRY_SERVER_USERNAME  
> - DOCKER_REGISTRY_SERVER_PASSWORD  
>
> Você terá que parar e iniciar o aplicativo de função para que esses valores sejam coletados

Agora você pode testar suas funções em execução no Linux no Azure.

[!INCLUDE [functions-test-function-code](../../includes/functions-test-function-code.md)]

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu como:

> [!div class="checklist"]
> * Crie um aplicativo de funções e o Dockerfile usando o Core Tools.
> * Compile uma imagem personalizada usando o Docker.
> * Publique uma imagem personalizada em um registro de contêiner.
> * Criar uma conta do Armazenamento do Azure.
> * Crie um Plano do Serviço de Aplicativo do Linux.
> * Implante um aplicativo de funções do Hub do Docker.
> * Adicione configurações de aplicativo ao aplicativo de funções.

Saiba como habilitar a funcionalidade de integração contínua incorporada à plataforma central de Serviço de Aplicativo. É possível configurar seu aplicativo de funções para que o contêiner seja reimplantado quando você atualizar a imagem no Hub do Docker.

> [!div class="nextstepaction"] 
> [Implantação contínua com o Aplicativo Web para Contêineres](../app-service/containers/app-service-linux-ci-cd.md)
