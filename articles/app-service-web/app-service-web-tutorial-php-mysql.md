---
title: Compilar um aplicativo Web PHP e MySQL no Azure | Microsoft Docs
description: "Saiba como fazer com que um aplicativo PHP funcione no Azure com conexão a um banco de dados MySQL."
services: app-service\web
documentationcenter: nodejs
author: cephalin
manager: erikre
editor: 
ms.assetid: 14feb4f3-5095-496e-9a40-690e1414bd73
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 05/05/2017
ms.author: cephalin
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: 5edc47e03ca9319ba2e3285600703d759963e1f3
ms.openlocfilehash: 3ad716fab4f5084c38c83f4bc90a616856666b38
ms.contentlocale: pt-br
ms.lasthandoff: 05/31/2017

---
# <a name="build-a-php-and-mysql-web-app-in-azure"></a>Compilar um aplicativo Web PHP e MySQL no Azure
Este tutorial mostra como criar um aplicativo Web PHP no Azure e conectá-lo a um banco de dados MySQL. Quando tiver terminado, você terá um aplicativo [Laravel](https://laravel.com/) em execução nos [Aplicativos Web do Serviço de Aplicativo do Azure](app-service-web-overview.md).

![Aplicativo PHP em execução no Serviço de Aplicativo do Azure](./media/app-service-web-tutorial-php-mysql/complete-checkbox-published.png)

Neste tutorial, você aprenderá a:

> [!div class="checklist"]
> * Criar um banco de dados MySQL no Azure
> * Conectar um aplicativo PHP ao MySQL
> * Implantar o aplicativo no Azure
> * Atualizar o modelo de dados e reimplantar o aplicativo
> * Transmitir logs de diagnóstico do Azure
> * Gerenciar o aplicativo no portal do Azure

## <a name="prerequisites"></a>Pré-requisitos

Antes de executar este exemplo, instale localmente os seguintes pré-requisitos:

1. [Baixe e instale o git](https://git-scm.com/)
1. [Baixe e instale o PHP 5.6.4 ou superior](http://php.net/downloads.php)
1. [Baixe e instale o Composer](https://getcomposer.org/doc/00-intro.md)
1. Habilite as seguintes extensões do PHP de que o Laravel precisa: OpenSSL, PDO-MySQL, Mbstring, Tokenizer, XML
1. [Baixe, instale e inicie o MySQL](https://dev.mysql.com/doc/refman/5.7/en/installing.html) 
1. [Baixe e instale o Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prepare-local-mysql"></a>Preparar o MySQL local

Nesta etapa, você cria um banco de dados em seu servidor MySQL local para uso neste tutorial.

### <a name="connect-to-mysql-server"></a>Conectar ao servidor MySQL
Em uma janela de terminal, conecte-se ao servidor MySQL local.

```bash
mysql -u root -p
```

Se for solicitada uma senha, insira a senha da conta `root`. Caso não se lembre da senha de sua conta raiz, consulte [MySQL: como redefinir a senha raiz](https://dev.mysql.com/doc/refman/5.7/en/resetting-permissions.html).

Se o comando for executado com êxito, o servidor MySQL já estará sendo executado. Caso contrário, veja se o servidor MySQL local foi iniciado seguindo as [Etapas pós-instalação do MySQL](https://dev.mysql.com/doc/refman/5.7/en/postinstallation.html).

### <a name="create-a-database"></a>Criar um banco de dados

No prompt `mysql`, crie um banco de dados.

```sql
CREATE DATABASE sampledb;
```

Saia da conexão do servidor digitando `quit`.

```sql
quit
```

<a name="step2"></a>

## <a name="create-local-php-app"></a>Criar o aplicativo PHP local
Nesta etapa, você obtém um aplicativo de exemplo Laravel, configura sua conexão de banco de dados e o executa localmente. 

### <a name="clone-the-sample"></a>Clonar o exemplo

Abra a janela do terminal e `cd` para um diretório de trabalho.  

Execute os comandos a seguir para clonar o repositório de exemplo. 

```bash
git clone https://github.com/Azure-Samples/laravel-tasks
```

`cd` para o diretório clonado e instale os pacotes necessários.

```bash
cd laravel-tasks
composer install
```

### <a name="configure-mysql-connection"></a>Configurar a conexão do MySQL

Na raiz do repositório, crie um arquivo _.env_ e copie as seguintes variáveis para ele. Substitua o espaço reservado _&lt;root_password >_ pela senha do usuário raiz.

```
APP_ENV=local
APP_DEBUG=true
APP_KEY=SomeRandomString

DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_DATABASE=sampledb
DB_USERNAME=root
DB_PASSWORD=<root_password>
```

> [!NOTE]
> Para obter informações sobre como o Laravel utiliza esse arquivo _.env_, consulte [Configuração de ambiente do Laravel](https://laravel.com/docs/5.4/configuration#environment-configuration).
>
>

### <a name="run-the-sample"></a>Execute o exemplo

Execute [migrações de banco de dados do Laravel](https://laravel.com/docs/5.4/migrations) para criar as tabelas de que seu aplicativo precisa. Para ver quais tabelas são criadas nas migrações, examine o diretório de _banco de dados/migrações_ em seu repositório Git.

```bash
php artisan migrate
```

Gere uma nova chave de aplicativo do Laravel.

```bash
php artisan key:generate 
```

Execute o aplicativo.

```bash
php artisan serve
```

Navegue até `http://localhost:8000` em um navegador. Adicione algumas tarefas à página.

![O PHP se conecta com êxito ao MySQL](./media/app-service-web-tutorial-php-mysql/mysql-connect-success.png)

Para parar o PHP a qualquer momento, digite `Ctrl`+`C` no terminal. 

## <a name="create-production-mysql-in-azure"></a>Criar MySQL de produção no Azure

Nesta etapa, você cria um banco de dados MySQL no [Banco de dados do Azure para MySQL (versão prévia)](/azure/mysql). Posteriormente, você configurará seu aplicativo PHP para se conectar a esse banco de dados.

### <a name="log-in-to-azure"></a>Fazer logon no Azure

Agora, você usará a CLI do Azure 2.0 em uma janela de terminal para criar os recursos necessários para hospedar seu aplicativo PHP no Serviço de Aplicativo do Azure. Faça logon na sua assinatura do Azure com o comando [az login](/cli/azure/#login) e siga as instruções na tela. 

```azurecli-interactive 
az login 
``` 

### <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um [grupo de recursos](../azure-resource-manager/resource-group-overview.md) com o comando [az group create](/cli/azure/group#create). Um grupo de recursos do Azure é um contêiner lógico no qual recursos do Azure, como aplicativos da Web, bancos de dados e contas de armazenamento são implantados e gerenciados. 

O exemplo a seguir cria um grupo de recursos na região da Europa Setentrional:

```azurecli-interactive
az group create --name myResourceGroup --location "North Europe"
```

Para ver quais são os possíveis valores que podem ser usados para `--location`, use o comando [az appservice list-locations](/cli/azure/appservice#list-locations).

### <a name="create-a-mysql-server"></a>Criar um servidor MySQL

Crie um servidor no Banco de Dados do Azure para MySQL (versão prévia) com o comando [az mysql server create](/cli/azure/mysql/server#create).

No seguinte comando, substitua seu próprio nome do servidor MySQL exclusivo onde vir o espaço reservado _&lt;mysql_server_name>_. Esse nome é parte do nome de host do servidor MySQL, `<mysql_server_name>.database.windows.net`, portanto, ele precisa ser globalmente exclusivo. Da mesma forma, substitua _&lt;admin_user>_ e _&lt;admin_password>_ por seus próprios valores.

```azurecli-interactive
az mysql server create \
    --name <mysql_server_name> \
    --resource-group myResourceGroup \
    --location "North Europe" \
    --user <admin_user> \
    --password <admin_password>
```

Quando o servidor MySQL for criado, a CLI do Azure mostrará informações semelhantes ao exemplo a seguir:

```json
{
  "administratorLogin": "<admin_user>",
  "administratorLoginPassword": null,
  "fullyQualifiedDomainName": "<mysql_server_name>.database.windows.net",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.DBforMySQL/servers/<mysql_server_name>",
  "location": "northeurope",
  "name": "<mysql_server_name>",
  "resourceGroup": "myResourceGroup",
  ...
}
```

### <a name="configure-server-firewall"></a>Configurar o firewall do servidor

Crie uma regra de firewall para o servidor MySQL para permitir conexões de cliente usando o comando [az mysql server firewall-rule create](/cli/azure/mysql/server/firewall-rule#create). 

```azurecli-interactive
az mysql server firewall-rule create \
    --name allIPs \
    --server <mysql_server_name> \
    --resource-group myResourceGroup \
    --start-ip-address 0.0.0.0 \
    --end-ip-address 255.255.255.255
```

> [!NOTE]
> O Banco de Dados do Azure para MySQL (versão prévia) ainda não permite conexões somente de serviços do Azure. Como os endereços IP no Azure são atribuídos dinamicamente, é melhor habilitar todos os endereços IP por enquanto. Como o serviço está na fase de versão prévia, melhores métodos para proteger seu banco de dados serão habilitados em breve.
>
>

### <a name="connect-to-production-mysql-server"></a>Conectar-se ao servidor MySQL de produção

Na janela do terminal, conecte-se ao servidor MySQL no Azure. Use o valor especificado anteriormente para _&lt;admin_user>_ e _&lt;mysql_server_name>_.

```bash
mysql -u <admin_user>@<mysql_server_name> -h <mysql_server_name>.database.windows.net -P 3306 -p
```

### <a name="create-a-production-database"></a>Criar um banco de dados de produção

No prompt `mysql`, crie um banco de dados.

```sql
CREATE DATABASE sampledb;
```

### <a name="create-a-user-with-permissions"></a>Criar um usuário com permissões

Crie um usuário do banco de dados e dê a ele todos os privilégios no banco de dados `sampledb`. Substitua os espaços reservados _&lt;phpapp_user>_ e _&lt;phpapp_password>_ pelo seu próprio nome exclusivo do aplicativo.

```sql
CREATE USER '<phpapp_user>' IDENTIFIED BY '<phpapp_password>'; 
GRANT ALL PRIVILEGES ON sampledb.* TO '<phpapp_user>';
```

Saia da conexão do servidor digitando `quit`.

```sql
quit
```

## <a name="connect-app-to-production-mysql"></a>Conectar o aplicativo ao MySQL de produção

Nesta etapa, você conecta seu aplicativo PHP ao banco de dados MySQL que acabou de criar no Banco de Dados do Azure para MySQL (versão prévia). 

<a name="devconfig"></a>
### <a name="configure-the-connection"></a>Configurar a conexão 

Na raiz do repositório, crie um arquivo _.env.production_ e copie as seguintes variáveis para ele. Substitua os espaços reservados _&lt;mysql_server_name>_, _&lt;phpapp_user>_ e _&lt;phpapp_password>_.

```
APP_ENV=production
APP_DEBUG=true
APP_KEY=SomeRandomString

DB_CONNECTION=mysql
DB_HOST=<mysql_server_name>.database.windows.net
DB_DATABASE=sampledb
DB_USERNAME=<phpapp_user>@<mysql_server_name>
DB_PASSWORD=<phpapp_password>
```

Salve suas alterações.

### <a name="test-the-application"></a>Testar o aplicativo

Execute migrações de banco de dados do Laravel com _.env.production_ como arquivo de ambiente para criar as tabelas em seu banco de dados MySQL no Banco de Dados do Azure para MySQL (versão prévia).

```bash
php artisan migrate --env=production --force
```

_.env.production_ ainda não tem uma chave de aplicativo válida. Gere uma nova chave de aplicativo para ele no terminal. 

```bash
php artisan key:generate --env=production --force
```

Execute o aplicativo de exemplo com _.env.production_ como arquivo de ambiente.

```bash
php artisan serve --env=production
```

Navegue até `http://localhost:8000` em um navegador. Se a página for carregada sem erros, seu aplicativo PHP está se conectando ao banco de dados MySQL no Azure. 

Adicione algumas tarefas à página.

![O PHP se conecta com êxito ao Banco de Dados do Azure para MySQL (versão prévia)](./media/app-service-web-tutorial-php-mysql/mysql-connect-success.png)

Para parar o PHP a qualquer momento, digite `Ctrl`+`C` no terminal. 

### <a name="secure-sensitive-data"></a>Proteger dados confidenciais

Você precisará garantir que os dados confidenciais em _.env.production_ não sejam confirmados no Git.

Para fazer isso, abra _.gitignore_ do repositório raiz e adicione o nome do arquivo em uma nova linha:

```
.env.production
```

Salve suas alterações e confirme-as no Git.

```bash
git add .gitignore
git commit -m "keep sensitive data out of git"
```

Posteriormente, você aprenderá a configurar variáveis de ambiente no Serviço de Aplicativo para se conectar ao banco de dados no Banco de Dados do Azure para MySQL (versão prévia), para que não seja necessário nenhum arquivo `.env` no Serviço de Aplicativo. 

## <a name="deploy-php-app-to-azure"></a>Implantar o aplicativo PHP no Azure
Nessa etapa, você implanta seu aplicativo PHP conectado ao MySQL no Serviço de Aplicativo do Azure.

### <a name="create-an-app-service-plan"></a>Criar um plano de Serviço de Aplicativo

Criar um plano do Serviço de Aplicativo com o comando [az appservice plan create](/cli/azure/appservice/plan#create). 

[!INCLUDE [app-service-plan](../../includes/app-service-plan.md)]

O exemplo a seguir cria um Plano do Serviço de Aplicativo chamado _myAppServicePlan_ usando o tipo de preço **GRATUITO**:

```azurecli-interactive
az appservice plan create \
    --name myAppServicePlan \
    --resource-group myResourceGroup \
    --sku FREE
```

Quando o Plano do Serviço de Aplicativo é criado, a CLI do Azure mostra informações semelhantes ao exemplo a seguir:

```json 
{ 
  "adminSiteName": null,
  "appServicePlanName": "myAppServicePlan",
  "geoRegion": "North Europe",
  "hostingEnvironmentProfile": null,
  "id": "/subscriptions/0000-0000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myAppServicePlan",
  "kind": "app",
  "location": "North Europe",
  "maximumNumberOfWorkers": 1,
  "name": "myAppServicePlan",
  ...
  < Output has been truncated for readability >
} 
``` 

### <a name="create-a-web-app"></a>Criar um aplicativo Web

Agora que um Plano do Serviço de Aplicativo foi criado, crie um aplicativo Web dentro do Plano do Serviço de Aplicativo _myAppServicePlan_. O aplicativo Web fornece um espaço de hospedagem para implantar seu código e fornecer uma URL para exibir o aplicativo implantado. Use o comando [az appservice web create](/cli/azure/appservice/web#create) para criar o aplicativo Web. 

No seguinte comando, substitua espaço reservado _&lt;appname>_ pelo seu próprio nome de aplicativo exclusivo. Esse nome exclusivo é usado como a parte do nome de domínio padrão para o aplicativo Web, portanto, o nome precisa ser exclusivo entre todos os aplicativos no Azure. Posteriormente, você poderá mapear qualquer entrada DNS personalizada para o aplicativo Web antes de expor para seus usuários. 

```azurecli-interactive
az appservice web create \
    --name <app_name> \
    --resource-group myResourceGroup \
    --plan myAppServicePlan
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

### <a name="set-the-php-version"></a>Definir a versão do PHP

Defina a versão do PHP que seu aplicativo requer usando o comando [az appservice web config update](/cli/azure/appservice/web/config#update).

O comando a seguir define a versão do PHP como _7.0_.

```azurecli-interactive
az appservice web config update \
    --name <app_name> \
    --resource-group myResourceGroup \
    --php-version 7.0
```

### <a name="configure-database-settings"></a>Definir configurações de banco de dados

Conforme mencionado anteriormente, é possível se conectar ao banco de dados MySQL do Azure usando variáveis de ambiente no Serviço de Aplicativo.

No Serviço de Aplicativo, são definidas as variáveis de ambiente como _configurações do aplicativo_ usando o comando [az appservice web config appsettings update](/cli/azure/appservice/web/config/appsettings#update). 

O comando a seguir permite definir as configurações de aplicativo `DB_HOST`, `DB_DATABASE`, `DB_USERNAME` e `DB_PASSWORD`. Substitua os espaços reservados _&lt;appname>_, _&lt;mysql_server_name>_, _&lt;phpapp_user>_ e _&lt;phpapp_password>_.

```azurecli-interactive
az appservice web config appsettings update \
    --name <app_name> \
    --resource-group myResourceGroup \
    --settings DB_HOST="<mysql_server_name>.database.windows.net" DB_DATABASE="sampledb" DB_USERNAME="<phpapp_user>@<mysql_server_name>" DB_PASSWORD="<phpapp_password>"
```

É possível usar o método [getenv()](http://www.php.net/manual/function.getenv.php) do PHP para acessar as configurações. O código do Laravel usa um wrapper [env()](https://laravel.com/docs/5.4/helpers#method-env) em torno do `getenv()` do PHP. Por exemplo, a configuração do MySQL em _config/database.php_ tem esta aparência:

```php
'mysql' => [
    'driver'    => 'mysql',
    'host'      => env('DB_HOST', 'localhost'),
    'database'  => env('DB_DATABASE', 'forge'),
    'username'  => env('DB_USERNAME', 'forge'),
    'password'  => env('DB_PASSWORD', ''),
    ...
],
```

### <a name="configure-laravel-environment-variables"></a>Configurar variáveis de ambiente do Laravel

Assim como em seu computador local, o Laravel precisa de uma chave de aplicativo no Serviço de Aplicativo. É possível configurá-la com as configurações de aplicativo também.

Use `php artisan` para gerar uma nova chave de aplicativo sem salvá-la em _.env_.

```bash
php artisan key:generate --show
```

Defina a chave de aplicativo em seu aplicativo Web do Serviço de Aplicativo usando o comando [az appservice web config appsettings update](/cli/azure/appservice/web/config/appsettings#update). Substitua os espaços reservados _&lt;appname>_ e _&lt;outputofphpartisankey:generate>_.

```azurecli-interactive
az appservice web config appsettings update \
    --name <app_name> \
    --resource-group myResourceGroup \
    --settings APP_KEY="<output_of_php_artisan_key:generate>" APP_DEBUG="true"
```

> [!NOTE]
> `APP_DEBUG="true"` instrui o Laravel a retornar informações de depuração se seu aplicativo Web implantado encontrar erros. Ao executar um aplicativo de produção, você deverá defini-lo como `false` em vez disso, a fim de aumentar a segurança.
>
>

### <a name="set-the-virtual-application-path"></a>Definir o caminho do aplicativo virtual

Defina o caminho do aplicativo virtual para seu aplicativo Web. Você só precisa dessa etapa porque o [Ciclo de vida do aplicativo do Laravel](https://laravel.com/docs/5.4/lifecycle) começa no diretório _público_ em vez do diretório raiz do aplicativo. Outras estruturas PHP cujo ciclo de vida começa no diretório raiz podem funcionar sem a configuração manual do caminho do aplicativo virtual.

Defina o caminho do aplicativo virtual usando o comando [az resource update](/cli/azure/resource#update). Substitua o espaço reservado _&lt;appname>_.

```bash
az resource update \
    --name web \
    --resource-group myResourceGroup \
    --namespace Microsoft.Web \
    --resource-type config \
    --parent sites/<app_name> \
    --set properties.virtualApplications[0].physicalPath="site\wwwroot\public" \
    --api-version 2015-06-01
```

> [!NOTE]
> Por padrão, o Serviço de Aplicativo do Azure aponta o caminho do aplicativo virtual raiz (_/_) para o diretório raiz de seus arquivos de aplicativo implantados (_sites\wwwroot_). 
>
>

### <a name="configure-a-deployment-user"></a>Configurar um usuário de implantação

Para o FTP e o Git local, é necessário ter um usuário de implantação configurado no servidor para autenticar sua implantação. 

> [!NOTE] 
> Um usuário de implantação é necessário para a implantação do FTP e do Git Local em um Aplicativo Web. O nome de usuário e a senha estão no nível da conta e, dessa forma, são diferentes de suas credenciais de Assinatura do Azure.

Se tiver criado um nome de usuário e uma senha de implantação anteriormente, você poderá usar o seguinte comando para exibir o nome de usuário:

```azurecli-interactive
az appservice web deployment user show
```

Se ainda não tiver um usuário de implantação, execute o comando [az appservice web deployment user set](/cli/azure/appservice/web/deployment/user#set) para criar suas credenciais de implantação. 

```azurecli-interactive
az appservice web deployment user set \
    --user-name <username> \
    --password <minimum-8-char-capital-lowercase-number>
```

O nome de usuário deve ser exclusivo e a senha deve ser forte. Se receber um erro `'Conflict'. Details: 409`, altere o nome de usuário. Se receber um erro `'Bad Request'. Details: 400`, use uma senha mais forte.

Você só precisa criar esse usuário de implantação de uma vez; é possível usá-lo para todas as implantações do Azure.

Tome nota do nome de usuário e da senha, pois eles serão usados mais tarde quando você implantar o aplicativo.

### <a name="configure-local-git-deployment"></a>Configurar a implantação do git local 

É possível implantar seu aplicativo no Serviço de Aplicativo do Azure de várias maneiras, incluindo FTP, Git local, GitHub, Visual Studio Team Services e BitBucket. 

Use o comando [az appservice web source-control config-local-git](/cli/azure/appservice/web/source-control#config-local-git) para configurar o acesso do Git local para o aplicativo Web do Azure. 

```azurecli-interactive
az appservice web source-control config-local-git \
    --name <app_name> \
    --resource-group myResourceGroup
```

Quando o usuário de implantação é configurado, a CLI do Azure mostra a URL de implantação ao seu aplicativo Web Azure no seguinte formato:

```bash 
https://<username>@<app_name>.scm.azurewebsites.net:443/<app_name>.git 
``` 

Copie a saída do terminal, pois ela será usada na próxima etapa. 

### <a name="push-to-azure-from-git"></a>Enviar do Git para o Azure

Adicione um Azure remoto ao seu repositório Git local. 

```bash
git remote add azure <paste_copied_url_here> 
```

Envie por push para o Azure remoto para implantar seu aplicativo PHP. Será solicitada a senha que você forneceu anteriormente como parte da criação do usuário de implantação. 

```bash
git push azure master
```

Durante a implantação, o Serviço de Aplicativo do Azure comunica seu andamento com o Git.

```bash
Counting objects: 3, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (3/3), done.
Writing objects: 100% (3/3), 291 bytes | 0 bytes/s, done.
Total 3 (delta 2), reused 0 (delta 0)
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id 'a5e076db9c'.
remote: Running custom deployment command...
remote: Running deployment command...
...
< Output has been truncated for readability >
``` 

> [!NOTE]
> Você observará que o processo de implantação instala pacotes do [Composer](https://getcomposer.org/) no final. O Serviço de Aplicativo não executa essas automações durante a implantação padrão, de modo que esse repositório de exemplo tem três arquivos adicionais em seu diretório raiz para habilitá-las: 
>
> - `.deployment` - Esse arquivo informa o Serviços de Aplicativo para executar `bash deploy.sh` como o script de implantação personalizada.
> - `deploy.sh` - O script de implantação personalizada. Se examinar o arquivo, você verá que ele executa `php composer.phar install` após `npm install`. 
> - `composer.phar` – O gerenciador de pacotes do Composer.
>
> É possível usar essa abordagem para adicionar qualquer etapa à implantação base em Git no Serviço de Aplicativo. Para obter mais informações, consulte [Script de implantação personalizado](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script).
>
>

### <a name="browse-to-the-azure-web-app"></a>Navegar até o aplicativo Web do Azure

Navegue até `http://<app_name>.azurewebsites.net` e adicione algumas tarefas à lista. 

![Aplicativo PHP em execução no Serviço de Aplicativo do Azure](./media/app-service-web-tutorial-php-mysql/php-mysql-in-azure.png)

**Parabéns!** Você está executando um aplicativo PHP controlado por dados no Serviço de Aplicativo do Azure.

## <a name="update-data-model-and-redeploy"></a>Atualizar o modelo de dados e reimplantar

Nessa etapa, você faz algumas alterações no modelo de dados `task` e publica as alterações no Azure.

Para o cenário de tarefas, é recomendável modificar o aplicativo para que você possa marcar uma tarefa como concluída. 

### <a name="add-a-column"></a>Adicionar uma coluna

No terminal, verifique se você está na raiz do repositório Git e, em seguida, gere uma nova migração de banco de dados para a tabela `tasks`.

```bash
php artisan make:migration add_complete_column --table=tasks
```

Esse comando exibe o nome do arquivo de migração gerado. Localize esse arquivo em _database/migrations_ e abra-o em um editor de texto.

Substitua o método up() pelo seguinte código:

```php
public function up()
{
    Schema::table('tasks', function (Blueprint $table) {
        $table->boolean('complete')->default(False);
    });
}
```

Este código adiciona uma coluna booliana à tabela `tasks` chamada `complete`.

Substitua o método down() pelo seguinte código para a ação de reversão:

```php
public function down()
{
    Schema::table('tasks', function (Blueprint $table) {
        $table->dropColumn('complete');
    });
}
```

No terminal, execute migrações de banco de dados do Laravel localmente para fazer a alteração no banco de dados local.

```bash
php artisan migrate
```

Com base na [Convenção de nomenclatura do Laravel](https://laravel.com/docs/5.4/eloquent#defining-models), o modelo `Task` (consulte _app/Task.php_) é mapeado para a tabela `tasks` por padrão, de modo que a atualização do modelo de dados está concluída.

### <a name="update-application-logic"></a>Atualizar a lógica do aplicativo

Abra _routes/web.php_. O aplicativo de exemplo define suas rotas e lógica de negócios aqui.

No final do arquivo, adicione uma rota com o código a seguir:

```php
/**
 * Toggle Task completeness
 */
Route::post('/task/{id}', function ($id) {
    error_log('INFO: post /task/'.$id);
    $task = Task::findOrFail($id);

    $task->complete = !$task->complete;
    $task->save();

    return redirect('/');
});
```

Este código faz uma atualização simples para o modelo de dados alternando o valor de `complete`.

### <a name="update-the-view"></a>Atualizar a exibição

Abra _resources/views/tasks.blade.php_. Encontre a marcação de abertura `<tr>` e a substitua por:

```html
<tr class="{{ $task->complete ? 'success' : 'active' }}" >
```

Isso altera a cor da linha dependendo de a tarefa ter sido concluída.

Na linha seguinte, você tem o código a seguir:

```html
<td class="table-text"><div>{{ $task->name }}</div></td>
```

Substitua a linha inteira pelo código a seguir:

```html
<td>
    <form action="{{ url('task/'.$task->id) }}" method="POST">
        {{ csrf_field() }}

        <button type="submit" class="btn btn-xs">
            <i class="fa {{$task->complete ? 'fa-check-square-o' : 'fa-square-o'}}"></i>
        </button>
        {{ $task->name }}
    </form>
</td>
```

Este código adiciona o botão Enviar que faz referência à rota que você definiu anteriormente.

### <a name="test-your-changes-locally"></a>Testar suas alterações localmente

No diretório raiz de seu repositório Git, execute novamente o servidor de desenvolvimento.

```bash
php artisan serve
```

Navegue até `http://localhost:8000` em um navegador e clique na caixa de seleção para ver o status da tarefa.

![Caixa de seleção adicionada à tarefa](./media/app-service-web-tutorial-php-mysql/complete-checkbox.png)

### <a name="publish-changes-to-azure"></a>Publicar alterações no Azure

No terminal, execute migrações de banco de dados do Laravel com a cadeia de conexão de produção para fazer a alteração em seu banco de dados de produção no Azure.

```bash
php artisan migrate --env=production --force
```

Confirme todas as suas alterações no Git e, em seguida, envie por push as alterações de código para o Azure.

```bash
git add .
git commit -m "added complete checkbox"
git push azure master
```

Quando `git push` estiver completo, navegue até seu aplicativo Web do Azure novamente e teste a nova funcionalidade.

![Alterações de banco de dados e modelos publicadas no Azure](media/app-service-web-tutorial-php-mysql/complete-checkbox-published.png)

> [!NOTE]
> Se tiver adicionado tarefas anteriores, você ainda poderá vê-las. Suas atualizações do esquema de dados deixam os dados existentes intactos.
>
>

## <a name="stream-diagnostic-logs"></a>Logs de diagnóstico de fluxo 

Enquanto o aplicativo PHP é executado no Serviço de Aplicativo do Azure, é possível fazer com que os logs do console sejam encaminhados diretamente para seu terminal. Dessa forma, é possível obter as mesmas mensagens de diagnóstico para ajudá-lo a depurar erros de aplicativo.

Para iniciar o streaming de log, use o comando [az appservice web log tail](/cli/azure/appservice/web/log#tail).

```azurecli-interactive 
az appservice web log tail \
    --name <app_name> \
    --resource-group myResourceGroup 
``` 

Uma vez iniciado o streaming de log, atualize seu aplicativo Web do Azure no navegador para obter algum tráfego da Web. Agora, deve ser possível ver os logs do console transferidos para seu terminal.

Para interromper o streaming de log a qualquer momento, digite `Ctrl`+`C`. 

> [!TIP]
> Um aplicativo PHP pode usar o [error_log()](http://php.net/manual/function.error-log.php) padrão para gerar a saída para o console. O aplicativo de exemplo usa essa abordagem em _app/Http/routes.php_.
>
> Como uma estrutura da Web, o [Laravel usa o Monolog](https://laravel.com/docs/5.4/errors) como provedor de logs. Para saber como fazer com que o Monolog gere mensagens de saída para o console, consulte [PHP: como usar o Monolog para se registrar no console (php://out)](http://stackoverflow.com/questions/25787258/php-how-to-use-monolog-to-log-to-console-php-out).
>
>

## <a name="manage-your-azure-web-app"></a>Gerenciar seu aplicativo Web do Azure

Vá para o portal do Azure para ver o aplicativo Web que você criou.

Para fazer isso, entre em [https://portal.azure.com](https://portal.azure.com).

No menu à esquerda, clique em **Serviço de Aplicativo**, em seguida, clique no nome do seu aplicativo Web do Azure.

![Navegação do portal para o aplicativo Web do Azure](./media/app-service-web-tutorial-php-mysql/access-portal.png)

Você foi para a _folha_ de seu aplicativo Web (uma página do portal que abre horizontalmente).

Por padrão, a folha de seu aplicativo Web mostra a página **Visão Geral**. Esta página fornece uma visão de como está seu aplicativo. Aqui, você também pode executar tarefas básicas de gerenciamento como procurar, parar, iniciar, reiniciar e excluir. As guias no lado esquerdo da folha mostram as páginas de configuração diferentes que você pode abrir.

![Folha Serviço de Aplicativo no portal do Azure](./media/app-service-web-tutorial-php-mysql/web-app-blade.png)

Essas guias na folha mostram muitos recursos excelentes que você pode adicionar ao seu aplicativo Web. A lista a seguir fornece algumas possibilidades:

* mapear um nome DNS personalizado
* associar um certificado SSL personalizado
* configurar uma implantação contínua
* Escalar vertical e horizontalmente
* adicionar a autenticação do usuário

## <a name="clean-up-resources"></a>Limpar recursos
 
Se não precisar desses recursos para outro tutorial (consulte [Próximas etapas](#next)), você poderá excluí-los executando o seguinte comando: 
  
```azurecli-interactive
az group delete --name myResourceGroup 
``` 

<a name="next"></a>

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a:

> [!div class="checklist"]
> * Criar um banco de dados MySQL no Azure
> * Conectar um aplicativo PHP ao MySQL
> * Implantar o aplicativo no Azure
> * Atualizar o modelo de dados e reimplantar o aplicativo
> * Transmitir logs de diagnóstico do Azure
> * Gerenciar o aplicativo no portal do Azure

Vá para o próximo tutorial para aprender a mapear um nome DNS personalizado para ele.

> [!div class="nextstepaction"] 
> [Mapear um nome DNS personalizado existente para aplicativos Web do Azure](app-service-web-tutorial-custom-domain.md)

