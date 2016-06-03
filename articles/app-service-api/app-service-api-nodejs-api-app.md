<properties
	pageTitle="Aplicativo de API do Node.js no Serviço de Aplicativo do Azure | Microsoft Azure"
	description="Saiba como criar um pacote do aplicativo de API RESTful do Node.js e implantá-lo em um aplicativo de API no Serviço de Aplicativo do Azure."
	services="app-service\api"
	documentationCenter="node"
	authors="bradygaster"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="app-service-api"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="node"
	ms.topic="get-started-article"
	ms.date="05/06/2016"
	ms.author="bradygaster"/>

# Criar uma API RESTful do Node. js e implantá-la em um aplicativo de API no Azure

[AZURE.INCLUDE [app-service-api-get-started-selector](../../includes/app-service-api-get-started-selector.md)]

Este tutorial mostra como criar uma API [Node.js](http://nodejs.org) simples e como implantá-la em um [aplicativo de API](app-service-api-apps-why-best-platform.md) no [Serviço de Aplicativo do Azure](../app-service/app-service-value-prop-what-is.md) usando [Git](http://git-scm.com). Você pode usar qualquer sistema operacional que possa executar o Node.js e fará todo o trabalho usando ferramentas de linha de comando como cmd.exe ou bash.

## Pré-requisitos

1. [Node.js](http://nodejs.org) instalado (este exemplo pressupõe que você tenha o Node.js versão 4.2.2)
2. [Git](https://git-scm.com/) instalado
1. Conta do [GitHub](https://github.com/)
1. [Conta de avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/) do Microsoft Azure

## Obter o código de amostra

1. Abra uma interface de linha de comando que pode executar comandos Node.js e Git.

1. Navegue até uma pasta que você possa usar para um repositório Git local e clone o [repositório GitHub que contém o código de exemplo](https://github.com/Azure-Samples/app-service-api-node-contact-list).

		git clone https://github.com/Azure-Samples/app-service-api-node-contact-list.git

	A API de exemplo fornece dois pontos de extremidade: uma solicitação Get para `/contacts` retorna uma lista de nomes e endereços de email no formato JSON, enquanto `/contacts/{id}` retorna apenas o contato selecionado.

## Código do Node.js em scaffolding baseado nos metadados do Swagger

[Swagger](http://swagger.io/) é um formato de arquivo para metadados que descrevem uma API RESTful. O Serviço de Aplicativo do Azure tem [suporte interno para metadados do Swagger](app-service-api-metadata.md). Este tutorial modela um fluxo de trabalho de desenvolvimento de API que prossegue criando primeiro os metadados do Swagger e usando-os como scaffold para o código de servidor para a API. Nesta parte do tutorial, você aprenderá como realizar scaffold do código de servidor do Node.js com base em um arquivo de metadados do Swagger.

>[AZURE.NOTE] Se não quer percorrer as etapas de scaffolding, basta implantar o código de exemplo para um novo aplicativo de API indo diretamente para a seção [Criar um aplicativo de API no Azure](#createapiapp).

1. Execute os comandos a seguir para instalar os módulos NPM **yo** e **generator-swaggerize** globalmente.

		npm install -g yo
		npm install -g generator-swaggerize

	Swaggerize é uma ferramenta que gera o código do servidor para uma API descrita por um arquivo de metadados do Swagger. O arquivo Swagger que você usará é denominado *api.json* e está localizado na pasta *start* do repositório clonado.

2. Navegue até a pasta *start* e execute o comando `yo swaggerize`. O Swaggerize irá fazer uma série de perguntas. Em **Como chamar esse projeto**, digite "contactlist". No caso do **caminho para o documento de swagger**, digite "api.json" e, em **Express, Hapi ou Restify**, digite "express".

		yo swaggerize

	![Linha de comando do Swaggerize](media/app-service-api-nodejs-api-app/swaggerize-command-line.png)
    
	**Observação**: se você encontrar um erro nessa etapa, a próxima etapa explicará como corrigi-lo.

	O Swaggerize cria uma pasta de aplicativo, manipuladores de scaffolding e arquivos de configuração e gera um arquivo **Package.json**. O mecanismo de exibição expressa é usado para gerar a página de Ajuda do Swagger.

3. Se o comando `swaggerize` falhar com um erro de "token inesperado" ou "sequência de escape inválido", corrija a causa do erro editando o arquivo *package.json* gerado. Na linha `regenerate` em `scripts`, altere a barra invertida que precede *api.json* para uma barra "/", para que a linha fique semelhante ao seguinte exemplo:

 		"regenerate": "yo swaggerize --only=handlers,models,tests --framework express --apiPath config/api.json"

1. Navegue até a pasta que contém o código em scaffolding (nesse caso, a subpasta *ContactList*).

1. Execute `npm install`.
	
		npm install
		
2. Instale o módulo NPM **jsonpath**.

		npm install --save jsonpath
        
    ![Instalação de Jsonpath](media/app-service-api-nodejs-api-app/jsonpath-install.png)

1. Instale o módulo NPM **swaggerize-ui**.

		npm install --save swaggerize-ui
        
    ![Instalação de interface do usuário do Swaggerize](media/app-service-api-nodejs-api-app/swaggerize-ui-install.png)

## Personalizar o código com scaffolding

1. Cópia a pasta **lib** da pasta **iniciar** para a pasta **ContactList** criada pelo scaffolder. 

1. Substitua o código no arquivo **handlers/contacts.js** pelo código a seguir.

	Esse código usa os dados JSON armazenados no arquivo **lib/contacts.json** atendido por **lib/contactRepository.js**. O novo código contacts.js responde a solicitações HTTP para obter todos os contatos e retorná-los como uma carga JSON.

        'use strict';
        
        var repository = require('../lib/contactRepository');
        
        module.exports = {
            get: function contacts_get(req, res) {
                res.json(repository.all())
            }
        };

1. Substitua o código no arquivo **handlers/contacts/{id}.js** pelo código a seguir.

        'use strict';

        var repository = require('../../lib/contactRepository');
        
        module.exports = {
            get: function contacts_get(req, res) {
                res.json(repository.get(req.params['id']));
            }    
        };

1. Substitua o código em **server.js** pelo código a seguir.

	As alterações feitas no arquivo server.js são chamadas usando comentários, para que você possa ver as alterações feitas.

        'use strict';

        var port = process.env.PORT || 8000; // first change

        var http = require('http');
        var express = require('express');
        var bodyParser = require('body-parser');
        var swaggerize = require('swaggerize-express');
        var swaggerUi = require('swaggerize-ui'); // second change
        var path = require('path');

        var app = express();

        var server = http.createServer(app);

        app.use(bodyParser.json());

        app.use(swaggerize({
            api: path.resolve('./config/api.json'), // third change
            handlers: path.resolve('./handlers'),
            docspath: '/swagger' // fourth change
        }));

        // change four
        app.use('/docs', swaggerUi({
          docs: '/swagger'  
        }));

        server.listen(port, function () { // fifth and final change
        });

## Testar com a API em execução localmente

1. Ative o servidor usando o executável de linha de comando do Node.js. 

        node server.js

1. Ao navegar até **http://localhost:8000/contacts**, você verá a saída JSON da lista de contatos (ou receberá uma solicitação para baixá-la, dependendo de seu navegador).

    ![Chamada à Api de todos os contatos](media/app-service-api-nodejs-api-app/all-contacts-api-call.png)

1. Ao navegar até **http://localhost:8000/contacts/2**, você verá o contato representado por esse valor de ID.

    ![Chamada à Api de contato específico](media/app-service-api-nodejs-api-app/specific-contact-api-call.png)

1. Os dados JSON do Swagger são atendidos por meio do ponto de extremidade **/swagger**:

    ![Json de Contatos do Swagger](media/app-service-api-nodejs-api-app/contacts-swagger-json.png)

1. A interface de usuário do Swagger é atendida por meio do ponto de extremidade **/docs**. Na interface de usuário do Swagger, você pode usar os recursos avançados de cliente HTML para testar a API.

    ![Interface de usuário do Swagger](media/app-service-api-nodejs-api-app/swagger-ui.png)

## <a id="createapiapp"></a> Criar um novo Aplicativo de API no Portal do Azure

Nesta seção, você verá o processo de criação de um Aplicativo de API novo e vazio no Azure. Na seção seguinte, você conecta o aplicativo a um repositório Git para habilitar o fornecimento contínuo de suas alterações no código.

1. Navegar até o [Portal do Azure](https://portal.azure.com/). 

1. Clique em **Novo > Web + Móvel > Aplicativo de API**.

    ![Novo aplicativo de API no portal](media/app-service-api-nodejs-api-app/new-api-app-portal.png)

4. Digite um **Nome do aplicativo** exclusivo no domínio *azurewebsites.net*, como NodejsAPIApp, além de um número para torná-lo exclusivo.

	Se você inserir um nome usado por outra pessoa, verá um ponto de exclamação vermelho à direita em vez de uma marca de verificação verde e precisará inserir outro nome de site.

	O Azure usará esse nome como o prefixo para a URL da sua API. A URL completa consiste nesse nome mais *.azurewebsites.net*. Por exemplo, se o nome for `NodejsAPIApp`, a URL será `nodejsapiapp.azurewebsites.net`.

6. Na lista suspensa **Grupo de Recursos**, clique em **Novo** e, em **Nome do novo grupo de recursos**, digite "NodejsAPIAppGroup" ou outro nome, se preferir.

	Um [grupo de recursos](../azure-portal/resource-group-portal.md) é uma coleção de recursos do Azure, como aplicativos de API, bancos de dados e VMs. Para este tutorial, é melhor criar um novo grupo de recursos porque isso facilitará a exclusão de todos os recursos do Azure criados para o tutorial em uma única etapa.

4. Clique em **Plano do Serviço de Aplicativo/Local** e clique em **Criar Novo**.

	![Criar plano de Serviço de Aplicativo](./media/app-service-api-nodejs-api-app/newappserviceplan.png)

	Nas etapas a seguir, você criará um plano de Serviço de Aplicativo para o novo grupo de recursos. Um plano de Serviço de Aplicativo especifica os recursos de computação em que seu aplicativo de API é executado. Por exemplo, se você escolher a camada gratuita, seu aplicativo de API será executado em VMs compartilhadas, enquanto que para algumas camadas pagas, ele é executado em VMs dedicadas. Para saber mais sobre os planos do Serviço de Aplicativo, consulte a [Visão geral dos planos do Serviço de Aplicativo](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md).

5. Na folha **Plano do Serviço de Aplicativo**, digite "NodejsAPIAppPlan" ou outro nome, se preferir.

5. Na lista suspensa **Local**, escolha o local mais próximo de você.

	Essa configuração especifica em qual datacenter do Azure o aplicativo será executado. Para este tutorial, você pode selecionar qualquer região e isto não fará uma diferença notável. Porém, para um aplicativo de produção, o ideal é que seu servidor esteja o mais próximo possível dos clientes que o acessam, de modo a minimizar a [latência](http://www.bing.com/search?q=web%20latency%20introduction&qs=n&form=QBRE&pq=web%20latency%20introduction&sc=1-24&sp=-1&sk=&cvid=eefff99dfc864d25a75a83740f1e0090).

5. Clique em **Tipo de preços > Exibir Tudo > F1 Gratuito**.

	Neste tutorial, o tipo de preço gratuito permitirá um desempenho suficiente.

	![Selecione o tipo de preço Gratuito](./media/app-service-api-nodejs-api-app/selectfreetier.png)

6. Na folha **Plano de Serviço de Aplicativo**, clique em **OK**.

7. Na folha **Aplicativo de API**, clique em **Criar**.

## Configurar seu novo aplicativo de API para implantação do Git

Nesta seção do tutorial, você criará as credenciais que usará para a implantação e um repositório Git para o aplicativo de API no Serviço de Aplicativo do Azure. Você implanta o código no aplicativo de API enviando confirmações por push para esse repositório no Serviço de Aplicativo do Azure.

1. Após criar o aplicativo de API, clique em **Serviços de Aplicativos > {o aplicativo de API}** na home page do portal. 

	O portal exibe as folhas **Aplicativo de API** e **Configurações**.

    ![Folha Aplicativo de API do Portal](media/app-service-api-nodejs-api-app/portalapiappblade.png)

    ![Folha Configurações do Portal](media/app-service-api-nodejs-api-app/portalsettingsblade.png)

1. Na folha **Configurações**, role para baixo até a seção **Publicação** e clique em **Credenciais de implantação**.
 
3. Na folha **Definir credenciais de implantação**, digite um nome de usuário e uma senha e clique em **Salvar**.

	Você usará essas credenciais para publicar seu código do Node.js em seu aplicativo de API. Na próxima etapa, você criará o repositório Git do Azure que está associado ao aplicativo de API.

    ![Credenciais de implantação](media/app-service-api-nodejs-api-app/deployment-credentials.png)

1. Na folha **Configurações**, clique em **Fonte da implantação > Escolher Fonte > Repositório Git Local** e clique em **OK**.

    ![Criar um repositório Git](media/app-service-api-nodejs-api-app/create-git-repo.png)

1. Após a criação do repositório Git, a folha muda para mostrar as implantações ativas. Como o repositório é novo, não existem implantações ativas na lista.

    ![Nenhuma implantação ativa](media/app-service-api-nodejs-api-app/no-active-deployments.png)

1. Copie a URL do repositório Git. Para fazer isso, navegue até a folha de seu novo Aplicativo de API e examine a seção **Essentials** da folha. Observe a **URL do clone de Git** na seção **Fundamentos**. Ao passar o mouse sobre essa URL, você vê um ícone à direita que copiará a URL para a área de transferência. Clique nesse ícone para copiar a URL.

    ![Obter a Url do Git no Portal](media/app-service-api-nodejs-api-app/get-the-git-url-from-the-portal.png)

    **Observação**: você precisará da URL do clone Git na próxima etapa, portanto, salve-a em algum lugar por enquanto.

Agora que tem um Aplicativo de API com um repositório Git para fazer o backup dele, você pode enviar o código ao repositório para implantar o código no aplicativo de API.

## Implantar seu código de API no Azure

Nesta seção, você executará as seguintes etapas:

* Crie um repositório Git local que contém o código do servidor para a API.
* Nesse repositório, crie um remoto que aponta para o repositório que você criou para seu aplicativo de API no Azure.
* Envie seu código por push do repositório local para o repositório remoto. 

Os recursos internos de fornecimento contínuo do Serviço de Aplicativo do Azure permitem que você implante o código simplesmente enviando confirmações por push para um repositório Git associado ao seu aplicativo de API.

1. Se você fez a primeira parte do tutorial, copie a pasta `start\ContactList` que criou usando o scaffolder Swaggerize para outra pasta. Caso contrário, copie a pasta `end\ContactList` para outra pasta.

1. Em sua ferramenta de linha de comando, navegue até a nova pasta e execute o comando a seguir para criar um novo repositório Git local.

        git init

     ![Novo Repositório Git local](media/app-service-api-nodejs-api-app/new-local-git-repo.png)

1. Execute o comando a seguir para adicionar um Git remoto ao repositório do seu aplicativo de API.

        git remote add azure YOUR_GIT_CLONE_URL_HERE

    **Observação**: substitua a cadeia de caracteres "YOUR\_GIT\_CLONE\_URL\_HERE" acima por sua própria URL do clone do Git copiada anteriormente.

1. Execute os comandos a seguir para criar uma confirmação que contém todo o código.

        git add .
        git commit -m "initial revision"

    ![Saída da confirmação Git](media/app-service-api-nodejs-api-app/git-commit-output.png)

1. Execute o comando para enviar o seu código no Azure por push. Quando for solicitada uma senha, digite aquela que você criou anteriormente no portal do Azure.

        git push azure master

	Isso dispara uma implantação para o aplicativo de API.

1. No navegador, volte à folha **Implantação** do Aplicativo de API e você verá que a implantação está ocorrendo.

    ![Implantação acontecendo](media/app-service-api-nodejs-api-app/deployment-happening.png)

    Simultaneamente, a interface de linha de comando refletirá o status da implantação enquanto ela estiver ocorrendo.

    ![Implantação do Node.Js acontecendo](media/app-service-api-nodejs-api-app/node-js-deployment-happening.png)

	Após a conclusão da implantação, a folha **Implantações** refletirá a implantação bem-sucedida das alterações de código no Aplicativo de API.

## Teste com a API em execução no Azure
 
3. Copie a **URL** na seção **Essentials** da folha do Aplicativo de API. 

    ![Implantação concluída](media/app-service-api-nodejs-api-app/deployment-completed.png)

1. Usando um cliente de API REST, como Postman ou Fiddler (ou seu navegador da Web), forneça a URL da chamada de API de contatos, que é o ponto de extremidade `/contacts` do Aplicativo de API.

    **Observação:** a URL será `https://{your API app name}.azurewebsites.net/contacts`

    Ao emitir uma solicitação GET para esse ponto de extremidade, você obterá a saída JSON do Aplicativo de API.

    ![Postman acessando Api](media/app-service-api-nodejs-api-app/postman-hitting-api.png)

2. Em um navegador, vá para o ponto de extremidade `/docs` para experimentar a interface do usuário do Swagger enquanto ela é executada no Azure.

Agora que você tem o fornecimento contínuo conectado, pode fazer alterações de código e implantá-las no Azure simplesmente enviando confirmações por push para seu repositório Git do Azure.

## Próximas etapas

Agora, você já criou e implantou com êxito seu primeiro Aplicativo de API usando o Node.js. O próximo tutorial mostra como [consumir aplicativos de API de clientes JavaScript usando CORS](app-service-api-cors-consume-javascript.md). Tutoriais subsequentes na série mostram como implementar a autenticação e a autorização.

<!----HONumber=AcomDC_0518_2016-->