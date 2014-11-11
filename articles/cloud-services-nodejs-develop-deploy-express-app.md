<properties linkid="dev-nodejs-basic-web-app-with-express" urlDisplayName="Web App with Express" pageTitle="Web App with Express (Node.js) - Azure Tutorial" metaKeywords="Azure Node.js hello world tutorial, Azure Node.js hello world, Azure Node.js Getting Started tutorial, Azure Node.js tutorial, Azure Node.js Express tutorial" description="An tutorial that builds on the cloud service tutorial, and demonstrates how to use the Express module." metaCanonical="" services="cloud-services" documentationCenter="nodejs" title="Build a Node.js web application using Express on an Azure Cloud Service" authors="larryfr" solutions="" manager="" editor="" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="nodejs" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr" />

# Criar um aplicativo web do Node.js usando o Express em um Serviço de Nuvem do Windows Azure

O Node.js inclui um conjunto mínimo de funcionalidades em tempo de execução básico.
Os desenvolvedores normalmente usam módulos de terceiros para fornecer
funcionalidade adicional ao desenvolver um aplicativo do Node.js. Neste tutorial,
você irá criar um novo aplicativo usando o módulo [Express][Express] que fornece uma estrutura MVC para criar aplicativos web do Node.js.

A seguinte é uma captura de tela do aplicativo concluído:

![Um navegador da web exibindo Bem-vindo ao Express no Azure][Um navegador da web exibindo Bem-vindo ao Express no Azure]

## Criar um projeto de Serviço de Nuvem

Execute as seguintes etapas para criar um novo projeto de serviço de nuvem chamado 'expressapp':

1.  No **Menu Iniciar** ou **Tela Iniciar**, pesquise por **PowerShell do Azure**. Finalmente, clique com o botão direito do mouse no **PowerShell do Azure** e selecione **Executar como Administrador**.

    ![Ícone PowerShell do Azure][Ícone PowerShell do Azure]

    [WACOM.INCLUDE [install-dev-tools](../includes/install-dev-tools.md)]

2.  Altere os diretórios para o diretório **c:\\node** e digite os seguintes comandos para criar uma nova solução denominada **expressapp** e uma função de trabalho denominada **WebRole1**:

        PS C:\node> New-AzureServiceProject expressapp
        PS C:\Node\expressapp> Add-AzureNodeWebRole
        PS C:\Node\expressapp> Set-AzureServiceProjectRole WebRole1 node 0.10.21

    > [WACOM.NOTE] Por padrão, o **Add-AzureNodeWebRole** usa uma versão mais antiga do Node.js. A instrução **Set-AzureServiceProjectRole** acima instrui o Azure usar a versão 0.10.21 do Nó.

## Instalar o Express

1.  Instale o gerador Express emitindo o comando a seguir:

        PS C:\node\expressapp> npm install express-generator -g

    A saída do comando npm deve ser semelhante ao resultado abaixo.

    ![Windows PowerShell exibindo a saída do comando npm install express.][Windows PowerShell exibindo a saída do comando npm install express.]

2.  Altere os diretórios para o diretório **WebRole1** e use o comando express para gerar um novo aplicativo:

        PS C:\node\expressapp\WebRole1> express

    Você será solicitado a substituir o aplicativo anterior. Digite **s** ou **sim** para continuar. O Express gerará o arquivo app.js e uma estrutura de pastas para criar seu aplicativo.

    ![A saída do comando express][A saída do comando express]

3.  Para instalar dependências adicionais definidas no arquivo package.json,
    digite o seguinte comando:

        PS C:\node\expressapp\WebRole1> npm install

    ![A saída do comando npm install][A saída do comando npm install]

4.  Use o seguinte comando para copiar o arquivo **bin/www** no **server.js**. Isso é para que o serviço de nuvem possa encontrar o ponto de entrada para este aplicativo.

        PS C:\node\expressapp\WebRole1> copy bin/www server.js

    Após este comando tiver concluído, você deve ter um arquivo **server.js** no diretório WebRole1.

5.  Modifique o **server.js** para remover um dos caracteres '.' da seguinte linha.

        var app = require('../app');

    Após fazer esta modificação, a linha deve aparecer da seguinte maneira.

        var app = require('./app');

    Esta alteração é requerida porque movemos o arquivo (anteriormente **bin/www**,) ao mesmo diretório conforme o arquivo do aplicativo seja requerido. Após fazer de fazer essa alteração, salve o arquivo **server.js**.

6.  Use o seguinte comando para executar o aplicativo no emulador do Windows
    Azure:

        PS C:\node\expressapp\WebRole1> Start-AzureEmulator -launch

    ![Uma página da web que contém as boas-vindas ao express.][Uma página da web que contém as boas-vindas ao express.]

## Modificando a exibição

Agora modifique a exibição para exibir a mensagem "Bem-vindo ao Express no
 Azure".

1.  Digite o comando a seguir para abrir o arquivo index.jade:

        PS C:\node\expressapp\WebRole1> notepad views/index.jade

    ![O conteúdo do arquivo index.jade.][O conteúdo do arquivo index.jade.]

    Jade é o mecanismo de exibição padrão usado por aplicativos do Express. Para obter mais
    informações sobre o mecanismo de exibição Jade, consulte [][]<http://jade-lang.com></a>.

2.  Modifique a última linha de texto acrescentando **no Azure**.

    ![O arquivo index.jade, a última linha diz: Bem-vindo ao \#{title} no Azure][O arquivo index.jade, a última linha diz: Bem-vindo ao \#{title} no Azure]

3.  Salve o arquivo e saia do Bloco de Notas.

4.  Atualize o navegador para ver as alterações.

    ![Uma janela do navegador, a página contém Bem-vindo ao Express no Azure][Uma janela do navegador, a página contém Bem-vindo ao Express no Azure]

Depois de testar o aplicativo, use o cmdlet **Stop-AzureEmulator** para parar o emulador.

## Publicar o Aplicativo no Azure

Na janela PowerShell do Azure, use o cmdlet **Publish-AzureServiceProject** para implantar o aplicativo em um serviço de nuvem

    PS C:\node\expressapp\WebRole1> Publish-AzureServiceProject -ServiceName myexpressapp -Location "East US" -Launch

Quando a operação de implantação estiver concluída, o navegador será aberto e exibirá a página da web.

![Um navegador da web exibindo a página do Express. A URL indica que agora a página está hospedada no Azure.][Um navegador da web exibindo Bem-vindo ao Express no Azure]

  [Express]: http://expressjs.com/
  [Um navegador da web exibindo Bem-vindo ao Express no Azure]: ./media/cloud-services-nodejs-develop-deploy-express-app/node36.png
  [Ícone PowerShell do Azure]: ./media/cloud-services-nodejs-develop-deploy-express-app/azure-powershell-start.png
  [install-dev-tools]: ../includes/install-dev-tools.md
  [Windows PowerShell exibindo a saída do comando npm install express.]: ./media/cloud-services-nodejs-develop-deploy-express-app/express-g.png
  [A saída do comando express]: ./media/cloud-services-nodejs-develop-deploy-express-app/node23.png
  [A saída do comando npm install]: ./media/cloud-services-nodejs-develop-deploy-express-app/node26.png
  [Uma página da web que contém as boas-vindas ao express.]: ./media/cloud-services-nodejs-develop-deploy-express-app/node28.png
  [O conteúdo do arquivo index.jade.]: ./media/cloud-services-nodejs-develop-deploy-express-app/getting-started-19.png
  []: http://jade-lang.com
  [Uma janela do navegador, a página contém Bem-vindo ao Express no Azure]: ./media/cloud-services-nodejs-develop-deploy-express-app/node32.png
