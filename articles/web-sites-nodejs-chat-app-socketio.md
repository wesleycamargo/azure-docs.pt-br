<properties urlDisplayName="Website Using Socket.IO" pageTitle="Site do Node.js usando Socket.io - tutorial do Azure" metaKeywords="tutorial Node.js socket.io do Azure, Azure Node.js socket.io, tutorial Node.js do Azure" description="Um tutorial que demonstra o uso de socket.io em um site do node.js hospedado no Azure." metaCanonical="" services="web-sites" documentationCenter="nodejs" title="Build a Node.js Chat Application with Socket.IO on an Azure Website" authors="larryfr" solutions="" videoId="" scriptId="" manager="wpickett" editor="mollybos" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="nodejs" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr" />




#Constrói um aplicativo de bate-papo Node.js com Socket.IO em um website Azure

Socket.IO fornece uma comunicação em tempo real entre o seu servidor node.js e clientes usando WebSockets. Também oferece suporte de retorno a outros transportes (como sondagem comprido) que funcionam com outros navegadores. Este tutorial anda sobre um Socket.IO host com base no aplicativo de bate-papo com um website do Azure. Para mais informações sobre o Socket.IO, consulte [http://socket.io/][socketio].

> [WACOM.NOTE] Os procedimentos nesta tarefa se aplicam para sites do Azure. Para serviços de nuvem, consulte <a href="http://www.windowsazure.com/pt-br/develop/nodejs/tutorials/app-using-socketio/">Criar um aplicativo de chat do Node.js com Socket.IO em um serviço de nuvem do Azure</a>.


## <a id="Download"></a>Baixar o exemplo de chat

Para este projeto, usaremos o exemplo de chat do [Socket.IO
Repositório GitHub]. Execute as seguintes etapas para baixar o exemplo
e adicioná-lo ao projeto que você criou anteriormente.

1.  Baixe uma versão [versão arquivada ZIP ou GZ][] do projeto Socket.IO (a versão 1.0.6 foi usada para este documento)


3.  Extraia o arquivo e copie o diretório **examples\\chat**
    em um novo local. Por exemplo, 
    **\\node\\chat**.

## <a id="Modify"></a>Modificar App.js e instalar os módulos

Antes de implantar o aplicativo no Azure, devemos
fazer algumas pequenas modificações.

1.  Renomeie o arquivo **index.js** como **app.js**. Isso permite que o Azure detecte que este é um aplicativo Node.js.

1.  Abra o arquivo **app.js** no Bloco de Notas ou em outro editor de texto.

2.  Encontre a seção **Dependências do módulo** no início de app.js e altere a linha que contém `var io = require('../..')(server);` para `var io = require('socket.io')(server);` como mostrado abaixo:

		var express = require('express');
		var app = express();
		var server = require('http').createServer(app);
		// var io = require('../..')(server);
		var io = require('socket.io')(server);
		var port = process.env.PORT || 3000;


Depois de salvar as alterações no app.js, use as seguintes etapas para
instalar os módulos necessários:

1.  Na linha de comando, altere os diretórios para o diretório **\\node\\chat** e use o seguinte comando para instalar os módulos necessários por esse aplicativo:

        npm install

    Isso instalará os módulos listados no arquivo package.json. Depois de
    o comando concluir, você verá uma saída semelhante
    à seguinte:

	    express@3.4.8 node_modules\express
		├── methods@0.1.0
		├── merge-descriptors@0.0.1
		├── debug@0.8.1
		├── cookie-signature@1.0.1
		├── range-parser@0.0.4
		├── fresh@0.2.0
		├── buffer-crc32@0.2.1
		├── cookie@0.1.0
		├── mkdirp@0.3.5
		├── commander@1.3.2 (keypress@0.1.0)
		├── send@0.1.4 (mime@1.2.11)
		└── connect@2.12.0 (uid2@0.0.3, pause@0.0.1, qs@0.6.6, bytes@0.2.1, raw-body@1.1.2, batch@0.5.0, negotiator@0.3.0, multiparty@2.2.0)

2.  Como esse exemplo originalmente fazia parte do repositório GitHub do Socket.IO
    e fazia referência direta à biblioteca do Socket.IO pelo
    caminho relativo, o Socket.IO não era referenciado no arquivo
    package.json, portanto, precisamos instalá-lo emitindo o comando a seguir:

        npm install socket.io@1.0.6 -save

	> [WACOM.NOTE] Embora as versões mais recentes do Socket.IO possam trabalhar com as etapas neste artigo, ele foi testado com a versão 1.0.6.

## <a id="Publish"></a>Criar um site do Azure

Siga estas etapas para criar um Site do Azure e habilitar a publicação Git e, em seguida, habilitar o suporte do WebSocket para o site.

> [WACOM.NOTE]Para concluir este tutorial, você precisa de uma conta do Azure. Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter mais informações, consulte <a href="http://www.windowsazure.com/pt-br/pricing/free-trial/?WT.mc_id=A7171371E" target="_blank">Azure Free Trial</a>.

1. From the command-line, change directories to the **\\node\chat** directory and use the following command to create a new Azure Website and enable a Git repository for the website and the local directory. This will also create a Git remote named 'azure'.

		azure site create mysitename --git

	You must replace 'mysitename' with a unique name for your website.

2. Commit the existing files to the local repository by using the following commands:

		git add .
		git commit -m "Initial commit"

3. Push the files to the Azure Website repository with the following command:

		git push azure master

	You will receive status messages as modules are imported on the server. Once this process has completed, the application will be hosted on your Azure Website.

 	> [WACOM.NOTE] During module installation, you may notice errors that 'The imported project ... was not found'. These can safely be ignored.

4. Socket.IO uses WebSockets, which are not enabled by default on Azure. To enable web sockets, use the following command:

		azure site set -w

	If prompted, enter the name of the website.

	>[WACOM.NOTE]
	>The 'azure site set -w' command will only work with version 0.7.4 or higher of the Azure Cross-Platform Command-Line Interface. You can also enable WebSocket support using the Azure Management Portal.
	>
	>To enable WebSockets using the [Azure Management Portal](https://manage.windowsazure.com), select the Configure page for your website, select 'ON' for the Web Sockets entry, and then click Save.
	>	
	>![websockets](./media/web-sites-nodejs-chat-app-socketio/websockets.png)
	
5. To view the website on Azure, use the following command to launch your web browser and navigate to the hosted website:

		azure site browse

Your application is now running on Azure, and can relay chat
messages between different clients using Socket.IO.

> [WACOM.NOTE] For simplicity, this sample is limited to chatting between users connected to the same instance. This means that if the cloud service creates two worker role instances, users will only be able to chat with others connected to the same worker role instance. To scale the application to work with multiple role instances, you could use a technology like Service Bus to share the Socket.IO store state across instances. For examples, see the Service Bus Queues and Topics usage samples in the <a href="https://github.com/WindowsAzure/azure-sdk-for-node">Azure SDK for Node.js GitHub repository</a>.

##Scale out

Socket.IO applications can be scaled out by using an __adapter__ to distribute messages and events between multiple application instances. While there are several adapters available, the [socket.io-redis](https://github.com/automattic/socket.io-redis) adapter can be easily used with the Azure Redis Cache feature.

> [WACOM.NOTE] An additional requirement for scaling out a Socket.IO solution is support for sticky sessions. Sticky sessions are enabled by default for Azure Websites through Azure Request Routing. For more information, see [Instance Affinity in Azure Web Sites](http://azure.microsoft.com/blog/2013/11/18/disabling-arrs-instance-affinity-in-windows-azure-web-sites/)

###Create a Redis cache

Perform the steps in [Create a cache in Azure Redis Cache](http://go.microsoft.com/fwlink/p/?linkid=398592&clcid=0x409) to create a new cache.

> [WACOM.NOTE] Save the __Host name__ and __Primary key__ for your cache, as these will be needed in the next steps.

###Add the redis and socket.io-redis modules

1. From a command-line, change to the __\\node\\chat__ directory and use the following command.

		npm install socket.io-redis@0.1.3 redis@0.11.0 --save

	> [WACOM.NOTE] The versions specified in this command are the versions used when testing this article.

2. Modify the __app.js__ file to add the following lines immediately after `var io = require('socket.io')(server);`

		var pub = require('redis').createClient(6379,'redishostname', {auth_pass: 'rediskey', return_buffers: true});
		var sub = require('redis').createClient(6379,'redishostname', {auth_pass: 'rediskey', return_buffers: true});
		
		var redis = require('socket.io-redis');
		io.adapter(redis({pubClient: pub, subClient: sub}));


	Replace __redishostname__ and __rediskey__ with the host name and key for your Redis cache.

	This will create a publish and subscribe client to the Redis cache created previously. The clients are then used with the adapter to configure Socket.IO to use the Redis cache for passing messages and events between instances of your application

	> [WACOM.NOTE] While the __socket.io-redis__ adapter can communicate directly to Redis, the current version (as of 7/14/2014) does not support the authentication required by Azure Redis cache. So the initial connection is created using the __redis__ module, then the client is passed to the __socket.io-redis__ adapter.
	> 
	> While Azure Redis Cache supports secure connections using port 6380, the modules used in this example do not support secure connections as of 7/14/2014. The above code uses the default, unsecure port of 6380.

3. Save the modified __app.js__

###Commit changes and redeploy

From the command-line in the __\\node\\chat__ directory, use the following commands to commit changes and redeploy the application.

	git add .
	git commit -m "implementing scale out"
	git push azure master

Once the changes have been pushed to the server, you can scale your site across multiple instances by using the following command.

	azure site scale instances --instances #

Where __#__ is the number of instances to create. 

You can connect to your website from multiple browsers or computers to verify that messages are correctly sent to all clients.

##<a id="tshooting"></a>Troubleshooting

###Connection limits

Azure Websites is available in multiple SKUs, which determine the resources available to your site. This includes the number of allowed WebSocket connections. For more information, see the [Web Sites Pricing page][pricing].

###Messages aren't being sent using WebSockets

If client browsers keep falling back to long polling instead of using WebSockets, it may be because of one of the following.

* **Try limiting the transport to just WebSockets**

	In order for Socket.IO to use WebSockets as the messaging transport, both the server and client must support WebSockets. If one or the other does not, Socket.IO will negotiate another transport, such as long polling. The default list of transports used by Socket.IO is ` websocket, htmlfile, xhr-polling, jsonp-polling`. You can force it to only use WebSockets by adding the following code to the **app.js** file, after the line containing `, nicknames = {};`.

		io.configure(function() {
		  io.set('transports', ['websocket']);
		});

	> [WACOM.NOTE] Note that older browsers that do not support WebSockets will not be able to connect to the site while the above code is active, as it restricts communication to WebSockets only.

* **Use SSL**

	WebSockets relies on some lesser used HTTP headers, such as the **Upgrade** header. Some intermediate network devices, such as web proxies, may remove these headers. To avoid this problem, you can establish the WebSocket connection over SSL.

	An easy way to accomplish this is to configure Socket.IO to `match origin protocol`. This instructs Socket.IO to secure WebSockets communication the same as the originating HTTP/HTTPS request for the web page. If a browser uses an HTTPS URL to visit your website, subsequent WebSocket communications through Socket.IO will be secured over SSL.

	To modify this example to enable this configuration, add the following code to the **app.js** file after the line containing `, nicknames = {};`.

		io.configure(function() {
		  io.set('match origin protocol', true);
		});

* **Verify web.config settings**

	Azure Websites that host Node.js applications use the **web.config** file to route incoming requests to the Node.js application. For WebSockets to function correctly with Node.js applications, the **web.config** must contain the following entry.

		<webSocket enabled="false"/>

	Isso desabilita o módulo IIS WebSockets, que inclui sua própria implementação de Websockets e conflitos com os módulos WebSocket específicos do Node.js, como o Socket.IO. Se essa linha não estiver presente, ou for configurada como `true, esse pode ser o motivo pelo qual o transporte WebSocket não esteja funcionando para seu aplicativo.

Normalmente, os aplicativos Node.js não incluem um arquivo **web.config**, portanto, os sites do Azure gerarão automaticamente um para os aplicativos Node.js assim que estiverem implantados. Como este arquivo é gerado automaticamente no servidor, você deve usar a URL FTP ou FTPS para seu site para visualizar esse arquivo. Você pode encontrar as URLs do FTP e FTPS para seu site no portal de Gerenciamento do Azure, selecionando seu site e, em seguida, o link **Painel**. As URLs são exibidas na seção **visão rápida**.

	> [WACOM.NOTE] O arquivo **web.config** é gerado apenas pelos sites do Azure se seu aplicativo não fornecer um. Se você fornecer um arquivo **web.config** na raiz do projeto do seu aplicativo, será usado por sites do Azure.

Se a entrada não estiver presente, ou for configurada para um valor `true`, então você deve criar um **web.config** na raiz do seu aplicativo Node.js e especificar um valor `false`.  Para referência, abaixo se encontra um **web.config** padrão para um aplicativo que usa o **app.js** como o ponto de entrada.

		<?xml version="1.0" encoding="utf-8" ?>
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
		      <!-- Indicates that the server.js file is a node.js site to be handled by the iisnode module -->
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
		
		        <!-- All other URLs are mapped to the node.js site entry point -->
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

	> [WACOM.NOTE] Se seu aplicativo usar um ponto de entrada diferente de **app.js**, você deve substituir todas as ocorrências do **app.js** pelo ponto de entrada correto. Por exemplo, substituir o **app.js** por **server.js**.

##Próximas etapas

Neste tutorial, você aprendeu como criar um aplicativo de chat básico hospedado em um site do Azure. Você também pode hospedar o aplicativo como um serviço de nuvem do Azure. Para obter etapas sobre como fazer isso, consulte [Criar um aplicativo de chat do Node.js com Socket.IO em um serviço de nuvem do Azure][cloudservice].

[socketio]: http://socket.io/
[completed-app]: ./media/web-sites-nodejs-chat-app-socketio/websitesocketcomplete.png
[Repositório do Socket.IO GitHub]: https://github.com/Automattic/socket.io
[release]: https://github.com/Automattic/socket.io/releases
[cloudservice]: /pt-br/develop/nodejs/tutorials/app-using-socketio/

[chat-example-view]: ./media/web-sites-nodejs-chat-app-socketio/socketio-2.png
[npm-output]: ./media/web-sites-nodejs-chat-app-socketio/socketio-7.png
[pricing]: /pt-br/pricing/details/web-sites/
