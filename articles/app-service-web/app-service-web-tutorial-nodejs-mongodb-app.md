---
title: Compilar um aplicativo Web Node.js e MongoDB no Azure | Microsoft Docs
description: "Saiba como obter um aplicativo Node.js funcionando no Azure, com conexão a um banco de dados DocumentDB com uma cadeia de conexão MongoDB."
services: app-service\web
documentationcenter: nodejs
author: cephalin
manager: erikre
editor: 
ms.assetid: 0b4d7d0e-e984-49a1-a57a-3c0caa955f0e
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 03/30/2017
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: 9eafbc2ffc3319cbca9d8933235f87964a98f588
ms.openlocfilehash: 8dcb006a8cf167cdbfb67de5a11dabf0edbbe41c
ms.lasthandoff: 04/22/2017


---
# <a name="build-a-nodejs-and-mongodb-web-app-in-azure"></a>Compilar um aplicativo Web Node.js e MongoDB no Azure
Este tutorial mostra como criar um aplicativo Web Node.js no Azure e conectá-lo a um banco de dados MongoDB. Quando terminar, você terá um aplicativo MEAN (MongoDB, Express, AngularJS e Node.js) executando em [Aplicativos Web do Serviço de Aplicativo do Azure](app-service-web-overview.md).

![Aplicativo MEAN.js em execução no Serviço de Aplicativo do Azure](./media/app-service-web-tutorial-nodejs-mongodb-app/meanjs-in-azure.png)

## <a name="before-you-begin"></a>Antes de começar

Antes de executar este exemplo, instale localmente os seguintes pré-requisitos:

1. [Baixe e instale o git](https://git-scm.com/)
1. [Baixe e instale o Node.js e NPM](https://nodejs.org/)
1. [Baixe, instale e execute o MongoDB Community Edition](https://docs.mongodb.com/manual/administration/install-community/). 
1. [Baixe e instale o Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="test-local-mongodb-database"></a>Testar banco de dados MongoDB local
Nessa etapa, certifique-se de que seu banco de dados MongoDB local em execução.

Abra a janela do terminal e `CD` para o `bin` diretório da instalação do MongoDB. 

Execute `mongo` no terminal para se conectar ao servidor do MongoDB local.

```bash
mongo
```

Se sua conexão tiver êxito, então seu banco de dados MongoDB já está sendo executado. Caso contrário, certifique-se de que seu banco de dados MongoDB local foi iniciado seguindo as etapas em[Baixe, instale e execute o MongoDB Community Edition](https://docs.mongodb.com/manual/administration/install-community/).

Ao terminar o teste do seu banco de dados MongoDB, digite `Ctrl`+`C` no terminal. 

<a name="step2"></a>

## <a name="create-local-nodejs-application"></a>Criar aplicativo Node.js local
Nessa etapa, você configura o projeto Node.js local.

### <a name="clone-the-sample-application"></a>Clonar o aplicativo de exemplo

Abra a janela do terminal e `CD` para um diretório de trabalho.  

Execute os comandos a seguir para clonar o repositório de exemplo. 

```bash
git clone https://github.com/Azure-Samples/meanjs.git
```

Esse repositório de exemplo contém um aplicativo [MEAN.js](http://meanjs.org/). 

### <a name="run-the-application"></a>Executar o aplicativo

Instale os pacotes necessários e inicie o aplicativo.

```bash
cd meanjs
npm install
npm start
```

Quando o aplicativo estiver totalmente carregado, você deverá ver algo semelhante à seguinte mensagem:

```
--
MEAN.JS - Development Environment

Environment:     development
Server:          http://0.0.0.0:3000
Database:        mongodb://localhost/mean-dev
App version:     0.5.0
MEAN.JS version: 0.5.0
--
```

Navegue até `http://localhost:3000` em um navegador. Clique em **Assinar** no menu superior e tente criar um usuário fictício. 

O aplicativo de exemplo MEAN.js armazena dados do usuário no banco de dados. Se você tiver êxito e o MEAN.js entrar automaticamente no usuário criado, significa que seu aplicativo está gravando dados no banco de dados MongoDB local.

![O MEAN.js conecta-se com êxito ao MongoDB](./media/app-service-web-tutorial-nodejs-mongodb-app/mongodb-connect-success.png)

Tente clicar em **Admin** > **Gerenciar Artigos** para adicionar alguns artigos.

Para parar Node.js a qualquer momento, digite `Ctrl`+`C` no terminal. 

## <a name="create-a-production-mongodb-database"></a>Criar um banco de dados MongoDB de produção

Ness etapa, você cria um banco de dados MongoDB no Azure. Quando seu aplicativo é implantado no Azure, ele usa esse banco de dados para sua carga de trabalho de produção.

Para MongoDB, este tutorial usa [Azure DocumentDB](/azure/documentdb/), que pode oferecer suporte a conexões de cliente MongoDB. Em outras palavras, o aplicativo Node.js sabe apenas que está se conectando a um banco de dados MongoDB. O fato da conexão ter suporte de um banco de dados DocumentDB é transparente para o aplicativo.

### <a name="log-in-to-azure"></a>Fazer logon no Azure

Agora, você vai usar o Azure CLI 2.0 em uma janela de terminal para criar os recursos necessários para hospedar seu aplicativo Node.js no Serviço de Aplicativo do Azure .  Faça logon na sua assinatura do Azure com o comando [az login](/cli/azure/#login) e siga as instruções na tela. 

```azurecli 
az login 
``` 
   
### <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um [grupo de recursos](../azure-resource-manager/resource-group-overview.md) com o [az group create](/cli/azure/group#create). Um grupo de recursos do Azure é um contêiner lógico no qual recursos do Azure, como aplicativos da Web, bancos de dados e contas de armazenamento são implantados e gerenciados. 

O exemplo a seguir cria um grupo de recursos na região da Europa Ocidental:

```azurecli
az group create --name myResourceGroup --location "West Europe"
```

Para ver quais são os valores possíveis para `--location`, use o comando `az appservice list-locations` da CLI do Azure.

### <a name="create-a-documentdb-account"></a>Criar uma conta do Banco de Dados de Documentos

Criar uma conta DocumentDB com o comando [az documentdb create](/cli/azure/documentdb#create).

No seguinte comando, substitua seu próprio nome de DocumentDB exclusivo onde você vê o espaço reservado `<documentdb_name>`. Esse nome exclusivo será usado como parte do seu ponto de extremidade do DocumentDB (`https://<documentdb_name>.documents.azure.com/`), portanto, o nome precisa ser exclusivo em todas as contas do DocumentDB no Azure. 

```azurecli
az documentdb create --name <documentdb_name> --resource-group myResourceGroup --kind MongoDB
```

O parâmetro `--kind MongoDB` habilita conexões do cliente MongoDB.

Quando a conta DocumentDB é criada, a CLI do Azure mostra informações semelhantes ao exemplo a seguir:

```json
{
  "databaseAccountOfferType": "Standard",
  "documentEndpoint": "https://<documentdb_name>.documents.azure.com:443/",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Document
DB/databaseAccounts/<documentdb_name>",
  "kind": "MongoDB",
  "location": "West Europe",
  "name": "<documentdb_name>",
  "readLocations": [
    ...
  ],
  "resourceGroup": "myResourceGroup",
  "type": "Microsoft.DocumentDB/databaseAccounts",
  "writeLocations": [
    ...
  ]
} 
```

## <a name="connect-your-nodejs-application-to-the-database"></a>Conectar o aplicativo Node.js ao banco de dados

Nessa etapa, você conecta o aplicativo de exemplo MEAN.js ao banco de dados DocumentDB que acabou de ser criado, usando uma cadeia de conexão MongoDB. 

### <a name="retrieve-the-database-key"></a>Recuperar a chave do banco de dados

Para conectar-se ao banco de dados DocumentDB, você precisa da chave do banco de dados. Use o comando [az documentdb list-keys](/cli/azure/documentdb#list-keys) para recuperar a chave primária.

```azurecli
az documentdb list-keys --name <documentdb_name> --resource-group myResourceGroup
```

A CLI do Azure emite informações semelhantes ao seguinte exemplo:

```json
{
  "primaryMasterKey": "RUayjYjixJDWG5xTqIiXjC...",
  "primaryReadonlyMasterKey": "...",
  "secondaryMasterKey": "...",
  "secondaryReadonlyMasterKey": "..."
}
```

Copie o valor de `primaryMasterKey` para um editor de texto. Essas informações serão necessárias na próxima etapa.

<a name="devconfig"></a>
### <a name="configure-the-connection-string-in-your-nodejs-application"></a>Configurar a cadeia de conexão em seu aplicativo Node.js

No seu repositório MEAN.js abra `config/env/production.js`.

No objeto `db`, substitua o valor de `uri` conforme mostrado no exemplo a seguir. Certifique-se também de substituir os dois espaços reservados `<documentdb_name>` com seu nome do banco de dados DocumentDB e o espaço reservado `<primary_maste_key>` com a chave copiada na etapa anterior.

```javascript
db: {
  uri: 'mongodb://<documentdb_name>:<primary_maste_key>@<documentdb_name>.documents.azure.com:10250/mean?ssl=true&sslverifycertificate=false',
  ...
},
```

> [!NOTE] 
> A opção `ssl=true` é importante porque o [Azure DocumentDB requer SSL](../documentdb/documentdb-connect-mongodb-account.md#connection-string-requirements). 
>
>

Salve suas alterações.

### <a name="test-the-application-in-production-mode"></a>Testar o aplicativo em modo de produção 

Como alguns outros aplicativos Node.js, o MEAN.js usa `gulp prod` para reduzir e agrupar scripts para o ambiente de produção. Isso gera os arquivos necessários para o ambiente de produção. 

Executar `gulp prod` agora.

```bash
gulp prod
```

Em seguida, execute o seguinte comando para usar a cadeia de conexão configurada em `config/env/production.js`.

```bash
NODE_ENV=production node server.js
```

`NODE_ENV=production` define a variável de ambiente que informa ao Node.js para ser executado no ambiente de produção e `node server.js` inicia o servidor Node.js com `server.js` na sua raiz de repositório. É dessa forma que o aplicativo Node.js é carregado no Azure. 

Quando o aplicativo for carregado, verifique se ele está sendo executado em produção:

```
--
MEAN.JS

Environment:     production
Server:          http://0.0.0.0:8443
Database:        mongodb://<documentdb_name>:<primary_maste_key>@<documentdb_name>.documents.azure.com:10250/mean?ssl=true&sslverifycertificate=false
App version:     0.5.0
MEAN.JS version: 0.5.0
```

Navegue até `http://localhost:8443` em um navegador. Clique em **Assinar** no menu superior e tente criar um usuário fictício exatamente como antes. Se você tiver êxito, seu aplicativo estará gravando dados no banco de dados DocumentDB no Azure. 

## <a name="deploy-the-nodejs-application-to-azure"></a>Implantar o aplicativo Node.js no Azure
Nessa etapa, você implanta seu aplicativo Node.js conectado ao MongoDB no Serviço de Aplicativo do Azure.

### <a name="create-an-app-service-plan"></a>Criar um plano de Serviço de Aplicativo

Criar um plano do Serviço de Aplicativo com o comando [az appservice plan create](/cli/azure/appservice/plan#create). 

> [!NOTE] 
> Um plano do Serviço de Aplicativo representa a coleção de recursos físicos usados para hospedar seus aplicativos. Todos os aplicativos atribuídos a um plano do Serviço de Aplicativo compartilham os recursos definidos por ele, permitindo que você economize ao hospedar vários aplicativos. 
> 
> Os Planos do Serviço de Aplicativo definem: 
> 
> * Região (Europa Setentrional, Leste dos EUA, Sudeste Asiático) 
> * Tamanha da Instância (Pequena, Média, Grande) 
> * Contagem da Escala (uma, duas ou três instâncias etc.) 
> * SKU (Gratuito, Compartilhado, Básico, Standard, Premium) 
> 

O exemplo a seguir cria um plano do Serviço de Aplicativo denominado `myAppServicePlan` usando o tipo de preço **GRATUITO**:

```azurecli
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku FREE
```

Quando o Plano do Serviço de Aplicativo é criado, a CLI do Azure mostra informações semelhantes ao exemplo a seguir:

```json 
{ 
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myAppServicePlan", 
    "kind": "app", 
    "location": "West Europe", 
    "sku": { 
    "capacity": 0, 
    "family": "F", 
    "name": "F1", 
    "tier": "Free" 
    }, 
    "status": "Ready", 
    "type": "Microsoft.Web/serverfarms" 
} 
``` 

### <a name="create-a-web-app"></a>Criar um aplicativo Web

Agora que um plano do Serviço de Aplicativo foi criado, crie um aplicativo Web no `myAppServicePlan` plano do Serviço de Aplicativo. O aplicativo Web fornece um espaço de hospedagem para implantar seu código e fornecer uma URL para exibir o aplicativo implantado. Use o comando [az appservice web create](/cli/azure/appservice/web#create) para criar o aplicativo Web. 

No seguinte comando, substitua `<app_name>` espaço reservado com seu próprio nome de aplicativo exclusivo. O nome exclusivo será usado como a parte do nome de domínio padrão para o aplicativo Web, portanto, o nome deve ser exclusivo entre todos os aplicativos no Azure. Posteriormente, você poderá mapear qualquer entrada DNS personalizada para o aplicativo Web antes de expor para seus usuários. 

```azurecli
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

### <a name="configure-an-environment-variable"></a>Configurar um variável de ambiente

Anteriormente, no tutorial, você codificou a cadeia de conexão do banco de dados em `config/env/production.js`. De acordo com as melhores práticas de segurança, você deseja manter esses dados confidenciais fora do seu repositório Git. Para seu aplicativo em execução no Azure, você usará uma variável de ambiente.

No Serviço de Aplicativo, são definidas as variáveis de ambiente como _configurações do aplicativo_ usando o comando [az appservice web config appsettings update](/cli/azure/appservice/web/config/appsettings#update). 

O exemplo a seguir permite configurar uma configuração de aplicativo `MONGODB_URI` no seu aplicativo Web do Azure. Certifique-se de substituir os espaços reservados como antes.

```azurecli
az appservice web config appsettings update --name <app_name> --resource-group myResourceGroup --settings MONGODB_URI="mongodb://<documentdb_name>:<primary_maste_key>@<documentdb_name>.documents.azure.com:10250/mean?ssl=true"
```

No código Node.js, você acessa essa configuração de aplicativo com `process.env.MONGODB_URI`, assim como você teria acesso a qualquer variável de ambiente. 

Agora, desfaça as alterações para `config/env/production.js` com o seguinte comando:

```bash
git checkout -- .
```

Abra `config/env/production.js` novamente. Observe que o aplicativo MEAN.js padrão já está configurado para usar a variável de ambiente `MONGODB_URI` que você acabou de criar.

```javascript
db: {
  uri: ... || process.env.MONGODB_URI || ...,
  ...
},
```

### <a name="configure-local-git-deployment"></a>Configurar a implantação do git local 

É possível implantar seu aplicativo no Serviço de Aplicativo do Azure de várias maneiras, incluindo FTP, Git local, GitHub, Visual Studio Team Services e BitBucket. Para o FTP e o Git local, é necessário ter um usuário de implantação configurado no servidor para autenticar sua implantação. 

Use o comando [az appservice web deployment user set](/cli/azure/appservice/web/deployment/user#set) para criar suas credenciais no nível da conta. 

> [!NOTE] 
> Um usuário de implantação é necessário para a implantação do FTP e do Git Local em um Serviço de Aplicativo. Esse usuário de implantação é de nível de conta. Sendo assim, é diferente da sua conta de assinatura do Azure. É necessário configurar esse usuário de implantação somente uma vez.

```azurecli
az appservice web deployment user set --user-name <specify-a-username> --password <mininum-8-char-captital-lowercase-number>
```

Use o comando [az appservice web source-control config-local-git](/cli/azure/appservice/web/source-control#config-local-git) para configurar o acesso do Git local para o aplicativo Web do Azure. 

```azurecli
az appservice web source-control config-local-git --name <app_name> --resource-group myResourceGroup
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

Envie para o Azure remoto para implantar seu aplicativo Node.js. Será solicitada a senha que você forneceu anteriormente como parte da criação do usuário de implantação. 

```bash
git push azure master
```

Durante a implantação, o Serviço de Aplicativo do Azure comunica seu andamento com o Git.

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

> [!NOTE]
> É possível notar que o processo de implantação executa [Gulp](http://gulpjs.com/) após `npm install`. O Serviço de Aplicativo não executa tarefas Gulp ou Grunt durante a implantação, portanto, esse repositório de exemplo possui dois arquivos adicionais em seu diretório raiz para habilitá-lo: 
>
> - `.deployment` - Esse arquivo informa o Serviços de Aplicativo para executar `bash deploy.sh` como o script de implantação personalizada.
> - `deploy.sh` - O script de implantação personalizada. Se você revisar o arquivo, verá que ele executa `gulp prod` após `npm install` e `bower install`. 
>
> É possível usar essa abordagem para adicionar qualquer etapa à implantação com base em Git.
>
> Observe que, se você reiniciar seu aplicativo Web do Azure em qualquer ponto, o Serviço de Aplicativo não executará novamente essas tarefas de automação.
>
>

### <a name="browse-to-the-azure-web-app"></a>Navegar até o aplicativo Web do Azure 
Navegue até o aplicativo Web implantado usando o navegador da Web. 

```bash 
http://<app_name>.azurewebsites.net 
``` 

Clique em **Assinar** no menu superior e tente criar um usuário fictício. 

Se você for tiver êxito e o aplicativo entrar automaticamente no usuário criado, significa que seu aplicativo MEAN.js no Azure tem conectividade com o banco de dados MongoDB (DocumentDB). 

![Aplicativo MEAN.js em execução no Serviço de Aplicativo do Azure](./media/app-service-web-tutorial-nodejs-mongodb-app/meanjs-in-azure.png)

Tente clicar em **Admin** > **Gerenciar Artigos** para adicionar alguns artigos. 

**Parabéns!** Você está executando um aplicativo Node.js controlado por dados no Serviço de Aplicativo do Azure.

## <a name="update-data-model-and-redeploy"></a>Atualizar o modelo de dados e reimplantar

Nessa etapa, você faz algumas alterações no modelo de dados `article` e publica as alterações no Azure.

### <a name="update-the-data-model"></a>Atualizar o modelo de dados

Abra `modules/articles/server/models/articles.server.controller.js`.

Em `ArticleSchema`, adicione um tipo `String` chamado `comment`. Quando terminar, seu código de esquema deverá ter essa aparência:

```javascript
var ArticleSchema = new Schema({
  ...,
  user: {
    type: Schema.ObjectId,
    ref: 'User'
  },
  comment: {
    type: String,
    default: '',
    trim: true
  }
});
```

Você concluiu as alterações do modelo. 

### <a name="update-the-articles-code"></a>Atualizar o código dos artigos

Em seguida, atualize o resto do código `articles` para usar `comment`.

No geral, há cinco (5) arquivos que você precisa modificar, o controlador de servidor e as quatro exibições do cliente. 

Primeiro, abra `modules/articles/server/controllers/articles.server.controller.js`.

Na função `update` adicione uma atribuição para `article.comment`. Quando terminar, a função `update` deverá ter esta aparência:

```javascript
exports.update = function (req, res) {
  var article = req.article;

  article.title = req.body.title;
  article.content = req.body.content;
  article.comment = req.body.comment;

  ...
};
```

Em seguida, abra `modules/client/views/view-article.client.view.js`.

Logo acima da marca de fechamento `</section>`, adicione a seguinte linha para exibir `comment` juntamente com o resto dos dados do artigo:

```HTML
<p class="lead" ng-bind="vm.article.comment"></p>
```

Em seguida, abra `modules/client/views/list-articles.client.view.js`.

Logo acima da marca de fechamento `</a>`, adicione a seguinte linha para exibir `comment` juntamente com o resto dos dados do artigo:

```HTML
<p class="list-group-item-text" ng-bind="article.comment"></p>
```

Em seguida, abra `modules/client/views/admin/list-articles.client.view.js`.

Dentro da marca `<div class="list-group">` e logo acima da marca de fechamento `</a>`, adicione a seguinte linha para exibir `comment` juntamente com o resto dos dados do artigo:

```HTML
<p class="list-group-item-text" data-ng-bind="article.comment"></p>
```

Finalmente, abra `modules/client/views/admin/list-articles.client.view.js`.

Localize a marca `<div class="form-group">` que contém o botão enviar, que se parece com isto:

```HTML
<div class="form-group">
  <button type="submit" class="btn btn-default">{{vm.article._id ? 'Update' : 'Create'}}</button>
</div>
```

Logo acima dessa marca, adicione outra marca `<div class="form-group">` que permite editar o campo `comment`. Sua nova marca deve ter esta aparência:

```HTML
<div class="form-group">
  <label class="control-label" for="comment">Comment</label>
  <textarea name="comment" data-ng-model="vm.article.comment" id="comment" class="form-control" cols="30" rows="10" placeholder="Comment"></textarea>
</div>
```

### <a name="test-your-changes-locally"></a>Testar suas alterações localmente

Salve todas as alterações.

Teste suas alterações em modo de produção novamente.

```bash
gulp prod
NODE_ENV=production node server.js
```

> [!NOTE]
> Lembre-se de que `config/env/production.js` foi revertido e a variável de ambiente `MONGODB_URI` está definida apenas no seu aplicativo Web do Azure e não na sua máquina local. Se analisar o arquivo de configuração você verá que a configuração de produção é definida por padrão para usar um banco de dados MongoDB local. Isso garante seus dados de produção não sejam tocados ao testar suas alterações de código localmente.
>
>

Navegue até `http://localhost:8443` em um navegador e certifique-se de que está conectado.

Clique em **Admin** > **Gerenciar Artigos** e, em seguida, adicione um artigo clicando no botão **+**.

Agora, você deverá ver a nova caixa de texto `Comment`.

![Campo de comentário adicionado para Artigos](./media/app-service-web-tutorial-nodejs-mongodb-app/added-comment-field.png)

### <a name="publish-changes-to-azure"></a>Publicar alterações no Azure

Confirme suas alterações no git, em seguida, envie as alterações do código para o Azure.

```bash
git commit -am "added article comment"
git push azure master
```

Quando `git push` estiver completo, navegue até seu aplicativo Web do Azure e tente a nova funcionalidade novamente.

![Alterações de banco de dados e modelos publicadas no Azure](media/app-service-web-tutorial-nodejs-mongodb-app/added-comment-field-published.png)

> [!NOTE]
> Se você adicionou artigos anteriores, ainda será possível vê-los. Os dados existentes no seu DocumentDB não são perdidos. Também, suas atualizações para o esquema de dados e deixa seus dados existentes intactos.
>
>

## <a name="stream-diagnostic-logs"></a>Logs de diagnóstico de fluxo 

Enquanto o aplicativo Node.js executa no Serviço de Aplicativo do Azure, você pode obter logs do console transferidos diretamente para o seu terminal. Dessa forma, é possível obter as mesmas mensagens de diagnóstico para ajudá-lo a depurar erros de aplicativo.

Para iniciar o streaming de log, use o comando [az appservice web log tail](/cli/azure/appservice/web/log#tail).

```azurecli 
az appservice web log tail --name <app_name> --resource-group myResourceGroup 
``` 

Uma vez iniciado o streaming de log, atualize seu aplicativo Web do Azure no navegador para obter algum tráfego da Web. Agora, deve ser possível ver os logs do console transferidos para seu terminal.

Para interromper o streaming de log a qualquer momento, digite `Ctrl`+`C`. 

## <a name="manage-your-azure-web-app"></a>Gerenciar seu aplicativo Web do Azure

Vá para o portal do Azure para ver o aplicativo Web que você criou.

Para fazer isso, entre em [https://portal.azure.com](https://portal.azure.com).

No menu à esquerda, clique em **Serviço de Aplicativo**, em seguida, clique no nome do seu aplicativo Web do Azure.

![Navegação do portal para o aplicativo Web do Azure](./media/app-service-web-tutorial-nodejs-mongodb-app/access-portal.png)

Você foi para a _folha_ de seu aplicativo Web (uma página do portal que abre horizontalmente).

Por padrão, a folha de seu aplicativo Web mostra a página **Visão Geral**. Esta página fornece uma visão de como está seu aplicativo. Aqui, você também pode executar tarefas básicas de gerenciamento como procurar, parar, iniciar, reiniciar e excluir. As guias no lado esquerdo da folha mostram as páginas de configuração diferentes que você pode abrir.

![Folha Serviço de Aplicativo no portal do Azure](./media/app-service-web-tutorial-nodejs-mongodb-app/web-app-blade.png)

Essas guias na folha mostram muitos recursos excelentes que você pode adicionar ao seu aplicativo Web. A lista a seguir fornece algumas possibilidades:

* mapear um nome DNS personalizado
* associar um certificado SSL personalizado
* configurar uma implantação contínua
* Escalar vertical e horizontalmente
* adicionar a autenticação do usuário

<!--

## Step 4 - Download server logs
In this step, you turn on monitoring of your web app with web server logs, and then download these logs. 

### Enable logging
Enable all logging options for your web app.

```azurecli
az appservice web log config --name <app_name> --resource-group myResourceGroup --application-logging true --detailed-error-messages true --failed-request-tracing true --web-server-logging filesystem
```

### Generate errors

To generate some error entries, navigate to a nonexistent page in your web app. For example: `http://<app_name>.azurewebsites.net/404`. 

### Download log files
Download the log files for review.

```azurecli
az appservice web log download --name <app_name> --resource-group myResourceGroup
```

## Step 5 - Scale to another region
In this step, you scale your Node.js app to serve your customers in a new region. That way, you can tailor your web app to customers in different regions, and also put your web app closer to them to improve performance. When you're done with this step, you will have a [Traffic Manager](https://docs.microsoft.com/en-us/azure/traffic-manager/) profile with two endpoints, which route traffic to two web apps which reside in different geographical regions.

1. Create a Traffic Manager profile with a unique name and add it to your resource group.

    ```azurecli
    az network traffic-manager profile create --name myTrafficManagerProfile --resource-group myResourceGroup --routing-method Performance --unique-dns-name <unique-dns-name>
    ```

    > [!NOTE]
    > `--routing-method Performance` specifies that this profile [routes user traffic to the closest endpoint](../traffic-manager/traffic-manager-routing-methods.md).

2. Get the resource ID of your existing Node.js web app.

    ```azurecli
    az appservice web show --name <app_name> --resource-group myResourceGroup --query id --output tsv
    ```

3. Add an endpoint to the Traffic Manager profile and put the output of the last command in `<web-app-1-resource-id>`:

    ```azurecli
    az network traffic-manager endpoint create --name <app_name>-westeurope --profile-name myTrafficManagerProfile --resource-group myResourceGroup --type azureEndpoints --target-resource-id <web-app-1-resource-id>
    ```

4. Your Traffic Manager profile now has an endpoint that points to your web app. Query for its URL to try it out.

    ```azurecli
    az network traffic-manager profile show --name cephalin-express --resource-group myResourceGroup --query dnsConfig.fqdn --output tsv
    ```

    Copy the output into your browser. You should get the default Express page again, with data from your database.

5. Let's add some identifying characteristic to your West Europe app. Add an environment variable.

    ```azurecli
    az appservice web config appsettings update --settings region="Europe" --name <app_name> --resource-group myResourceGroup    
    ```

6. Open `routes/index.js` and change the `router.get()` to use the environment variable.

    ```javascript
    router.get('/', function(req, res, next) {
      res.render('index', { title: 'Express ' + process.env.region, data: output });
    });
    ```

7. Save your changes and push them to Azure.

    ```
    git add .
    git commit -m "added region string."
    git push azure master
    ```

8. Refresh your browser on your Traffic Manager profile's URL. You should now see `Express Europe` in the homepage. 

    Since your Traffic Manager profile only has one endpoint which points to your West Europe web app, this is the only page you'll see. Next, you create a new web app in Southeast Asia and add a new endpoint to the profile.

4. Create an App Service plan and web app in the Southeast Asia region, and deploy the same code to it just like you did in [Step 2]<#step2>.

    ```azurecli
    az appservice plan create --name my-expressjs-appservice-plan-asia --resource-group myResourceGroup --location "Southeast Asia" --sku FREE
    az appservice web create --name <app_name>-asia --plan my-expressjs-appservice-plan-asia --resource-group myResourceGroup
    url=$(az appservice web source-control config-local-git --name <app_name>-asia --resource-group myResourceGroup --query url --output tsv)
    git remote add azureasia $url
    git push azureasia master
    ```

5. Add the same application settings to the new web app. Set the region to `"Asia"`.

    ```azurecli
    az appservice web config appsettings update --settings dbconnstring="mongodb://$accountname:$password@$accountname.documents.azure.com:10250/tutorial?ssl=true&sslverifycertificate=false" --name <app_name>-asia --resource-group myResourceGroup    
    az appservice web config appsettings update --settings region="Asia" --name <app_name>-asia --resource-group myResourceGroup    
    ```

    Since DocumentDB is a [geographically distributed](../documentdb/documentdb-distribute-data-globally.md) NoSQL service, you can use the same MongoDB connection string in the Southeast Asia web app. When the MongoDB client driver connects to your DocumentDB account, Azure automatically figures out where is the closest place to route the connection. No code change is necessary. You only need to add the regions you want to support to your DocumentDB account, which you will do next.

6. Add `Southeast Asia` as a region to your DocumentDB account.

    ```azurecli
    az documentdb update --locations "West Europe"=0 "Southeast Asia"=1 --name $accountname --resource-group myResourceGroup
    ```

3. To finish, add a second endpoint to the Traffic Manager profile and put the output of the last command in `<web-app-2-resource-id>`:

    ```azurecli
    resourceid=$(az appservice web show --name <app_name>-asia --resource-group myResourceGroup --query id --output tsv)
    az network traffic-manager endpoint create -n <app_name>-southeastasia --profile-name myTrafficManagerProfile -g myResourceGroup --type azureEndpoints --target-resource-id resourceid
    ```
  
Now, try to access the URL of your Traffic Manager profile. If you access the URL from the Europe region, you should see "Express Europe", but from the Asia region, you should see "Express Asia".

-->
## <a name="more-resources"></a>Mais recursos

