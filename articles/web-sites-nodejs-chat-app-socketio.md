<properties linkid="dev-nodejs-website-with-socketio" urlDisplayName="Website Using Socket.IO" pageTitle="Node.js Website using Socket.io - Azure tutorial" metaKeywords="Azure Node.js socket.io tutorial, Azure Node.js socket.io, Azure Node.js tutorial" description="A tutorial that demonstrates using socket.io in a node.js website hosted on Azure." metaCanonical="" services="web-sites" documentationCenter="nodejs" title="Build a Node.js Chat Application with Socket.IO on an Azure Website" authors="larryfr" solutions="" videoId="" scriptId="" manager="paulettm" editor="mollybos" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="nodejs" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr"></tags>

# Constrói um aplicativo de bate-papo Node.js com Socket.IO em um website Azure

Socket.IO fornece uma comunicação em tempo real entre o seu servidor node.js e clientes usando WebSockets. Também oferece suporte de retorno a outros transportes (como sondagem comprido) que funcionam com outros navegadores. Este tutorial anda sobre um Socket.IO host com base no aplicativo de bate-papo com um website do Azure. Para mais informações sobre o Socket.IO, consultar [][]<http://socket.io/></a>.

> [WACOM.NOTE] Os procedimentos nesta tarefa se aplicam para Sites do Azure; para serviços em nuvem, consulte [criar um aplicativo de bate-papo do Node. js com Socket.IO em um serviço de nuvem do Azure][criar um aplicativo de bate-papo do Node. js com Socket.IO em um serviço de nuvem do Azure].

## <span id="Download"></span></a> Baixar o exemplo de chat

Para este projeto, usaremos o exemplo de chat do [repositório Socket.IO
GitHub][repositório Socket.IO GitHub]. Execute as seguintes etapas para baixar o exemplo
e adicioná-lo ao projeto que você criou anteriormente.

1.  Baixar uma [versão arquivada ZIP ou GZ][versão arquivada ZIP ou GZ] do projeto Socket.IO (a versão 1.0.6 foi usada para este documento)

2.  Extrair o arquivo e copiar o diretório **examples\\chat**
     ao novo local. Por exemplo,
    **\\node\\chat**.

## <span id="Modify"></span></a>Modificar App.js e instalar os módulos

Antes de implantar o aplicativo no Azure, é necessário
fazer algumas modificações secundárias.

1.  Renomeie o arquivo **index.js** para o **app.js**. Isso permite que o Azure detecte que este é um aplicativo Node.js.

2.  Abra o arquivo **app.js** no Bloco de Notas ou em outro editor de texto.

3.  Localize a seção **Module dependencies** no início de app.js e altere a linha que contém `var io = require('../..')(server);` para `var io = require('socket.io')(server);` conforme mostrado abaixo:

        var express = require('express');
        var app = express();
        var server = require('http').createServer(app);
        // var io = require('../..')(server);
        var io = require('socket.io')(server);
        var port = process.env.PORT || 3000;

Depois de salvar as alterações para app.js, execute as seguintes etapas para
instalar os módulos necessários::

1.  A partir da linha de comando, altere os diretórios para o diretório **\\node\\chat** e use o seguinte comando para instalar os módulos necessários por esse aplicativo:

        npm install

    Isso instalará os módulos listados no arquivo package.json. Quando
    o comando for concluído, você verá uma saída semelhante à
    seguinte:

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

2.  Como esse exemplo originalmente fazia parte do
    repositório Socket.IO GitHub e fazia referência direta à biblioteca do Socket.IO pelo
    caminho relativo, o Socket.IO não era referenciado no arquivo
    package.json, portanto, precisamos instalá-lo emitindo o comando a seguir:

        npm install socket.io@1.0.6 -save

    > [WACOM.NOTE] Embora as versões mais recentes do Socket.IO podem trabalhar com as etapas neste artigo, foi testado com a versão 1.0.6.

## <span id="Publish"></span></a>Criar um site do Azure

Siga estas etapas para criar um Site do Azure e habilitar a publicação Git e, em seguida, habilitar o suporte do WebSocket para o site.

> [WACOM.NOTE]Para concluir este tutorial, você precisa de uma conta do Azure. Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Avaliação gratuita do Azure][Avaliação gratuita do Azure].

1.  Na linha de comando, altere os diretórios para o diretório **\\node\\chat** e use o comando a seguir para criar um novo Site do Azure e ativar um repositório Git para o site e o diretório local. Isso também cria um Git remoto nomeado 'azure'.

        azure site create mysitename --git

    Você deve substituir 'mysitename' por um nome exclusivo para seu site.

2.  Confirme os arquivos existentes no local repositório usando os seguintes comandos:

        git add .
        git commit -m "Initial commit"

3.  Enviar os arquivos para o repositório do Web Site do Azure com o seguinte comando:

        git push azure master

    Você receberá mensagens de status que módulos são importados no servidor. Assim que o processo tiver concluído, o aplicativo será hospedado no seu site do Azure.

    > [WACOM.NOTE] Durante a instalação do módulo, você poderá observar erros que '... O projeto importado não foi encontrado '. Isso podem ser ignorados.

4.  Socket.IO usa o WebSocket, que não é habilitado por padrão no Azure. Para habilitar os websockets, use o seguinte comando:

        azure site set -w

    Se solicitado, digite o nome do site da web.

    > [WACOM.NOTE]
    > O 'site azure set -w' comando fará com que só funcionam com a versão 0.7.4 ou superior da Interface de linha de comando de multiplataforma do Azure. Você também pode habilitar o suporte ao WebSocket usando o Portal de Gerenciamento do Azure.
    >
    > Para habilitar o WebSocket usando o [Portal de Gerenciamento Azure][Portal de Gerenciamento Azure], selecione a página de configuração para seu site da web, selecione 'ON' para a entrada de Web Sockets e, em seguida, clique em Salvar.
    >
    > ![websockets][websockets]

5.  Para exibir o site no Azure, use o seguinte comando para iniciar o navegador da web e navegue até o site da web hospedado:

        azure site browse

Seu aplicativo agora está sendo executado no Azure e pode retransmitir
mensagens de chat entre diferentes clientes usando o Socket.IO.

> [WACOM.NOTE]Para simplificar, este exemplo é limitado a chat entre usuários conectados à mesma instância. Isso significa que se o serviço de nuvem criar duas instâncias de função de trabalho, os usuários só poderão conversar com outros usuários conectados à mesma instância de função de trabalho. Para dimensionar o aplicativo para trabalhar com várias instâncias de função, você pode usar uma tecnologia, como o Service Bus para compartilhar o estado do armazenamento do Socket.IO entre instâncias. Para obter exemplos, consulte os exemplos de uso de Tópicos e filas do Service Bus em [SDK do Azure para o repositório Node.js GitHub][SDK do Azure para o repositório Node.js GitHub].

## Expansão

Os aplicativos Socket.IO podem ser expandidos usando um **adaptador** para distribuir as mensagens e eventos entre várias instâncias do aplicativo. Embora haja vários adaptadores disponíveis, o adaptador [socket.io-redis][socket.io-redis] pode ser facilmente usado com o recurso Cache Redis do Azure.

> [WACOM.NOTE] Um requisito adicional para expandir uma solução Socket.IO é oferecer suporte para sessões complexas. As sessões complexas são habilitadas por padrão para Sites do Azure por meio do Roteamento da Solicitação do Azure. Para obter mais informações, consulte [Afinidade da instância nos Sites do Azure][Afinidade da instância nos Sites do Azure].

### Criar um cache Redis

Executar as etapas no [Criar um cache no Cache Redis do Azure][Criar um cache no Cache Redis do Azure] para criar um novo cache.

> [WACOM.NOTE] Salvar o **Nome do Host** e **Chave primária** para seu cache, dado que serão necessários nas próximas etapas.

### Adicionar os módulos redis e socket.io-redis

1.  Na linha de comando, altere para o diretório **\\node\\chat** e use o seguinte comando:

        npm install socket.io-redis@0.1.3 redis@0.11.0 --save

    > [WACOM.NOTE] As versões especificadas neste comando são as versões usadas ao testar este artigo.

2.  Modificar o arquivo **App.js** para adicionar as seguintes líneas imediatamente após `var io = require('socket.io')(server);`

        var pub = require('redis').createClient(6379,'redishostname', {auth_pass: 'rediskey', return_buffers: true});
        var sub = require('redis').createClient(6379,'redishostname', {auth_pass: 'rediskey', return_buffers: true});

        var redis = require('socket.io-redis');
        io.adapter(redis({pubClient: pub, subClient: sub}));

    Substituir **redishostname** e **rediskey** com o nome e a chave do host para seu cache Redis.

    Isso criará um cliente público e assinado no cache Redis criado anteriormente. Os clientes são usados com o adaptador para configurar o Socket.IO para usar o cache Redis para passar mensagens e eventos entre instâncias do seu aplicativo.

    > [WACOM.NOTE] Embora o adaptador **socket.io-redis** pode se comunicar diretamente com o Redis, a versão atual (a partir do 7/14/2014) não suporta a autenticação requerida pelo cache Redis do Azure. Para que a conexão inicial seja criada usando o módulo **redis**, o cliente é passado ao adaptador **socket.io-redis**.
    >
    > Embora o Cache Redis do Azure oferece suporte a conexões seguras usando a porta 6380, os módulos usados neste exemplo não oferecem suporte a conexões seguras a partir do 7/14/2014. O código acima usa o padrão, porta não protegida de 6380.

3.  Salvar o **app.js** modificado

### Confirme as alterações e implantar novamente

Da linha de comando no diretório **\\node\\chat**, use os seguintes comandos para confirmar as alterações e implantar novamente o aplicativo.

    git add .
    git commit -m "implementing scale out"
    git push azure master

Assim que as alterações tenham sido enviadas por push ao servidor, você pode dimensionar seu site através de várias instâncias usando o seguinte comando.

    azure site scale instances --instances #

Onde **\#** é o número de instâncias a ser criado.

Você pode conectar seu site de vários navegadores ou computadores para verificar que as mensagens sejam enviadas corretamente a todos os clientes.

## <span id="tshooting"></span></a> Solucionar problemas

### Limites de conexão

Os sites do Azure estão disponíveis em vários SKUs, que determina os recursos disponíveis no seu site. Isso inclui o número de conexões permitidas do WebSocket. Para obter mais informações, consulte [Página de Preço de Sites][Página de Preço de Sites].

### As mensagens não estão sendo enviadas usando o WebSockets

Se os navegadores do cliente continuam para sondagens compridas em vez de usar o WebSockets, pode ser devido ao seguinte:

-   **Tente limitar o transporte para apenas WebSockets**

    Para que o Socket.IO use o WebSockets como o transporte de mensagens, o servidor e o cliente devem oferecer suporte ao WebSockets. Se um deles não fizer isso, o Socket.IO negociará outro transporte, como a sondagem comprida. A lista padrão de transportes usados pelo Socket.IO é `websocket, htmlfile, xhr-polling, jsonp-polling`. Você pode forçá-la para usar apenas WebSockets adicionando o seguinte código ao arquivo **app.js**, após a linha que contenha `, nicknames = {};`.

        io.configure(function() {
          io.set('transports', ['websocket']);
        });

    > [WACOM.NOTE] Observe que os navegadores antigos que não ofereçam suporte para WebSockets não serão capazes de conectar-se ao site enquanto o código acima estiver ativo, porque restringe a comunicação apenas ao WebSockets.

-   **Usar o SSL**

    O WebSockets depende de alguns cabeçalhos HTTP menos usados, como o cabeçalho **Atualizar**. Alguns dispositivos de rede intermediários, como os proxies da web, podem remover esses cabeçalhos. Para evitar esse problema, você pode estabelecer a conexão WebSocket em vez da SSL.

    Uma maneira fácil de conseguir isso é configurar o Socket.IO para `match origin protocol`. Isso instrui ao Socket.IO para proteger a comunicação de WebSockets da mesma maneira que a solicitação HTTP/HTTPS originadora para a página web. Se um navegador usar uma URL HTTPS para visitar seu site, as comunicações WebSocket subsequentes através do Socket.IO serão protegidas sobre o SSL.

    Para modificar este exemplo para habilitar essa configuração, adicione o seguinte código ao arquivo **app.js** após a linha que contém `, nicknames = {};`.

        io.configure(function() {
          io.set('match origin protocol', true);
        });

-   **Verificar as configurações do web.config**

    Os sites do Azure que hospedam aplicativos Node.js usam o arquivo **web.config** para rotear as solicitações de entrada para o aplicativo Node.js. Para que o WebSockets funcione corretamente com os aplicativos Note.js, o **web.config** deve conter a seguinte entrada.

        <webSocket enabled="false"/>

    Isso desabilita o módulo IIS WebSockets, que inclui sua própria implementação de Websockets e conflitos com os módulos WebSocket específicos do Node.js, como o Socket.IO. Se essa linha não estiver presente, ou for configurada como `true`, pode ser o motivo pelo qual o transporte WebSocket não esteja funcionando para seu aplicativo.

    Normalmente, os aplicativos Node.js não incluem um arquivo **web.config**, por tanto os Sites do Azure gerarão automaticamente um para os aplicativos Node.js assim que estiverem implantados. Como este arquivo é gerado automaticamente no servidor, você deve usar a URL FTP ou FTPS para seu site para visualizar esse arquivo. Você pode encontrar as URLs do FTP e FTPS para seu site no portal de Gerenciamento do Azure, selecionando seu site e, em seguida, o link **Painel**. As URLs são exibidas na seção **visão rápida**.

    > [WACOM.NOTE] O arquivo **web.config** é gerado apenas pelos sites do Azure se seu aplicativo não fornecer um. Se você fornecer um arquivo **web.config** na raiz do projeto do seu aplicativo, será usado por sites do Azure.

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

    > [WACOM.NOTE] Se seu aplicativo usar um ponto de entrada diferente de **app.js**, você deve substituir todas as ocorrências do **app.js** com o ponto de entrada correto. Por exemplo, substituir o **app.js** com **server.js**.

## Próximas etapas

Neste tutorial, você aprendeu como criar um aplicativo de chat básico hospedado em um site do Azure. Você também pode hospedar o aplicativo como um serviço de nuvem do Azure. Para obter etapas sobre como fazer isso, consulte [criar um aplicativo de bate-papo do Node. js com Socket.IO em um serviço de nuvem do Azure][1]

  []: http://socket.io/
  [criar um aplicativo de bate-papo do Node. js com Socket.IO em um serviço de nuvem do Azure]: http://www.windowsazure.com/pt-BR/develop/nodejs/tutorials/app-using-socketio/
  [repositório Socket.IO GitHub]: https://github.com/Automattic/socket.io
  [versão arquivada ZIP ou GZ]: https://github.com/Automattic/socket.io/releases
  [Avaliação gratuita do Azure]: http://www.windowsazure.com/pt-BR/pricing/free-trial/?WT.mc_id=A7171371E
  [Portal de Gerenciamento Azure]: https://manage.windowsazure.com
  [websockets]: ./media/web-sites-nodejs-chat-app-socketio/websockets.png
  [SDK do Azure para o repositório Node.js GitHub]: https://github.com/WindowsAzure/azure-sdk-for-node
  [socket.io-redis]: https://github.com/automattic/socket.io-redis
  [Afinidade da instância nos Sites do Azure]: http://azure.microsoft.com/blog/2013/11/18/disabling-arrs-instance-affinity-in-windows-azure-web-sites/
  [Criar um cache no Cache Redis do Azure]: http://go.microsoft.com/fwlink/p/?linkid=398592&clcid=0x409
  [Página de Preço de Sites]: /pt-BR/pricing/details/web-sites/
  [1]: /pt-BR/develop/nodejs/tutorials/app-using-socketio/
