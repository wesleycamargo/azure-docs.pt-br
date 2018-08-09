---
title: Compilar um aplicativo Web Python e PostgreSQL no Serviço de Aplicativo do Azure | Microsoft Docs
description: Saiba como executar um aplicativo Python alimentado por dados no Azure com conexão a um banco de dados PostgreSQL.
services: app-service\web
documentationcenter: python
author: berndverst
manager: jeconnoc
ms.service: app-service-web
ms.workload: web
ms.devlang: python
ms.topic: tutorial
ms.date: 07/13/2018
ms.author: beverst;cephalin
ms.custom: mvc
ms.openlocfilehash: ce84498ab89891bd7b96cfcc6b0c7ac029c93cbd
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/02/2018
ms.locfileid: "39423072"
---
# <a name="build-a-docker-python-and-postgresql-web-app-in-azure"></a>Compilar um aplicativo Web Docker Python e PostgreSQL no Azure

O Aplicativo Web para Contêineres fornece um serviço de hospedagem na Web altamente escalonável e com aplicação automática de patches. Este tutorial mostra como criar um aplicativo Web Python alimentado por dados usando o PostgreSQL como back-end de banco de dados. Quando terminar, você terá um aplicativo Python Flask em execução dentro de um contêiner do Docker no [Serviço de Aplicativo no Linux](app-service-linux-intro.md).

![Aplicativo Python Flask do Docker no Serviço de Aplicativo no Linux](./media/tutorial-docker-python-postgresql-app/docker-flask-in-azure.png)

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar um servidor de banco de dados PostgreSQL no Azure
> * Conectar um aplicativo Python ao PostgreSQL
> * Implantar o aplicativo no Azure
> * Atualizar o modelo de dados e reimplantar o aplicativo
> * Gerenciar o aplicativo no portal do Azure

Você pode seguir as etapas deste artigo no macOS. As instruções do Linux e do Windows são as mesmas na maioria dos casos, mas as diferenças não são detalhadas neste tutorial.
 
[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial:

1. [Instalar o Git](https://git-scm.com/)
1. [Instalar o Python](https://www.python.org/downloads/)
1. [Instale e execute o PostgreSQL](https://www.postgresql.org/download/)
1. [Instale o Docker Community Edition](https://www.docker.com/community-edition)

## <a name="test-local-postgresql-installation-and-create-a-database"></a>Testar a instalação do PostgreSQL local e criar um banco de dados

Em uma janela do terminal local, execute `psql` para se conectar ao servidor PostgreSQL local.

```bash
sudo -u postgres psql
```

Se sua conexão tiver êxito, o banco de dados PostgreSQL já estará em execução. Caso contrário, certifique-se de que o banco de dados PostgresQL local seja iniciado seguindo as etapas em [Baixe - PostgreSQL Core Distribution](https://www.postgresql.org/download/).

Crie um banco de dados chamado *eventregistration* e configure um usuário de banco de dados separado chamado *manager* com a senha *supersecretpass*.

```sql
CREATE DATABASE eventregistration;
CREATE USER manager WITH PASSWORD 'supersecretpass';
GRANT ALL PRIVILEGES ON DATABASE eventregistration TO manager;
```

Digite `\q` para sair do cliente do PostgreSQL. 

<a name="step2"></a>

## <a name="create-local-python-app"></a>Criar um aplicativo Python local

Nesta etapa, você configura o projeto Python Flask local.

### <a name="clone-the-sample-app"></a>Clonar o aplicativo de exemplo

Abra a janela do terminal e `CD` para um diretório de trabalho.

Execute os comandos a seguir para clonar o repositório de exemplo e vá até a versão *0.1-initialapp*.

```bash
git clone https://github.com/Azure-Samples/docker-flask-postgres.git
cd docker-flask-postgres
git checkout tags/0.1-initialapp
```

Esse repositório de exemplo contém um aplicativo [Flask](http://flask.pocoo.org/). 

### <a name="run-the-app-locally"></a>Executar o aplicativo localmente

Instale os pacotes necessários e inicie o aplicativo.

```bash
pip install virtualenv
virtualenv venv
source venv/bin/activate
pip install -r requirements.txt
cd app
FLASK_APP=app.py DBHOST="localhost" DBUSER="manager" DBNAME="eventregistration" DBPASS="supersecretpass" flask db upgrade
FLASK_APP=app.py DBHOST="localhost" DBUSER="manager" DBNAME="eventregistration" DBPASS="supersecretpass" flask run
```

Quando o aplicativo estiver totalmente carregado, você verá algo semelhante à seguinte mensagem:

```bash
INFO  [alembic.runtime.migration] Context impl PostgresqlImpl.
INFO  [alembic.runtime.migration] Will assume transactional DDL.
INFO  [alembic.runtime.migration] Running upgrade  -> 791cd7d80402, empty message
 * Serving Flask app "app"
 * Running on http://127.0.0.1:5000/ (Press CTRL+C to quit)
```

Navegue até `http://localhost:5000` em um navegador. Clique em **Registrar!** e crie um usuário de teste.

![Aplicativo Python Flask em execução localmente](./media/tutorial-docker-python-postgresql-app/local-app.png)

O aplicativo de exemplo Flask armazena dados do usuário no banco de dados. Se você tiver êxito ao registrar um usuário, seu aplicativo está gravando os dados no banco de dados PostgreSQL local.

Para parar o servidor Flask a qualquer momento, digite Ctrl+C no terminal. 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-production-postgresql-database"></a>Criar um banco de dados PostgreSQL de produção

Nesta etapa, você cria um banco de dados PostgreSQL no Azure. Quando seu aplicativo é implantado no Azure, ele usa esse banco de dados na nuvem.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

### <a name="create-a-resource-group"></a>Criar um grupo de recursos

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-linux-no-h.md)] 

### <a name="create-an-azure-database-for-postgresql-server"></a>Criar um Banco de Dados do Azure para o servidor PostgreSQL

Crie um servidor PostgreSQL com o comando [ `az postgres server create` ](/cli/azure/postgres/server?view=azure-cli-latest#az-postgres-server-create) no Cloud Shell.

No comando de exemplo a seguir, substitua  *\<postgresql_name>* por um nome de servidor exclusivo e substitua  *\<admin_username >* e  *\<admin_password >* pelas credenciais do usuário desejado. Esse nome do servidor é usado como parte de seu ponto de extremidade do PostgreSQL (`https://<postgresql_name>.postgres.database.azure.com`), portanto, ele precisa ser exclusivo entre todos os servidores no Azure. As credenciais do usuário são da conta de usuário do administrador do banco de dados. 

```azurecli-interactive
az postgres server create --resource-group myResourceGroup --name <postgresql_name> --location "West Europe" --admin-user <admin_username> --admin-password <admin_password> --sku-name GP_Gen4_2
```

Quando o servidor PostgreSQL do Banco de Dados do Azure for criado, a CLI do Azure mostrará informações semelhantes ao exemplo a seguir:

```json
{
  "administratorLogin": "<admin_username>",
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

### <a name="create-a-firewall-rule-for-the-postgresql-server"></a>Criar uma regra de firewall para o servidor PostgreSQL

No Cloud Shell, execute o comando da CLI do Azure a seguir para permitir o acesso ao banco de dados a partir de todos os endereços IP. Quando o IP inicial e o IP final estiverem definidos como `0.0.0.0`, o firewall estará aberto somente para outros recursos do Azure. 

```azurecli-interactive
az postgres server firewall-rule create --resource-group myResourceGroup --server-name <postgresql_name> --start-ip-address=0.0.0.0 --end-ip-address=0.0.0.0 --name AllowAzureIPs
```

> [!TIP] 
> Você pode ser ainda mais restritivo na regra de firewall ao [usar somente os endereços de IP de saída que seu aplicativo usa](../app-service-ip-addresses.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#find-outbound-ips).
>

No Cloud Shell, execute o comando novamente para permitir acesso ao banco de dados no computador local, substituindo  *\<you_ip_address>* por [seu endereço IP IPv4 local](https://whatismyipaddress.com/). 

```azurecli-interactive
az postgres server firewall-rule create --resource-group myResourceGroup --server-name <postgresql_name> --start-ip-address=<you_ip_address> --end-ip-address=<you_ip_address> --name AllowLocalClient
```

## <a name="connect-python-app-to-production-database"></a>Conectar o aplicativo Python ao banco de dados de produção

Nesta etapa, você conecta seu aplicativo de exemplo Flask ao servidor do Banco de Dados do Azure para PostgreSQL criado.

### <a name="create-empty-database-and-user-access"></a>Criar acesso de usuário e banco de dados vazio

No Cloud Shell, conecte-se ao banco de dados executando `psql`. Quando for solicitada a senha de administrador, use a mesma senha especificada em [Criar um servidor do Banco de Dados do Azure para PostgreSQL](#create-an-azure-database-for-postgresql-server).

```bash
psql -h <postgresql_name>.postgres.database.azure.com -U <my_admin_username>@<postgresql_name> postgres
```

Crie o banco de dados e o usuário usando a CLI do PostgreSQL.

```bash
CREATE DATABASE eventregistration;
CREATE USER manager WITH PASSWORD 'supersecretpass';
GRANT ALL PRIVILEGES ON DATABASE eventregistration TO manager;
```

Digite `\q` para sair do cliente do PostgreSQL.

### <a name="test-app-connectivity-to-production-database"></a>Testar a conectividade do aplicativo com o banco de dados de produção

De volta na janela do terminal local, execute os comandos a seguir para executar a migração do banco de dados e do servidor Flask.

```bash
FLASK_APP=app.py DBHOST="<postgresql_name>.postgres.database.azure.com" DBUSER="manager@<postgresql_name>" DBNAME="eventregistration" DBPASS="supersecretpass" flask db upgrade
FLASK_APP=app.py DBHOST="<postgresql_name>.postgres.database.azure.com" DBUSER="manager@<postgresql_name>" DBNAME="eventregistration" DBPASS="supersecretpass" flask run
```

Quando o aplicativo estiver totalmente carregado, você verá algo semelhante à seguinte mensagem:

```bash
INFO  [alembic.runtime.migration] Context impl PostgresqlImpl.
INFO  [alembic.runtime.migration] Will assume transactional DDL.
INFO  [alembic.runtime.migration] Running upgrade  -> 791cd7d80402, empty message
 * Serving Flask app "app"
 * Running on http://127.0.0.1:5000/ (Press CTRL+C to quit)
```

Navegue até http://localhost:5000 em um navegador. Clique em **Registrar!** e crie um registro de teste. Agora, você está gravando dados no banco de dados no Azure.

![Aplicativo Python Flask em execução localmente](./media/tutorial-docker-python-postgresql-app/local-app.png)

## <a name="upload-app-to-a-container-registry"></a>Carregar aplicativo em um registro de contêiner

Nesta etapa, crie uma imagem do Docker e carregue-a em Registro de Contêiner do Azure. Você também pode usar registros populares como o Hub do Docker.

### <a name="build-the-docker-image-and-test-it"></a>Criar a imagem do Docker e testá-la

Na janela do terminal local, crie a imagem do Docker.

```bash
cd ..
docker build -t flask-postgresql-sample .
```

O Docker exibe uma confirmação de que criou a imagem com êxito.

```bash
Successfully built 7548f983a36b
```

Na raiz do repositório, adicione um arquivo de variável de ambiente chamado _db.env_ e, em seguida, adicione as seguintes variáveis de ambiente de banco de dados a ele. O aplicativo se conecta ao banco de dados de produção do Banco de dados do Azure para PostgreSQL.

```text
DBHOST=<postgresql_name>.postgres.database.azure.com
DBUSER=manager@<postgresql_name>
DBNAME=eventregistration
DBPASS=supersecretpass
```

Execute a imagem localmente em um contêiner do Docker. O seguinte comando especifica o arquivo de variável de ambiente e mapeia a porta 5000 padrão do Flask para nossa porta 5000 local.

```bash
docker run -it --env-file db.env -p 5000:5000 flask-postgresql-sample
```

A saída é semelhante ao que você viu anteriormente. No entanto, a migração de banco de dados inicial não precisa mais ser executada e, portanto, é ignorada.

```bash
INFO  [alembic.runtime.migration] Context impl PostgresqlImpl.
INFO  [alembic.runtime.migration] Will assume transactional DDL.
 * Serving Flask app "app"
 * Running on http://0.0.0.0:5000/ (Press CTRL+C to quit)
```

O banco de dados já contém o registro que você criou anteriormente.

![Aplicativo Python Flask baseado em contêiner do Docker em execução localmente](./media/tutorial-docker-python-postgresql-app/local-docker.png)

Agora que você verificou que o contêiner funciona localmente, exclua _db.env_. No Serviço de Aplicativo do Azure, você usará as configurações do aplicativo para definir as variáveis de ambiente.  

### <a name="create-an-azure-container-registry"></a>Criar um Registro de Contêiner do Azure

No Cloud Shell, crie um registro no Registro de Contêiner do Azure com o comando a seguir. Substitua  *\<registry_name>* por um nome exclusivo do registro.

```azurecli-interactive
az acr create --name <registry_name> --resource-group myResourceGroup --location "West US" --sku Basic
```

### <a name="retrieve-registry-credentials"></a>Recuperar credenciais de registro

No Cloud Shell, execute os comandos a seguir para recuperar as credenciais de registro. Você precisará delas para enviar as imagens por push e efetuar pull.

```azurecli-interactive
az acr update --name <registry_name> --admin-enabled true
az acr credential show -n <registry_name>
```

Na saída, você verá duas senhas. Anote o nome de usuário (que é o nome do registro por padrão) e a primeira senha.

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

### <a name="upload-docker-image-to-registry"></a>Carregar imagem do Docker no registro

Na janela do terminal local, entre no novo registro com `docker`. Mediante solicitação, forneça a senha que você recuperou.

```bash
docker login <registry_name>.azurecr.io -u <registry_name>
```

Envie sua imagem do Docker por push para o Registro.

```bash
docker tag flask-postgresql-sample <registry_name>.azurecr.io/flask-postgresql-sample
docker push <registry_name>.azurecr.io/flask-postgresql-sample
```

## <a name="create-web-app-with-uploaded-image"></a>Criar aplicativo Web com imagem carregada

Nesta etapa, crie um aplicativo no Serviço de Aplicativo do Azure e configure-o para usar a imagem do Docker carregada no Registro de Contêiner do Azure.

### <a name="create-an-app-service-plan"></a>Criar um plano de Serviço de Aplicativo

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

### <a name="create-a-web-app"></a>Criar um aplicativo Web

No Cloud Shell, crie um aplicativo Web no plano do Serviço de Aplicativo do *myAppServicePlan* com o comando [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create).

No seguinte comando, substitua o espaço reservado *\<app_name>* por um nome de aplicativo exclusivo. Esse nome é parte da URL padrão para o aplicativo Web, portanto, o nome precisa ser exclusivo em todos os aplicativos no Serviço de Aplicativo do Azure.

```azurecli-interactive
az webapp create --name <app_name> --resource-group myResourceGroup --plan myAppServicePlan --deployment-container-image-name "<registry_name>.azurecr.io/flask-postgresql-sample"
```

Quando o aplicativo Web tiver sido criado, a CLI do Azure mostrará informações semelhantes ao exemplo a seguir:

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
  ...
  < Output has been truncated for readability >
}
```

### <a name="configure-environment-variables"></a>Configurar variáveis de ambiente

No início do tutorial, você definiu as variáveis de ambiente para se conectar a seu banco de dados PostgreSQL.

No Serviço de Aplicativo, defina as variáveis de ambiente como _configurações do aplicativo_ usando o comando [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set).

O seguinte exemplo especifica os detalhes da conexão de banco de dados como configurações do aplicativo. Ele também usa a variável *WEBSITES_PORT* para a porta de contêiner 5000, que permite que o contêiner receba o tráfego HTTP na porta 80.

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group myResourceGroup --settings DBHOST="<postgresql_name>.postgres.database.azure.com" DBUSER="manager@<postgresql_name>" DBPASS="supersecretpass" DBNAME="eventregistration" WEBSITES_PORT=5000
```

### <a name="configure-custom-container-deployment"></a>Configurar implantação de contêiner personalizada

Mesmo que você já tenha especificado o nome da imagem de contêiner, ainda precisará especificar a URL de registro personalizada e as credenciais do usuário. No Cloud Shell, execute o comando [az webapp config container set](/cli/azure/webapp/config/container?view=azure-cli-latest#az-webapp-config-container-set).

```azurecli-interactive
az webapp config container set --resource-group myResourceGroup --name <app_name> --docker-registry-server-user "<registry_name>" --docker-registry-server-password "<registry_password>" --docker-registry-server-url "https://<registry_name>.azurecr.io"
```

No Cloud Shell, reinicie o aplicativo. Reiniciar garante que todas as configurações serão aplicadas e o último contêiner será extraído do registro.

```azurecli-interactive
az webapp restart --resource-group myResourceGroup --name <app_name>
```

### <a name="browse-to-the-azure-web-app"></a>Navegar até o aplicativo Web do Azure 

Navegue até o aplicativo Web implantado. 

```bash 
http://<app_name>.azurewebsites.net 
```

> [!NOTE]
> O aplicativo Web leva algum tempo para ser iniciado porque o contêiner tem que ser baixado e executado quando o aplicativo é solicitado pela primeira vez. Se a princípio você vir um erro após um longo tempo, atualize a página.

Você verá convidados registrados anteriormente que foram salvos no banco de dados de produção do Azure na etapa anterior.

![Aplicativo Python Flask baseado em contêiner do Docker em execução localmente](./media/tutorial-docker-python-postgresql-app/docker-app-deployed.png)

**Parabéns!** Você está executando um aplicativo Python no Aplicativo Web para Contêineres.

## <a name="update-data-model-and-redeploy"></a>Atualizar o modelo de dados e reimplantar

Nesta etapa, você adiciona o número de participantes a cada registro de evento atualizando o modelo `Guest`.

Na janela do terminal local, confira a versão *0.2-migration* com o seguinte comando git:

```bash
git checkout tags/0.2-migration
```

Essa versão já fez as alterações necessárias nos modos de exibição, nos controladores e no modelo. Ela também inclui uma migração de banco de dados gerada por meio de *alembic* (`flask db migrate`). É possível ver todas as alterações feitas por meio do seguinte comando Git:

```bash
git diff 0.1-initialapp 0.2-migration
```

### <a name="test-your-changes-locally"></a>Testar suas alterações localmente

Na janela do terminal local, execute os comandos a seguir para testar as alterações localmente executando o servidor Flask.

```bash
source venv/bin/activate
cd app
FLASK_APP=app.py DBHOST="<postgresql_name>.postgres.database.azure.com" DBUSER="manager@<postgresql_name>" DBNAME="eventregistration" DBPASS="supersecretpass" flask db upgrade
FLASK_APP=app.py DBHOST="<postgresql_name>.postgres.database.azure.com" DBUSER="manager@<postgresql_name>" DBNAME="eventregistration" DBPASS="supersecretpass" flask run
```

Navegue até http://localhost:5000 no navegador para exibir as alterações. Crie um registro de teste.

![Aplicativo Python Flask baseado em contêiner do Docker em execução localmente](./media/tutorial-docker-python-postgresql-app/local-app-v2.png)

### <a name="publish-changes-to-azure"></a>Publicar alterações no Azure

Na janela do terminal local, crie a nova imagem do Docker e envie por push ao registro.

```bash
cd ..
docker build -t flask-postgresql-sample .
docker tag flask-postgresql-sample <registry_name>.azurecr.io/flask-postgresql-sample
docker push <registry_name>.azurecr.io/flask-postgresql-sample
```

No Cloud Shell, reinicie o aplicativo para fazer com que o último contêiner tenha o pull feito do registro.

```azurecli-interactive
az webapp restart --resource-group myResourceGroup --name <app_name>
```

Navegue até seu aplicativo Web do Azure e teste a nova funcionalidade novamente. Crie outro registro de evento.

```bash 
http://<app_name>.azurewebsites.net 
```

![Aplicativo Docker Python Flask no Serviço de Aplicativo do Azure](./media/tutorial-docker-python-postgresql-app/docker-flask-in-azure.png)

## <a name="manage-your-azure-web-app"></a>Gerenciar seu aplicativo Web do Azure

Vá para o [portal do Azure](https://portal.azure.com) para ver o aplicativo Web que você criou.

No menu à esquerda, clique em **Serviço de Aplicativos** e, em seguida, clique no nome do seu aplicativo Web do Azure.

![Navegação do portal para o aplicativo Web do Azure](./media/tutorial-docker-python-postgresql-app/app-resource.png)

Por padrão, o portal mostra a página **Visão geral** do seu aplicativo Web . Esta página fornece uma visão de como está seu aplicativo. Aqui, você também pode executar tarefas básicas de gerenciamento como procurar, parar, iniciar, reiniciar e excluir. As guias no lado esquerdo da página mostram as páginas de configuração diferentes que você pode abrir.

![Página Serviço de Aplicativo no portal do Azure](./media/tutorial-docker-python-postgresql-app/app-mgmt.png)

## <a name="next-steps"></a>Próximas etapas

Vá para o próximo tutorial para saber como mapear um nome DNS personalizado para o seu aplicativo Web.

> [!div class="nextstepaction"]
> [Mapear um nome DNS personalizado existente para aplicativos Web do Azure](../app-service-web-tutorial-custom-domain.md)
