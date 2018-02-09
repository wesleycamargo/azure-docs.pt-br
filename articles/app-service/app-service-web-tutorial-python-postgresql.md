---
title: Compilar um aplicativo Web Python e PostgreSQL no Azure | Microsoft Docs
description: "Saiba como fazer com que um aplicativo Python funcione no Azure com conexão a um banco de dados PostgreSQL."
services: app-service\web
documentationcenter: python
author: berndverst
manager: erikre
ms.service: app-service-web
ms.workload: web
ms.devlang: python
ms.topic: tutorial
ms.date: 01/25/2018
ms.author: beverst
ms.custom: mvc
ms.openlocfilehash: 87ed4015e06e0a05e628e8e356b835b9b886eb5c
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/01/2018
---
# <a name="build-a-python-and-postgresql-web-app-in-azure"></a>Criar um aplicativo Web Python e PostgreSQL no Azure

> [!NOTE]
> Este artigo implanta um aplicativo no Serviço de Aplicativo no Windows. Para implantar o serviço de aplicativo em _Linux_, consulte [Compilar um aplicativo Web Docker Python e PostgreSQL no Azure](./containers/tutorial-docker-python-postgresql-app.md).
>

O [Serviço de Aplicativo do Azure](app-service-web-overview.md) fornece um serviço de hospedagem na Web altamente escalonável e com aplicação automática de patches. Este tutorial mostra como criar um aplicativo Web Python básico no Azure. Você conectará esse aplicativo a um banco de dados PostgreSQL. Quando você terminar, terá um aplicativo Python Flask em execução no Serviço de Aplicativo.

![Aplicativo Python Flask no Serviço de Aplicativo no Linux](./media/app-service-web-tutorial-python-postgresql/docker-flask-in-azure.png)

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar um servidor de banco de dados PostgreSQL no Azure
> * Conectar a um aplicativo Python ao MySQL
> * Implantar o aplicativo no Azure
> * Atualizar o modelo de dados e reimplantar o aplicativo
> * Gerenciar o aplicativo no portal do Azure

Você pode seguir as etapas deste tutorial no macOS. As instruções do Linux e do Windows são as mesmas na maioria dos casos, mas as diferenças não são detalhadas neste tutorial.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>pré-requisitos

Para concluir este tutorial:

1. [Instalar o Git](https://git-scm.com/)
1. [Instalar o Python](https://www.python.org/downloads/)
1. [Instale e execute o PostgreSQL](https://www.postgresql.org/download/)

## <a name="test-local-postgresql-installation-and-create-a-database"></a>Testar a instalação do PostgreSQL local e criar um banco de dados

Abra a janela do terminal e execute `psql` para se conectar ao servidor PostgreSQL local.

```bash
sudo -u postgres psql
```

Se sua conexão tiver êxito, o banco de dados PostgreSQL já estará em execução. Caso contrário, certifique-se de que o banco de dados PostgresQL local seja iniciado seguindo as etapas em [Baixe - PostgreSQL Core Distribution](https://www.postgresql.org/download/).

Crie um banco de dados chamado *eventregistration* e configure um usuário de banco de dados separado chamado *manager* com a senha *supersecretpass*.

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

Execute os comandos a seguir para clonar o repositório de exemplo e reverta para a confirmação do aplicativo inicial (antes de `modelChange`).

```bash
git clone https://github.com/Azure-Samples/flask-postgresql-app
cd flask-postgresql-app
git revert modelChange --no-edit
```

Esse repositório de exemplo contém um aplicativo [Flask](http://flask.pocoo.org/). 

### <a name="run-the-application"></a>Executar o aplicativo

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

![Aplicativo Python Flask em execução localmente](./media/app-service-web-tutorial-python-postgresql/local-app.png)

O aplicativo de exemplo Flask armazena dados do usuário no banco de dados. Se você tiver êxito ao registrar um usuário, seu aplicativo está gravando os dados no banco de dados PostgreSQL local.

Para parar o servidor Flask a qualquer momento, digite Ctrl+C no terminal. 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-postgresql-in-azure"></a>Criar PostgreSQL no Azure

Nesta etapa, você cria um banco de dados PostgreSQL no Azure. Quando seu aplicativo é implantado no Azure, ele usa esse banco de dados na nuvem.

### <a name="create-a-resource-group"></a>Criar um grupo de recursos

[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group-no-h.md)] 

### <a name="create-a-postgresql-server"></a>Criar um servidor PostgreSQL

Criar um servidor PostgreSQL com o comando [`az postgres server create`](/cli/azure/postgres/server?view=azure-cli-latest#az_postgres_server_create).

No comando a seguir, substitua um nome do servidor exclusivo para o espaço reservado  *\<postgresql_name >* e um nome de usuário para o espaço reservado  *\<admin_username >*. Esse nome do servidor é usado como parte de seu ponto de extremidade do PostgreSQL (`https://<postgresql_name>.postgres.database.azure.com`), portanto, ele precisa ser exclusivo entre todos os servidores no Azure. O nome de usuário é necessário para a conta do usuário administrador de banco de dados inicial. É solicitado que você escolha uma senha para esse usuário.

```azurecli-interactive
az postgres server create --resource-group myResourceGroup --name <postgresql_name> --admin-user <admin_username>  --storage-size 51200
```

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

### <a name="configure-server-firewall"></a>Configurar o firewall do servidor

Execute o seguinte comando da CLI do Azure para permitir o acesso ao banco de dados a partir de todos os endereços IP.

```azurecli-interactive
az postgres server firewall-rule create --resource-group myResourceGroup --server-name <postgresql_name> --start-ip-address=0.0.0.0 --end-ip-address=255.255.255.255 --name AllowAllIPs
```

A CLI do Azure confirma a criação da regra de firewall com saída semelhante ao exemplo a seguir:

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

### <a name="create-a-production-database-and-user"></a>Criar um banco de dados e um usuário de produção

Crie um usuário de banco de dados com acesso somente a um banco de dados individual. Você usará essas credenciais para evitar fornecer acesso completo ao aplicativo para o servidor.

Conecte-se ao banco de dados (sua senha de administrador será solicitada).

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

### <a name="test-app-locally-with-azure-postgresql"></a>Testar o aplicativo localmente com o Azure PostgreSQL

Voltando para a pasta *aplicativo* do repositório do Github clonado, podemos executar o aplicativo Python Flask atualizando as variáveis de ambiente do banco de dados.

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

Navegue para http://localhost:5000 em um navegador. Clique em **Registrar!** e crie um registro de teste. Agora, você está gravando dados no banco de dados no Azure.

![Aplicativo Python Flask em execução localmente](./media/app-service-web-tutorial-python-postgresql/local-app.png)

## <a name="deploy-to-azure"></a>Implantar no Azure

Nesta etapa, você implanta o aplicativo Python conectado ao PostgreSQL no Serviço de Aplicativo do Azure.

O repositório do Git já contém os seguintes arquivos necessários para executar o aplicativo Web Flask no Serviço de Aplicativo:

- `.deployment`: especifica o script de implantação personalizado a ser executado.
- `deploy.cmd`: o script de implantação. É onde `pip install` é executado.
- `web.config`: especifica o script de ponto de entrada a ser executado em um `httpPlatformHandler` no IIS.
- `run_waitress_server.py`: o script de ponto de entrada. Ele inicia o aplicativo Web Flask em um servidor [ `waitress` ](https://docs.pylonsproject.org/projects/waitress).

### <a name="configure-a-deployment-user"></a>Configurar um usuário de implantação

[!INCLUDE [Configure deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="create-an-app-service-plan"></a>Criar um plano de Serviço de Aplicativo

[!INCLUDE [Create app service plan](../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

<a name="create"></a>
### <a name="create-a-web-app"></a>Criar um aplicativo Web

[!INCLUDE [Create web app no h](../../includes/app-service-web-create-web-app-no-h.md)] 

### <a name="install-python"></a>Instalar o Python

Nesta etapa, você instala o Python 3.6.2 com [extensões de site](https://www.siteextensions.net/packages?q=Tags%3A%22python%22) no Serviço de Aplicativo. Você usará as credenciais configuradas em [Configurar um usuário de implantação](#configure-a-deployment-user) para autenticar no ponto de extremidade REST.

No Cloud Shell, execute o comando a seguir para obter as informações do pacote Python 3.6.2. Substitua  *\<deployment_user >* pelo nome de usuário de implantação que você configurou, e  *\<app_name >* pelo nome do aplicativo. Quando solicitado, use a senha de implantação configurada anteriormente.

```bash
packageinfo=$(curl -u <deployment_user> https://<app_name>.scm.azurewebsites.net/api/extensionfeed/python362x86)
```

No Cloud Shell, execute o comando a seguir para instalar o pacote Python. Substitua  *\<deployment_user >* pelo nome de usuário de implantação que você configurou, e  *\<app_name >* pelo nome do aplicativo. Quando solicitado, use a senha de implantação configurada anteriormente.

```bash
curl -X PUT -u <deployment_user> -H "Content-Type: application/json" -d '$packageinfo' https://<app_name>.scm.azurewebsites.net/api/siteextensions/python362x86
```

Na saída do comando, você pode ver que o Python foi instalado no caminho `D:\home\python362x86\python.exe`.

### <a name="configure-database-settings"></a>Definir configurações de banco de dados

No início do tutorial, você definiu as variáveis de ambiente para se conectar a seu banco de dados PostgreSQL.

No Serviço de Aplicativo, defina as variáveis de ambiente como _configurações do aplicativo_ usando o comando [az webapp config appsettings set](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az_webapp_config_appsettings_set).

O seguinte exemplo especifica os detalhes da conexão de banco de dados como configurações do aplicativo. Substitua  *\<app_name >* pelo nome do aplicativo e  *\<postgresql_name >* pelo nome do servidor PostgreSQL.

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group myResourceGroup --settings DBHOST="<postgresql_name>.postgres.database.azure.com" DBUSER="manager@<postgresql_name>" DBPASS="supersecretpass" DBNAME="eventregistration"
```

### <a name="push-to-azure-from-git"></a>Enviar do Git para o Azure

[!INCLUDE [app-service-plan-no-h](../../includes/app-service-web-git-push-to-azure-no-h.md)]

```bash
Counting objects: 5, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (5/5), done.
Writing objects: 100% (5/5), 489 bytes | 0 bytes/s, done.
Total 5 (delta 3), reused 0 (delta 0)
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id '6c7c716eee'.
remote: Running custom deployment command...
remote: Running deployment command...
remote: Handling node.js deployment.
.
.
.
remote: Deployment successful.
To https://<app_name>.scm.azurewebsites.net/<app_name>.git
 * [new branch]      master -> master
``` 

### <a name="browse-to-the-azure-web-app"></a>Navegar até o aplicativo Web do Azure 

Navegue até o aplicativo Web implantado usando o navegador da Web. 

```bash 
http://<app_name>.azurewebsites.net 
```

Você verá convidados registrados anteriormente que foram salvos no banco de dados de produção do Azure na etapa anterior.

![Aplicativo Python Flask em execução localmente](./media/app-service-web-tutorial-python-postgresql/docker-app-deployed.png)

**Parabéns!** Você está executando um aplicativo Python Flask no Serviço de Aplicativo do Azure.

## <a name="update-data-model-and-redeploy"></a>Atualizar o modelo de dados e reimplantar

Nesta etapa, você adiciona o número de participantes a cada registro de evento atualizando o modelo `Guest`.

Confira os arquivos sinalizados pela confirmação `modelChange`:

```bash
git checkout modelChange -- *
```

Essa versão já fez as alterações necessárias nos modos de exibição, controladores e modelo. Ela também inclui uma migração de banco de dados gerada por meio de *alembic* (`flask db migrate`). Você pode ver as alterações em todos os arquivos no [modo de exibição de confirmação do GitHub](https://github.com/Azure-Samples/flask-postgresql-app/commit/139a53023688631c3cc2caefd70086f4722ecd7e).

### <a name="test-your-changes-locally"></a>Testar suas alterações localmente

Execute os comandos a seguir para testar as alterações localmente executando o servidor Flask.

```bash
source venv/bin/activate
cd app
FLASK_APP=app.py DBHOST="localhost" DBUSER="manager" DBNAME="eventregistration" DBPASS="supersecretpass" flask db upgrade
FLASK_APP=app.py DBHOST="localhost" DBUSER="manager" DBNAME="eventregistration" DBPASS="supersecretpass" flask run
```

Navegue até http://localhost:5000 no navegador para ver as alterações. Crie um registro de teste.

![Aplicativo Python Flask em execução localmente](./media/app-service-web-tutorial-python-postgresql/local-app-v2.png)

### <a name="publish-changes-to-azure"></a>Publicar alterações no Azure

Na janela do terminal local, confirme todas as alterações no Git e envie por push as alterações do código para o Azure.

```bash
git add .
git commit -m "updated data model"
git push azure master
```

Navegue até seu aplicativo Web do Azure e teste a nova funcionalidade novamente. Crie outro registro de evento.

```bash 
http://<app_name>.azurewebsites.net 
```

![Aplicativo Python Flask no Serviço de Aplicativo do Azure](./media/app-service-web-tutorial-python-postgresql/docker-flask-in-azure.png)

## <a name="manage-your-azure-web-app"></a>Gerenciar seu aplicativo Web do Azure

Vá para o [portal do Azure](https://portal.azure.com) para ver o aplicativo Web que você criou.

No menu à esquerda, clique em **Serviço de Aplicativos** e, em seguida, clique no nome do seu aplicativo Web do Azure.

![Navegação do portal para o aplicativo Web do Azure](./media/app-service-web-tutorial-python-postgresql/app-resource.png)

Por padrão, o portal mostra a página **Visão geral** do seu aplicativo Web . Esta página fornece uma visão de como está seu aplicativo. Aqui, você também pode executar tarefas básicas de gerenciamento como procurar, parar, iniciar, reiniciar e excluir. As guias no lado esquerdo da página mostram as páginas de configuração diferentes que você pode abrir.

![Página Serviço de Aplicativo no portal do Azure](./media/app-service-web-tutorial-python-postgresql/app-mgmt.png)

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Próximas etapas

Vá para o próximo tutorial para saber como mapear um nome DNS personalizado para o seu aplicativo Web.

> [!div class="nextstepaction"]
> [Mapear um nome DNS personalizado existente para aplicativos Web do Azure](app-service-web-tutorial-custom-domain.md)
