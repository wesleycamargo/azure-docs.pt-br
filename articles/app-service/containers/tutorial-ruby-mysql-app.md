---
title: "Compilar um aplicativo Web em Ruby e MySQL no Serviço de Aplicativo do Azure no Linux | Microsoft Docs"
description: "Saiba como fazer com que um aplicativo Ruby funcione no Azure com conexão a um banco de dados MySQL."
services: app-service\web
documentationcenter: nodejs
author: cephalin
manager: cfowler
ms.service: app-service-web
ms.workload: web
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 12/21/2017
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: 03b2b4e8f8827a08e1414512d848bd5bed48d674
ms.sourcegitcommit: df4ddc55b42b593f165d56531f591fdb1e689686
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/04/2018
---
# <a name="build-a-ruby-and-mysql-web-app-in-azure-app-service-on-linux"></a>Compilar um aplicativo Web em Ruby e MySQL no Serviço de Aplicativo do Azure no Linux

O [Serviço de Aplicativo no Linux](app-service-linux-intro.md) fornece um serviço de hospedagem na Web altamente escalonável e com aplicação automática de patches usando o sistema operacional Linux. Este tutorial mostra como criar um aplicativo Web em Ruby e conectá-lo a um banco de dados MySQL. Quando terminar, você terá um aplicativo [Ruby on Rails](http://rubyonrails.org/) sendo executado no Serviço de Aplicativo no Linux.

![Aplicativo Ruby on Rails em execução no Serviço de Aplicativo do Azure](./media/tutorial-ruby-mysql-app/complete-checkbox-published.png)

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar um banco de dados MySQL no Azure
> * Conectar um aplicativo Ruby on Rails para MySQL
> * Implantar o aplicativo no Azure
> * Atualizar o modelo de dados e reimplantar o aplicativo
> * Transmitir logs de diagnóstico do Azure
> * Gerenciar o aplicativo no portal do Azure

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial:

* [Instalar o Git](https://git-scm.com/)
* [Instalar o Ruby 2.3](https://www.ruby-lang.org/documentation/installation/)
* [Instalar o Ruby on Rails 5.1](http://guides.rubyonrails.org/v5.1/getting_started.html)
* [Instalar e iniciar o MySQL](https://dev.mysql.com/doc/refman/5.7/en/installing.html) 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prepare-local-mysql"></a>Preparar o MySQL local

Nesta etapa, você cria um banco de dados em seu servidor MySQL local para uso neste tutorial.

### <a name="connect-to-local-mysql-server"></a>Conectar ao servidor MySQL local

Em uma janela de terminal, conecte-se ao servidor MySQL local. Você pode usar essa janela do terminal para executar todos os comandos deste tutorial.

```bash
mysql -u root -p
```

Se for solicitada uma senha, insira a senha da conta `root`. Caso não se lembre da senha de sua conta raiz, consulte [MySQL: como redefinir a senha raiz](https://dev.mysql.com/doc/refman/5.7/en/resetting-permissions.html).

Se o comando for executado com êxito, o MySQL Server estará em execução. Caso contrário, veja se o servidor MySQL local foi iniciado seguindo as [Etapas pós-instalação do MySQL](https://dev.mysql.com/doc/refman/5.7/en/postinstallation.html).

Saia da conexão do servidor digitando `quit`.

```sql
quit
```

<a name="step2"></a>

## <a name="create-a-ruby-on-rails-app-locally"></a>Criar um aplicativo Ruby on Rails localmente
Nesta etapa, você obtém um aplicativo de exemplo Ruby on Rails, configura sua conexão de banco de dados e o executa localmente. 

### <a name="clone-the-sample"></a>Clonar o exemplo

Na janela do terminal, `cd` para um diretório de trabalho.

Execute o comando a seguir para clonar o repositório de exemplo.

```bash
git clone https://github.com/Azure-Samples/rubyrails-tasks.git
```

`cd` para o diretório clonado. Instale os pacotes necessários.

```bash
cd rubyrails-tasks
bundle install --path vendor/bundle
```

### <a name="configure-mysql-connection"></a>Configurar a conexão do MySQL

No repositório, abra _config/database.yml_ e forneça o nome de usuário e senha raiz de MySQL local (linha 13). Se você instalou o MySQL usando uma ferramenta como [Homebrew](https://brew.sh/), as credenciais no arquivo já estarão definidas para os valores padrão (que é `root` e uma senha vazia).

```txt
default: &default
  adapter: mysql2
  encoding: utf8
  pool: <%= ENV.fetch("RAILS_MAX_THREADS") { 5 } %>
  username: root
  password:
  socket: /tmp/mysql.sock
```

### <a name="run-the-sample-locally"></a>Executar o exemplo localmente

Execute as [migrações do Rails](http://guides.rubyonrails.org/active_record_migrations.html#running-migrations) para criar as tabelas necessárias ao aplicativo. Para ver quais tabelas são criadas nas migrações, examine o diretório _db/migrate_ no repositório Git.

```bash
rake db:create
rake db:migrate
```

Execute o aplicativo.

```bash
rails server
```

Navegue até `http://localhost:3000` em um navegador. Adicione algumas tarefas à página.

![Ruby on Rails se conecta com êxito ao MySQL](./media/tutorial-ruby-mysql-app/mysql-connect-success.png)

Para parar o servidor Ruby on Rails, digite `Ctrl + C` no terminal.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-mysql-in-azure"></a>Criar o MySQL no Azure

Nesta etapa, você cria um banco de dados MySQL no [Banco de dados do Azure para MySQL (versão prévia)](/azure/mysql). Posteriormente, você configura o aplicativo Ruby on Rails para se conectar a esse banco de dados.

### <a name="create-a-resource-group"></a>Criar um grupo de recursos

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-no-h.md)] 

### <a name="create-a-mysql-server"></a>Criar um servidor MySQL

Crie um servidor no Banco de Dados do Azure para MySQL (versão prévia) com o comando [az mysql server create](/cli/azure/mysql/server?view=azure-cli-latest#az_mysql_server_create).

No comando a seguir, substitua o nome do MySQL Server em que o espaço reservado _&lt;mysql_server_name>_ é exibido (os caracteres válidos são `a-z`, `0-9` e `-`). Esse nome faz parte do nome do host do MySQL Server (`<mysql_server_name>.mysql.database.azure.com`) e precisa ser global exclusivo.

```azurecli-interactive
az mysql server create --name <mysql_server_name> --resource-group myResourceGroup --location "North Europe" --admin-user adminuser --admin-password My5up3r$tr0ngPa$w0rd!
```

Quando o servidor MySQL for criado, a CLI do Azure mostrará informações semelhantes ao exemplo a seguir:

```json
{
  "administratorLogin": "adminuser",
  "administratorLoginPassword": null,
  "fullyQualifiedDomainName": "<mysql_server_name>.database.mysql.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.DBforMySQL/servers/<mysql_server_name>",
  "location": "northeurope",
  "name": "<mysql_server_name>",
  "resourceGroup": "myResourceGroup",
  ...
}
```

### <a name="configure-server-firewall"></a>Configurar o firewall do servidor

Crie uma regra de firewall para o servidor MySQL para permitir conexões de cliente usando o comando [az mysql server firewall-rule create](/cli/azure/mysql/server/firewall-rule?view=azure-cli-latest#az_mysql_server_firewall_rule_create).

```azurecli-interactive
az mysql server firewall-rule create --name allIPs --server <mysql_server_name> --resource-group myResourceGroup --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255
```

> [!NOTE]
> Atualmente, o Banco de Dados do Azure para MySQL (Versão Prévia) não limita as conexões somente aos serviços do Azure. Como os endereços IP no Azure são atribuídos dinamicamente, é melhor habilitar todos os endereços IP. O serviço está em versão prévia. Estamos planejando melhores métodos para proteger o banco de dados.
>

### <a name="connect-to-production-mysql-server-locally"></a>Conectar-se ao servidor MySQL de produção localmente

Na janela do terminal, conecte-se ao servidor MySQL no Azure. Use o valor especificado anteriormente para _&lt;mysql_server_name>_.

```bash
mysql -u adminuser@<mysql_server_name> -h <mysql_server_name>.mysql.database.azure.com -P 3306 -p
```

Quando for solicitada uma senha, use _My5up3r$tr0ngPa$w0rd!_, que você especificou quando criou o banco de dados.

### <a name="create-a-production-database"></a>Criar um banco de dados de produção

No prompt `mysql`, crie um banco de dados.

```sql
CREATE DATABASE sampledb;
```

### <a name="create-a-user-with-permissions"></a>Criar um usuário com permissões

Crie um usuário de banco de dados chamado _railsappuser_ e conceda a ele todos os privilégios no banco de dados `sampledb`.

```sql
CREATE USER 'railsappuser' IDENTIFIED BY 'MySQLAzure2017'; 
GRANT ALL PRIVILEGES ON sampledb.* TO 'railsappuser';
```

Saia da conexão do servidor digitando `quit`.

```sql
quit
```

## <a name="connect-app-to-azure-mysql"></a>Conectar o aplicativo ao MySQL do Azure

Nesta etapa, você conecta o aplicativo Ruby on Rails ao banco de dados MySQL criado no Banco de Dados do Azure para MySQL (Versão Prévia).

<a name="devconfig"></a>

### <a name="configure-the-database-connection"></a>Configurar a conexão de banco de dados

No repositório, abra _config/database.yml_. Na parte inferior do arquivo, substitua as variáveis de produção pelo código a seguir. Para o espaço reservado _&lt;mysql_server_name>_, use o nome do servidor MySQL que você criou.

```txt
production:
  <<: *default
  host: <%= ENV['DB_HOST'] %>
  port: 3306
  database: <%= ENV['DB_DATABASE'] %>
  username: <%= ENV['DB_USERNAME'] %>
  password: <%= ENV['DB_PASSWORD'] %>
  sslca: ssl/BaltimoreCyberTrustRoot.crt.pem
```

Salve as alterações.

> [!NOTE]
> O `sslca` é adicionado e aponta para um arquivo _. PEM_ existente no repositório de exemplo. Por padrão, o Banco de Dados do Azure para MySQL impõe conexões SSL de clientes. Esse certificado `.pem` é como você faz conexões SSL ao Banco de Dados do Azure para MySQL. Para saber mais, veja [Configurar conectividade SSL em seu aplicativo para se conectar com segurança ao Banco de Dados do Azure para MySQL](../../mysql/howto-configure-ssl.md).
>

### <a name="test-the-application-locally"></a>Testar o aplicativo localmente

No terminal local, defina as seguintes variáveis de ambiente:

```bash
export DB_HOST=<mysql_server_name>.mysql.database.azure.com
export DB_DATABASE=sampledb 
export DB_USERNAME=railsappuser@<mysql_server_name>
export DB_PASSWORD=MySQLAzure2017
```

Execute migrações de banco de dados do Rails com os valores de produção que você acabou de configurar para criar as tabelas em seu banco de dados MySQL no Banco de Dados do Azure para MySQL (versão prévia). 

```bash
rake db:migrate RAILS_ENV=production
```

Ao executar no ambiente de produção, o aplicativo Rails precisa de ativos pré-compilados. Gere os ativos necessários com o seguinte comando:

```bash
rake assets:precompile
```

O ambiente de produção Rails também usa segredos para gerenciar a segurança. Gerar uma chave secreta.

```bash
rails secret
```

Salve a chave secreta para as respectivas variáveis usadas pelo ambiente de produção do Rails. Para sua conveniência, use a mesma chave para as duas variáveis.

```bash
export RAILS_MASTER_KEY=<output_of_rails_secret>
export SECRET_KEY_BASE=<output_of_rails_secret>
```

Habilite o ambiente de produção do Rails para atender aos arquivos JavaScript e CSS.

```bash
export RAILS_SERVE_STATIC_FILES=true
```

Execute o aplicativo de exemplo no ambiente de produção.

```bash
rails server -e production
```

Navegue até `http://localhost:3000`. Se a página for carregada sem erros, o aplicativo Ruby on Rails estará se conectando ao banco de dados MySQL no Azure.

Adicione algumas tarefas à página.

![O Ruby on Rails se conecta com êxito ao Banco de Dados do Azure para MySQL (versão prévia)](./media/tutorial-ruby-mysql-app/azure-mysql-connect-success.png)

Para parar o servidor Ruby on Rails, digite `Ctrl + C` no terminal.

### <a name="commit-your-changes"></a>Confirmar as alterações

Execute os seguintes comandos do Git para confirmar as alterações:

```bash
git add .
git commit -m "database.yml updates"
```

O aplicativo está pronto para ser implantado.

## <a name="deploy-to-azure"></a>Implantar no Azure

Nesta etapa, você implanta o aplicativo Ruby on Rails conectado ao MySQL no Serviço de Aplicativo do Azure.

### <a name="configure-a-deployment-user"></a>Configurar um usuário de implantação

[!INCLUDE [Configure deployment user](../../../includes/configure-deployment-user-no-h.md)]

### <a name="create-an-app-service-plan"></a>Criar um plano de Serviço de Aplicativo

[!INCLUDE [Create app service plan no h](../../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

### <a name="create-a-web-app"></a>Criar um aplicativo Web

No Cloud Shell, crie um aplicativo Web no plano do `myAppServicePlan`Serviço de Aplicativo do com o comando [az webapp list-runtimes](/cli/azure/webapp?view=azure-cli-latest#az_webapp_create). 

No exemplo a seguir, substitua `<app_name>` por um nome do aplicativo exclusivo globalmente (os caracteres válidos são `a-z`, `0-9` e `-`). O tempo de execução é definido como `RUBY|2.3`, que implanta a [imagem padrão de Ruby](https://hub.docker.com/r/appsvc/ruby/). Para ver todos os tempos de execução com suporte, execute [az webapp list-runtimes](/cli/azure/webapp?view=azure-cli-latest#az_webapp_list_runtimes). 

```azurecli-interactive
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app_name> --runtime "RUBY|2.3" --deployment-local-git
```

Quando o aplicativo Web for criado, a CLI do Azure mostrará um resultado semelhante ao seguinte exemplo:

```json
Local git is configured with url of 'https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git'
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

Você criou um novo aplicativo Web vazio com a implantação do Git habilitada.

> [!NOTE]
> A URL do Git remoto é mostrada na propriedade `deploymentLocalGitUrl` com o formato `https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git`. Salve essa URL pois você precisará dela mais tarde.
>

### <a name="configure-database-settings"></a>Definir configurações de banco de dados

No Serviço de Aplicativo, defina as variáveis de ambiente como _configurações do aplicativo_ usando o comando [az webapp config appsettings set](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az_webapp_config_appsettings_set) no Cloud Shell.

O comando do Cloud Shell a seguir define as configurações do aplicativo `DB_HOST`, `DB_DATABASE`, `DB_USERNAME` e `DB_PASSWORD`. Substitua os espaços reservados _&lt;appname>_ e _&lt;mysql_server_name>_.

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group myResourceGroup --settings DB_HOST="<mysql_server_name>.mysql.database.azure.com" DB_DATABASE="sampledb" DB_USERNAME="railsappuser@<mysql_server_name>" DB_PASSWORD="MySQLAzure2017"
```

### <a name="configure-rails-environment-variables"></a>Configurar as variáveis de ambiente do Rails

No terminal local, gere uma nova chave secreta para o ambiente de produção do Rails no Azure.

```bash
rails secret
```

Configure as variáveis exigidas pelo ambiente de produção do Rails.

No seguinte comando do Cloud Shell, substitua os dois espaços reservados _&lt;output_of_rails_secret>_ pela nova chave de segredo gerada no terminal local.

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group myResourceGroup --settings RAILS_MASTER_KEY="<output_of_rails_secret>" SECRET_KEY_BASE="<output_of_rails_secret>" RAILS_SERVE_STATIC_FILES="true" ASSETS_PRECOMPILE="true"
```

`ASSETS_PRECOMPILE="true"` pede ao contêiner Ruby padrão que pré-compile os ativos em cada implantação do Git.

### <a name="push-to-azure-from-git"></a>Enviar do Git para o Azure

No terminal local, adicione um remoto do Azure ao repositório Git local.

```bash
git remote add azure <paste_copied_url_here>
```

Envie por push para o Azure remoto para implantar o Ruby no aplicativo Rails. Você deverá inserir a senha fornecida anteriormente como parte da criação do usuário de implantação.

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

### <a name="browse-to-the-azure-web-app"></a>Navegar até o aplicativo Web do Azure

Navegue até `http://<app_name>.azurewebsites.net` e adicione algumas tarefas à lista.

![Aplicativo Ruby on Rails em execução no Serviço de Aplicativo do Azure](./media/tutorial-ruby-mysql-app/ruby-mysql-in-azure.png)

Parabéns! Você está executando um aplicativo Ruby on Rails controlado por dados no Serviço de Aplicativo do Azure.

## <a name="update-model-locally-and-redeploy"></a>Atualizar o modelo localmente e reimplantar

Nesta etapa, você faz uma alteração simples no modelo de dados `task` e no aplicativo Web e, em seguida, publica a atualização no Azure.

Para o cenário de tarefas, modifique o aplicativo para que você possa marcar uma tarefa como concluída.

### <a name="add-a-column"></a>Adicionar uma coluna

No terminal, navegue para a raiz do repositório Git.

Gere uma nova migração que adiciona uma coluna booliana chamada `Done` à tabela `Tasks`:

```bash
rails generate migration add_Done_to_Tasks Done:boolean
```

Este comando gera um novo arquivo de migração no diretório _db/migrate_.


No terminal, execute migrações de banco de dados do Rails para fazer a alteração no banco de dados local.

```bash
rake db:migrate
```

### <a name="update-application-logic"></a>Atualizar a lógica do aplicativo

Abra o arquivo *app/controllers/tasks_controller.rb*. Encontre a seguinte linha no final do arquivo:

```rb
params.require(:task).permit(:Description)
```

Modifique essa linha para incluir o novo parâmetro `Done`.

```rb
params.require(:task).permit(:Description, :Done)
```

### <a name="update-the-views"></a>Atualizar os modos de exibição

Abra o arquivo *app/views/tasks/_form.html.erb*, que é o Formulário de edição.

Localize a linha `<%=f.error_span(:Description) %>` e insira o código a seguir diretamente abaixo dela:

```erb
<%= f.label :Done, :class => 'control-label col-lg-2' %>
<div class="col-lg-10">
  <%= f.check_box :Done, :class => 'form-control' %>
</div>
```

Abra o arquivo *app/views/tasks/show.html.erb*, que é a página de Exibição de registro único. 

Localize a linha `<dd><%= @task.Description %></dd>` e insira o código a seguir diretamente abaixo dela:

```erb
  <dt><strong><%= model_class.human_attribute_name(:Done) %>:</strong></dt>
  <dd><%= check_box "task", "Done", {:checked => @task.Done, :disabled => true}%></dd>
```

Abra o arquivo *app/views/tasks/index.html.erb*, que é a página de Índice para todos os registros.

Localize a linha `<th><%= model_class.human_attribute_name(:Description) %></th>` e insira o código a seguir diretamente abaixo dela:

```erb
<th><%= model_class.human_attribute_name(:Done) %></th>
```

No mesmo arquivo, localize a linha `<td><%= task.Description %></td>` e insira o código a seguir diretamente abaixo dela:

```erb
<td><%= check_box "task", "Done", {:checked => task.Done, :disabled => true} %></td>
```

### <a name="test-the-changes-locally"></a>Testar as alterações localmente

No terminal local, execute o servidor Rails.

```bash
rails server
```

Para ver a alteração de status da tarefa, navegue até `http://localhost:3000` e adicione ou edite itens.

![Caixa de seleção adicionada à tarefa](./media/tutorial-ruby-mysql-app/complete-checkbox.png)

Para parar o servidor Ruby on Rails, digite `Ctrl + C` no terminal.

### <a name="publish-changes-to-azure"></a>Publicar alterações no Azure

No terminal, execute migrações de banco de dados do Rails para o ambiente de produção para fazer a alteração no banco de dados do Azure.

```bash
rake db:migrate RAILS_ENV=production
```

Confirme todas as alterações no Git e, em seguida, envie as alterações de código por push para o Azure.

```bash
git add .
git commit -m "added complete checkbox"
git push azure master
```

Quando `git push` for concluído, navegue para o aplicativo Web do Azure e teste a nova funcionalidade.

![Alterações de banco de dados e modelos publicadas no Azure](media/tutorial-ruby-mysql-app/complete-checkbox-published.png)

Se você tiver adicionado tarefas, elas serão retidas no banco de dados. As atualizações no esquema de dados deixam os dados existentes intactos.

## <a name="manage-the-azure-web-app"></a>Gestão do aplicativo web do Azure

Vá para o [portal do Azure](https://portal.azure.com) para gerenciar o aplicativo Web que você criou.

No menu à esquerda, clique em **Serviços de Aplicativos** e então clique no nome do seu aplicativo Web do Azure.

![Navegação do portal para o aplicativo Web do Azure](./media/tutorial-php-mysql-app/access-portal.png)

A página Visão Geral do seu aplicativo Web é exibida. Aqui, você pode executar tarefas básicas de gerenciamento, como parar, iniciar, reiniciar, procurar e excluir.

O menu à esquerda fornece páginas para configurar o aplicativo.

![Página Serviço de Aplicativo no portal do Azure](./media/tutorial-php-mysql-app/web-app-blade.png)

[!INCLUDE [cli-samples-clean-up](../../../includes/cli-samples-clean-up.md)]

<a name="next"></a>

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu como:

> [!div class="checklist"]
> * Criar um banco de dados MySQL no Azure
> * Conectar um aplicativo Ruby on Rails para MySQL
> * Implantar o aplicativo no Azure
> * Atualizar o modelo de dados e reimplantar o aplicativo
> * Transmitir logs de diagnóstico do Azure
> * Gerenciar o aplicativo no portal do Azure

Vá para o próximo tutorial para saber como mapear um nome DNS personalizado para um aplicativo Web.

> [!div class="nextstepaction"]
> [Mapear um nome DNS personalizado existente para aplicativos Web do Azure](../app-service-web-tutorial-custom-domain.md)
