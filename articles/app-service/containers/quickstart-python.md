---
title: Implantar um aplicativo Python no Aplicativo Web para Contêineres do Azure
description: Como implantar uma imagem do Docker executando um aplicativo Python para o Aplicativo Web para Contêineres do Azure.
keywords: serviço de aplicativo do azure, aplicativo web, python, docker, contêiner
services: app-service
author: cephalin
manager: jeconnoc
ms.service: app-service
ms.devlang: python
ms.topic: quickstart
ms.date: 07/13/2018
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: 5686266774603413fc255c53a0d1ad30f9baa8eb
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/20/2018
ms.locfileid: "39173853"
---
# <a name="deploy-a-python-web-app-in-web-app-for-containers"></a>Implantar um aplicativo Web Python no Aplicativo Web para Contêineres

O [Serviço de Aplicativo no Linux](app-service-linux-intro.md) fornece um serviço de hospedagem na Web altamente escalonável e com aplicação automática de patches usando o sistema operacional Linux. Este guia de início rápido mostra como criar um aplicativo Web e implantar um aplicativo Flask nele usando uma imagem personalizada do Hub do Docker. Crie o aplicativo Web usando a [CLI do Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli).

![Aplicativo de exemplo em execução no Azure](media/quickstart-python/hello-world-in-browser.png)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial:

* <a href="https://git-scm.com/" target="_blank">Instalar o Git</a>
* <a href="https://www.docker.com/community-edition" target="_blank">Instale o Docker Community Edition</a>
* <a href="https://hub.docker.com/" target="_blank">Inscrever-se em uma conta do Hub do Docker</a>

## <a name="download-the-sample"></a>Baixar o exemplo

Em uma janela de terminal, execute os comandos a seguir para clonar o aplicativo de exemplo no computador local e navegue para o diretório que contém o código de exemplo.

```bash
git clone https://github.com/Azure-Samples/python-docs-hello-world
cd python-docs-hello-world
```

Este repositório contém um aplicativo Flask simples na pasta _/app_ e um _Dockerfile_ que especifica três coisas:

- Use a imagem de base [tiangolo/uwsgi-nginx-flask:python3.6-alpine3.7](https://hub.docker.com/r/tiangolo/uwsgi-nginx-flask/).
- O contêiner deve escutar na porta 8000.
- Copie o diretório `/app` para o diretório do contêiner `/app`.

A configuração segue as [instruções para a imagem base](https://hub.docker.com/r/tiangolo/uwsgi-nginx-flask/).

## <a name="run-the-app-locally"></a>Executar o aplicativo localmente

Execute o aplicativo em um contêiner do Docker.

```bash
docker build --rm -t flask-quickstart .
docker run --rm -it -p 8000:8000 flask-quickstart
```

Abra um navegador da Web e navegue até o aplicativo de exemplo em `http://localhost:8000`.

Você poderá ver a mensagem **Hello World** no aplicativo de exemplo exibido na página.

![Aplicativo de exemplo em execução local](media/quickstart-python/localhost-hello-world-in-browser.png)

Na janela do terminal, pressione **Ctrl+C** para interromper o contêiner.

## <a name="deploy-image-to-docker-hub"></a>Imagem de implantação para o Hub do Docker

Entre em sua conta do Hub do Docker. Siga o prompt para inserir suas credenciais de Hub do Docker.

```bash
docker login
```

Marque sua imagem de marca e envie-a para um novo repositório _público_ na sua conta do Hub do Docker, para um repositório chamado `flask-quickstart`. Substitua *\<dockerhub_id>* com a ID do Hub do Docker.

```bash
docker tag flask-quickstart <dockerhub_id>/flask-quickstart
docker push <dockerhub_id>/flask-quickstart
```

> [!NOTE]
> `docker push` cria um repositório público se o repositório especificado não for encontrado. Este guia de início rápido pressupõe um repositório público no Hub do Docker. Se você preferir enviar por push para um repositório privado, você precisa configurar suas credenciais de Hub do Docker no Serviço de Aplicativo do Azure mais tarde. Consulte [Criar um aplicativo Web](#create-a-web-app).

Depois que o envio por push de imagem for concluído, você está pronto para usá-lo em seu aplicativo Web do Azure.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-linux.md)]

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux.md)]

## <a name="create-a-web-app"></a>Criar um aplicativo Web

Crie um [aplicativo Web](../app-service-web-overview.md) no plano do Serviço de Aplicativo do `myAppServicePlan` com o comando [az webapp create](/cli/azure/webapp?view=azure-cli-latest#az_webapp_create). Substitua  *\<nome do aplicativo >* por um nome de aplicativo globalmente exclusivo e substitua  *\<dockerhub_id >* por sua ID de Hub do Docker.

```azurecli-interactive
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app name> --deployment-container-image-name <dockerhub_id>/flask-quickstart
```

Quando o aplicativo Web for criado, a CLI do Azure mostrará um resultado semelhante ao seguinte exemplo:

```json
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app name>.azurewebsites.net",
  "deploymentLocalGitUrl": "https://<username>@<app name>.scm.azurewebsites.net/<app name>.git",
  "enabled": true,
  < JSON data removed for brevity. >
}
```

Se você carregou para um repositório privado anteriormente, você também precisará configurar as credenciais de Hub do Docker no Serviço de Aplicativo. Para obter mais informações, consulte [Usar uma imagem privada do Hub do Docker](tutorial-custom-docker-image.md#use-a-private-image-from-docker-hub-optional).

### <a name="specify-container-port"></a>Especificar uma porta de contêiner

Conforme especificado no _Dockerfile_, seu contêiner escuta na porta 8000. Para que o Serviço de Aplicativo roteie a solicitação para a porta certa, você precisa definir a configuração do aplicativo *WEBSITES_PORT*.

No Cloud Shell, execute o comando [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az_webapp_config_appsettings_set).


```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group myResourceGroup --settings WEBSITES_PORT=8000
```

## <a name="browse-to-the-app"></a>Navegar até o aplicativo

```bash
http://<app_name>.azurewebsites.net/
```

![Aplicativo de exemplo em execução no Azure](media/quickstart-python/hello-world-in-browser.png)

> [!NOTE]
> O aplicativo Web leva algum tempo para ser iniciado porque a imagem do Hub do Docker tem que ser baixada e executada quando o aplicativo é solicitado pela primeira vez. Se a princípio você vir um erro após um longo tempo, atualize a página.

**Parabéns!** Você implantou uma imagem personalizada do Docker executando um aplicativo Python no Aplicativo Web para Contêineres.

## <a name="update-locally-and-redeploy"></a>Atualizar localmente e reimplantar

Usando um editor de texto local, abra o arquivo `app/main.py` no aplicativo do Python e faça uma pequena alteração no texto próximo à instrução `return`:

```python
return 'Hello, Azure!'
```

Recompile a imagem e envie-a por push ao Hub do Docker novamente.

```bash
docker build --rm -t flask-quickstart .
docker tag flask-quickstart <dockerhub_id>/flask-quickstart
docker push <dockerhub_id>/flask-quickstart
```

No Cloud Shell, reinicie o aplicativo. Reiniciar o aplicativo garante que todas as configurações serão aplicadas e que o último contêiner será extraído do registro.

```azurecli-interactive
az webapp restart --resource-group myResourceGroup --name <app_name>
```

Aguarde cerca de 15 segundos para o Serviço de Aplicativo obter a imagem atualizada. Mude para a janela do navegador aberta na etapa **Navegar até o aplicativo** e atualize a página.

![Aplicativo de exemplo atualizado em execução no Azure](media/quickstart-python/hello-azure-in-browser.png)

## <a name="manage-your-azure-web-app"></a>Gerenciar seu aplicativo Web do Azure

Vá para o [portal do Azure](https://portal.azure.com) para ver o aplicativo Web que você criou.

No menu à esquerda, clique em **Serviço de Aplicativos** e, em seguida, clique no nome do seu aplicativo Web do Azure.

![Navegação do portal para o aplicativo Web do Azure](./media/quickstart-python/app-service-list.png)

Por padrão, o portal mostra a página **Visão geral** do seu aplicativo Web . Esta página fornece uma visão de como está seu aplicativo. Aqui, você também pode executar tarefas básicas de gerenciamento como procurar, parar, iniciar, reiniciar e excluir. As guias no lado esquerdo da página mostram as páginas de configuração diferentes que você pode abrir.

![Página Serviço de Aplicativo no portal do Azure](./media/quickstart-python/app-service-detail.png)

[!INCLUDE [Clean-up section](../../../includes/cli-script-clean-up.md)]

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Python com PostgreSQL](tutorial-docker-python-postgresql-app.md)

> [!div class="nextstepaction"]
> [Usar imagens personalizadas](tutorial-custom-docker-image.md)