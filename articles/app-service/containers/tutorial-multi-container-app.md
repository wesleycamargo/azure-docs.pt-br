---
title: Criar um aplicativo multicontêiner (versão prévia) no Aplicativo Web para Contêineres
description: Saiba como usar vários contêineres no Azure com os arquivos de configuração do Kubernetes e Docker Compose, com um aplicativo MySQL e WordPress.
keywords: serviço de aplicativo do azure, aplicativo web, linux, docker, compor, multicontêiner, vários contêineres, aplicativo web para contêineres, vários contêineres, contêiner, kubernetes, wordpress, banco de dados do azure para mysql, banco de dados de produção com contêineres
services: app-service
documentationcenter: ''
author: msangapu
manager: jeconnoc
editor: ''
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/25/2018
ms.author: msangapu
ms.custom: mvc
ms.openlocfilehash: 4b00f28d3a1183d5033d0655d8c3f244e3d108e7
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/24/2018
ms.locfileid: "39225985"
---
# <a name="tutorial-create-a-multi-container-preview-app-in-web-app-for-containers"></a>Tutorial: Criar um aplicativo multicontêiner (versão prévia) no Aplicativo Web para Contêineres

O [Aplicativo Web para Contêineres](app-service-linux-intro.md) fornece uma maneira flexível de usar imagens do Docker. Neste tutorial, você aprenderá como criar um aplicativo multicontêiner usando WordPress e MySQL. Você concluirá este tutorial no Cloud Shell, mas também pode executar esses comandos localmente com o [Cloud Shell](/cli/azure/install-azure-cli) (2.0.32 ou posterior).

Neste tutorial, você aprenderá como:
> [!div class="checklist"]
> * Converter uma configuração do Docker Compose para trabalhar com Aplicativo Web para Contêineres
> * Converter uma configuração do Kubernetes para funcionar com Aplicativo Web para Contêineres
> * Implantar um aplicativo multicontêiner no Azure
> * Adicionar configurações de aplicativo
> * Usar armazenamento persistente para contêineres
> * Conectar-se ao Banco de Dados do Azure para MySQL
> * Solucionar problemas de erros

[!INCLUDE [Free trial note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisa de experiência com [Docker Compose](https://docs.docker.com/compose/) ou [Kubernetes](https://kubernetes.io/).

## <a name="download-the-sample"></a>Baixar o exemplo

Para este tutorial será utilizado o arquivo de composição do [Docker](https://docs.docker.com/compose/wordpress/#define-the-project), mas você irá modificá-lo incluindo o Banco de Dados do Azure para MySQL, armazenamento persistente e Redis. O arquivo de configuração podem ser localizados em [Exemplos do Azure](https://github.com/Azure-Samples/multicontainerwordpress).

[!code-yml[Main](../../../azure-app-service-multi-container/docker-compose-wordpress.yml)]

No Cloud Shell, crie um diretório de tutorial e depois altere para ele.

```bash
mkdir tutorial

cd tutorial
```

Em seguida, execute o comando a seguir para clonar o repositório de aplicativos de exemplo ao seu diretório de tutorial. Em seguida, altere para o diretório `multicontainerwordpress`.

```bash
git clone https://github.com/Azure-Samples/multicontainerwordpress

cd multicontainerwordpress
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

[!INCLUDE [resource group intro text](../../../includes/resource-group.md)]

No Cloud Shell, crie um grupo de recursos com o comando [`az group create`](/cli/azure/group?view=azure-cli-latest#az_group_create). O exemplo a seguir cria um grupo de recursos nomeado *myResourceGroup* no localização *Centro-Sul dos EUA*. Para ver todos os locais com suporte para o Serviço de Aplicativo no Linux no nível **Standard**, execute o comando [`az appservice list-locations --sku S1 --linux-workers-enabled`](/cli/azure/appservice?view=azure-cli-latest#az_appservice_list_locations).

```azurecli-interactive
az group create --name myResourceGroup --location "South Central US"
```

Em geral, você cria seu grupo de recursos e os recursos em uma região próxima a você.

Quando o comando for concluído, uma saída JSON mostra as propriedades do grupo de recursos.

## <a name="create-an-azure-app-service-plan"></a>Criar um Plano do Serviço de Aplicativo do Azure

No Cloud Shell, crie um plano do Serviço de Aplicativo no grupo de recursos com o comando [`az appservice plan create`](/cli/azure/appservice/plan?view=azure-cli-latest#az_appservice_plan_create).

<!-- [!INCLUDE [app-service-plan](app-service-plan-linux.md)] -->

O exemplo a seguir cria um plano do Serviço de Aplicativo denominado `myAppServicePlan` no tipo de preço **Standard** (`--sku S1`) e em um contêiner do Linux (`--is-linux`).

```azurecli-interactive
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku S1 --is-linux
```

Quando o Plano do Serviço de Aplicativo tiver sido criado, o Cloud Shell mostrará informações semelhantes ao exemplo a seguir:

```json
{
  "adminSiteName": null,
  "appServicePlanName": "myAppServicePlan",
  "geoRegion": "South Central US",
  "hostingEnvironmentProfile": null,
  "id": "/subscriptions/0000-0000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myAppServicePlan",
  "kind": "linux",
  "location": "South Central US",
  "maximumNumberOfWorkers": 1,
  "name": "myAppServicePlan",
  < JSON data removed for brevity. >
  "targetWorkerSizeId": 0,
  "type": "Microsoft.Web/serverfarms",
  "workerTierName": null
}
```

## <a name="docker-compose-configuration-options"></a>Opções de configuração do Docker Compose

Para este tutorial será utilizado o arquivo de composição do [Docker](https://docs.docker.com/compose/wordpress/#define-the-project), mas você irá modificá-lo incluindo o Banco de Dados do Azure para MySQL, armazenamento persistente e Redis. Como alternativa, é possível utilizar [configuração do Kubernetes](#use-a-kubernetes-configuration-optional). Os arquivos de configuração podem ser localizados em [Exemplos do Azure](https://github.com/Azure-Samples/multicontainerwordpress).

As listas a seguir mostram opções de configuração do Docker Compose com e sem suporte no Aplicativo Web para Contêineres:

### <a name="supported-options"></a>Opções com suporte

* command
* entrypoint
* environment
* image
* ports
* restart
* services
* volumes

### <a name="unsupported-options"></a>Opções sem suporte

* build (não permitido)
* depends_on (ignorado)
* networks (ignorada)
* secrets (ignorados)

> [!NOTE]
> Outras opções não explicitamente chamadas também são ignoradas na Versão prévia pública.

### <a name="docker-compose-with-wordpress-and-mysql-containers"></a>Docker Compose com contêineres do WordPress e MySQL

## <a name="create-a-docker-compose-app"></a>Criar um aplicativo Docker Compose

Em seu Cloud Shell, crie um [aplicativo Web](app-service-linux-intro.md) multicontêiner no plano do Serviço de Aplicativo do `myAppServicePlan` com o comando [az webapp create](/cli/azure/webapp?view=azure-cli-latest#az_webapp_create). Substitua _\<app_name>_ por um nome exclusivo do aplicativo.

```bash
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app_name> --multicontainer-config-type compose --multicontainer-config-file docker-compose-wordpress.yml
```

Quando o aplicativo Web tiver sido criado, o Cloud Shell mostrará um resultado semelhante ao exemplo a seguir:

```json
{
  "additionalProperties": {},
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app_name>.azurewebsites.net",
  "enabled": true,
  < JSON data removed for brevity. >
}
```

### <a name="browse-to-the-app"></a>Navegar até o aplicativo

Navegue até o aplicativo implantado em (`http://<app_name>.azurewebsites.net`). O aplicativo pode demorar alguns minutos para carregar. Se um erro for exibido, aguarde mais alguns minutos e atualize o navegador. Caso tenha problemas e queira solucioná-los, analise os [ logs do contêiner](#find-docker-container-logs).

![Aplicativo multicontêiner de exemplo no Aplicativo Web para Contêineres][1]

**Parabéns**, você criou um aplicativo multicontêiner no Aplicativo Web para Contêineres. Em seguida, você configurará o aplicativo para usar o Banco de Dados do Azure para MySQL. Não instale o WordPress neste momento.

## <a name="connect-to-production-database"></a>Conectar o banco de dados de produção

Não é recomendável usar contêineres de banco de dados em um ambiente de produção. Os contêineres locais não são escalonáveis. Em vez disso, será utilizado o Banco de Dados do Azure para MySQL, que pode ser dimensionado.

### <a name="create-an-azure-database-for-mysql-server"></a>Criar um Banco de Dados do Azure para o servidor MySQL

Crie um Banco de Dados do Azure para MySQL com o comando [`az mysql server create`](/cli/azure/mysql/server?view=azure-cli-latest#az_mysql_server_create).

No comando a seguir, substitua o nome do MySQL Server em que o espaço reservado _&lt;mysql_server_name>_ é exibido (os caracteres válidos são `a-z`, `0-9` e `-`). Esse nome faz parte do nome do host do MySQL Server (`<mysql_server_name>.database.windows.net`) e precisa ser global exclusivo.

```azurecli-interactive
az mysql server create --resource-group myResourceGroup --name <mysql_server_name>  --location "South Central US" --admin-user adminuser --admin-password My5up3rStr0ngPaSw0rd! --sku-name B_Gen4_1 --version 5.7
```

A conclusão da criação do servidor pode demorar alguns minutos. Quando o servidor MySQL for criado, o Cloud Shell mostrará informações semelhantes ao exemplo a seguir:

```json
{
  "administratorLogin": "adminuser",
  "administratorLoginPassword": null,
  "fullyQualifiedDomainName": "<mysql_server_name>.database.windows.net",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.DBforMySQL/servers/<mysql_server_name>",
  "location": "southcentralus",
  "name": "<mysql_server_name>",
  "resourceGroup": "myResourceGroup",
  ...
}
```

### <a name="configure-server-firewall"></a>Configurar o firewall do servidor

Crie uma regra de firewall para o servidor MySQL para permitir conexões de cliente usando o comando [`az mysql server firewall-rule create`](/cli/azure/mysql/server/firewall-rule?view=azure-cli-latest#az_mysql_server_firewall_rule_create). Quando o IP inicial e o IP final estiverem definidos como 0.0.0.0, o firewall estará aberto somente para outros recursos do Azure.

```azurecli-interactive
az mysql server firewall-rule create --name allAzureIPs --server <mysql_server_name> --resource-group myResourceGroup --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

> [!TIP]
> Você pode ser ainda mais restritivo na regra de firewall ao [usar somente os endereços de IP de saída que seu aplicativo usa](../app-service-ip-addresses.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#find-outbound-ips).
>

### <a name="create-the-wordpress-database"></a>Criar o banco de dados do WordPress

```bash
az mysql db create --resource-group myResourceGroup --server-name <mysql_server_name> --name wordpress
```

Quando o banco de dados for criado, o Cloud Shell mostrará informações semelhantes ao exemplo a seguir:

```json
{
  "additionalProperties": {},
  "charset": "latin1",
  "collation": "latin1_swedish_ci",
  "id": "/subscriptions/12db1644-4b12-4cab-ba54-8ba2f2822c1f/resourceGroups/myResourceGroup/providers/Microsoft.DBforMySQL/servers/<mysql_server_name>/databases/wordpress",
  "name": "wordpress",
  "resourceGroup": "myResourceGroup",
  "type": "Microsoft.DBforMySQL/servers/databases"
}
```

### <a name="configure-database-variables-in-wordpress"></a>Configurar variáveis de banco de dados no WordPress

Para conectar o aplicativo WordPress a esse novo servidor MySQL, você configurará algumas variáveis de ambiente específicas do WordPress, incluindo o caminho de AC do SSL definido por `MYSQL_SSL_CA`. O [Baltimore CyberTrust Root](https://www.digicert.com/digicert-root-certificates.htm) da [DigiCert](http://www.digicert.com/) é fornecido na [imagem personalizada](https://docs.microsoft.com/en-us/azure/app-service/containers/tutorial-multi-container-app#use-a-custom-image-for-mysql-ssl-and-other-configurations) abaixo.

Para fazer essas alterações, use o comando [az webapp config appsettings set](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az_webapp_config_appsettings_set) no Cloud Shell. As configurações do aplicativo diferenciam maiúsculas de minúsculas e são separadas por espaços.

```bash
az webapp config appsettings set --resource-group myResourceGroup --name <app_name> --settings WORDPRESS_DB_HOST="<mysql_server_name>.mysql.database.azure.com" WORDPRESS_DB_USER="adminuser@<mysql_server_name>" WORDPRESS_DB_PASSWORD="My5up3rStr0ngPaSw0rd!" WORDPRESS_DB_NAME="wordpress" MYSQL_SSL_CA="BaltimoreCyberTrustroot.crt.pem"
```

Após a criação da configuração do aplicativo, o Cloud Shell mostrará informações semelhantes ao exemplo a seguir:

```json
[
  {
    "name": "WORDPRESS_DB_HOST",
    "slotSetting": false,
    "value": "<mysql_server_name>.mysql.database.azure.com"
  },
  {
    "name": "WORDPRESS_DB_USER",
    "slotSetting": false,
    "value": "adminuser@<mysql_server_name>"
  },
  {
    "name": "WORDPRESS_DB_NAME",
    "slotSetting": false,
    "value": "wordpress"
  },
  {
    "name": "WORDPRESS_DB_PASSWORD",
    "slotSetting": false,
    "value": "My5up3rStr0ngPaSw0rd!"
  },
  {
    "name": "MYSQL_SSL_CA",
    "slotSetting": false,
    "value": "BaltimoreCyberTrustroot.crt.pem"
  }
]
```

### <a name="use-a-custom-image-for-mysql-ssl-and-other-configurations"></a>Usar uma imagem personalizada para MySQL SSL e outras configurações

Por padrão, o SSL é usado pelo Banco de Dados do Azure para MySQL. O WordPress requer configuração adicional para usar SSL com MySQL. A "imagem oficial" do WordPress não fornece a configuração adicional, mas uma [imagem personalizada](https://hub.docker.com/r/microsoft/multicontainerwordpress/builds/) foi preparada para sua conveniência. Na prática, você adicionaria as alterações desejadas à sua própria imagem.

A imagem personalizada é baseada na 'imagem oficial' do [WordPress do Hub do Docker](https://hub.docker.com/_/wordpress/). As alterações a seguir foram feitas nessa imagem personalizada do Banco de Dados do Azure para MySQL:

* [Adicionar arquivo do certificado de Baltimore CyberTrust Root do SSL para MySQL.](https://github.com/Azure-Samples/multicontainerwordpress/blob/5669a89e0ee8599285f0e2e6f7e935c16e539b92/docker-entrypoint.sh#L61)
* [Usar Configuração do Aplicativo para certificado de Autoridade de Certificação do SSL do MySQL no wp-config.php do WordPress.](https://github.com/Azure-Samples/multicontainerwordpress/blob/5669a89e0ee8599285f0e2e6f7e935c16e539b92/docker-entrypoint.sh#L163)
* [Adicionar WordPress define para MYSQL_CLIENT_FLAGS necessário para MySQL SSL.](https://github.com/Azure-Samples/multicontainerwordpress/blob/5669a89e0ee8599285f0e2e6f7e935c16e539b92/docker-entrypoint.sh#L164)

As alterações a seguir foram feitas para Redis (a ser usado em uma seção posterior):

* [Adicionar extensão PHP para Redis v4.0.2.](https://github.com/Azure-Samples/multicontainerwordpress/blob/5669a89e0ee8599285f0e2e6f7e935c16e539b92/Dockerfile#L35)
* [Adicionar descompactador necessário para extração de arquivo.](https://github.com/Azure-Samples/multicontainerwordpress/blob/5669a89e0ee8599285f0e2e6f7e935c16e539b92/docker-entrypoint.sh#L71)
* [Adicionar plug-in do WordPress do Cache de Objetos do Redis 1.3.8.](https://github.com/Azure-Samples/multicontainerwordpress/blob/5669a89e0ee8599285f0e2e6f7e935c16e539b92/docker-entrypoint.sh#L74)
* [Usar Configuração do Aplicativo para nome do host do Redis no wp-config.php do WordPress.](https://github.com/Azure-Samples/multicontainerwordpress/blob/5669a89e0ee8599285f0e2e6f7e935c16e539b92/docker-entrypoint.sh#L162)

Para usar a imagem personalizada, você atualizará o arquivo docker-compose-wordpress.yml. No Cloud Shell, digite `nano docker-compose-wordpress.yml` para abrir o editor de texto nano. Altere `image: wordpress` para usar `image: microsoft/multicontainerwordpress`. Não será mais necessário o contêiner do banco de dados. Remova as seções  `db`, `environment`, `depends_on` e `volumes` do arquivo de configuração. O arquivo deverá ser semelhante ao código a seguir:

```yaml
version: '3.3'

services:
   wordpress:
     image: microsoft/multicontainerwordpress
     ports:
       - "8000:80"
     restart: always
```

Salve suas alterações e saia do nano. Use o comando `^O` para salvar e `^X` para sair.

### <a name="update-app-with-new-configuration"></a>Atualizar aplicativo com nova configuração

No Cloud Shell, reconfigure o [aplicativo Web](app-service-linux-intro.md) multicontêiner com o comando [az webapp config container set](/cli/azure/webapp/config/container?view=azure-cli-latest#az_webapp_config_container_set). Substitua _\<app_name>_ pelo nome do aplicativo Web que você criou anteriormente.

```bash
az webapp config container set --resource-group myResourceGroup --name <app_name> --multicontainer-config-type compose --multicontainer-config-file docker-compose-wordpress.yml
```

Quando o aplicativo for reconfigurado, o Cloud Shell mostrará informações semelhantes ao exemplo a seguir:

```json
[
  {
    "name": "DOCKER_CUSTOM_IMAGE_NAME",
    "value": "COMPOSE|dmVyc2lvbjogJzMuMycKCnNlcnZpY2VzOgogICB3b3JkcHJlc3M6CiAgICAgaW1hZ2U6IG1zYW5nYXB1L3dvcmRwcmVzcwogICAgIHBvcnRzOgogICAgICAgLSAiODAwMDo4MCIKICAgICByZXN0YXJ0OiBhbHdheXM="
  }
]
```

### <a name="browse-to-the-app"></a>Navegar até o aplicativo

Navegue até o aplicativo implantado em (`http://<app_name>.azurewebsites.net`). O aplicativo agora está usando o Banco de Dados do Azure para MySQL.

![Aplicativo multicontêiner de exemplo no Aplicativo Web para Contêineres][1]

## <a name="add-persistent-storage"></a>Adicionar armazenamento persistente

O multicontêiner agora está em execução no Aplicativo Web para Contêineres. No entanto, se você instalar o WordPress agora e reiniciar o aplicativo mais tarde, descobrirá que a instalação do WordPress não existe mais. Isso acontece porque a configuração do Docker Compose atualmente aponta para um local de armazenamento dentro do contêiner. Os arquivos instalados no contêiner não persistem além do reinício do aplicativo. Nesta seção, você adicionará armazenamento persistente ao contêiner do WordPress.

### <a name="configure-environment-variables"></a>Configurar variáveis de ambiente

Para usar o armazenamento persistente, você habilitará essa configuração no Serviço de Aplicativo. Para fazer essa alteração, use o comando [az webapp config appsettings set](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az_webapp_config_appsettings_set) no Cloud Shell. As configurações do aplicativo diferenciam maiúsculas de minúsculas e são separadas por espaços.

```bash
az webapp config appsettings set --resource-group myResourceGroup --name <app_name> --settings WEBSITES_ENABLE_APP_SERVICE_STORAGE=TRUE
```

Após a criação da configuração do aplicativo, o Cloud Shell mostrará informações semelhantes ao exemplo a seguir:

```json
[
  < JSON data removed for brevity. >
  {
    "name": "WORDPRESS_DB_NAME",
    "slotSetting": false,
    "value": "wordpress"
  },
  {
    "name": "WEBSITES_ENABLE_APP_SERVICE_STORAGE",
    "slotSetting": false,
    "value": "TRUE"
  }
]
```

### <a name="modify-configuration-file"></a>Modifique o arquivo de configuração

No Cloud Shell, digite `nano docker-compose-wordpress.yml` para abrir o editor de texto nano.

A opção `volumes` mapeia o sistema de arquivos para um diretório dentro do contêiner. `${WEBAPP_STORAGE_HOME}` é uma variável de ambiente no Serviço de Aplicativo mapeada para armazenamento persistente para o aplicativo. Você usará essa variável de ambiente na opção de volumes para que os arquivos do WordPress sejam instalados no armazenamento persistente em vez do contêiner. Faça as modificações a seguir no arquivo:

Na seção `wordpress`, adicione uma opção `volumes` para que seja semelhante ao código a seguir:

```yaml
version: '3.3'

services:
   wordpress:
     image: microsoft/multicontainerwordpress
     volumes:
      - ${WEBAPP_STORAGE_HOME}/site/wwwroot:/var/www/html
     ports:
       - "8000:80"
     restart: always
```

### <a name="update-app-with-new-configuration"></a>Atualizar aplicativo com nova configuração

No Cloud Shell, reconfigure o [aplicativo Web](app-service-linux-intro.md) multicontêiner com o comando [az webapp config container set](/cli/azure/webapp/config/container?view=azure-cli-latest#az_webapp_config_container_set). Substitua _\<app_name>_ por um nome exclusivo do aplicativo.

```bash
az webapp config container set --resource-group myResourceGroup --name <app_name> --multicontainer-config-type compose --multicontainer-config-file docker-compose-wordpress.yml
```

Após a execução do comando, ele mostrará uma saída semelhante ao exemplo a seguir:

```json
[
  {
    "name": "WEBSITES_ENABLE_APP_SERVICE_STORAGE",
    "slotSetting": false,
    "value": "TRUE"
  },
  {
    "name": "DOCKER_CUSTOM_IMAGE_NAME",
    "value": "COMPOSE|dmVyc2lvbjogJzMuMycKCnNlcnZpY2VzOgogICBteXNxbDoKICAgICBpbWFnZTogbXlzcWw6NS43CiAgICAgdm9sdW1lczoKICAgICAgIC0gZGJfZGF0YTovdmFyL2xpYi9teXNxbAogICAgIHJlc3RhcnQ6IGFsd2F5cwogICAgIGVudmlyb25tZW50OgogICAgICAgTVlTUUxfUk9PVF9QQVNTV09SRDogZXhhbXBsZXBhc3MKCiAgIHdvcmRwcmVzczoKICAgICBkZXBlbmRzX29uOgogICAgICAgLSBteXNxbAogICAgIGltYWdlOiB3b3JkcHJlc3M6bGF0ZXN0CiAgICAgcG9ydHM6CiAgICAgICAtICI4MDAwOjgwIgogICAgIHJlc3RhcnQ6IGFsd2F5cwogICAgIGVudmlyb25tZW50OgogICAgICAgV09SRFBSRVNTX0RCX1BBU1NXT1JEOiBleGFtcGxlcGFzcwp2b2x1bWVzOgogICAgZGJfZGF0YTo="
  }
]
```

### <a name="browse-to-the-app"></a>Navegar até o aplicativo

Navegue até o aplicativo implantado em (`http://<app_name>.azurewebsites.net`).

O contêiner do WordPress agora está usando Banco de Dados do Azure para MySQL e armazenamento persistente.

## <a name="add-redis-container"></a>Adicionar contêiner do Redis

 A 'imagem oficial' do WordPress não inclui as dependências para Redis. Essas dependências e configurações adicionais necessárias para usar Redis com WordPress foram preparadas para você nesta [imagem personalizada](https://github.com/Azure-Samples/multicontainerwordpress). Na prática, você adicionaria as alterações desejadas à sua própria imagem.

A imagem personalizada é baseada na 'imagem oficial' do [WordPress do Hub do Docker](https://hub.docker.com/_/wordpress/). As alterações a seguir foram feitas nesta imagem personalizada para Redis:

* [Adicionar extensão PHP para Redis v4.0.2.](https://github.com/Azure-Samples/multicontainerwordpress/blob/5669a89e0ee8599285f0e2e6f7e935c16e539b92/Dockerfile#L35)
* [Adicionar descompactador necessário para extração de arquivo.](https://github.com/Azure-Samples/multicontainerwordpress/blob/5669a89e0ee8599285f0e2e6f7e935c16e539b92/docker-entrypoint.sh#L71)
* [Adicionar plug-in do WordPress do Cache de Objetos do Redis 1.3.8.](https://github.com/Azure-Samples/multicontainerwordpress/blob/5669a89e0ee8599285f0e2e6f7e935c16e539b92/docker-entrypoint.sh#L74)
* [Usar Configuração do Aplicativo para nome do host do Redis no wp-config.php do WordPress.](https://github.com/Azure-Samples/multicontainerwordpress/blob/5669a89e0ee8599285f0e2e6f7e935c16e539b92/docker-entrypoint.sh#L162)

Adicione o contêiner do Redis na parte inferior do arquivo de configuração para que seja semelhante ao exemplo a seguir:

[!code-yml[Main](../../../azure-app-service-multi-container/compose-wordpress.yml)]

### <a name="configure-environment-variables"></a>Configurar variáveis de ambiente

Para usar o Redis, você habilitará essa configuração `WP_REDIS_HOST` no Serviço de Aplicativo. Esta é uma *configuração necessária* para WordPress comunicar-se com host do Redis. Para fazer essa alteração, use o comando [az webapp config appsettings set](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az_webapp_config_appsettings_set) no Cloud Shell. As configurações do aplicativo diferenciam maiúsculas de minúsculas e são separadas por espaços.

```bash
az webapp config appsettings set --resource-group myResourceGroup --name <app_name> --settings WP_REDIS_HOST="redis"
```

Após a criação da configuração do aplicativo, o Cloud Shell mostrará informações semelhantes ao exemplo a seguir:

```json
[
  < JSON data removed for brevity. >
  {
    "name": "WORDPRESS_DB_USER",
    "slotSetting": false,
    "value": "adminuser@<mysql_server_name>"
  },
  {
    "name": "WP_REDIS_HOST",
    "slotSetting": false,
    "value": "redis"
  }
]
```

### <a name="update-app-with-new-configuration"></a>Atualizar aplicativo com nova configuração

No Cloud Shell, reconfigure o [aplicativo Web](app-service-linux-intro.md) multicontêiner com o comando [az webapp config container set](/cli/azure/webapp/config/container?view=azure-cli-latest#az_webapp_config_container_set). Substitua _\<app_name>_ por um nome exclusivo do aplicativo.

```bash
az webapp config container set --resource-group myResourceGroup --name <app_name> --multicontainer-config-type compose --multicontainer-config-file compose-wordpress.yml
```

Após a execução do comando, ele mostrará uma saída semelhante ao exemplo a seguir:

```json
[
  {
    "name": "DOCKER_CUSTOM_IMAGE_NAME",
    "value": "COMPOSE|dmVyc2lvbjogJzMuMycKCnNlcnZpY2VzOgogICBteXNxbDoKICAgICBpbWFnZTogbXlzcWw6NS43CiAgICAgdm9sdW1lczoKICAgICAgIC0gZGJfZGF0YTovdmFyL2xpYi9teXNxbAogICAgIHJlc3RhcnQ6IGFsd2F5cwogICAgIGVudmlyb25tZW50OgogICAgICAgTVlTUUxfUk9PVF9QQVNTV09SRDogZXhhbXBsZXBhc3MKCiAgIHdvcmRwcmVzczoKICAgICBkZXBlbmRzX29uOgogICAgICAgLSBteXNxbAogICAgIGltYWdlOiB3b3JkcHJlc3M6bGF0ZXN0CiAgICAgcG9ydHM6CiAgICAgICAtICI4MDAwOjgwIgogICAgIHJlc3RhcnQ6IGFsd2F5cwogICAgIGVudmlyb25tZW50OgogICAgICAgV09SRFBSRVNTX0RCX1BBU1NXT1JEOiBleGFtcGxlcGFzcwp2b2x1bWVzOgogICAgZGJfZGF0YTo="
  }
]
```

### <a name="browse-to-the-app"></a>Navegar até o aplicativo

Navegue até o aplicativo implantado em (`http://<app_name>.azurewebsites.net`).

Conclua as etapas e instale o WordPress.

### <a name="connect-wordpress-to-redis"></a>Conecte o WordPress ao Redis

Faça logon no administrador do WordPress. Na navegação esquerda, selecione **Plug-ins**, e, em seguida, selecione **Plug-ins Instalados**.

![Selecione Plug-ins do WordPress][2]

Mostrar todos os plug-ins aqui

Na página de plug-ins, localize **Cache de Objetos do Redis** e clique em **Ativar**.

![Ativar Redis][3]

Clique em **Configurações**.

![Clique em configurações][4]

Clique no botão **Habilitar Cache de Objetos**.

![Clicar no botão "Habilitar Cache de Objetos"][5]

O WordPress conecta-se ao servidor de Redis. A conexão **status** aparece na mesma página.

![O WordPress conecta-se ao servidor de Redis. A conexão ** status ** aparece na mesma página.][6]

**Parabéns**, você conectou o WordPress ao Redis. O aplicativo pronto para produção agora está usando **Banco de Dados do Azure para MySQL, armazenamento persistente e Redis**. Agora é possível escalar horizontalmente o Plano do Serviço de Aplicativo para várias instâncias.

## <a name="use-a-kubernetes-configuration-optional"></a>Use uma configuração do Kubernetes (opcional)

Nesta seção, você aprenderá como usar uma configuração do Kubernetes para implantar vários contêineres. Certifique-se de seguir as etapas anteriores para criar um [grupo de recursos](#create-a-resource-group) e um [Plano do Serviço de Aplicativo](#create-an-azure-app-service-plan). Como a maioria das etapas é semelhante à da seção de composição, o arquivo de configuração foi combinado para você.

### <a name="supported-kubernetes-options-for-multi-container"></a>Opções do Kubernetes com suporte para vários contêineres

* args
* command
* containers
* image
* name
* ports
* spec

> [!NOTE]
>Outras opções de Kubernetes não explicitamente chamadas não são compatíveis com a Versão prévia pública.
>

### <a name="kubernetes-configuration-file"></a>Arquivo de configuração do Kubernetes

Você usará *kubernetes-wordpress.yml* para esta parte do tutorial. Ele é exibido aqui para sua referência:

[!code-yml[Main](../../../azure-app-service-multi-container/kubernetes-wordpress.yml)]

### <a name="create-an-azure-database-for-mysql-server"></a>Criar um Banco de Dados do Azure para o servidor MySQL

Crie um servidor no Banco de Dados do Azure para MySQL com o comando [`az mysql server create`](/cli/azure/mysql/server?view=azure-cli-latest#az_mysql_server_create).

No comando a seguir, substitua o nome do MySQL Server em que o espaço reservado _&lt;mysql_server_name>_ é exibido (os caracteres válidos são `a-z`, `0-9` e `-`). Esse nome faz parte do nome do host do MySQL Server (`<mysql_server_name>.database.windows.net`) e precisa ser global exclusivo.

```azurecli-interactive
az mysql server create --resource-group myResourceGroup --name <mysql_server_name>  --location "South Central US" --admin-user adminuser --admin-password My5up3rStr0ngPaSw0rd! --sku-name B_Gen4_1 --version 5.7
```

Quando o servidor MySQL for criado, o Cloud Shell mostrará informações semelhantes ao exemplo a seguir:

```json
{
  "administratorLogin": "adminuser",
  "administratorLoginPassword": null,
  "fullyQualifiedDomainName": "<mysql_server_name>.database.windows.net",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.DBforMySQL/servers/<mysql_server_name>",
  "location": "southcentralus",
  "name": "<mysql_server_name>",
  "resourceGroup": "myResourceGroup",
  ...
}
```

### <a name="configure-server-firewall"></a>Configurar o firewall do servidor

Crie uma regra de firewall para o servidor MySQL para permitir conexões de cliente usando o comando [`az mysql server firewall-rule create`](/cli/azure/mysql/server/firewall-rule?view=azure-cli-latest#az_mysql_server_firewall_rule_create). Quando o IP inicial e o IP final estiverem definidos como 0.0.0.0, o firewall estará aberto somente para outros recursos do Azure.

```azurecli-interactive
az mysql server firewall-rule create --name allAzureIPs --server <mysql_server_name> --resource-group myResourceGroup --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

> [!TIP]
> Você pode ser ainda mais restritivo na regra de firewall ao [usar somente os endereços de IP de saída que seu aplicativo usa](../app-service-ip-addresses.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#find-outbound-ips).
>

### <a name="create-the-wordpress-database"></a>Criar o banco de dados do WordPress

Se isso ainda não foi feito, crie um [Banco de Dados do Azure para MySQL](#create-an-azure-database-for-mysql-server).

```bash
az mysql db create --resource-group myResourceGroup --server-name <mysql_server_name> --name wordpress
```

Quando o banco de dados for criado, o Cloud Shell mostrará informações semelhantes ao exemplo a seguir:

```json
{
  "additionalProperties": {},
  "charset": "latin1",
  "collation": "latin1_swedish_ci",
  "id": "/subscriptions/12db1644-4b12-4cab-ba54-8ba2f2822c1f/resourceGroups/myResourceGroup/providers/Microsoft.DBforMySQL/servers/<mysql_server_name>/databases/wordpress",
  "name": "wordpress",
  "resourceGroup": "myResourceGroup",
  "type": "Microsoft.DBforMySQL/servers/databases"
}
```

### <a name="configure-database-variables-in-wordpress"></a>Configurar variáveis de banco de dados no WordPress

Para conectar o aplicativo WordPress a esse novo servidor MySQL, você configurará algumas variáveis de ambiente específicas do WordPress. Para fazer essa alteração, use o comando [az webapp config appsettings set](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az_webapp_config_appsettings_set) no Cloud Shell. As configurações do aplicativo diferenciam maiúsculas de minúsculas e são separadas por espaços.

```bash
az webapp config appsettings set --resource-group myResourceGroup --name <app_name> --settings WORDPRESS_DB_HOST="<mysql_server_name>.mysql.database.azure.com" WORDPRESS_DB_USER="adminuser@<mysql_server_name>" WORDPRESS_DB_PASSWORD="My5up3rStr0ngPaSw0rd!" WORDPRESS_DB_NAME="wordpress" MYSQL_SSL_CA="BaltimoreCyberTrustroot.crt.pem"
```

Após a criação da configuração do aplicativo, o Cloud Shell mostrará informações semelhantes ao exemplo a seguir:

```json
[
  {
    "name": "WORDPRESS_DB_HOST",
    "slotSetting": false,
    "value": "<mysql_server_name>.mysql.database.azure.com"
  },
  {
    "name": "WORDPRESS_DB_USER",
    "slotSetting": false,
    "value": "adminuser@<mysql_server_name>"
  },
  {
    "name": "WORDPRESS_DB_NAME",
    "slotSetting": false,
    "value": "wordpress"
  },
  {
    "name": "WORDPRESS_DB_PASSWORD",
    "slotSetting": false,
    "value": "My5up3rStr0ngPaSw0rd!"
  }
]
```

### <a name="add-persistent-storage"></a>Adicionar armazenamento persistente

O multicontêiner agora está em execução no Aplicativo Web para Contêineres. Os dados serão apagados no reinício porque os arquivos não são persistidos. Nesta seção, você adicionará armazenamento persistente ao contêiner do WordPress.

### <a name="configure-environment-variables"></a>Configurar variáveis de ambiente

Para usar o armazenamento persistente, você habilitará essa configuração no Serviço de Aplicativo. Para fazer essa alteração, use o comando [az webapp config appsettings set](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az_webapp_config_appsettings_set) no Cloud Shell. As configurações do aplicativo diferenciam maiúsculas de minúsculas e são separadas por espaços.

```bash
az webapp config appsettings set --resource-group myResourceGroup --name <app_name> --settings WEBSITES_ENABLE_APP_SERVICE_STORAGE=TRUE
```

Após a criação da configuração do aplicativo, o Cloud Shell mostrará informações semelhantes ao exemplo a seguir:

```json
[
  {
    "name": "WEBSITES_ENABLE_APP_SERVICE_STORAGE",
    "slotSetting": false,
    "value": "TRUE"
  }
]
```

### <a name="create-a-multi-container-app-kubernetes"></a>Criar um aplicativo multicontêiner (Kubernetes)

Em seu Cloud Shell, crie um [aplicativo Web](app-service-linux-intro.md) multicontêiner no grupo de recurso `myResourceGroup` e o plano do Serviço de Aplicativo `myAppServicePlan` com o comando [az webapp create](/cli/azure/webapp?view=azure-cli-latest#az_webapp_create). Substitua _\<app_name>_ por um nome exclusivo do aplicativo.

```bash
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app_name> --multicontainer-config-type kube --multicontainer-config-file kubernetes-wordpress.yml
```

Quando o aplicativo Web tiver sido criado, o Cloud Shell mostrará um resultado semelhante ao exemplo a seguir:

```json
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app_name>.azurewebsites.net",
  "enabled": true,
  < JSON data removed for brevity. >
}
```

### <a name="browse-to-the-app"></a>Navegar até o aplicativo

Navegue até o aplicativo implantado em (`http://<app_name>.azurewebsites.net`).

O aplicativo agora está executando vários contêineres no Aplicativo Web para Contêineres.

![Aplicativo multicontêiner de exemplo no Aplicativo Web para Contêineres][1]

**Parabéns**, você criou um aplicativo multicontêiner no Aplicativo Web para Contêineres.

Para usar o Redis, execute as etapas em [Conecte o WordPress ao Redis](#connect-wordpress-to-redis).

## <a name="find-docker-container-logs"></a>Localizar logs do contêiner do Docker

Se você tiver problemas ao usar vários contêineres, poderá acessar os logs do contêiner navegando até: `https://<app_name>.scm.azurewebsites.net/api/logs/docker`.

Você verá uma saída semelhante ao exemplo a seguir:

```json
[
   {
      "machineName":"RD00XYZYZE567A",
      "lastUpdated":"2018-05-10T04:11:45Z",
      "size":25125,
      "href":"https://<app_name>.scm.azurewebsites.net/api/vfs/LogFiles/2018_05_10_RD00XYZYZE567A_docker.log",
      "path":"/home/LogFiles/2018_05_10_RD00XYZYZE567A_docker.log"
   }
]
```

Você verá um log para cada contêiner e um log adicional para o processo pai. Copie o respectivo valor `href` no navegador para exibir o log.

[!INCLUDE [Clean-up section](../../../includes/cli-script-clean-up.md)]

Neste tutorial, você aprendeu como:
> [!div class="checklist"]
> * Converter uma configuração do Docker Compose para trabalhar com Aplicativo Web para Contêineres
> * Converter uma configuração do Kubernetes para funcionar com Aplicativo Web para Contêineres
> * Implantar um aplicativo multicontêiner no Azure
> * Adicionar configurações de aplicativo
> * Usar armazenamento persistente para contêineres
> * Conectar-se ao Banco de Dados do Azure para MySQL
> * Solucionar problemas de erros

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Usar uma imagem personalizada do Docker para o Aplicativo Web para Contêineres](tutorial-custom-docker-image.md)

<!--Image references-->
[1]: ./media/tutorial-multi-container-app/azure-multi-container-wordpress-install.png
[2]: ./media/tutorial-multi-container-app/wordpress-plugins.png
[3]: ./media/tutorial-multi-container-app/activate-redis.png
[4]: ./media/tutorial-multi-container-app/redis-settings.png
[5]: ./media/tutorial-multi-container-app/enable-object-cache.png
[6]: ./media/tutorial-multi-container-app/redis-connected.png
