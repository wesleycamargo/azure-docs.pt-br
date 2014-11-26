<properties linkid="dev-nodejs-worker-app-with-socketio" urlDisplayName="App Using Socket.IO" pageTitle="Node.js application using Socket.io - Azure tutorial" metaKeywords="Azure Node.js socket.io tutorial, Azure Node.js socket.io, Azure Node.js tutorial" description="A tutorial that demonstrates using socket.io in a node.js application hosted on Azure." metaCanonical="" services="cloud-services" documentationCenter="nodejs" title="Build a Node.js Chat Application with Socket.IO on an Azure Cloud Service" authors="larryfr" solutions="" manager="" editor="" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="nodejs" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr" />

# Constrói um aplicativo de bate-papo Node.js com Socket.IO em um serviço de nuvem do Azure

O Socket.IO fornece comunicação em tempo real entre seu
servidor e clientes do node.js. Este tutorial explica como hospedar um aplicativo de chat baseado em
socket.IO no Windows Azure. Para mais informações
sobre o Socket.IO, consultar <http://socket.io/>.

A seguinte é uma captura de tela do aplicativo concluído:

![Uma janela do navegador exibindo o serviço hospedado no Azure][Uma janela do navegador exibindo o serviço hospedado no Azure]

## Criar um projeto de Serviço de Nuvem

As etapas a seguir criam o projeto de serviço de nuvem que hospedará o aplicativo Socket.IO.

1.  No **Menu Iniciar** ou **Tela Iniciar**, pesquise por **PowerShell do Azure**. Finalmente, clique com o botão direito do mouse no **PowerShell do Azure** e selecione **Executar como Administrador**.

    ![Ícone PowerShell do Azure][Ícone PowerShell do Azure]

    [WACOM.INCLUDE [install-dev-tools](../includes/install-dev-tools.md)]

2.  Altere os diretórios para o diretório **c:\\node** e digite os seguintes comandos para criar uma nova solução denominada **chatapp** e uma função de trabalho denominada **WorkerRole1**:

        PS C:\node> New-AzureServiceProject chatapp
        PS C:\Node> Add-AzureNodeWorkerRole

    Você verá a seguinte resposta:

    ![Saída de new-azureservice e add-azurenodeworkerrolecmdlets][Saída de new-azureservice e add-azurenodeworkerrolecmdlets]

## Baixar o exemplo de chat

Para este projeto, usaremos o exemplo de chat do [repositório Socket.IO
GitHub][repositório Socket.IO
GitHub]. Execute as seguintes etapas para baixar o exemplo
e adicioná-lo ao projeto que você criou anteriormente.

1.  Crie uma cópia local do repositório usando o botão **Clonar**. Você também pode usar o botão **ZIP** para baixar o projeto.

    ![Uma janela do navegador exibindo https://github.com/LearnBoost/socket.io/tree/master/examples/chat, com o ícone de download ZIP realçado][Uma janela do navegador exibindo https://github.com/LearnBoost/socket.io/tree/master/examples/chat, com o ícone de download ZIP realçado]

2.  Navegue pela estrutura do diretório do repositório local até chegar no diretório **examples\\chat**
    . Copiar os conteúdos deste diretório ao diretório
    **C:\\node\\chatapp\\WorkerRole1** criado anteriormente.

    ![Explorer, exibindo o conteúdo do diretório examples\chat extraído do arquivo morto][Explorer, exibindo o conteúdo do diretório examples\chat extraído do arquivo morto]

    Os itens realçados na captura de tela acima são os arquivos copiados do diretório **examples\\chat**

3.  No diretório **C:\\node\\chatapp\\WorkerRole1**, exclua o arquivo **server.js** e, em seguida, renomeie o arquivo **app.js** para **server.js**. Isso remove o arquivo padrão **server.js** criado anteriormente com o cmdlet **Add-AzureNodeWorkerRole** e o substitui pelo arquivo do aplicativo do exemplo de chat.

### Modificar o Server.js e instalar os módulos

Antes de testar o aplicativo no emulador do Windows Azure, é necessário
fazer algumas modificações secundárias. Execute as seguintes etapas para o arquivo
server.js:

1.  Abra o arquivo server.js no Bloco de Notas ou em outro editor de texto.

2.  Encontrar as **dependências do módulo** seção no início de app.js e altere a linha que contém **sio = require('..//..//lib//socket.io')** para **sio = require('socket.io')** como mostrado abaixo:

        var express = require('express')
        , stylus = require('stylus')
        , nib = require('nib')
        //, sio = require('..//..//lib//socket.io'); //Original
        , sio = require('socket.io');                //Updated

3.  Para garantir que o aplicativo escute na porta correta, abra o
    server.js no Bloco de Notas ou em seu editor favorito e altere
    a linha a seguir, substituindo **3000** por **process.env.port** conforme mostrado abaixo:

        //app.listen(3000, function () {            //Original
        app.listen(process.env.port, function () {  //Updated
          var addr = app.address();
          console.log('   app listening on http://' + addr.address + ':' + addr.port);
        });

Depois de salvar as alterações no server.js, use as seguintes etapas para
instalar os módulos necessários e testar o aplicativo no
emulador do Azure:

1.  Usando o **PowerShell do Azure**, altere os diretórios para o diretório **C:\\node\\chatapp\\WorkerRole1** e use o seguinte comando para instalar os módulos necessários por esse aplicativo:

        PS C:\node\chatapp\WorkerRole1> npm install

    Isso instalará os módulos listados no arquivo package.json. Quando
    o comando for concluído, você verá uma saída semelhante à
    seguinte:

    ![A saída do comando npm install][A saída do comando npm install]

2.  Como esse exemplo originalmente fazia parte do
    repositório Socket.IO GitHub e fazia referência direta à biblioteca do Socket.IO pelo
    caminho relativo, o Socket.IO não era referenciado no arquivo
    package.json, portanto, precisamos instalá-lo emitindo o comando a seguir:

        PS C:\node\chatapp\WorkerRole1> npm install socket.io -save

### Testar e implantar

1.  Inicie o emulador emitindo o comando a seguir:

        PS C:\node\chatapp\WorkerRole1> Start-AzureEmulator -Launch

2.  Quando a janela do navegador for aberta, digite um apelido e, em seguida, pressione enter.
    Isso permitirá que você poste mensagens como um apelido específico. Para testar a
    funcionalidade de vários usuários, abra janelas adicionais do navegador usando
    a mesma URL e digite apelidos diferentes.

    ![Duas janelas de navegador exibindo mensagens de chat do Usuário1 e do Usuário2][Duas janelas de navegador exibindo mensagens de chat do Usuário1 e do Usuário2]

3.  Depois de testar o aplicativo, pare o emulador, emitindo o
    comando a seguir:

        PS C:\node\chatapp\WorkerRole1> Stop-AzureEmulator

4.  Para implantar o aplicativo no Windows Azure, use o cmdlet
    **Publish-AzureServiceProject**. Por exemplo:

        PS C:\node\chatapp\WorkerRole1> Publish-AzureServiceProject -ServiceName mychatapp -Location "East US" -Launch

    <div class="dev-callout">
<strong>Observa&ccedil;&atilde;o</strong>
<p>Use um nome exclusivo, caso contr&aacute;rio, haver&aacute; falha no processo de publica&ccedil;&atilde;o. Depois que a implanta&ccedil;&atilde;o for conclu&iacute;da, o navegador ser&aacute; aberto e navegar&aacute; para o servi&ccedil;o implantado.</p>
<p>Se voc&ecirc; receber um erro informando que o nome da assinatura fornecido n&atilde;o existe no perfil de publica&ccedil;&atilde;o importado, baixe e importe o perfil de publica&ccedil;&atilde;o para sua assinatura antes de implantar no Azure. Consulte a se&ccedil;&atilde;o <b>Implantando o aplicativo no Azure</b> se&ccedil;&atilde;o de <a href="https://www.windowsazure.com/pt-BR/develop/nodejs/tutorials/getting-started/">Criar e implantar um aplicativo do Node.js em um Servi&ccedil;o de Nuvem do Azure (a p&aacute;gina pode estar em ingl&ecirc;s)</a></p>
</div>

    ![Uma janela do navegador exibindo o serviço hospedado no Azure][Uma janela do navegador exibindo o serviço hospedado no Azure]

    <div class="dev-callout">
<strong>Observa&ccedil;&atilde;o</strong>
<p>Se voc&ecirc; receber um erro informando que o nome da assinatura fornecido n&atilde;o existe no perfil de publica&ccedil;&atilde;o importado, baixe e importe o perfil de publica&ccedil;&atilde;o para sua assinatura antes de implantar no Azure. Consulte a se&ccedil;&atilde;o <b>Implantando o aplicativo no Azure</b> se&ccedil;&atilde;o de <a href="https://www.windowsazure.com/pt-BR/develop/nodejs/tutorials/getting-started/">Criar e implantar um aplicativo do Node.js em um Servi&ccedil;o de Nuvem do Azure (a p&aacute;gina pode estar em ingl&ecirc;s)</a></p>
</div>

Seu aplicativo agora está sendo executado no Azure e pode retransmitir
mensagens de chat entre diferentes clientes usando o Socket.IO.

<div class="dev-callout">
<strong>Observa&ccedil;&atilde;o</strong>
<p>Para simplificar, este exemplo &eacute; limitado a chat entre usu&aacute;rios conectados &agrave; mesma inst&acirc;ncia. Isso significa que se o servi&ccedil;o de nuvem criar duas inst&acirc;ncias de fun&ccedil;&atilde;o de trabalho, os usu&aacute;rios s&oacute; poder&atilde;o conversar com outros usu&aacute;rios conectados &agrave; mesma inst&acirc;ncia de fun&ccedil;&atilde;o de trabalho. Para dimensionar o aplicativo para trabalhar com v&aacute;rias inst&acirc;ncias de fun&ccedil;&atilde;o, voc&ecirc; pode usar uma tecnologia, como o Service Bus para compartilhar o estado do armazenamento do Socket.IO entre inst&acirc;ncias. Para obter exemplos, consulte os exemplos de uso de T&oacute;picos e filas do Service Bus em <a href="https://github.com/WindowsAzure/azure-sdk-for-node">SDK do Azure para o reposit&oacute;rio Node.js GitHub</a>.</p>
</div>

## Próximas etapas

Neste tutorial, você aprendeu como criar um aplicativo de chat básico hospedado em um Serviço de Nuvem do Azure. Para saber como hospedar esse aplicativo em um Site do Azure, consulte [Criar um aplicativo de chat do Node.js com Socket.IO em um Site do Azure][Criar um aplicativo de chat do Node.js com Socket.IO em um Site do Azure]

  [Uma janela do navegador exibindo o serviço hospedado no Azure]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-10.png
  [Ícone PowerShell do Azure]: ./media/cloud-services-nodejs-chat-app-socketio/azure-powershell-start.png
  [Saída de new-azureservice e add-azurenodeworkerrolecmdlets]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-1.png
  [Uma janela do navegador exibindo https://github.com/LearnBoost/socket.io/tree/master/examples/chat, com o ícone de download ZIP realçado]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-22.png
  [A saída do comando npm install]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-7.png
  [Duas janelas de navegador exibindo mensagens de chat do Usuário1 e do Usuário2]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-8.png
  [Criar um aplicativo de chat do Node.js com Socket.IO em um Site do Azure]: /pt-BR/develop/nodejs/tutorials/website-using-socketio/
