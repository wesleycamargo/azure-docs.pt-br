---
title: "Usar uma imagem personalizada do Docker para o Aplicativo Web para Contêineres – Azure | Microsoft Docs"
description: "Como usar uma imagem personalizada do Docker para o Aplicativo Web para Contêineres."
keywords: "serviço de aplicativo do azure, aplicativo web, linux, docker, contêiner"
services: app-service
documentationcenter: 
author: SyntaxC4
manager: SyntaxC4
editor: 
ms.assetid: b97bd4e6-dff0-4976-ac20-d5c109a559a8
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/24/2017
ms.author: cfowler
ms.custom: mvc
ms.openlocfilehash: 2580c2109ce33b1ce99aa491f7d0002edf060693
ms.sourcegitcommit: 0e4491b7fdd9ca4408d5f2d41be42a09164db775
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/14/2017
---
# <a name="use-a-custom-docker-image-for-web-app-for-containers"></a>Usar uma imagem personalizada do Docker para o Aplicativo Web para Contêineres

O [Aplicativo Web para Contêineres](app-service-linux-intro.md) fornece imagens internas do Docker no Linux com suporte para versões específicas, como PHP 7.0 e Node.js 4.5. O Aplicativo Web para Contêineres usa a tecnologia de contêiner do Docker para hospedar imagens internas e imagens personalizadas como uma plataforma como serviço. Neste tutorial, você aprenderá como criar uma imagem personalizada do Docker e implantá-la no Aplicativo Web para Contêineres. Esse padrão é útil quando as imagens internas não incluem a linguagem de sua escolha ou quando seu aplicativo requer uma configuração específica que não é fornecida nas imagens internas.

## <a name="prerequisites"></a>pré-requisitos

Para concluir este tutorial, você precisará:

* [Git](https://git-scm.com/downloads)
* Uma [assinatura ativa do Azure](https://azure.microsoft.com/pricing/free-trial/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
* [Docker](https://docs.docker.com/get-started/#setup)
* Uma [conta do Hub do Docker](https://docs.docker.com/docker-id/)

[!INCLUDE [Free trial note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="download-the-sample"></a>Baixar o exemplo

Em uma janela de terminal, execute o comando a seguir para clonar o repositório de aplicativos de exemplo no computador local e, em seguida, alterar para o diretório que contém o código de exemplo.

```bash
git clone https://github.com/Azure-Samples/docker-django-webapp-linux.git --config core.autocrlf=input
cd docker-django-webapp-linux
```

## <a name="build-the-image-from-the-docker-file"></a>Criar a imagem com base no arquivo do Docker

No repositório Git, confira o _Dockerfile_. Este arquivo descreve o ambiente do Python necessário para executar seu aplicativo. Além disso, a imagem configura um servidor [SSH](https://www.ssh.com/ssh/protocol/) para a comunicação segura entre o contêiner e o host.

```docker
FROM python:3.4

RUN mkdir /code
WORKDIR /code
ADD requirements.txt /code/
RUN pip install -r requirements.txt
ADD . /code/

# ssh
ENV SSH_PASSWD "root:Docker!"
RUN apt-get update \
        && apt-get install -y --no-install-recommends dialog \
        && apt-get update \
    && apt-get install -y --no-install-recommends openssh-server \
    && echo "$SSH_PASSWD" | chpasswd 

COPY sshd_config /etc/ssh/
COPY init.sh /usr/local/bin/
    
RUN chmod u+x /usr/local/bin/init.sh
EXPOSE 8000 2222
#CMD ["python", "/code/manage.py", "runserver", "0.0.0.0:8000"]
ENTRYPOINT ["init.sh"]
```

Para criar a imagem do Docker, execute o comando `docker build` e forneça um nome, uma `mydockerimage` e uma marcação, `v1.0.0`. Substitua `<docker-id>` pela ID da conta do Hub do Docker.

```bash
docker build --tag <docker-id>/mydockerimage:v1.0.0 .
```

O comando produz um resultado semelhante ao seguinte:

```
# The output from the commands in this article has been shortened for brevity.

Sending build context to Docker daemon  5.558MB
Step 1/13 : FROM python:3.4
 ---> 9ff45ddb54e9
Step 2/13 : RUN mkdir /code
 ---> Using cache
 ---> f3f3ac01db0a
Step 3/13 : WORKDIR /code
 ---> Using cache
 ---> 38b32f15b442
.
.
.
Step 13/13 : ENTRYPOINT init.sh
 ---> Running in 5904e4c70043
 ---> e7cf08275692
Removing intermediate container 5904e4c70043
Successfully built e7cf08275692
Successfully tagged cephalin/mydockerimage:v1.0.0
```

Teste se o build funciona executando o contêiner do Docker. Execute o comando [docker run](https://docs.docker.com/engine/reference/commandline/run/) e envie o nome e a marcação da imagem para ele. Lembre-se de especificar a porta usando o argumento `-p`.

```bash
docker run -p 2222:8000 <docker-ID>/mydockerimage:v1.0.0
```

Verifique se o aplicativo Web e o contêiner estão funcionando corretamente, navegando para `http://localhost:2222`.

![Testar o aplicativo Web localmente](./media/app-service-linux-using-custom-docker-image/app-service-linux-browse-local.png)

## <a name="push-the-docker-image-to-docker-hub"></a>Enviar a imagem do Docker por push para o Hub do Docker

Um Registro é um aplicativo que hospeda imagens e fornece a imagem de serviços e serviços de contêiner. Para compartilhar sua imagem, você deve enviá-la por push para um Registro. 

<!-- Depending on your requirements, you may have your docker images in a Public Docker Registry, such as Docker Hub, or a Private Docker Registry such as Azure Container Registry. Select the appropriate tab for your scenario below (your selection will switch multiple tabs on this page). -->

> [!NOTE]
> Enviando por push para um Registro particular do Docker? Consulte as instruções opcionais de como [Enviar uma imagem do Docker por push para um Registro particular](#push-a-docker-image-to-private-registry-optional).

<!--## [Docker Hub](#tab/docker-hub)-->

O Hub do Docker é um Registro para imagens do Docker que permite hospedar seus próprios repositórios públicos ou particulares. Para enviar uma imagem personalizada do Docker por push para o Hub público do Docker, use o comando [docker push](https://docs.docker.com/engine/reference/commandline/push/) e forneça um nome de imagem completo e uma marcação. Um nome de imagem completo e uma marcação são parecidos com a seguinte amostra:

```
<docker-id>/image-name:tag
```

Se você ainda não fez logon no Hub do Docker, faça logon usando o comando [docker login](https://docs.docker.com/engine/reference/commandline/login/) antes de tentar enviar uma imagem por push.

```bash
docker login --username <docker-id> --password <docker-hub-password>
```

Uma mensagem “Logon com êxito” confirma que você está conectado. Depois de conectado, você pode enviar a imagem por push para o Hub do Docker usando o comando [docker push](https://docs.docker.com/engine/reference/commandline/push/).

```bash
docker push <docker-id>/mydockerimage:v1.0.0
```

Verifique se o push foi bem-sucedido examinando o resultado do comando.

```
The push refers to a repository [docker.io/<docker-id>/mydockerimage:v1.0.0]
c33197c3f6d4: Pushed
ccd2c850ee43: Pushed
02dff2853466: Pushed
6ce78153632a: Pushed
efef3f03cc58: Pushed
3439624d77fb: Pushed
3a07adfb35c5: Pushed
2fcec228e1b7: Mounted from library/python
97d2d3bae505: Mounted from library/python
95aadeabf504: Mounted from library/python
b456afdc9996: Mounted from library/python
d752a0310ee4: Mounted from library/python
db64edce4b5b: Mounted from library/python
d5d60fc34309: Mounted from library/python
c01c63c6823d: Mounted from library/python
v1.0.0: digest: sha256:21f2798b20555f4143f2ca0591a43b4f6c8138406041f2d32ec908974feced66 size: 3676
```

<!--
# [Private Registry](#tab/private-registry)

// Move Private Registry instructions here when Tabbed Conceptual bug is fixed

---
-->

[!INCLUDE [Try Cloud Shell](../../../includes/cloud-shell-try-it.md)]

## <a name="deploy-app-to-azure"></a>Implantar o aplicativo no Azure

Hospede aplicativos Linux nativos na nuvem usando os Aplicativos Web do Azure. Para criar um Aplicativo Web para Contêineres, você deve executar os comandos da CLI do Azure que cria um grupo, um plano de serviço e, finalmente, o aplicativo Web em si. 

### <a name="create-a-resource-group"></a>Criar um grupo de recursos

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-no-h.md)] 

### <a name="create-a-linux-app-service-plan"></a>Criar um Plano do Serviço de Aplicativo do Linux

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux-no-h.md)] 

### <a name="create-a-web-app"></a>Criar um aplicativo Web

No Cloud Shell, crie um [aplicativo Web](app-service-linux-intro.md) no plano do Serviço de Aplicativo do `myAppServicePlan` com o comando [az webapp list-runtimes](/cli/azure/webapp?view=azure-cli-latest#az_webapp_create). Não se esqueça de substituir `<app_name>` por um nome de aplicativo exclusivo e <docker-ID> pela ID do Docker.

```azurecli-interactive
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app_name> --deployment-container-image-name <docker-ID>/mydockerimage:v1.0.0
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
  "defaultHostName": "<app_name>.azurewebsites.net",
  "deploymentLocalGitUrl": "https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git",
  "enabled": true,
  < JSON data removed for brevity. >
}
```

### <a name="configure-environment-variables"></a>Configurar variáveis de ambiente

A maioria das imagens do Docker têm variáveis de ambiente que precisam ser configuradas. Se você estiver usando uma imagem do Docker existente criada por outra pessoa, a imagem poderá usar uma porta diferente da 80. Informe o Azure sobre a porta que suas imagens usam por meio da configuração `WEBSITES_PORT` do aplicativo. A página do GitHub que contém a [amostra do Python neste tutorial](https://github.com/Azure-Samples/docker-django-webapp-linux) mostra que você precisa definir `WEBSITES_PORT` para _8000_.

Para definir as configurações de aplicativo, use o comando [az webapp config appsettings set](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az_webapp_config_appsettings_set) no Cloud Shell. As configurações do aplicativo diferenciam maiúsculas de minúsculas e são separadas por espaços.

```azurecli-interactive
az webapp config appsettings set --resource-group myResourceGroup --name <app_name> --settings WEBSITES_PORT=8000
```

<!-- Depending on your requirements, you may have your docker images in a Public Docker Registry, such as Docker Hub, or a Private Docker Registry, such as Azure Container Registry. Select the appropriate tab for your scenario below: -->

> [!NOTE]
> Implantando de um Registro Particular do Docker? Consulte as instruções opcionais sobre como [Configurar o Aplicativo Web para usar o contêiner do Docker de um Registro Particular](#configure-web-app-to-use-docker-container-from-a-private-registry-optional).

<!-- # [Docker Hub](#tab/docker-hub)-->

<!-- # [Private Registry](#tab/private-registry)

// Place Private Registry text back here once Tabbed Conceptual bug is fixed

---
-->

### <a name="test-the-web-app"></a>Testar o aplicativo Web

Verifique se o aplicativo Web funciona procurando por ele (`http://<app_name>azurewebsites.net`). 

![Testar a configuração de porta do aplicativo Web](./media/app-service-linux-using-custom-docker-image/app-service-linux-browse-azure.png)

## <a name="change-web-app-and-redeploy"></a>Alterar o aplicativo Web e reimplantá-lo

No seu repositório Git local, abra app/templates/app/index.html. Localize o primeiro elemento HTML e altere-o para:

```python
<nav class="navbar navbar-inverse navbar-fixed-top">
    <div class="container">
      <div class="navbar-header">         
        <a class="navbar-brand" href="#">Azure App Service - Updated Here!</a>       
      </div>            
    </div>
  </nav> 
```

Depois de modificar o arquivo do Python e salvá-lo, recrie e envie por push a nova imagem do Docker. Em seguida, reinicie o aplicativo Web para que as alterações entrem em vigor. Use os mesmos comandos usados anteriormente neste tutorial. Você pode consultar [Criar a imagem com base no arquivo do Docker](#build-the-image-from-the-docker-file) e [Enviar uma imagem do Docker por push para o Hub do Docker](#push-the-docker-image-to-docker-hub). Teste o aplicativo Web, seguindo as instruções em [Testar o aplicativo Web](#test-the-web-app).

## <a name="connect-to-web-app-for-containers-using-ssh"></a>Conectar-se ao Aplicativo Web para Contêineres usando SSH

O SSH permite a comunicação segura entre um contêiner e um cliente. Para que uma imagem personalizada do Docker dê suporte ao SSH, você deve criá-la em um arquivo do Docker. Habilite o SSH no próprio arquivo do Docker. As instruções sobre o SSH já foram adicionadas ao arquivo do Docker de exemplo, para que você possa seguir estas instruções com sua própria imagem personalizada:

* Uma instrução [RUN](https://docs.docker.com/engine/reference/builder/#run) que chama `apt-get` e, em seguida, define a senha da conta raiz como `"Docker!"`.

    ```docker
    ENV SSH_PASSWD "root:Docker!"
    RUN apt-get update \
            && apt-get install -y --no-install-recommends dialog \
            && apt-get update \
      && apt-get install -y --no-install-recommends openssh-server \
      && echo "$SSH_PASSWD" | chpasswd 
    ```

    > [!NOTE]
    > Essa configuração não permite conexões externas com o contêiner. O SSH está disponível apenas por meio do Site do Kudu/SCM. O site do Kudu/SCM é autenticado com as credenciais de publicação.

* Uma instrução [COPY](https://docs.docker.com/engine/reference/builder/#copy) que instrui o mecanismo do Docker para copiar o arquivo [sshd_config](http://man.openbsd.org/sshd_config) para o diretório */etc/ssh/*. O arquivo de configuração deve ser baseado [nesse arquivo sshd_config](https://github.com/Azure-App-Service/node/blob/master/6.11.1/sshd_config).

    ```docker
    COPY sshd_config /etc/ssh/
    ```

    > [!NOTE]
    > O arquivo *sshd_config* deve incluir os seguintes itens: 
    > * `Ciphers` deve incluir pelo menos um item nesta lista: `aes128-cbc,3des-cbc,aes256-cbc`.
    > * `MACs` deve incluir pelo menos um item nesta lista: `hmac-sha1,hmac-sha1-96`.

* Uma instrução [EXPOSE](https://docs.docker.com/engine/reference/builder/#expose) que expõe a porta 2222 no contêiner. Embora a senha raiz seja conhecida, a porta 2222 não pode ser acessada da Internet. Ela é uma porta interna que pode ser acessada somente por contêineres dentro da rede ponte de uma rede virtual privada. Depois disso, os comandos copiam os detalhes da configuração do SSH e iniciam o serviço `ssh`.

    ```docker
    EXPOSE 8000 2222
    ```

* Certifique-se de [iniciar o serviço ssh](https://github.com/Azure-App-Service/node/blob/master/6.9.3/startup/init_container.sh) usando um script de Shell no diretório /bin.
 
    ```bash
    #!/bin/bash
    service ssh start
    ```
     
### <a name="open-ssh-connection-to-container"></a>Abrir conexão SSH no contêiner

O Aplicativo Web para Contêineres não permite conexões externas com o contêiner. O SSH está disponível apenas por meio do site do Kudu, que pode ser acessado em `https://<app_name>.scm.azurewebsites.net`.

Para conectar-se, navegue até `https://<app_name>.scm.azurewebsites.net/webssh/host` e entre com sua conta do Azure.

Em seguida, você será redirecionado para uma página que exibe um console interativo. 

Talvez você deseje verificar se determinados aplicativos estão em execução no contêiner. Para inspecionar o contêiner e verificar os processos em execução, execute o comando `top` no prompt.

```bash
top
```

O comando `top` expõe todos os processos em execução em um contêiner.

```
PID USER      PR  NI    VIRT    RES    SHR S %CPU %MEM     TIME+ COMMAND
 1 root      20   0  945616  35372  15348 S  0.0  2.1   0:04.63 node
20 root      20   0   55180   2776   2516 S  0.0  0.2   0:00.00 sshd
42 root      20   0  944596  33340  15352 S  0.0  1.9   0:05.80 node /opt/s+
56 root      20   0   59812   5244   4512 S  0.0  0.3   0:00.93 sshd
58 root      20   0   20228   3128   2664 S  0.0  0.2   0:00.00 bash
62 root      20   0   21916   2272   1944 S  0.0  0.1   0:03.15 top
63 root      20   0   59812   5344   4612 S  0.0  0.3   0:00.03 sshd
65 root      20   0   20228   3140   2672 S  0.0  0.2   0:00.00 bash
71 root      20   0   59812   5380   4648 S  0.0  0.3   0:00.02 sshd
73 root      20   0   20228   3160   2696 S  0.0  0.2   0:00.00 bash
77 root      20   0   21920   2304   1972 R  0.0  0.1   0:00.00 top
```

Parabéns! Você configurou uma imagem personalizada do Docker para um Aplicativo Web para Contêineres.

## <a name="use-a-private-image-from-docker-hub-optional"></a>Usar uma imagem privada do Hub do Docker (opcional)

Em [Criar um aplicativo Web](#create-a-web-app), você especificou uma imagem no Hub do Docker no comando `az webapp create`. Isso é o suficiente para uma imagem pública. Para usar uma imagem privada, você precisa configurar a ID e a senha da conta do Docker em seu aplicativo Web do Azure.

No Cloud Shell, após o comando `az webapp create`, insira [az webapp config container set](/cli/azure/webapp/config/container?view=azure-cli-latest#az_webapp_config_container_set). Substitua *\<app_name>* e também _<docker-id>_ e _<password>_ pela ID e a senha do Docker.

```azurecli-interactive
az webapp config container set --name <app_name> --resource-group myResourceGroup --docker-registry-server-user <docker-id> --docker-registry-server-password <password>
```

O comando revela um resultado semelhante à seguinte cadeia de caracteres JSON, mostrando que a alteração na configuração foi bem-sucedida:

```json
[
  {
    "name": "WEBSITES_ENABLE_APP_SERVICE_STORAGE",
    "slotSetting": false,
    "value": "false"
  },
  {
    "name": "DOCKER_REGISTRY_SERVER_USERNAME",
    "slotSetting": false,
    "value": "<docker-id>"
  },
  {
    "name": "DOCKER_REGISTRY_SERVER_PASSWORD",
    "slotSetting": false,
    "value": null
  },
  {
    "name": "DOCKER_CUSTOM_IMAGE_NAME",
    "value": "DOCKER|<image-name-and-tag>"
  }
]
```

## <a name="use-a-docker-image-from-any-private-registry-optional"></a>Usar uma imagem do Docker de qualquer registro privado (opcional)

Nesta seção, você aprenderá a usar uma imagem de Docker de um registro privado no Aplicativo Web para Contêineres e o Registro de Contêiner do Azure será usado como exemplo. As etapas para usar outros registros privados são semelhantes. 

O Registro de Contêiner do Azure é um serviço gerenciado do Docker de hospedagem de imagens particulares. As implantações podem ser de qualquer tipo, incluindo [Docker Swarm](https://docs.docker.com/engine/swarm/), [Kubernetes](https://kubernetes.io/) e Aplicativo Web para Contêineres. 

### <a name="create-an-azure-container-registry"></a>Criar um Registro de Contêiner do Azure

No Cloud Shell, use o comando [az acr create](/cli/azure/acr?view=azure-cli-latest#az_acr_create) para criar um Registro de Contêiner do Azure. Passe o nome, o grupo de recursos e `Basic` do SKU. Os SKUs disponíveis são `Classic`, `Basic`, `Standard` e `Premium`.

```azurecli-interactive
az acr create --name <azure-container-registry-name> --resource-group myResourceGroup --sku Basic --admin-enabled true
```

A criação de um contêiner produz o seguinte resultado:

```
 - Finished ..
Create a new service principal and assign access:
  az ad sp create-for-rbac --scopes /subscriptions/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/<azure-container-registry-name> --role Owner --password <password>

Use an existing service principal and assign access:
  az role assignment create --scope /subscriptions/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/<azure-container-registry-name> --role Owner --assignee <app-id>
{
  "adminUserEnabled": false,
  "creationDate": "2017-08-09T04:21:09.654153+00:00",
  "id": "/subscriptions/<subscriptionId>/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/<azure-container-registry-name>",
  "location": "westeurope",
  "loginServer": "<azure-container-registry-name>.azurecr.io",
  "name": "<azure-container-registry-name>",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "sku": {
    "name": "Basic",
    "tier": "Basic"
  },
  "storageAccount": {
    "name": "myazurecontainerre042025"
  },
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}
```

### <a name="log-in-to-azure-container-registry"></a>Fazer logon no Registro de Contêiner do Azure

Para enviar uma imagem por push para o Registro, você precisa fornecer credenciais para que o Registro aceite o push. Você pode recuperar essas credenciais usando o comando [az acr show](/cli/azure/acr?view=azure-cli-latest#az_acr_show) no Cloud Shell. 

```azurecli-interactive
az acr credential show --name <azure-container-registry-name>
```

O comando revela duas senhas que podem ser usadas com o nome de usuário.

```json
<
  "passwords": [
    {
      "name": "password",
      "value": "{password}"
    },
    {
      "name": "password2",
      "value": "{password}"
    }
  ],
  "username": "<registry-username>"
}
```

Na janela do terminal local, faça logon no Registro de Contêiner do Azure usando o comando `docker login`. O nome do servidor é necessário para fazer logon. Use o formato `{azure-container-registry-name>.azurecr.io`.

```bash
docker login <azure-container-registry-name>.azurecr.io --username <registry-username> --password <password> 
```

Confirme se o logon foi bem-sucedido. 

### <a name="push-an-image-to-azure-container-registry"></a>Enviar uma imagem por push para o Registro de Contêiner do Azure

> [!NOTE]
> Se você estiver utilizando sua própria imagem, marque a imagem da seguinte maneira:
> ```bash
> docker tag <azure-container-registry-name>.azurecr.io/mydockerimage
> ```

Envie a imagem por push usando o comando `docker push`. Marque a imagem com o nome do registro, seguido pelo seu nome da imagem e a marca.

```bash
docker push <azure-container-registry-name>.azurecr.io/mydockerimage:v1.0.0
```

Verifique se o push adicionou com êxito um contêiner ao Registro, listando os repositórios do ACR. 

```azurecli-interactive
az acr repository list -n <azure-container-registry-name>
```

A listagem das imagens no Registro confirma que `mydockerimage` está no Registro.

```json
[
  "mydockerimage"
]
```

### <a name="configure-web-app-to-use-the-image-from-azure-container-registry-or-any-private-registry"></a>Configurar o aplicativo Web para usar a imagem do Registro de Contêiner do Azure (ou de qualquer registro privado)

Você pode configurar o Aplicativo Web para Contêineres para que ele execute um contêiner armazenado no Registro de Contêiner do Azure. Usar o Registro de Contêiner do Azure é como usar qualquer registro privado, portanto, se você precisar usar seu próprio registro privado, as etapas para concluir essa tarefa serão semelhantes.

No Cloud Shell, execute [az acr credential show](/cli/azure/acr/credential?view=azure-cli-latest#az_acr_credential_show) para exibir o nome de usuário e a senha do Registro de Contêiner do Azure. Copie o nome de usuário e uma das senhas para que você possa usá-la para configurar o aplicativo Web na próxima etapa.

```bash
az acr credential show --name <azure-container-registry-name>
```

```json
{
  "passwords": [
    {
      "name": "password",
      "value": "password"
    },
    {
      "name": "password2",
      "value": "password2"
    }
  ],
  "username": "<registry-username>"
}
```

No Cloud Shell, execute o comando [az webapp config container set](/cli/azure/webapp/config/container?view=azure-cli-latest#az_webapp_config_container_set) para atribuir a imagem personalizada do Docker ao aplicativo Web. Substitua *\<app_name>*, *\<docker-registry-server-url>*, _\<registry-username>_ e _\<password>_. Para o Registro de Contêiner do Azure, *\<docker-registry-server-url>* está no formato `https://<azure-container-registry-name>.azurecr.io`. 

```azurecli-interactive
az webapp config container set --name <app_name> --resource-group myResourceGroup --docker-custom-image-name mydockerimage --docker-registry-server-url https://<azure-container-registry-name>.azurecr.io --docker-registry-server-user <registry-username> --docker-registry-server-password <password>
```

> [!NOTE]
> `https://` é necessário no *\<docker-registry-server-url>*.
>

O comando revela um resultado semelhante à seguinte cadeia de caracteres JSON, mostrando que a alteração na configuração foi bem-sucedida:

```json
[
  {
    "name": "DOCKER_CUSTOM_IMAGE_NAME",
    "slotSetting": false,
    "value": "mydockerimage"
  },
  {
    "name": "DOCKER_REGISTRY_SERVER_URL",
    "slotSetting": false,
    "value": "<azure-container-registry-name>.azurecr.io"
  },
  {
    "name": "DOCKER_REGISTRY_SERVER_USERNAME",
    "slotSetting": false,
    "value": "<registry-username>"
  },
  {
    "name": "DOCKER_REGISTRY_SERVER_PASSWORD",
    "slotSetting": false,
    "value": null
  }
]
```

[!INCLUDE [Clean-up section](../../../includes/cli-script-clean-up.md)]

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Criar um aplicativo Web Docker Python e PostgreSQL no Azure](tutorial-docker-python-postgresql-app.md)
