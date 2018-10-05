---
title: Compilar um aplicativo Web Python e PostgreSQL no Serviço de Aplicativo do Azure | Microsoft Docs
description: Saiba como executar um aplicativo Python alimentado por dados no Azure com conexão a um banco de dados PostgreSQL.
services: app-service\web
documentationcenter: python
author: cephalin
manager: jeconnoc
ms.service: app-service-web
ms.workload: web
ms.devlang: python
ms.topic: tutorial
ms.date: 09/28/2018
ms.author: beverst;cephalin
ms.custom: mvc
ms.openlocfilehash: f4ce197d541b8573e38fd85dcebb575c8ee99f59
ms.sourcegitcommit: 7c4fd6fe267f79e760dc9aa8b432caa03d34615d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/28/2018
ms.locfileid: "47435689"
---
# <a name="build-a-docker-python-and-postgresql-web-app-in-azure"></a>Compilar um aplicativo Web Docker Python e PostgreSQL no Azure

O [Serviço de Aplicativo no Linux](app-service-linux-intro.md) fornece um serviço de hospedagem na Web altamente escalonável e com aplicação automática de patches. Este tutorial mostra como criar um aplicativo Web Python alimentado por dados usando o PostgreSQL como back-end de banco de dados. Quando terminar, você terá um aplicativo Python Flask em execução dentro de um contêiner do Docker no Serviço de Aplicativo no Linux.

![Aplicativo Python Flask do Docker no Serviço de Aplicativo no Linux](./media/tutorial-python-postgresql-app/docker-flask-in-azure.png)

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar um servidor de banco de dados PostgreSQL no Azure
> * Conectar um aplicativo Python ao PostgreSQL
> * Implantar o aplicativo no Azure
> * Exibir logs de diagnóstico
> * Atualizar o modelo de dados e reimplantar o aplicativo
> * Gerenciar o aplicativo no portal do Azure

Você pode seguir as etapas deste artigo no macOS. As instruções do Linux e do Windows são as mesmas na maioria dos casos, mas as diferenças não são detalhadas neste tutorial.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial:

1. [Instalar o Git](https://git-scm.com/)
1. [Instalar o Python](https://www.python.org/downloads/)
1. [Instale e execute o PostgreSQL](https://www.postgresql.org/download/)

## <a name="test-local-postgresql-installation-and-create-a-database"></a>Testar a instalação do PostgreSQL local e criar um banco de dados

Em uma janela do terminal local, execute `psql` para se conectar ao servidor PostgreSQL local.

```bash
sudo -u postgres psql postgres
```

Se você receber uma mensagem de erro semelhante a `unknown user: postgres`, talvez sua instalação do PostgreSQL esteja configurada com seu nome de usuário conectado. Experimente o seguinte comando em vez disso.

```bash
psql postgres
```

Se sua conexão tiver êxito, o banco de dados PostgreSQL já estará em execução. Se não, verifique se o banco de dados PostgresQL local foi iniciado seguindo as instruções para seu sistema operacional em [Downloads – Distribuição Principal do PostgreSQL](https://www.postgresql.org/download/).

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

Execute os comandos a seguir para clonar o repositório de exemplo.

```bash
git clone https://github.com/Azure-Samples/flask-postgresql-app.git
cd flask-postgresql-app
```

Esse repositório de exemplo contém um aplicativo [Flask](http://flask.pocoo.org/).

### <a name="run-the-app-locally"></a>Executar o aplicativo localmente

Instale os pacotes necessários e inicie o aplicativo.

```bash
# Bash
python3 -m venv venv
source venv/bin/activate
pip install -r requirements.txt
cd app
FLASK_APP=app.py DBHOST="localhost" DBUSER="manager" DBNAME="eventregistration" DBPASS="supersecretpass" flask db upgrade
FLASK_APP=app.py DBHOST="localhost" DBUSER="manager" DBNAME="eventregistration" DBPASS="supersecretpass" flask run

# PowerShell
pip install virtualenv
virtualenv venv
source venv/bin/activate
pip install -r requirements.txt
cd app
Set-Item Env:FLASK_APP ".\app.py"
DBHOST="localhost" DBUSER="manager" DBNAME="eventregistration" DBPASS="supersecretpass" flask db upgrade
DBHOST="localhost" DBUSER="manager" DBNAME="eventregistration" DBPASS="supersecretpass" flask run
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

![Aplicativo Python Flask em execução localmente](./media/tutorial-python-postgresql-app/local-app.png)

O aplicativo de exemplo Flask armazena dados do usuário no banco de dados. Se você tiver êxito ao registrar um usuário, seu aplicativo está gravando os dados no banco de dados PostgreSQL local.

Para parar o servidor Flask a qualquer momento, digite Ctrl+C no terminal.

## <a name="create-a-production-postgresql-database"></a>Criar um banco de dados PostgreSQL de produção

Nesta etapa, você cria um banco de dados PostgreSQL no Azure. Quando seu aplicativo é implantado no Azure, ele usa esse banco de dados na nuvem.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

### <a name="create-a-resource-group"></a>Criar um grupo de recursos

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-linux-no-h.md)]

### <a name="create-an-azure-database-for-postgresql-server"></a>Criar um Banco de Dados do Azure para o servidor PostgreSQL

Crie um servidor PostgreSQL com o comando [ `az postgres server create` ](/cli/azure/postgres/server?view=azure-cli-latest#az-postgres-server-create) no Cloud Shell.

No comando de exemplo a seguir, substitua  *\<postgresql_name>* por um nome de servidor exclusivo e substitua  *\<admin_username >* e  *\<admin_password >* pelas credenciais do usuário desejado. As credenciais do usuário são da conta de usuário do administrador do banco de dados. Esse nome do servidor é usado como parte de seu ponto de extremidade do PostgreSQL (`https://<postgresql_name>.postgres.database.azure.com`), portanto, ele precisa ser exclusivo entre todos os servidores no Azure.

```azurecli-interactive
az postgres server create --resource-group myResourceGroup --name <postgresql_name> --location "West Europe" --admin-user <admin_username> --admin-password <admin_password> --sku-name B_Gen4_1
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
    "capacity": 1,
    "family": "Gen4",
    "name": "B_Gen4_1",
    "size": null,
    "tier": "Basic"
  },
  < JSON data removed for brevity. >
}
```

> [!NOTE]
> Lembre-se de \<admin_username> e \<admin_password> para mais tarde. Você precisa que eles entrem no servidor Postgre e seus bancos de dados.


### <a name="create-firewall-rules-for-the-postgresql-server"></a>Criar regras de firewall para o servidor PostgreSQL

No Cloud Shell, execute os seguintes comandos da CLI do Azure para permitir acesso ao banco de dados de recurso do Azure.

```azurecli-interactive
az postgres server firewall-rule create --resource-group myResourceGroup --server-name <postgresql_name> --start-ip-address=0.0.0.0 --end-ip-address=0.0.0.0 --name AllowAllAzureIPs
```

> [!NOTE]
> Essa configuração permite conexões de rede de todos os IPs na rede do Azure. Para uso em produção, tente configurar as regras de firewall mais restritivas possíveis [usando somente os endereços IP de saída que seu aplicativo usa](../app-service-ip-addresses.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#find-outbound-ips).

No Cloud Shell, execute o comando novamente para permitir acesso do seu computador local, substituindo  *\<you_ip_address>* por [seu endereço IP IPv4 local](http://www.whatsmyip.org/).

```azurecli-interactive
az postgres server firewall-rule create --resource-group myResourceGroup --server-name <postgresql_name> --start-ip-address=<your_ip_address> --end-ip-address=<your_ip_address> --name AllowLocalClient
```

## <a name="connect-python-app-to-production-database"></a>Conectar o aplicativo Python ao banco de dados de produção

Nesta etapa, você conecta seu aplicativo de exemplo Flask ao servidor do Banco de Dados do Azure para PostgreSQL criado.

### <a name="create-empty-database-and-user-access"></a>Criar acesso de usuário e banco de dados vazio

Na janela do terminal local, conecte-se ao banco de dados, executando o comando a seguir. Quando for solicitada a senha de administrador, use a mesma senha especificada em [Criar um servidor do Banco de Dados do Azure para PostgreSQL](#create-an-azure-database-for-postgresql-server).

```bash
psql -h <postgresql_name>.postgres.database.azure.com -U <my_admin_username>@<postgresql_name> postgres
```

Assim como em seu servidor local do Postgres, crie o banco de dados e o usuário no servidor Postgres do Azure.

```bash
CREATE DATABASE eventregistration;
CREATE USER manager WITH PASSWORD 'supersecretpass';
GRANT ALL PRIVILEGES ON DATABASE eventregistration TO manager;
```

Digite `\q` para sair do cliente do PostgreSQL.

> [!NOTE]
> É uma melhor prática criar usuários de banco de dados com permissões restritas para aplicativos específicos, em vez de usar o usuário administrador. Neste exemplo, o usuário `manager` tem privilégios totais _apenas_ para o banco de dados `eventregistration`.

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

![Aplicativo Python Flask em execução localmente](./media/tutorial-python-postgresql-app/local-app.png)

## <a name="deploy-to-azure"></a>Implantar no Azure

Nesta etapa, você implanta o aplicativo Python conectado ao Postgres no Serviço de Aplicativo do Azure.

### <a name="configure-repository"></a>Configurar repositório

O mecanismo de implantação do Git no Serviço de Aplicativo invoca automação de `pip` quando há um _application.py_ na raiz do repositório. Neste tutorial, você vai permitir que o mecanismo de implantação execute a automação para você. Na janela do terminal local, navegue até a raiz do repositório, crie um _application.py_ simulado e confirme suas alterações.

```bash
cd ..
touch application.py
git add .
git commit -m "ensure azure automation"
```

### <a name="configure-a-deployment-user"></a>Configurar um usuário de implantação

[!INCLUDE [Configure deployment user](../../../includes/configure-deployment-user-no-h.md)]

### <a name="create-an-app-service-plan"></a>Criar um plano de Serviço de Aplicativo 

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

### <a name="create-a-web-app"></a>Criar um aplicativo Web 

[!INCLUDE [Create web app](../../../includes/app-service-web-create-web-app-python-linux-no-h.md)]

### <a name="configure-environment-variables"></a>Configurar variáveis de ambiente

No início do tutorial, você definiu as variáveis de ambiente para se conectar a seu banco de dados PostgreSQL.

No Serviço de Aplicativo, defina as variáveis de ambiente como _configurações do aplicativo_ usando o comando [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) no Cloud Shell.

O seguinte exemplo especifica os detalhes da conexão de banco de dados como configurações do aplicativo. 

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group myResourceGroup --settings DBHOST="<postgresql_name>.postgres.database.azure.com" DBUSER="manager@<postgresql_name>" DBPASS="supersecretpass" DBNAME="eventregistration"
```

### <a name="push-to-azure-from-git"></a>Enviar do Git para o Azure

[!INCLUDE [app-service-plan-no-h](../../../includes/app-service-web-git-push-to-azure-no-h.md)]

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

### <a name="configure-entry-point"></a>Configurar o ponto de entrada

Por padrão, a imagem interna procura um _wsgi.py_ ou _application.py_ na raiz do diretório como ponto de entrada, mas o ponto de entrada é _app/app.py_. O _application.py_ adicionado anteriormente está vazio e não faz nada.

No Cloud Shell, execute o comando [`az webapp config set`](/cli/azure/webapp/config?view=azure-cli-latest#az-webapp-config-set) para definir um script de inicialização personalizado.

```azurecli-interactive
az webapp config set --name <app_name> --resource-group myResourceGroup --startup-file "gunicorn '--bind=0.0.0.0' --chdir /home/site/wwwroot/app app:app"
```

O parâmetro `--startup-file` usa um comando personalizado ou o caminho para o arquivo que contém o comando personalizado. O comando personalizado deve ter o seguinte formato:

```
gunicorn '--bind=0.0.0.0' --chdir /home/site/wwwroot/<subdirectory> <module>:<variable>
```

No comando personalizado, `--chdir` será necessário se o ponto de entrada não estiver no diretório raiz e `<subdirectory>` for o subdiretório. `<module>` é o nome do arquivo _.py_ e `<variable>` é a variável no módulo que representa seu aplicativo Web.

### <a name="browse-to-the-azure-web-app"></a>Navegar até o aplicativo Web do Azure

Navegue até o aplicativo Web implantado. Ele leva algum tempo para ser iniciado porque o contêiner tem que ser baixado e executado quando o aplicativo é solicitado pela primeira vez. Se a página expirar ou exibir uma mensagem de erro, aguarde alguns minutos e atualize-a.

```bash
http://<app_name>.azurewebsites.net
```

Você verá convidados registrados anteriormente que foram salvos no banco de dados de produção do Azure na etapa anterior.

![Aplicativo Python Flask em execução no Azure](./media/tutorial-python-postgresql-app/docker-app-deployed.png)

**Parabéns!** Você está executando um aplicativo Python no Serviço de Aplicativo para Linux.

## <a name="access-diagnostic-logs"></a>Acessar logs de diagnóstico

Porque o aplicativo Python está em execução em um contêiner, o serviço de aplicativo no Linux permite que você acesse os logs do console gerados de dentro do contêiner. Para localizar os logs, navegue até esta URL:

```
https://<app_name>.scm.azurewebsites.net/api/logs/docker
```

Você deverá ver dois objetos JSON, cada um com uma propriedade `href`. Um `href` aponta para os logs do console de Docker (termina com `_docker.log`) e outro `href` aponta para os logs do console gerados de dentro do contêiner do Python. 

```json
[  
   {  
      "machineName":"RD0003FF61ACD0_default",
      "lastUpdated":"2018-09-27T16:48:17Z",
      "size":4766,
      "href":"https://<app_name>.scm.azurewebsites.net/api/vfs/LogFiles/2018_09_27_RD0003FF61ACD0_default_docker.log",
      "path":"/home/LogFiles/2018_09_27_RD0003FF61ACD0_default_docker.log"
   },
   {  
      "machineName":"RD0003FF61ACD0",
      "lastUpdated":"2018-09-27T16:48:19Z",
      "size":2589,
      "href":"https://<app_name>.scm.azurewebsites.net/api/vfs/LogFiles/2018_09_27_RD0003FF61ACD0_docker.log",
      "path":"/home/LogFiles/2018_09_27_RD0003FF61ACD0_docker.log"
   }
]
```

Copie o valor `href` desejado para uma janela do navegador para navegar até os logs. Os logs não são transmitidos, portanto, você pode enfrentar algum atraso. Para ver novos logs, atualize a página do navegador.

## <a name="update-data-model-and-redeploy"></a>Atualizar o modelo de dados e reimplantar

Nesta etapa, você adiciona o número de participantes a cada registro de evento atualizando o modelo `Guest` e, em seguida, reimplanta a atualização para o Azure.

Na janela do terminal local, faça o check-out de arquivos da ramificação `modelChange` usando o seguinte comando git:

```bash
git checkout origin/modelChange -- .
```

Este check-out já faz as alterações necessárias ao modelo, às exibições e aos controladores. Ela também inclui uma migração de banco de dados gerada por meio de *alembic* (`flask db migrate`). É possível ver todas as alterações por meio do seguinte comando Git:

```bash
git diff master origin/modelChange
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

![Aplicativo Python Flask baseado em contêiner do Docker em execução localmente](./media/tutorial-python-postgresql-app/local-app-v2.png)

### <a name="publish-changes-to-azure"></a>Publicar alterações no Azure

Na janela do terminal local, confirme todas as alterações no Git e envie por push as alterações do código para o Azure.

```bash 
git add . 
git commit -m "updated data model" 
git push azure master 
``` 

Navegue até seu aplicativo Web do Azure e teste a nova funcionalidade novamente. Atualize a página.

```bash
http://<app_name>.azurewebsites.net
```

![Aplicativo Docker Python Flask no Serviço de Aplicativo do Azure](./media/tutorial-python-postgresql-app/docker-flask-in-azure.png)

## <a name="manage-your-web-app-in-the-azure-portal"></a>Monitore o aplicativo Web no portal do Azure

Vá para o [portal do Azure](https://portal.azure.com) para ver o aplicativo Web que você criou.

No menu à esquerda, clique em **Serviço de Aplicativos** e, em seguida, clique no nome do seu aplicativo Web do Azure.

![Navegação do portal para o aplicativo Web do Azure](./media/tutorial-python-postgresql-app/app-resource.png)

Por padrão, o portal mostra a página **Visão geral** do seu aplicativo Web . Esta página fornece uma visão de como está seu aplicativo. Aqui, você também pode executar tarefas básicas de gerenciamento como procurar, parar, iniciar, reiniciar e excluir. As guias no lado esquerdo da página mostram as páginas de configuração diferentes que você pode abrir.

![Página Serviço de Aplicativo no portal do Azure](./media/tutorial-python-postgresql-app/app-mgmt.png)

[!INCLUDE [cli-samples-clean-up](../../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu como:

> [!div class="checklist"]
> * Criar um servidor de banco de dados PostgreSQL no Azure
> * Conectar um aplicativo Python ao PostgreSQL
> * Implantar o aplicativo no Azure
> * Exibir logs de diagnóstico
> * Atualizar o modelo de dados e reimplantar o aplicativo
> * Gerenciar o aplicativo no portal do Azure

Vá para o próximo tutorial para saber como mapear um nome DNS personalizado para o seu aplicativo Web.

> [!div class="nextstepaction"]
> [Configurar imagem Python interna](how-to-configure-python.md)

> [!div class="nextstepaction"]
> [Mapear um nome DNS personalizado existente para aplicativos Web do Azure](../app-service-web-tutorial-custom-domain.md)

