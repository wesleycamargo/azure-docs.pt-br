<properties linkid="dev-nodejs-website-with-socketio" urlDisplayName="Website Usando Socket.IO" pageTitle="Website Node.js usando Socket.io - tutorial Azure" metaKeywords="Tutorial Azure Node.js socket.io, Azure Node.js socket.io, tutorial Azure Node.js" description="Um tutorial que demonstra o uso socket.io no website node.js website hospedado no Azure." metaCanonical="" services="web-sites" documentationCenter="Node.js" title="Constrói um aplicativo de bate-papo Node.js com Socket.IO em um website Azure" authors="larryfr" solutions="" videoId="" scriptId="" manager="paulettm" editor="mollybos" />




#Constrói um aplicativo de bate-papo Node.js com Socket.IO em um website Azure

Socket.IO fornece uma comunicação em tempo real entre o seu servidor node.js e clientes. Este tutorial anda sobre um Socket.IO host com base no aplicativo de bate-papo com um website do Azure. Para mais informações sobre o Socket.IO, consultar [http://socket.io/][socketio].

<div class="dev-callout"> 
<b>Observação</b> 
	<p>Os procedimentos nesta tarefa se aplicam para Sites do Azure; para serviços em nuvem, consulte <a href="http://www.windowsazure.com/pt-br/develop/nodejs/tutorials/app-using-socketio/">criar um aplicativo de bate-papo do Node. js com Socket.IO em um serviço de nuvem do Azure</a>.</p> 
</div>

A seguinte é uma captura de tela do aplicativo concluído:

![Um navegador exibe o aplicativo de bate-papo][completed-app]

## <a id="Download"></a>Baixar o exemplo de chat

Para este projeto, usaremos o exemplo de chat do [repositório Socket.IO GitHub] Execute as seguintes etapas para baixar o exemplo e adicioná-lo ao projeto que você criou anteriormente.

1.  Crie uma cópia local do repositório usando o botão **Clonar**. Você também pode usar o botão **ZIP** para baixar o projeto.

    ![Uma janela do navegador exibindo https://github.com/LearnBoost/socket.io/tree/master/examples/chat, com o ícone de download ZIP realçado][chat-example-view]

3.  Navegue pela estrutura do diretório do repositório local até chegar no diretório **examples\\chat**. Copie o conteúdo deste diretório para um diretório separado como
    **\\node\\chat**

## <a id="Modify"></a>Modificar App.js e instalar os módulos

Antes de implantar o aplicativo no Azure, é necessário fazer algumas modificações secundárias. Execute as seguintes etapas para o arquivo app.js:

1.  Abra o arquivo apps.js no Bloco de Notas ou em outro editor de texto.

2.  Encontrar as **dependências do módulo** seção no início de app.js e altere a linha que contém **sio = require('..//../ / lib//socket.io')** para **sio = require('socket.io')** como mostrado abaixo:

		var express = require('express')
  		, stylus = require('stylus')
  		, nib = require('nib')
		//, sio = require('..//..//lib//socket.io'); //Original
  		, sio = require('socket.io');                //Updated


3.  Para garantir que o aplicativo escute na porta correta, abra o app.js no Bloco de Notas ou em seu editor favorito e altere a linha a seguir, substituindo **3000** por **process.env.PORT**, conforme mostrado abaixo:

        //app.listen(3000, function () {            //Original
		app.listen(process.env.PORT, function () {  //Updated
		  var addr = app.address();
		  console.log('   app listening on http://' + addr.address + ':' + addr.port);
		});

Depois de salvar as alterações para app.js, execute as seguintes etapas para instalar o modules:: necessário

1.  A partir da linha de comando, altere os diretórios para o diretório **\\node\\chat** e use o seguinte comando para instalar os módulos necessários por esse aplicativo:

        npm install

    Isso instalará os módulos listados no arquivo package.json. Quando o comando for concluído, você verá uma saída semelhante à seguinte:

    ![A saída do comando npm install][npm-output]

2.  Como esse exemplo originalmente fazia parte do repositório Socket.IO GitHub e fazia referência direta à biblioteca do Socket.IO pelo caminho relativo, o Socket.IO não era referenciado no arquivopackage.json, portanto, precisamos instalá-lo emitindo o comando a seguir:

        npm install socket.io -save

## <a id="Publish"></a>Criar um Site do Azure e habilitar a publicação Git

Siga estas etapas para criar um Site do Azure e habilitar a publicação Git para o site.

<div class="dev-callout"><strong>Observação</strong>
<p>Para concluir este tutorial, você precisa de uma conta do Azure. Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte <a href="http://www.windowsazure.com/pt-br/pricing/free-trial/?WT.mc_id=A7171371E" target="_blank">Avaliação gratuita do Azure</a>.</p>
</div>

1. Na linha de comando, altere os diretórios para o **\\node\chat** directory e use o comando a seguir para criar um novo Site do Azure e ativar um repositório gito para o site e o diretório local. Isso também cria um Git remoto nomeado 'azure'.

		azure site create mysitename --git

	Você deve substituir 'mysitename' por um nome exclusivo para seu site.

2. Confirme os arquivos existentes no local reposotiry usando os seguintes comandos:

		git add .
		git commit -m "Initial commit"

3. Enviar os arquivos para o repositório do Web Site do Azure com o seguinte comando:

		git push azure master

	Você receberá mensagens de status que módulos são importados no servidor. Quando esse processo for concluído, o aplicativo será hospedado em seu Site da Web do Azure.

 	<div class="dev-callout">
	<b>Observação</b>
	<p>Durante a instalação do módulo, você poderá observar erros que '... O projeto importado não foi encontrado '. Isso podem ser ignorados.</p>
	</div>

4. Socket.IO usa o WebSocket, que não é habilitado por padrão no Azure. Para habilitar os websockets, use o seguinte comando:

		azure site set -w

	Se solicitado, digite o nome do site da web.

	>[WACOM.NOTE]
	>O 'site azure set -w' comando fará com que só funcionam com a versão 0.7.4 ou superior da Interface de linha de comando de multiplataforma do Azure. Você também pode habilitar o suporte ao WebSocket usando o Portal de Gerenciamento do Azure.
	>
	>Para habilitar o WebSocket usando o [Portal de Gerenciamento Azure](https://manage.windowsazure.com), selecione a página de configuração para seu site da web, selecione 'ON' para a entrada de Web Sockets e, em seguida, clique em Salvar.
	>	
	>![websockets](./media/web-sites-nodejs-chat-app-socketio/websockets.png)
	
5. Para exibir o site no Azure, use o seguinte comando para iniciar o navegador da web e navegue até o site da web hospedado:

		azure site browse

Seu aplicativo agora está sendo executado no Azure e pode retransmitir mensagens de chat entre diferentes clientes usando o Socket.IO.

<div class="dev-callout">
<strong>Observação</strong>
<p>Para simplificar, este exemplo é limitado a chat entre usuários conectados à mesma instância. Isso significa que se o serviço de nuvem criar duas instâncias de função de trabalho, os usuários só poderão conversar com outros usuários conectados à mesma instância de função de trabalho. Para dimensionar o aplicativo para trabalhar com várias instâncias de função, você pode usar uma tecnologia, como o Service Bus para compartilhar o estado do armazenamento do Socket.IO entre instâncias. Para obter exemplos, consulte os exemplos de uso de Tópicos e filas do Service Bus em <a href="https://github.com/WindowsAzure/azure-sdk-for-node">SDK do Azure para o repositório Node.js GitHub</a>.</p>
</div>

##Próximas etapas

Neste tutorial, você aprendeu como criar um aplicativo de chat básico hospedado em um site do Azure. Você também pode hospedar o aplicativo como um serviço de nuvem do Azure. Para obter etapas sobre como fazer isso, consulte [criar um aplicativo de bate-papo do Node. js com Socket.IO em um serviço de nuvem do Azure][cloudservice]

[socketio]: http://socket.io/
[completed-app]: ./media/web-sites-nodejs-chat-app-socketio/websitesocketcomplete.png
[repositório Socket.IO GitHub]: https://github.com/LearnBoost/socket.io/tree/0.9.14
[cloudservice]: /pt-br/develop/nodejs/tutorials/app-using-socketio/

[chat-example-view]: ./media/web-sites-nodejs-chat-app-socketio/socketio-2.png
[npm-output]: ./media/web-sites-nodejs-chat-app-socketio/socketio-7.png

