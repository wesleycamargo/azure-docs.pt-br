<properties linkid="dev-nodejs-basic-web-app-with-express" urlDisplayName="Aplicativo Web com o Express" pageTitle="Aplicativo Web com o Express (Node.js) - Tutorial do Azure" metaKeywords="Tutorial de hello world com o Node.js do Azure, hello world com o Node.js do Azure, tutorial de introdução ao Node.js do Azure, tutorial do Node.js Express do Azure" description="Um tutorial que é baseado no tutorial de serviço de nuvem e que demonstra como usar o módulo do Express." metaCanonical="" services="cloud-services" documentationCenter="Node.js" title="Criar um aplicativo web do Node.js usando o Express em um Serviço de Nuvem do Azure" authors=""  solutions="" writer="" manager="" editor=""  />





# Criar um aplicativo web do Node.js usando o Express em um Serviço de Nuvem do Azure

O Node.js inclui um conjunto mínimo de funcionalidades em tempo de execução básico.
Os desenvolvedores normalmente usam módulos de terceiros para fornecer funcionalidade adicional ao desenvolver um aplicativo do Node.js. Neste tutorial, você irá criar um novo aplicativo usando o módulo [Express][], que fornece uma estrutura MVC para criar aplicativos web do Node.js.

A seguinte é uma captura de tela do aplicativo concluído:

![Um navegador da web exibindo Bem-vindo ao Express no Azure](./media/cloud-services-nodejs-develop-deploy-express-app/node36.png)

##Criar um projeto de Serviço de Nuvem

Execute as seguintes etapas para criar um novo projeto de serviço de nuvem chamado 'expressapp':

1. No **Menu Iniciar** ou **Tela Iniciar**, pesquise por **PowerShell do Azure**. Por fim, clique com o botão direito no **PowerShell do Azure** e selecione **Executar como Administrador**.

	![Ícone do Azure PowerShell](./media/cloud-services-nodejs-develop-deploy-express-app/azure-powershell-start.png)

	[WACOM.INCLUDE [install-dev-tools](../includes/install-dev-tools.md)]

2. Altere os diretórios para o diretório **c:\\node** e digite os seguintes comandos para criar uma nova solução denominada **expressapp** e uma função web denominada **WebRole1**:

		PS C:\node> New-AzureServiceProject expressapp
		PS C:\Node> Add-AzureNodeWebRole

##Instalar o Express

1. Instale o módulo Express emitindo o comando a seguir:

		PS C:\node\expressapp> npm install express -g

	A saída do comando npm deve ser semelhante ao resultado abaixo. 

	![Windows PowerShell exibindo a saída do comando npm install express.](./media/cloud-services-nodejs-develop-deploy-express-app/express-g.png)

2. Altere os diretórios para o diretório **WebRole1** e use o comando express para gerar um novo aplicativo:

        PS C:\node\expressapp\WebRole1> express

	Você será solicitado a substituir o aplicativo anterior. Digite **s** ou **sim** para continuar. O Express gerará o arquivo app.js e uma estrutura de pastas para criar seu aplicativo.

	![A saída do comando express](./media/cloud-services-nodejs-develop-deploy-express-app/node23.png)

3.  Exclua o arquivo **server.js** e, em seguida, renomeie o arquivo **app.js** gerado para **server.js**.

        PS C:\node\expressapp\WebRole1> del server.js
        PS C:\node\expressapp\WebRole1> ren app.js server.js

5.  Para instalar dependências adicionais definidas no arquivo package.json, digite o seguinte comando:

        PS C:\node\expressapp\WebRole1> npm install

	![A saída do comando npm install](./media/cloud-services-nodejs-develop-deploy-express-app/node26.png)

8.  Use o seguinte comando para executar o aplicativo no emulador do Azure:

        PS C:\node\expressapp\WebRole1> Start-AzureEmulator -launch

	![Uma página da web que contém as boas-vindas ao express.](./media/cloud-services-nodejs-develop-deploy-express-app/node28.png)

## Modificando a exibição

Agora modifique a exibição para exibir a mensagem "Bem-vindo ao Express no Azure".

1.  Digite o comando a seguir para abrir o arquivo index.jade:

        PS C:\node\expressapp\WebRole1> notepad views/index.jade

    ![O conteúdo do arquivo index.jade.](./media/cloud-services-nodejs-develop-deploy-express-app/getting-started-19.png)

    Jade é o mecanismo de exibição padrão usado por aplicativos do Express. Para obter mais informações sobre o mecanismo de exibição Jade, consulte [http://jade-lang.com][].

2.  Modifique a última linha de texto acrescentando **no Azure**.

	![No arquivo index.jade, a última linha diz: Bem-vindo ao \#{title} no Azure](./media/cloud-services-nodejs-develop-deploy-express-app/node31.png)

3.  Salve o arquivo e saia do Bloco de Notas.

4.  Atualize o navegador para ver as alterações.

	![Uma janela do navegador, a página contém Bem-vindo ao Express no Azure](./media/cloud-services-nodejs-develop-deploy-express-app/node32.png)

Depois de testar o aplicativo, use o cmdlet **Stop-AzureEmulator** para parar o emulador.

##Publicando o aplicativo no Azure

Na janela PowerShell do Azure, use o cmdlet **Publish-AzureServiceProject** para implantar o aplicativo em um serviço de nuvem

    PS C:\node\expressapp\WebRole1> Publish-AzureServiceProject -ServiceName myexpressapp -Location "East US" -Launch

Quando a operação de implantação estiver concluída, o navegador será aberto e exibirá a página da web.

![Um navegador da web exibindo a página do Express. A URL indica que agora a página está hospedada no Azure.](./media/cloud-services-nodejs-develop-deploy-express-app/node36.png)


  [Aplicativo Web do Node.js]: http://www.windowsazure.com/pt-br/develop/nodejs/tutorials/getting-started/
  [Express]: http://expressjs.com/
  [http://jade-lang.com]: http://jade-lang.com


