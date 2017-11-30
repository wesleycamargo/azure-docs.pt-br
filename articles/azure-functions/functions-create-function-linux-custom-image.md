---
title: "Criar uma função no Linux usando uma imagem personalizada (versão prévia) | Microsoft Docs"
description: "Saiba como criar Azure Functions em execução em uma imagem personalizada do Linux."
services: functions
keywords: 
author: ggailey777
ms.author: glenga
ms.date: 11/15/2017
ms.topic: article
ms.service: functions
ms.custom: mvc
ms.devlang: azure-cli
manager: cfowler
ms.openlocfilehash: 67ee02df2c42ba39c2f186cc95fa886a3d735ed2
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/16/2017
---
# <a name="create-a-function-on-linux-using-a-custom-image-preview"></a>Criar uma função no Linux usando uma imagem personalizada (versão prévia)

O Azure Functions permite hospedar suas funções no Linux em seu próprio contêiner personalizado. Esta funcionalidade está em preview. Você também pode [hospedar em um contêiner padrão do Serviço de Aplicativo do Azure](functions-create-first-azure-function-azure-cli-linux.md).  

Neste tutorial, você aprenderá a implantar um aplicativo de funções como uma imagem personalizada do Docker. Esse padrão será útil quando você precisar personalizar a imagem interna de contêiner do Serviço de Aplicativo. Convém usar uma imagem personalizada quando suas funções precisarem de uma versão de idioma específico, ou exigirem uma configuração ou dependência específica que não é fornecida na imagem interna.

Este tutorial orienta-o sobre como usar o Azure Functions para criar e enviar por push uma imagem personalizada para o Hub do Docker. Depois, você usa essa imagem como a origem de implantação para um aplicativo de funções que é executado no Linux. Use o Docker para compilar e enviar a imagem por push. Você pode usar a CLI do Azure para criar um aplicativo de funções e implantar a imagem do Hub do Docker. 

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Compile uma imagem personalizada usando o Docker.
> * Publique uma imagem personalizada em um registro de contêiner. 
> * Criar uma conta do Armazenamento do Azure. 
> * Crie um Plano do Serviço de Aplicativo do Linux. 
> * Implante um aplicativo de funções do Hub do Docker.
> * Adicione configurações de aplicativo ao aplicativo de funções. 

As etapas a seguir têm suporte em um computador Mac, Windows ou Linux.  

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisará:

* [Git](https://git-scm.com/downloads)
* Uma [assinatura ativa do Azure](https://azure.microsoft.com/pricing/free-trial/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
* [Docker](https://docs.docker.com/get-started/#setup)
* Uma [conta do Hub do Docker](https://docs.docker.com/docker-id/)

[!INCLUDE [Free trial note](../../includes/quickstarts-free-trial-note.md)]

## <a name="download-the-sample"></a>Baixar o exemplo

Em uma janela de terminal, execute o comando a seguir para clonar o repositório de aplicativos de exemplo no computador local e, em seguida, alterar para o diretório que contém o código de exemplo.

```bash
git clone https://github.com/Azure-Samples/functions-linux-custom-image.git --config core.autocrlf=input
cd functions-linux-custom-image
```

## <a name="build-the-image-from-the-docker-file"></a>Criar a imagem com base no arquivo do Docker

Neste repositório do Git, confira o _Dockerfile_. Este arquivo descreve o ambiente necessário para executar o aplicativo de funções no Linux. 

```docker
# Base the image on the built-in Azure Functions Linux image.
FROM microsoft/azure-functions-runtime:2.0.0-jessie
ENV AzureWebJobsScriptRoot=/home/site/wwwroot

# Add files from this repo to the root site folder.
COPY . /home/site/wwwroot 
```
>[!NOTE]
> Ao hospedar uma imagem em um registro de contêiner privado, você deve adicionar as configurações de conexão ao aplicativo de funções usando variáveis **ENV** no Dockerfile. Como este tutorial não pode garantir o seu uso de um registro privado, as configurações de conexão são [adicionadas após a implantação usando a CLI do Azure](#configure-the-function-app) como uma prática recomendada de segurança.   

### <a name="run-the-build-command"></a>Execute o comando de Compilação
Para criar a imagem do Docker, execute o comando `docker build` e forneça um nome, uma `mydockerimage` e uma marcação, `v1.0.0`. Substitua `<docker-id>` pela ID da conta do Hub do Docker.

```bash
docker build --tag <docker-id>/mydockerimage:v1.0.0 .
```

O comando produz um resultado semelhante ao seguinte:

```bash
Sending build context to Docker daemon  169.5kB
Step 1/3 : FROM microsoft/azure-functions-runtime:v2.0.0-jessie
v2.0.0-jessie: Pulling from microsoft/azure-functions-runtime
b178b12f7913: Pull complete
2d9ce077a781: Pull complete
4775d4ba55c8: Pull complete
Digest: sha256:073f45fc167b3b5c6642ef4b3c99064430d6b17507095...
Status: Downloaded newer image for microsoft/azure-functions-runtime:v2.0.0-jessie
 ---> 217799efa500
Step 2/3 : ENV AzureWebJobsScriptRoot /home/site/wwwroot
 ---> Running in 528fa2077d17
 ---> 7cc6323b8ae0
Removing intermediate container 528fa2077d17
Step 3/3 : COPY . /home/site/wwwroot
 ---> 5bdac9878423
Successfully built 5bdac9878423
Successfully tagged ggailey777/mydockerimage:v1.0.0
```

### <a name="test-the-image-locally"></a>Teste a imagem localmente
Verifique se a imagem interna funciona executando a imagem do Docker em um contêiner local. Execute o comando [docker run](https://docs.docker.com/engine/reference/commandline/run/) e envie o nome e a marcação da imagem para ele. Lembre-se de especificar a porta usando o argumento `-p`.

```bash
docker run -p 8080:80 -it <docker-ID>/mydockerimage:v1.0.0
```

Com a imagem personalizada em execução em um contêiner local do Docker, verifique se o aplicativo de funções e o contêiner estão funcionando corretamente navegando até <http://localhost:8080>.

![Teste o aplicativo de funções localmente.](./media/functions-create-function-linux-custom-image/run-image-local-success.png)

Depois de verificar o aplicativo de funções no contêiner, interrompa a execução. Agora, você pode enviar por push a imagem personalizada à sua conta do Hub do Docker.

## <a name="push-the-custom-image-to-docker-hub"></a>Envie por push a imagem personalizada ao Hub do Docker

Um Registro é um aplicativo que hospeda imagens e fornece a imagem de serviços e serviços de contêiner. Para compartilhar sua imagem, você deve enviá-la por push para um Registro. O Hub do Docker é um Registro para imagens do Docker que permite hospedar seus próprios repositórios públicos ou particulares. 

Antes de enviar uma imagem por push, você deve entrar no Hub do Docker usando o comando [docker login](https://docs.docker.com/engine/reference/commandline/login/). Substitua `<docker-id>` pelo nome de sua conta e digite sua senha no console do prompt. Para obter outras opções de senha de Hub do Docker, confira a [documentação de comandos de logon do docker](https://docs.docker.com/engine/reference/commandline/login/).

```bash
docker login --username <docker-id> 
```

Uma mensagem “Logon com êxito” confirma que você está conectado. Depois de conectado, envie a imagem por push para o Hub do Docker usando o comando [docker push](https://docs.docker.com/engine/reference/commandline/push/).

```bash
docker push <docker-id>/mydockerimage:v1.0.0 .
```

Verifique se o push foi bem-sucedido examinando o resultado do comando.

```bash
The push refers to a repository [docker.io/<docker-id>/mydockerimage:v1.0.0]
24d81eb139bf: Pushed
fd9e998161c9: Mounted from microsoft/azure-functions-runtime
e7796c35add2: Mounted from microsoft/azure-functions-runtime
ae9a05b85848: Mounted from microsoft/azure-functions-runtime
45c86e20670d: Mounted from microsoft/azure-functions-runtime
v1.0.0: digest: sha256:be080d80770df71234eb893fbe4d... size: 2422
```
Agora, você pode usar essa imagem como a origem de implantação para um novo aplicativo de funções no Azure. 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se você optar por instalar e usar a CLI localmente, este tópico exigirá a CLI do Azure versão 2.0.21 ou posterior. Execute `az --version` descobrir a versão que você tem. Se você precisa instalar ou atualizar, consulte [Instalar a CLI 2.0 do Azure]( /cli/azure/install-azure-cli). 

[!INCLUDE [functions-create-resource-group](../../includes/functions-create-resource-group.md)]

[!INCLUDE [functions-create-storage-account](../../includes/functions-create-storage-account.md)]

## <a name="create-a-linux-app-service-plan"></a>Criar um Plano do Serviço de Aplicativo do Linux

No momento, não há suporte para a hospedagem no Linux do Functions em planos de consumo. É necessário executar em um Plano de Serviço de Aplicativo do Linux. Para saber mais sobre hospedagem, confira [Comparação de planos de hospedagem do Azure Functions](functions-scale.md). 

[!INCLUDE [app-service-plan-no-h](../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]


## <a name="create-and-deploy-the-custom-image"></a>Criar e implantar a imagem personalizada

O aplicativo de funções hospeda a execução de suas funções. Crie um aplicativo de funções a partir de uma imagem do Hub do Docker usando o comando [az functionapp create](/cli/azure/functionapp#create). 

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

A função precisa da cadeia de conexão para se conectar à conta de armazenamento padrão. Quando você estiver publicando sua imagem personalizada em uma conta de contêiner privado, defina essas configurações de aplicativo como variáveis de ambiente no Dockerfile usando a [instrução ENV](https://docs.docker.com/engine/reference/builder/#env), ou algo equivalente. 

Nesse caso, `<storage_account>` é o nome da conta de armazenamento criada. Obtenha a cadeia de conexão com o comando [az storage account show-connection-string](/cli/azure/storage/account#show-connection-string). Adicione essas configurações de aplicativo ao aplicativo de funções com o comando [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings#set).

```azurecli-interactive
storageConnectionString=$(az storage account show-connection-string \
--resource-group myResourceGroup --name <storage_account> \
--query connectionString --output tsv)

az functionapp config appsettings set --name <function_app> \
--resource-group myResourceGroup \
--settings AzureWebJobsDashboard=$storageConnectionString \
AzureWebJobsStorage=$storageConnectionString
```

Agora você pode testar suas funções em execução no Linux no Azure.

[!INCLUDE [functions-test-function-code](../../includes/functions-test-function-code.md)]

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu como:

> [!div class="checklist"]
> * Compile uma imagem personalizada usando o Docker.
> * Publique uma imagem personalizada em um registro de contêiner. 
> * Criar uma conta do Armazenamento do Azure. 
> * Crie um Plano do Serviço de Aplicativo do Linux. 
> * Implante um aplicativo de funções do Hub do Docker.
> * Adicione configurações de aplicativo ao aplicativo de funções.

Saiba mais sobre como desenvolver localmente Azure Functions usando as Ferramentas Básicas do Azure Functions.

> [!div class="nextstepaction"] 
> [Codificar e testar o Azure Functions localmente](functions-run-local.md)
