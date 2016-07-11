<properties 
	pageTitle="Aplicativo Node.js usando Socket.io | Microsoft Azure" 
	description="Saiba como usar socket.io em um aplicativo node.js hospedado no Azure." 
	services="cloud-services" 
	documentationCenter="nodejs" 
	authors="rmcmurray" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="cloud-services" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="nodejs" 
	ms.topic="article" 
	ms.date="06/24/2016" 
	ms.author="robmcm"/>

# Constrói um aplicativo de bate-papo Node.js com Socket.IO em um serviço de nuvem do Azure

O Socket.IO fornece comunicação em tempo real entre seu servidor e clientes do node.js. Este tutorial explica como hospedar um aplicativo de chat baseado em socket.IO no Microsoft Azure. Para mais informações sobre o Socket.IO, veja <http://socket.io/>.

A seguinte é uma captura de tela do aplicativo concluído:

![Uma janela do navegador exibindo o serviço hospedado no Azure][completed-app]

## Pré-requisitos

Verifique se os seguintes produtos e versões estão instalados para concluir com êxito o exemplo deste artigo:

* Instale o [Visual Studio 2013](https://www.visualstudio.com/pt-BR/downloads/download-visual-studio-vs.aspx)
* Instale o [Node.js](https://nodejs.org/download/)
* Instale o [Python versão 2.7.10](https://www.python.org/)

## Criar um projeto de Serviço de Nuvem

As etapas a seguir criam o projeto de serviço de nuvem que hospedará o aplicativo Socket.IO.

1. No **menu Iniciar** ou **tela Inicial**, pesquise por **Windows PowerShell**. Finalmente, clique com o botão direito do mouse em **Windows PowerShell** e selecione **Executar como Administrador**.

	![Ícone PowerShell do Azure][powershell-menu]

2. Crie um diretório chamado **c:\\node**.
 
		PS C:\> md node

3. Altere os diretórios para o diretório **c:\\node**
 
		PS C:\> cd node

4. Digite os seguintes comandos para criar uma nova solução denominada **chatapp** e uma função de trabalho denominada **WorkerRole1**:

		PS C:\node> New-AzureServiceProject chatapp
		PS C:\Node> Add-AzureNodeWorkerRole

	Você verá a seguinte resposta:

	![Saída de new-azureservice e add-azurenodeworkerrolecmdlets](./media/cloud-services-nodejs-chat-app-socketio/socketio-1.png)

## Baixar o exemplo de chat

Para este projeto, usaremos o exemplo de chat do repositório [Socket.IO GitHub]. Execute as seguintes etapas para baixar o exemplo e adicioná-lo ao projeto que você criou anteriormente.

1.  Crie uma cópia local do repositório usando o botão **Clonar**. Você também pode usar o botão **ZIP** para baixar o projeto.

    ![Uma janela do navegador exibindo https://github.com/LearnBoost/socket.io/tree/master/examples/chat, com o ícone de download ZIP realçado][chat-example-view]

3.  Navegue pela estrutura do diretório do repositório local até chegar no diretório **examples\\chat**. Copie o conteúdo desse diretório no diretório **C:\\node\\chatapp\\WorkerRole1** criado anteriormente.

    ![Explorer, exibindo o conteúdo do diretório examples\\chat extraído do arquivo morto][chat-contents]

    Os itens realçados na captura de tela acima são os arquivos copiados do diretório **examples\\chat**

4.  No diretório **C:\\node\\chatapp\\WorkerRole1**, exclua o arquivo **server.js** e, em seguida, renomeie o arquivo **app.js** para **server.js**. Isso remove o arquivo padrão **server.js** criado anteriormente com o cmdlet **Add-AzureNodeWorkerRole** e o substitui pelo arquivo do aplicativo do exemplo de chat.

### Modificar o Server.js e instalar os módulos

Antes de testar o aplicativo no emulador do Microsoft Azure, é necessário fazer algumas modificações secundárias. Execute as seguintes etapas para o arquivo server.js:

1.  Abra o arquivo **server.js** no Visual Studio ou em qualquer editor de texto.

2.  Encontrar as **dependências do módulo** seção no início de app.js e altere a linha que contém **sio = require('..//..//lib//socket.io')** para **sio = require('socket.io')** como mostrado abaixo:

		var express = require('express')
  		, stylus = require('stylus')
  		, nib = require('nib')
		//, sio = require('..//..//lib//socket.io'); //Original
  		, sio = require('socket.io');                //Updated

3.  Para garantir que o aplicativo escute na porta correta, abra o server.js no Bloco de Notas ou em seu editor favorito e altere a linha a seguir, substituindo **3000** por **process.env.port** conforme mostrado abaixo:

        //app.listen(3000, function () {            //Original
		app.listen(process.env.port, function () {  //Updated
		  var addr = app.address();
		  console.log('   app listening on http://' + addr.address + ':' + addr.port);
		});

Depois de salvar as alterações no **server.js**, use as seguintes etapas para instalar os módulos necessários e testar o aplicativo no emulador do Azure:

1.  Usando o **PowerShell do Azure**, altere os diretórios para o diretório **C:\\node\\chatapp\\WorkerRole1** e use o seguinte comando para instalar os módulos necessários por esse aplicativo:

        PS C:\node\chatapp\WorkerRole1> npm install

    Isso instalará os módulos listados no arquivo package.json. Quando o comando for concluído, você verá uma saída semelhante à seguinte:

    ![A saída do comando npm install][The-output-of-the-npm-install-command]

4.  Como esse exemplo originalmente fazia parte do repositório Socket.IO GitHub e fazia referência direta à biblioteca do Socket.IO pelo caminho relativo, o Socket.IO não era referenciado no arquivo package.json, portanto, precisamos instalá-lo emitindo o comando a seguir:

        PS C:\node\chatapp\WorkerRole1> npm install socket.io --save

### Testar e implantar

1.  Inicie o emulador emitindo o comando a seguir:

        PS C:\node\chatapp\WorkerRole1> Start-AzureEmulator -Launch

2.  Abra o navegador da Web e acesse **http://127.0.0.1**.

3.  Quando a janela do navegador for aberta, digite um apelido e, em seguida, pressione enter. Isso permitirá que você poste mensagens como um apelido específico. Para testar a funcionalidade de vários usuários, abra janelas adicionais do navegador usando a mesma URL e digite apelidos diferentes.

    ![Duas janelas de navegador exibindo mensagens de chat do Usuário1 e do Usuário2](./media/cloud-services-nodejs-chat-app-socketio/socketio-8.png)

3.  Depois de testar o aplicativo, pare o emulador, emitindo o comando a seguir:

        PS C:\node\chatapp\WorkerRole1> Stop-AzureEmulator

4.  Para implantar o aplicativo no Azure, use o cmdlet **Publish-AzureServiceProject**. Por exemplo:

        PS C:\node\chatapp\WorkerRole1> Publish-AzureServiceProject -ServiceName mychatapp -Location "East US" -Launch

	> [AZURE.IMPORTANT] Use um nome exclusivo, caso contrário, haverá falha no processo de publicação. Depois que a implantação for concluída, o navegador será aberto e navegará para o serviço implantado.
	> 
	> Se você receber um erro informando que o nome da assinatura fornecido não existe no perfil de publicação importado, baixe e importe o perfil de publicação para sua assinatura antes de implantar no Azure. Consulte a seção **Implantando o aplicativo no Azure** seção de [Criar e implantar um aplicativo do Node.js em um Serviço de Nuvem do Azure (a página pode estar em inglês)](https://azure.microsoft.com/develop/nodejs/tutorials/getting-started/)

    ![Uma janela do navegador exibindo o serviço hospedado no Azure][completed-app]

	> [AZURE.NOTE] Se você receber um erro informando que o nome da assinatura fornecido não existe no perfil de publicação importado, baixe e importe o perfil de publicação para sua assinatura antes de implantar no Azure. Consulte a seção **Implantando o aplicativo no Azure** seção de [Criar e implantar um aplicativo do Node.js em um Serviço de Nuvem do Azure (a página pode estar em inglês)](https://azure.microsoft.com/develop/nodejs/tutorials/getting-started/)

Seu aplicativo agora está sendo executado no Azure e pode retransmitir mensagens de chat entre diferentes clientes usando o Socket.IO.

> [AZURE.NOTE] Para simplificar, este exemplo é limitado a chat entre usuários conectados à mesma instância. Isso significa que se o serviço de nuvem criar duas instâncias de função de trabalho, os usuários só poderão conversar com outros usuários conectados à mesma instância de função de trabalho. Para dimensionar o aplicativo para trabalhar com várias instâncias de função, você pode usar uma tecnologia, como o Service Bus para compartilhar o estado do armazenamento do Socket.IO entre instâncias. Para obter exemplos, consulte os exemplos de uso de Tópicos e filas do Service Bus em [SDK do Azure para o repositório Node.js GitHub](https://github.com/WindowsAzure/azure-sdk-for-node).

##Próximas etapas

Neste tutorial, você aprendeu como criar um aplicativo de chat básico hospedado em um Serviço de Nuvem do Azure. Para saber como hospedar esse aplicativo em um Site do Azure, confira [Criar um aplicativo de chat do Node.js com Socket.IO em um Site do Azure][chatwebsite].

Para obter mais informações, consulte também o [Centro de desenvolvedores do Node.js](/develop/nodejs/).

  [chatwebsite]: /develop/nodejs/tutorials/website-using-socketio/

  [Azure SLA]: http://www.windowsazure.com/support/sla/
  [Azure SDK for Node.js GitHub repository]: https://github.com/WindowsAzure/azure-sdk-for-node
  [completed-app]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-10.png
  [Azure SDK for Node.js]: https://www.windowsazure.com/develop/nodejs/
  [Node.js Web Application]: https://www.windowsazure.com/develop/nodejs/tutorials/getting-started/
  [Socket.IO GitHub]: https://github.com/LearnBoost/socket.io/tree/0.9.14
  [Azure Considerations]: #windowsazureconsiderations
  [Hosting the Chat Example in a Worker Role]: #hostingthechatexampleinawebrole
  [Summary and Next Steps]: #summary
  [powershell-menu]: ./media/cloud-services-nodejs-chat-app-socketio/azure-powershell-start.png

  [chat example]: https://github.com/LearnBoost/socket.io/tree/master/examples/chat
  [chat-example-view]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-22.png
  
  
  [chat-contents]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-5.png
  [The-output-of-the-npm-install-command]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-7.png
  [The output of the Publish-AzureService command]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-9.png
  
 

<!---HONumber=AcomDC_0629_2016-->