<properties
	pageTitle="Criar um aplicativo de chat do Node.js com Socket.IO no Serviço de Aplicativo do Azure"
	description="Um tutorial que demonstra como usar o socket.io em um aplicativo Web node.js hospedado no Azure."
	services="app-service\web"
	documentationCenter="nodejs"
	authors="rmcmurray"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="nodejs"
	ms.topic="article"
	ms.date="06/24/2016"
	ms.author="robmcm"/>

# Criar um aplicativo de chat do Node.js com Socket.IO no Serviço de Aplicativo do Azure

Socket.IO fornece uma comunicação em tempo real entre o seu servidor node.js e clientes usando WebSockets. Ele também dá suporte a fallback para outros transportes (como sondagem longa) que funcionam com navegadores mais antigos. Este tutorial explicará a você passo a passo como hospedar um aplicativo de chat baseado em Socket.IO como um aplicativo Web do Azure e como dimensionar o aplicativo usando o [Cache Redis do Azure]. Para obter mais informações sobre o Socket.IO, veja <http://socket.io/>.

> [AZURE.NOTE] Os procedimentos nesta tarefa aplicam-se a [Aplicativos Web do Serviço de Aplicativo]. Para serviços de nuvem, consulte [Criar um aplicativo de chat do Node.js com Socket.IO em um serviço de nuvem do Azure].

## Baixar o exemplo de chat

Para este projeto, usaremos o exemplo de chat do [repositório Socket.IOGitHub]. Execute as seguintes etapas para baixar o exemplo e adicioná-lo ao projeto que você criou anteriormente.

1.  Baixar uma [versão arquivada ZIP ou GZ] do projeto Socket.IO (a versão 1.3.5 foi usada para este documento)

1.  Extrair o arquivo e copiar o diretório **examples\\chat** para um novo local. Por exemplo, **\\node\\chat**.

## Modificar o app.js e instalar os módulos

1.  Renomeie o arquivo **index.js** para o **app.js**. Isso permite que o Azure detecte que este é um aplicativo Node.js.

1.  Abra o arquivo **app.js** em um editor de texto. Altere a linha que contém `var io = require('../..')(server);`, conforme mostrado abaixo:

		var express = require('express');
		var app = express();
		var server = require('http').createServer(app);
		// var io = require('../..')(server);
        // New:
		var io = require('socket.io')(server);
		var port = process.env.PORT || 3000;

1. Abra o arquivo **package.json** e adicione uma referência ao socket.io em `dependencies`, conforme mostrado abaixo:

        "dependencies": {
		  "express": "3.4.8",
		  "socket.io": "1.3.5"
		}

1. Na linha de comando, altere para o diretório **\\node\\chat** e use npm para instalar os módulos necessários para esse aplicativo:

        npm install

    Isso instalará os módulos em uma subpasta chamada **node\_modules**.

## Criar um aplicativo Web do Azure

Siga estas etapas para criar um aplicativo Web do Azure e habilitar a publicação Git e, em seguida, habilitar o suporte dado ao site pelo WebSocket.

> [AZURE.NOTE] Para concluir este tutorial, você precisa de uma conta do Azure. Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte <a href="http://www.windowsazure.com/pricing/free-trial/?WT.mc_id=A7171371E" target="_blank">Avaliação Gratuita do Azure</a>.

1. Instalar a interface de linha de comando do Azure (CLI do Azure) e conecte-se à sua assinatura do Azure. Consulte [Instalar e configurar a CLI do Azure](../xplat-cli-install.md).

1. Se for a primeira vez que você configura um repositório no Azure, você precisa criar suas credenciais de logon. Na CLI do Azure, digite o seguinte comando:

		azure site deployment user set [username] [password]

1. Alterne para o diretório **\\node\\chat** e use o comando a seguir para criar um novo aplicativo Web do Azure e um repositório Git local. Esse comando também cria um repositório remoto do Git chamado 'azure'.

		azure site create mysitename --git

	Você deve substituir 'mysitename' por um nome exclusivo para seu aplicativo Web.

1. Confirme os arquivos existentes no local repositório usando os seguintes comandos:

		git add .
		git commit -m "Initial commit"

1. Envie por push os arquivos para o repositório de aplicativos Web do Azure com o comando a seguir:

		git push azure master

	Quando solicitado, insira suas credenciais da etapa 2. Você receberá mensagens de status que módulos são importados no servidor. Quando esse processo for concluído, o aplicativo será hospedado em seu aplicativo Web do Azure.

 	> [AZURE.NOTE] Durante a instalação do módulo, você poderá observar erros que '... O projeto importado não foi encontrado '. Isso podem ser ignorados.

1. Socket.IO usa o WebSocket, que não é habilitado por padrão no Azure. Para habilitar os websockets, use o seguinte comando:

		azure site set -w

	Se solicitado, digite o nome do aplicativo Web.

	>[AZURE.NOTE]
	O comando 'azure site set -w' funcionará somente com a versão 0.7.4 ou superior da Interface de Linha de Comando do Azure. Você também pode habilitar o suporte ao WebSocket usando o [Portal do Azure](https://portal.azure.com).
	>
	>Para habilitar WebSockets usando o Portal do Azure, clique no aplicativo Web na folha de aplicativos Web, clique em **Todas as configurações** > **Configurações do aplicativo**. Em **Web Sockets**, clique em **Ativado**. Em seguida, clique em **Salvar**.

1. Para exibir o aplicativo Web no Azure, use o comando a seguir para iniciar seu navegador da Web e navegue até o aplicativo Web hospedado:

		azure site browse

Seu aplicativo agora está sendo executado no Azure e pode retransmitir mensagens de chat entre diferentes clientes usando o Socket.IO.

## Expansão

Os aplicativos Socket.IO podem ser expandidos usando um __adaptador__ para distribuir as mensagens e eventos entre várias instâncias do aplicativo. Embora haja vários adaptadores disponíveis, o adaptador [socket.io-redis] pode ser facilmente usado com o recurso Cache Redis do Azure.

> [AZURE.NOTE] Um requisito adicional para expandir uma solução Socket.IO é dar suporte a sessões complexas. As sessões complexas são habilitadas por padrão para Aplicativos Web do Azure por meio do Request Routing do Azure. Para obter mais informações, consulte [Afinidade da instância nos Sites do Azure].

### Criar um cache Redis

Executar as etapas no [Criar um cache no Cache Redis do Azure] para criar um novo cache.

> [AZURE.NOTE] Salvar __Nome do Host__ e __Chave primária__ para seu cache, já que estes serão necessários nas próximas etapas.

### Adicionar os módulos redis e socket.io-redis

1. Na linha de comando, altere para o diretório __\\node\\chat__ e use o seguinte comando:

		npm install socket.io-redis@0.1.4 redis@0.12.1 --save

	> [AZURE.NOTE] As versões especificadas neste comando são as versões usadas ao testar este artigo.

1. Modificar o arquivo __app.js__ para adicionar as seguintes linhas imediatamente após `var io = require('socket.io')(server);`

		var pub = require('redis').createClient(6379,'redishostname', {auth_pass: 'rediskey', return_buffers: true});
		var sub = require('redis').createClient(6379,'redishostname', {auth_pass: 'rediskey', return_buffers: true});

		var redis = require('socket.io-redis');
		io.adapter(redis({pubClient: pub, subClient: sub}));

	Substituir __redishostname__ e __rediskey__ com o nome e a chave do host para seu cache Redis.

	Isso criará um cliente público e assinado no cache Redis criado anteriormente. Os clientes são usados com o adaptador para configurar o Socket.IO para usar o cache Redis para passar mensagens e eventos entre instâncias do seu aplicativo.

	> [AZURE.NOTE] Embora o adaptador __socket.io-redis__ possa se comunicar diretamente com o Redis, a versão atual não dá suporte à autenticação requerida pelo cache Redis do Azure. Para que a conexão inicial seja criada usando o módulo __redis__, o cliente é passado ao adaptador __socket.io-redis__.
	>
	> Embora o Cache Redis do Azure dê suporte a conexões seguras usando a porta 6380, os módulos usados neste exemplo não dão suporte a conexões seguras a partir de 14/7/2014. O código acima usa o padrão, a porta não segura 6379.

1. Salvar o __app.js__ modificado

### Confirme as alterações e implantar novamente

Da linha de comando no diretório __\\node\\chat__, use os seguintes comandos para confirmar as alterações e implantar novamente o aplicativo.

	git add .
	git commit -m "implementing scale out"
	git push azure master

Assim que as alterações tenham sido enviadas por push ao servidor, você pode dimensionar seu site através de várias instâncias usando o seguinte comando.

	azure site scale instances --instances #

Onde __#__ é o número de instâncias a ser criado.

Você pode conectar seu aplicativo Web por meio de vários navegadores ou computadores para verificar que as mensagens sejam enviadas corretamente a todos os clientes.

## Solucionar problemas

### Limites de conexão

Os aplicativos Web do Azure estão disponíveis em vários SKUs, que determinam os recursos disponíveis no seu site. Isso inclui o número de conexões permitidas do WebSocket. Para obter mais informações, consulte a [Página de preços de aplicativos Web].

### As mensagens não estão sendo enviadas usando o WebSockets

Se os navegadores do cliente continuam para sondagens compridas em vez de usar o WebSockets, pode ser devido ao seguinte:

* **Tente limitar o transporte para apenas WebSockets**

	Para que o Socket.IO use o WebSockets como o transporte de mensagens, o servidor e o cliente devem oferecer suporte ao WebSockets. Se um deles não fizer isso, o Socket.IO negociará outro transporte, como a sondagem comprida. A lista padrão de transportes usados pelo Socket.IO é ` websocket, htmlfile, xhr-polling, jsonp-polling`. Você pode forçá-la para usar apenas WebSockets adicionando o seguinte código ao arquivo **app.js**, após a linha que contenha `, nicknames = {};`.

		io.configure(function() {
		  io.set('transports', ['websocket']);
		});

	> [AZURE.NOTE] Observe que os navegadores antigos que não deem suporte a WebSockets não serão capazes de conectar-se ao site enquanto o código acima estiver ativo, porque ele restringe a comunicação apenas aos WebSockets.

* **Usar o SSL**

	O WebSockets depende de alguns cabeçalhos HTTP menos usados, como o cabeçalho **Atualizar**. Alguns dispositivos de rede intermediários, como os proxies da web, podem remover esses cabeçalhos. Para evitar esse problema, você pode estabelecer a conexão WebSocket em vez da SSL.

	Uma maneira fácil de conseguir isso é configurar o Socket.IO para `match origin protocol`. Isso instrui ao Socket.IO para proteger a comunicação de WebSockets da mesma maneira que a solicitação HTTP/HTTPS originadora para a página web. Se um navegador usar uma URL HTTPS para visitar seu site, as comunicações WebSocket subsequentes através do Socket.IO serão protegidas sobre o SSL.

	Para modificar este exemplo para habilitar essa configuração, adicione o seguinte código ao arquivo **app.js** após a linha que contém `, nicknames = {};`.

		io.configure(function() {
		  io.set('match origin protocol', true);
		});

* **Verificar as configurações do web.config**

	Os aplicativos Web do Azure que hospedam aplicativos Node.js usam o arquivo **web.config** para rotear as solicitações de entrada para o aplicativo Node.js. Para que o WebSockets funcione corretamente com os aplicativos Note.js, o **web.config** deve conter a seguinte entrada.

		<webSocket enabled="false"/>

	Isso desabilita o módulo IIS do WebSockets, que inclui sua própria implementação do WebSockets e está em conflito com módulos WebSockets específicos de Node.js, como Socket.IO. Se essa linha não estiver presente ou estiver definida como `true`, isso pode ser o motivo pelo qual o transporte de WebSocket não está funcionando para seu aplicativo.

	Normalmente, os aplicativos Node.js não incluem um arquivo **web.config**, por tanto os Sites do Azure gerarão automaticamente um para os aplicativos Node.js assim que estiverem implantados. Como este arquivo é gerado automaticamente no servidor, você deve usar a URL FTP ou FTPS para seu site para visualizar esse arquivo. Você pode encontrar as URLs de FTP e FTPS para seu site no portal clássico selecionando seu aplicativo Web e o link **Painel**. As URLs são exibidas na seção **visão rápida**.

	> [AZURE.NOTE] O arquivo **web.config** será gerado pelos sites do Azure apenas se seu aplicativo não fornecer um. Se você fornecer um arquivo **web.config** na raiz do projeto do seu aplicativo, esse arquivo será usado por aplicativos Web do Azure.

	Se a entrada não estiver presente, ou for configurada para um valor `true`, então você deve criar um **web.config** na raiz do seu aplicativo Node.js e especificar um valor `false`. Para referência, abaixo se encontra um **web.config** padrão para um aplicativo que usa o **app.js** como o ponto de entrada.

		<?xml version="1.0" encoding="utf-8"?>
		<!--
		     This configuration file is required if iisnode is used to run node processes behind
		     IIS or IIS Express.  For more information, visit:

		     https://github.com/tjanczuk/iisnode/blob/master/src/samples/configuration/web.config
		-->

		<configuration>
		  <system.webServer>
		    <!-- Visit http://blogs.msdn.com/b/windowsazure/archive/2013/11/14/introduction-to-websockets-on-windows-azure-web-sites.aspx for more information on WebSocket support -->
		    <webSocket enabled="false" />
		    <handlers>
		      <!-- Indicates that the server.js file is a node.js web app to be handled by the iisnode module -->
		      <add name="iisnode" path="app.js" verb="*" modules="iisnode"/>
		    </handlers>
		    <rewrite>
		      <rules>
		        <!-- Do not interfere with requests for node-inspector debugging -->
		        <rule name="NodeInspector" patternSyntax="ECMAScript" stopProcessing="true">
		          <match url="^app.js\/debug[\/]?" />
		        </rule>

		        <!-- First we consider whether the incoming URL matches a physical file in the /public folder -->
		        <rule name="StaticContent">
		          <action type="Rewrite" url="public{REQUEST_URI}"/>
		        </rule>

		        <!-- All other URLs are mapped to the node.js web app entry point -->
		        <rule name="DynamicContent">
		          <conditions>
		            <add input="{REQUEST_FILENAME}" matchType="IsFile" negate="True"/>
		          </conditions>
		          <action type="Rewrite" url="app.js"/>
		        </rule>
		      </rules>
		    </rewrite>
		    <!--
		      You can control how Node is hosted within IIS using the following options:
		        * watchedFiles: semi-colon separated list of files that will be watched for changes to restart the server
		        * node_env: will be propagated to node as NODE_ENV environment variable
		        * debuggingEnabled - controls whether the built-in debugger is enabled

		      See https://github.com/tjanczuk/iisnode/blob/master/src/samples/configuration/web.config for a full list of options
		    -->
		    <!--<iisnode watchedFiles="web.config;*.js"/>-->
		  </system.webServer>
		</configuration>

	Se seu aplicativo usar um ponto de entrada diferente de **app.js**, você deve substituir todas as ocorrências do **app.js** pelo ponto de entrada correto. Por exemplo, substituir o **app.js** com **server.js**.

>[AZURE.NOTE] Se desejar começar a usar o Serviço de Aplicativo do Azure antes de inscrever-se em uma conta do Azure, vá para [Experimentar o Serviço de Aplicativo], onde você pode criar imediatamente um aplicativo Web inicial de curta duração no Serviço de Aplicativo. Nenhum cartão de crédito é exigido, sem compromissos.

## Próximas etapas

Neste tutorial, você aprendeu como criar um aplicativo de chat hospedado em um aplicativo Web do Azure. Você também pode hospedar o aplicativo como um serviço de nuvem do Azure. Para obter etapas sobre como fazer isso, consulte [criar um aplicativo de bate-papo do Node. js com Socket.IO em um serviço de nuvem do Azure]

Para obter mais informações, consulte também o [Centro de desenvolvedores do Node.js].

## O que mudou

* Para obter um guia sobre a alteração dos Sites para o Serviço de Aplicativo, consulte: [Serviço de Aplicativo do Azure e seu impacto sobre os serviços do Azure existentes].

<!-- URL List -->

[Cache Redis do Azure]: /documentation/services/redis-cache/
[Aplicativos Web do Serviço de Aplicativo]: http://go.microsoft.com/fwlink/?LinkId=529714
[Página de preços de aplicativos Web]: http://go.microsoft.com/fwlink/?LinkId=511643
[Criar um aplicativo de chat do Node.js com Socket.IO em um serviço de nuvem do Azure]: ../cloud-services/cloud-services-nodejs-chat-app-socketio.md
[criar um aplicativo de bate-papo do Node. js com Socket.IO em um serviço de nuvem do Azure]: ../cloud-services/cloud-services-nodejs-chat-app-socketio.md
[Install and Configure the Azure CLI]: ../xplat-cli-install.md
[Serviço de Aplicativo do Azure e seu impacto sobre os serviços do Azure existentes]: http://go.microsoft.com/fwlink/?LinkId=529714
[Centro de desenvolvedores do Node.js]: /develop/nodejs/
[Experimentar o Serviço de Aplicativo]: http://go.microsoft.com/fwlink/?LinkId=523751
[Afinidade da instância nos Sites do Azure]: https://azure.microsoft.com/blog/2013/11/18/disabling-arrs-instance-affinity-in-windows-azure-web-sites/
[Criar um cache no Cache Redis do Azure]: ../redis-cache/cache-dotnet-how-to-use-azure-redis-cache.md

[socket.io-redis]: https://github.com/socketio/socket.io-redis
[repositório Socket.IOGitHub]: https://github.com/socketio/socket.io
[versão arquivada ZIP ou GZ]: https://github.com/socketio/socket.io/releases

<!-- IMG List -->

[chat-example-view]: ./media/web-sites-nodejs-chat-app-socketio/socketio-2.png
[npm-output]: ./media/web-sites-nodejs-chat-app-socketio/socketio-7.png
[completed-app]: ./media/web-sites-nodejs-chat-app-socketio/websitesocketcomplete.png

<!---HONumber=AcomDC_0629_2016-->