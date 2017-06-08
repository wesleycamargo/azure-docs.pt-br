---
title: Compilar um aplicativo Web Docker Python e PostgreSQL no Azure | Microsoft Docs
description: "Saiba como fazer com que um aplicativo Docker Python funcione no Azure com conexão a um banco de dados PostgreSQL."
services: app-service\web
documentationcenter: python
author: berndverst
manager: erikre
editor: 
ms.assetid: 2bada123-ef18-44e5-be71-e16323b20466
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 05/03/2017
ms.author: beverst
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: 5edc47e03ca9319ba2e3285600703d759963e1f3
ms.openlocfilehash: ca086f76e50cb27f012bb2e7f05595be5fdcb241
ms.contentlocale: pt-br
ms.lasthandoff: 05/31/2017

---
# <a name="build-a-docker-python-and-postgresql-web-app-in-azure"></a>Compilar um aplicativo Web Docker Python e PostgreSQL no Azure
Este tutorial mostra como criar um aplicativo Web Docker Python básico no Azure. Você também conectará esse aplicativo a um banco de dados PostgreSQL. Quando terminar, você terá um aplicativo Python Flask em execução dentro de um contêiner do Docker nos [Aplicativos Web do Serviço de Aplicativo do Azure](app-service-web-overview.md).

![Aplicativo Docker Python Flask no Serviço de Aplicativo do Azure](./media/app-service-web-tutorial-docker-python-postgresql-app/docker-flask-in-azure.png)

## <a name="before-you-begin"></a>Antes de começar

Antes de executar este exemplo, instale localmente os seguintes pré-requisitos:

1. [Baixe e instale o git](https://git-scm.com/)
1. [Baixe e instale o Python](https://www.python.org/downloads/)
1. [Baixe, instale e execute o PostgreSQL](https://www.postgresql.org/download/)
1. [Baixe e instale o Docker Community Edition](https://www.docker.com/community-edition)
1. [Baixe e instale o Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="test-local-postgresql-installation-and-create-a-database"></a>Testar a instalação do PostgreSQL local e criar um banco de dados
Nesta etapa, certifique-se de que seu banco de dados PostgreSQL local esteja em execução.

Abra a janela do terminal e execute `psql postgres` para se conectar ao servidor PostgreSQL local.

```bash
psql postgres
```

Se sua conexão tiver êxito, o banco de dados PostgreSQL já estará em execução. Caso contrário, certifique-se de que o banco de dados PostgresQL local seja iniciado seguindo as etapas em [Baixe, instale e execute o PostgreSQL](https://www.postgresql.org/download/).

Crie um banco de dados chamado `eventregistration` e configure um usuário de banco de dados separado chamado `manager` com a senha `supersecretpass`.

```bash
CREATE DATABASE eventregistration;
CREATE USER manager WITH PASSWORD 'supersecretpass';
GRANT ALL PRIVILEGES ON DATABASE eventregistration TO manager;
```
Digite `\q` para sair do cliente do PostgreSQL. 

<a name="step2"></a>

## <a name="create-local-python-flask-application"></a>Criar um aplicativo Python Flask local
Nesta etapa, você configura o projeto Python Flask local.

### <a name="clone-the-sample-application"></a>Clonar o aplicativo de exemplo

Abra a janela do terminal e `CD` para um diretório de trabalho.  

Execute os comandos a seguir para clonar o repositório de exemplo e vá até a versão `0.1-initialapp`.

```bash
git clone https://github.com/Azure-Samples/docker-flask-postgres.gi
cd docker-flask-postgres
git checkout tags/0.1-initialapp
```

Esse repositório de exemplo contém um aplicativo [Flask](http://http://flask.pocoo.org/). 

### <a name="run-the-application"></a>Executar o aplicativo

> [!NOTE] 
> Em uma etapa posterior, simplificaremos esse processo criando um contêiner do Docker para ser usado com nosso banco de dados de produção.

Instale os pacotes necessários e inicie o aplicativo.

```bash
pip install virtualenv
virtualenv venv
source venv/bin/activate
pip install -r requirements.txt
cd app
FLASK_APP=app.py;DBHOST="localhost";DBUSER="manager";DBNAME="eventregistration";DBPASS="supersecretpass";flask db upgrade;flask run
```

Quando o aplicativo estiver totalmente carregado, você deverá ver algo semelhante à seguinte mensagem:

```
INFO  [alembic.runtime.migration] Context impl PostgresqlImpl.
INFO  [alembic.runtime.migration] Will assume transactional DDL.
INFO  [alembic.runtime.migration] Running upgrade  -> 791cd7d80402, empty message
 * Serving Flask app "app"
 * Running on http://127.0.0.1:5000/ (Press CTRL+C to quit)
```

Navegue até `http://127.0.0.1:5000` em um navegador. Clique em **Registrar!** e tente criar um registro fictício.

![Aplicativo Python Flask em execução localmente](./media/app-service-web-tutorial-docker-python-postgresql-app/local-app.png)

O aplicativo de exemplo Flask armazena dados do usuário no banco de dados. Se você tiver êxito e conseguir exibir seu registro no aplicativo, o aplicativo estará gravando dados no banco de dados PostgreSQL local.

Para parar o servidor Flask a qualquer momento, digite `Ctrl`+`C` no terminal. 

## <a name="create-a-production-postgresql-database"></a>Criar um banco de dados PostgreSQL de produção

Nesta etapa, você cria um banco de dados PostgreSQL no Azure. Quando seu aplicativo é implantado no Azure, nós especificamos esse banco de dados para sua carga de trabalho de produção.

### <a name="log-in-to-azure"></a>Fazer logon no Azure

Agora, você usará a CLI do Azure 2.0 em uma janela do terminal para criar os recursos necessários para hospedar o aplicativo Python no Serviço de Aplicativo do Azure.  Faça logon na sua assinatura do Azure com o comando [az login](/cli/azure/#login) e siga as instruções na tela. 

```azurecli-interactive 
az login 
``` 
   
### <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um [grupo de recursos](../azure-resource-manager/resource-group-overview.md) com o [az group create](/cli/azure/group#create). Um grupo de recursos do Azure é um contêiner lógico no qual recursos do Azure, como aplicativos da Web, bancos de dados e contas de armazenamento são implantados e gerenciados. 

O exemplo a seguir cria um grupo de recursos na região Oeste dos EUA:

```azurecli-interactive
az group create --name myResourceGroup --location "West US"
```

Para ver quais são os valores possíveis para `--location`, use o comando `az appservice list-locations` da CLI do Azure.

### <a name="create-an-azure-database-for-postgresql-server"></a>Criar um Banco de Dados do Azure para o servidor PostgreSQL

Crie um servidor PostgreSQL com o comando [az postgres server create](/cli/azure/documentdb#create).

No seguinte comando, substitua por seu próprio nome do servidor PostgreSQL exclusivo quando vir o espaço reservado `<postgresql_name>`. Esse nome exclusivo é usado como parte de seu ponto de extremidade do PostgreSQL (`https://<postgresql_name>.postgres.database.azure.com`), portanto, ele precisa ser exclusivo entre todos os servidores no Azure. 

```azurecli-interactive
az postgres server create --resource-group myResourceGroup --name <postgresql_name> --admin-user <my_admin_username>
```

O `--admin-user` é necessário para criar a conta do usuário administrador do banco de dados inicial. É solicitado que você escolha uma senha para esse usuário.

Quando o servidor PostgreSQL do Banco de Dados do Azure for criado, a CLI do Azure mostrará informações semelhantes ao exemplo a seguir:

```json
{
  "administratorLogin": "<my_admin_username>",
  "fullyQualifiedDomainName": "<postgresql_name>.postgres.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.DBforPostgreSQL/servers/<postgresql_name>",
  "location": "westus",
  "name": "<postgresql_name>",
  "resourceGroup": "myResourceGroup",
  "sku": {
    "capacity": 100,
    "family": null,
    "name": "PGSQLS3M100",
    "size": null,
    "tier": "Basic"
  },
  "sslEnforcement": null,
  "storageMb": 2048,
  "tags": null,
  "type": "Microsoft.DBforPostgreSQL/servers",
  "userVisibleState": "Ready",
  "version": null
}
```

### <a name="creating-a-firewall-rule-for-the-azure-database-for-postgresql-server"></a>Criando uma regra de firewall para o Banco de Dados do Azure para servidor PostgreSQL

Antes que possamos acessar o banco de dados, precisamos permitir que ele seja alcançado por todos os endereços IP. Isso pode ser feito usando o seguinte comando da CLI do Azure:

```azurecli-interactive
az postgres server firewall-rule create --resource-group myResourceGroup --server-name <postgresql_name> --start-ip-address=0.0.0.0 --end-ip-address=255.255.255.255 --name AllowAllIPs
```

Quando o firewall é criado, a CLI do Azure confirma a presença da regra da seguinte maneira:

```json
{
  "endIpAddress": "255.255.255.255",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.DBforPostgreSQL/servers/<postgresql_name>/firewallRules/AllowAllIPs",
  "name": "AllowAllIPs",
  "resourceGroup": "myResourceGroup",
  "startIpAddress": "0.0.0.0",
  "type": "Microsoft.DBforPostgreSQL/servers/firewallRules"
}
```

## <a name="connect-your-python-flask-application-to-the-database"></a>Conectar o aplicativo Python Flask ao banco de dados

Nesta etapa, você conecta seu aplicativo de exemplo Python Flask ao Banco de Dados do Azure para o servidor PostgreSQL criado.

### <a name="creating-an-empty-database-and-setting-up-a-new-database-application-user"></a>Criando um banco de dados vazio e configurando um novo usuário do aplicativo de banco de dados

Nós criamos um novo usuário de banco de dados com acesso a somente um Banco de Dados Individual. Com esta etapa, evitamos dar a nosso aplicativo acesso completo ao servidor por meio de nossas credenciais de administrador.

Conecte-se ao banco de dados (sua senha de administrador será solicitada).
```bash
psql -h <postgresql_name>.postgres.database.azure.com -U <my_admin_username>@<postgresql_name> postgres
```

Em seguida, crie o banco de dados e o usuário usando a CLI do PostgreSQL.
```
CREATE DATABASE eventregistration;
CREATE USER manager WITH PASSWORD 'supersecretpass';
GRANT ALL PRIVILEGES ON DATABASE eventregistration TO manager;
```

Digite `\q` para sair do cliente do PostgreSQL.

### <a name="test-the-application-locally-against-the-azure-postgresql-database"></a>Teste o aplicativo localmente no banco de dados PostgreSQL do Azure 

Voltando para a pasta `app` do repositório do GitHub clonado, podemos executar nosso aplicativo Python Flask atualizando as variáveis de ambiente do banco de dados.

```bash
FLASK_APP=app.py;DBHOST="<postgresql_name>.postgres.database.azure.com";DBUSER="manager@<postgresql_name>";DBNAME="eventregistration";DBPASS="supersecretpass";flask db upgrade;flask run
```

Quando o aplicativo estiver totalmente carregado, você deverá ver novamente algo semelhante à seguinte mensagem:

```
INFO  [alembic.runtime.migration] Context impl PostgresqlImpl.
INFO  [alembic.runtime.migration] Will assume transactional DDL.
INFO  [alembic.runtime.migration] Running upgrade  -> 791cd7d80402, empty message
 * Serving Flask app "app"
 * Running on http://127.0.0.1:5000/ (Press CTRL+C to quit)
```

Navegue até `http://127.0.0.1:5000` em um navegador. Clique em **Registrar!** e tente criar um registro fictício. Agora, você está gravando dados no banco de dados de produção no Azure.

![Aplicativo Python Flask em execução localmente](./media/app-service-web-tutorial-docker-python-postgresql-app/local-app.png)

### <a name="running-the-application-from-a-docker-container"></a>Executando o aplicativo de um contêiner do Docker

Agora, compilamos a imagem de contêiner do Docker e executamos localmente o aplicativo de dentro de um contêiner do Docker, enquanto permanecemos conectados ao banco de dados de produção PostgreSQL no Azure.

```bash
cd ..
docker build -t flask-postgresql-sample .
```

O Docker exibe uma confirmação de que criou o contêiner com êxito.

```
Successfully built 7548f983a36b
```

Vamos adicionar nossas variáveis de ambiente do banco de dados a um arquivo de variável de ambiente `db.env`.

```
DBHOST="<postgresql_name>.postgres.database.azure.com"
DBUSER="manager@<postgresql_name>"
DBNAME="eventregistration"
DBPASS="supersecretpass"
```

Agora, executamos o aplicativo de dentro do contêiner do Docker. Nós especificamos o arquivo de variável de ambiente e mapeamos a porta 5000 padrão do Flask para nossa porta 5000 local.

```bash
docker run -it --env-file db.env -p 5000:5000 flask-postgresql-sample
```

Como era de se esperar, a saída é semelhante à de antes. No entanto, a migração de banco de dados inicial não precisa mais ser executada e, portanto, é ignorada.
```
INFO  [alembic.runtime.migration] Context impl PostgresqlImpl.
INFO  [alembic.runtime.migration] Will assume transactional DDL.
 * Serving Flask app "app"
 * Running on http://0.0.0.0:5000/ (Press CTRL+C to quit)
 ```

 O banco de dados já contém o registro que criamos anteriormente.

 ![Aplicativo Python Flask baseado em contêiner do Docker em execução localmente](./media/app-service-web-tutorial-docker-python-postgresql-app/local-docker.png)

## <a name="upload-the-docker-container-to-a-container-registry"></a>Carregar o contêiner do Docker em um registro de contêiner
Nesta etapa, você carrega o contêiner do Docker que criamos em um registro de contêiner. Nós usaremos o Registro de Contêiner do Azure, mas você também pode usar outros registros populares, como o Hub do Docker.

### <a name="create-an-azure-container-registry"></a>Criar um Registro de Contêiner do Azure

No seguinte comando para criar um registro de contêiner, substitua `<registry_name>` por um nome de Registro de Contêiner do Azure exclusivo de sua escolha.

```azurecli-interactive
az acr create --name <registry_name> --resource-group myResourceGroup --location "West US" --sku Basic
```

Saída
```json
{
  "adminUserEnabled": false,
  "creationDate": "2017-05-04T08:50:55.635688+00:00",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/<registry_name>",
  "location": "westus",
  "loginServer": "<registry_name>.azurecr.io",
  "name": "<registry_name>",
  "provisioningState": "Succeeded",
  "sku": {
    "name": "Basic",
    "tier": "Basic"
  },
  "storageAccount": {
    "name": "<registry_name>01234"
  },
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}
```

### <a name="retrieve-the-registry-credentials-for-pushing-and-pulling-docker-images"></a>Recuperar as credenciais de registro para enviar e receber imagens do Docker

Primeiro, precisamos habilitar o modo admin antes que possamos acessar as credenciais.

```azurecli-interactive
az acr update --name <registry_name> --admin-enabled true
az acr credential show -n <registry_name>
```

Você verá duas senhas. Tome nota do nome de usuário e da primeira senha.
```json
{
  "passwords": [
    {
      "name": "password",
      "value": "<registry_password>"
    },
    {
      "name": "password2",
      "value": "<registry_password2>"
    }
  ],
  "username": "<registry_name>"
}
```

### <a name="upload-your-docker-container-to-azure-container-registry"></a>Carregue o contêiner do Docker no Registro de Contêiner do Azure

```bash
docker login <registry_name>.azurecr.io -u <registry_name> -p "<registry_password>"
docker tag flask-postgresql-sample <registry_name>.azurecr.io/flask-postgresql-sample
docker push <registry_name>.azurecr.io/flask-postgresql-sample
```

## <a name="deploy-the-docker-python-flask-application-to-azure"></a>Implantar o aplicativo Docker Python Flask no Azure
Nesta etapa, você implanta seu aplicativo Python Flask baseado em contêiner do Docker no Serviço de Aplicativo do Azure.

### <a name="create-an-app-service-plan"></a>Criar um plano de Serviço de Aplicativo

Criar um plano do Serviço de Aplicativo com o comando [az appservice plan create](/cli/azure/appservice/plan#create). 

[!INCLUDE [app-service-plan](../../includes/app-service-plan.md)]

O exemplo a seguir cria um plano do Serviço de Aplicativo baseado em Linux chamado `myAppServicePlan` usando o tipo de preço S1:

```azurecli-interactive
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku S1 --is-linux
```

Quando o Plano do Serviço de Aplicativo é criado, a CLI do Azure mostra informações semelhantes ao exemplo a seguir:

```json 
{
  "adminSiteName": null,
  "appServicePlanName": "myAppServicePlan",
  "geoRegion": "West US",
  "hostingEnvironmentProfile": null,
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myAppServicePlan", 
  "kind": "linux",
  "location": "West US",
  "maximumNumberOfWorkers": 10,
  "name": "myAppServicePlan",
  "numberOfSites": 0,
  "perSiteScaling": false,
  "provisioningState": "Succeeded",
  "reserved": true,
  "resourceGroup": "myResourceGroup",
  "sku": {
    "capabilities": null,
    "capacity": 1,
    "family": "S",
    "locations": null,
    "name": "S1",
    "size": "S1",
    "skuCapacity": null,
    "tier": "Standard"
  },
  "status": "Ready",
  "subscription": "00000000-0000-0000-0000-000000000000",
  "tags": null,
  "targetWorkerCount": 0,
  "targetWorkerSizeId": 0,
  "type": "Microsoft.Web/serverfarms",
  "workerTierName": null
}
``` 

### <a name="create-a-web-app"></a>Criar um aplicativo Web

Agora que um plano do Serviço de Aplicativo foi criado, crie um aplicativo Web no `myAppServicePlan` plano do Serviço de Aplicativo. O aplicativo Web fornece um espaço de hospedagem para implantar seu código e fornecer uma URL para exibir o aplicativo implantado. Use o comando [az appservice web create](/cli/azure/appservice/web#create) para criar o aplicativo Web. 

No seguinte comando, substitua `<app_name>` espaço reservado com seu próprio nome de aplicativo exclusivo. O nome exclusivo será usado como a parte do nome de domínio padrão para o aplicativo Web, portanto, o nome deve ser exclusivo entre todos os aplicativos no Azure. Posteriormente, você poderá mapear qualquer entrada DNS personalizada para o aplicativo Web antes de expor para seus usuários. 

```azurecli-interactive
az appservice web create --name <app_name> --resource-group myResourceGroup --plan myAppServicePlan
```

Quando o aplicativo Web tiver sido criado, a CLI do Azure mostrará informações semelhantes ao exemplo a seguir: 

```json 
{ 
    "clientAffinityEnabled": true, 
    "defaultHostName": "<app_name>.azurewebsites.net", 
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Web/sites/<app_name>", 
    "isDefaultContainer": null, 
    "kind": "app", 
    "location": "West Europe", 
    "name": "<app_name>", 
    "repositorySiteName": "<app_name>", 
    "reserved": true, 
    "resourceGroup": "myResourceGroup", 
    "serverFarmId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myAppServicePlan", 
    "state": "Running", 
    "type": "Microsoft.Web/sites", 
} 
```

### <a name="configure-the-database-environment-variables"></a>Configurar as variáveis de ambiente do banco de dados

No início do tutorial, você definiu manualmente variáveis de ambiente para se conectar a seu banco de dados PostgreSQL.

No Serviço de Aplicativo, são definidas as variáveis de ambiente como _configurações do aplicativo_ usando o comando [az appservice web config appsettings update](/cli/azure/appservice/web/config/appsettings#update). 

O seguinte permite que você especifique os detalhes da conexão de banco de dados como configurações do aplicativo. Além disso, usamos a variável `PORT` para especificar que desejamos mapear a PORTA 5000 de nosso contêiner do Docker para receber tráfego HTTP na PORTA 80.

```azurecli-interactive
az appservice web config appsettings update --name <app_name> --resource-group myResourceGroup --settings DBHOST="<postgresql_name>.postgres.database.azure.com" DBUSER="manager@<postgresql_name>" DBPASS="supersecretpass" DBNAME="eventregistration" PORT=5000
```

### <a name="configure-docker-container-deployment"></a>Configurar a implantação do contêiner do Docker 

O Serviço de Aplicativo pode baixar e executar automaticamente um contêiner do Docker.

Use o comando [az appservice web deployment user set](/cli/azure/appservice/web/deployment/user#set) para criar suas credenciais no nível da conta. 

```azurecli
az appservice web config container update --resource-group myResourceGroup --name <app_name> --docker-registry-server-user "<registry_name>" --docker-registry-server-password "<registry_password>" --docker-custom-image-name "<registry_name>.azurecr.io/flask-postgresql-sample" --docker-registry-server-url "https://<registry_name>.azurecr.io"
```

Sempre que atualizarmos o contêiner do Docker ou alterarmos as configurações acima, reinicie o aplicativo para garantir que todas as configurações sejam aplicadas e que o último contêiner seja retirado do registro.

```azurecli-interactive
az appservice web restart --resource-group myResourceGroup --name <app_name>
```

### <a name="browse-to-the-azure-web-app"></a>Navegar até o aplicativo Web do Azure 
Navegue até o aplicativo Web implantado usando o navegador da Web. 

```bash 
http://<app_name>.azurewebsites.net 
```
> [!NOTE]
> Na primeira vez que você acessar o aplicativo Web após fazer uma alteração na configuração do contêiner, aguarde algum tempo para que o contêiner seja baixado e iniciado.

Você verá convidados registrados anteriormente que foram salvos no banco de dados de produção do Azure na etapa anterior.

 ![Aplicativo Python Flask baseado em contêiner do Docker em execução localmente](./media/app-service-web-tutorial-docker-python-postgresql-app/docker-app-deployed.png)

**Parabéns!** Você está executando um aplicativo Python Flask baseado em um contêiner do Docker no Serviço de Aplicativo do Azure.


## <a name="update-data-model-and-redeploy"></a>Atualizar o modelo de dados e reimplantar

Nesta etapa, adicionamos o número de participantes a cada registro de evento atualizando o modelo de Convidado.

Veja a versão de `0.2-migration` com o seguinte comando Git.
```bash
git checkout tags/0.2-migration
```

Essa versão já fez as alterações necessárias nos modos de exibição, controladores e nosso modelo. Ela também inclui uma migração de banco de dados gerada por meio de *alembic* (`flask db migrate`). É possível ver todas as alterações feitas por meio do seguinte comando Git.

```bash
git diff 0.1-initialapp 0.2-migration
```

### <a name="test-your-changes-locally"></a>Testar suas alterações localmente

Execute os comandos a seguir para testar as alterações localmente executando o servidor Flask.

Mac / Linux:
```bash
source venv/bin/activate
cd app
FLASK_APP=app.py;DBHOST="localhost";DBUSER="manager";DBNAME="eventregistration";DBPASS="supersecretpass";flask db upgrade;flask run
```

Navegue até `http://127.0.0.1:5000` no navegador para exibir as alterações. Crie um novo registro fictício.

![Aplicativo Python Flask baseado em contêiner do Docker em execução localmente](./media/app-service-web-tutorial-docker-python-postgresql-app/local-app-v2.png)

### <a name="publish-changes-to-azure"></a>Publicar alterações no Azure

Crie a nova imagem do Docker, envie-a para o registro de contêiner e reinicie o aplicativo.

```bash
docker build -t flask-postgresql-sample .
docker tag flask-postgresql-sample <registry_name>.azurecr.io/flask-postgresql-sample
docker push <registry_name>.azurecr.io/flask-postgresql-sample
az appservice web restart --resource-group myResourceGroup --name <app_name>
```

Navegue até seu aplicativo Web do Azure e teste a nova funcionalidade novamente. Crie outro registro de evento.

```bash 
http://<app_name>.azurewebsites.net 
```

![Aplicativo Docker Python Flask no Serviço de Aplicativo do Azure](./media/app-service-web-tutorial-docker-python-postgresql-app/docker-flask-in-azure.png)

## <a name="manage-your-azure-web-app"></a>Gerenciar seu aplicativo Web do Azure

Vá para o portal do Azure para ver o aplicativo Web que você criou.

Para fazer isso, entre em [https://portal.azure.com](https://portal.azure.com).

No menu à esquerda, clique em **Serviço de Aplicativo**, em seguida, clique no nome do seu aplicativo Web do Azure.

![Navegação do portal para o aplicativo Web do Azure](./media/app-service-web-tutorial-docker-python-postgresql-app/app-resource.png)

Você foi para a _folha_ de seu aplicativo Web (uma página do portal que abre horizontalmente).

Por padrão, a folha de seu aplicativo Web mostra a página **Visão Geral**. Esta página fornece uma visão de como está seu aplicativo. Aqui, você também pode executar tarefas básicas de gerenciamento como procurar, parar, iniciar, reiniciar e excluir. As guias no lado esquerdo da folha mostram as páginas de configuração diferentes que você pode abrir.

![Folha Serviço de Aplicativo no portal do Azure](./media/app-service-web-tutorial-docker-python-postgresql-app/app-mgmt.png)

Essas guias na folha mostram muitos recursos excelentes que você pode adicionar ao seu aplicativo Web. A lista a seguir fornece algumas possibilidades:

* mapear um nome DNS personalizado
* associar um certificado SSL personalizado
* configurar uma implantação contínua
* Escalar vertical e horizontalmente
* adicionar a autenticação do usuário


