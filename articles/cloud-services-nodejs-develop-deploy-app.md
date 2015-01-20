<properties urlDisplayName="Cloud Service" pageTitle="Guia de introdução ao Node.js - Tutorial do Azure" metaKeywords="Azure node.js getting started, Azure Node.js tutorial, Azure Node.js tutorial" description="Um tutorial completo que ajuda você a desenvolver um aplicativo da Web do Node.js simples e implantá-lo no Azure." metaCanonical="" services="cloud-services" documentationCenter="nodejs" title="Build and deploy a Node.js application to an Azure Cloud Service" authors="larryfr" solutions="" manager="wpickett" editor="" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="nodejs" ms.topic="article" ms.date="09/17/2014" ms.author="wpickett" />






# Criar e implantar um aplicativo Node.jc para um Serviço de Nuvem do Windows Azure

Na conclusão deste guia, você terá um aplicativo simples do Node.js em execução em um Serviço de Nuvem do Azure. Serviços de Nuvem são os blocos de construção de aplicativos de nuvem dimensionáveis no Azure. Eles permitem a separação e o gerenciamento independente e o dimensionamento dos componentes de front-end e back-end de seu aplicativo.  Os serviços de nuvem fornecem uma máquina virtual exclusiva robusta para hospedar cada função confiável.

Para obter mais informações sobre serviços de nuvem e como eles são comparados aos Sites do Azure e máquinas virtuais, consulte [Comparação de Sites do Azure, Serviços de Nuvem e Máquinas Virtuais](http://azure.microsoft.com/pt-br/documentation/articles/choose-web-site-cloud-service-vm/).

<p />

<div class="dev-callout"><strong>Procurando desenvolver um site simples?</strong>
<p>Se seu cenário envolve apenas um site de front-end simples, considere <a href="/pt-br/documentation/articles/web-sites-nodejs-develop-deploy-mac/">usar um site do Azure leve.</a> Você pode atualizar facilmente para um serviço de nuvem conforme o site cresce e suas necessidades mudam.</p>
</div>
<br />

Seguindo este tutorial, você irá criar um aplicativo da Web simples hospedado dentro de uma função Web. Você usará o emulador de computação para testar o aplicativo localmente e, em seguida, o implantará usando ferramentas de linha de comando do PowerShell.

A seguinte é uma captura de tela do aplicativo concluído:

<p><img src = "https://wacomdpsstablestorage.blob.core.windows.net/articlesmedia/demo-ppe.windowsazure.com/pt-br/documentation/articles/cloud-services-nodejs-develop-deploy-app/20140107035927/node21.png" alt = "uma janela do navegador exibindo a página de boas-vindas. A URL indica que a página está hospedada no Azure.">
</p>



## Criando um novo aplicativo do nó

Execute as tarefas a seguir para criar um novo projeto do Serviço de Nuvem do Azure, juntamente com a estrutura básica do Node.js:

1. No **Menu Iniciar** ou na **Tela Iniciar**, pesquise por **PowerShell do Azure**. Finalmente, clique com o botão direito do mouse no **PowerShell do Azure** e selecione **Executar como Administrador**.

	![Azure PowerShell icon][powershell-menu]

	[WACOM.INCLUDE [install-dev-tools](../includes/install-dev-tools.md)]


2.  Crie um novo diretório **node** de diretório na unidade C e altere para o diretório c:\\node:
	
	![A command prompt displaying the commands 'mkdir c:\\node' and 'cd node'.][mkdir]

3.  Insira o seguinte cmdlet para criar uma nova solução:

        PS C:\node> New-AzureServiceProject helloworld

    	Você verá a seguinte resposta:

	![The result of the New-AzureService helloworld command](./media/cloud-services-nodejs-develop-deploy-app/node9.png)

	O cmdlet **New-AzureServiceProject** gera uma estrutura básica para a criação de um novo aplicativo do Azure Node que será publicado para um serviço de nuvem. Ele contém arquivos de configuração necessários para publicar no Azure. O cmdlet também altera o diretório de trabalho para o diretório de serviço.

	Os arquivos criados com o cmdlet **New-AzureServiceProject** são:

	-   **ServiceConfiguration.Cloud.cscfg**,
        **ServiceConfiguration.Local.cscfg** and **ServiceDefinition.csdef** are
        Azure-specific files necessary for publishing your
        application.
		
	For more information about these files, see
        [Overview of Creating a Hosted Service for Azure][].

	-   **deploymentSettings.json** stores local settings that are used by
        the Azure PowerShell deployment cmdlets.

4.  Digite o seguinte comando para adicionar uma nova função Web usando o**cmdlet Add-AzureNodeWebRole**:

        PS C:\node\helloworld> Add-AzureNodeWebRole

	Você verá a seguinte resposta:

	![The output of the Add-AzureNodeWebRole command.](./media/cloud-services-nodejs-develop-deploy-app/node11.png)

	O cmdlet **Add-AzureNodeWebRole** cria um novo diretório para seu aplicativo e gera a estrutura de um aplicativo básico Node.js. Ele também modifica os arquivos **ServiceConfiguration.Cloud.csfg**, **ServiceConfiguration.Local.csfg** e **ServiceDefinition.csdef** criados na etapa anterior para adicionar entradas de configuração para a nova função.

	<div class="dev-callout">
	<b>Observação</b>
	<p>Por padrão, se você não fornecer um nome de função, ela será criada para você. Você pode fornecer um nome como o primeiro parâmetro para <b>Add-AzureNodeWebRole</b>. Por exemplo, <code>Add-AzureNodeWebRole MyRole</code></p>
	</div>

5.  Use os comandos a seguir para navegar até o diretório **WebRole1** e, em seguida, abra o arquivo **server.js** no bloco de notas. 

	PS C:\\node\\helloworld> cd WebRole1
        PS C:\node\helloworld\WebRole1> notepad server.js

	O arquivo **server.js** foi criado com o cmdlet **Add-AzureNodeWebRole** e contém o seguinte código de inicialização. Esse código é semelhante ao exemplo "Hello World" no site [nodejs.org][], exceto:

   	-   A porta foi alterada para permitir que o aplicativo encontre a porta correta atribuída a ele pelo ambiente de nuvem.
   	-   O registro em log do console foi removido.

	![Notepad displaying the contents of server.js](./media/cloud-services-nodejs-develop-deploy-app/node13.png)

## Executando seu aplicativo localmente no emulador

Uma das ferramentas instaladas pelo SDK do Azure é o emulador de computação do Azure, que permite testar seu aplicativo localmente. O emulador de computação simula o ambiente de que seu aplicativo será executado quando ele é implantado na nuvem. Execute as seguintes etapas para testar o aplicativo no emulador.

1.  Feche o Bloco de Notas e volte para a janela do Windows PowerShell.
  	Insira o seguinte cmdlet para executar o serviço no emulador:

        PS C:\node\helloworld\WebRole1> Start-AzureEmulator -Launch

	O parâmetro**-Launch** especifica que as ferramentas devem abrir automaticamente uma janela do navegador e exibir o aplicativo quando ele estiver em execução no emulador. Um navegador abre e exibe "Hello World", como mostrado na captura de tela abaixo. Isso indica que o serviço está em execução no emulator de computação e está funcionando corretamente.

	![A web browser displaying the Hello World web page](./media/cloud-services-nodejs-develop-deploy-app/node14.png)

2.  Para parar o emulador de computação, use o comando **Stop-AzureEmulator**:
	
	PS C:\node\helloworld\WebRole1> Stop-AzureEmulator

## Implantando o aplicativo no Azure

	[WACOM.INCLUDE [create-account-note](../includes/create-account-note.md)]



###<a id="download_publishing_settings"></a>Baixando as configurações de publicação do Azure

Para implantar seu aplicativo do Azure, você deve primeiro baixar as definições de publicação para sua assinatura do Azure. As próximas etapas irão orientá-lo através desse processo:

1.  Na janela do Windows PowerShell, abra a página de download executando o seguinte cmdlet:

        PS C:\node\helloworld\WebRole1> Get-AzurePublishSettingsFile

	Isso irá usar o navegador para navegar para a página de download de configurações de publicação. Você precisará fazer logon com uma conta da Microsoft. Se fizer, use a conta associada com sua assinatura do Azure.

	Salve o perfil baixado para um local de arquivo, que você pode acessar facilmente.

2.  Na janela do Azure PowerShell, use o seguinte cmdlet para configurar o Windows PowerShell para os cmdlets do Node.js para usar o perfil de publicação do Azure que você fez o download:

        PS C:\node\helloworld\WebRole1> Import-AzurePublishSettingsFile [path to file]


	<div class="dev-callout">
	<b>Observação</b>
	<p>Depois de importar as configurações de publicação, considere a possibilidade de excluir o arquivo .publishSettings baixado devido ao fato de ele conter informações que podem ser usadas por outras pessoas para acessar sua conta.</p>
	</div>
    

### Publicar o aplicativo

1.  Publicar o aplicativo usando o cmdlet **Publish-AzureServiceProject**, conforme mostrado abaixo.

        PS C:\node\helloworld\WebRole1> Publish-AzureServiceProject -ServiceName NodeHelloWorld -Location "East US" -Launch

	- O parâmetro **servicename** especifica o nome usado para essa implantação. Esse deve ser um nome exclusivo, caso contrário, o processo de publicação falhará.

	- O parâmetro **location** especifica o centro de dados onde o aplicativo será hospedado. Para ver uma lista dos centros de dados disponíveis, use o cmdlet **Get-AzureLocation**.

	- O parâmetro **launch** iniciará seu navegador e navegue para o serviço hospedado após a conclusão da implantação.

	Após a publicação for bem-sucedida, você verá uma resposta semelhante à seguinte:

	![The output of the Publish-AzureService command](./media/cloud-services-nodejs-develop-deploy-app/node19.png)

	O cmdlet **Publish-AzureServiceProject** executa as seguintes etapas:

1.  Cria um pacote será implantado para Azure. O pacote contém todos os arquivos em sua pasta de aplicativo node.js.

2.  Cria uma nova **conta de armazenamento** se não existir. A conta de armazenamento do Azure é usada para armazenar o pacote de aplicativos durante a implantação. Você pode excluir com segurança a conta de armazenamento após a conclusão da implantação.

3.  Cria um novo **serviço de nuvem** se não existir. Um **serviço de nuvem** é o contêiner no qual seu aplicativo é hospedado quando é implantado no Azure. Para obter mais informações, consulte [Visão geral da criação de um serviço hospedado para o Azure][].

4.  Publica o pacote de implantação do Azure.


	> [WACOM.NOTE]
	> Pode levar de 5 a 7 minutos para o aplicativo ser implantado e tornar-se disponível quando for publicado pela primeira vez.

	Depois que a implantação for concluída, uma janela do navegador será aberta e irá navegar para o serviço de nuvem.


	![A browser window displaying the hello world page. The URL indicates the page is hosted on Azure.](./media/cloud-services-nodejs-develop-deploy-app/node21.png)

	Seu aplicativo está sendo executado no Azure!


## Parando e excluindo o aplicativo

Depois de implantar seu aplicativo, convém desativá-lo para que você possa evitar custos extras. O Azure cobra as instâncias de função web por hora de acordo com o tempo consumido do servidor. O tempo do servidor é consumido quando seu aplicativo é implantado, mesmo se as instâncias não estiverem sendo executadas e estiverem no estado parado.

1.  Na janela do Windows PowerShell, interrompa a implantação do serviço criada na seção anterior com o seguinte cmdlet:

        PS C:\node\helloworld\WebRole1> Stop-AzureService

	Interromper o serviço pode levar alguns minutos. Quando o serviço for interrompido, você recebe uma mensagem indicando que foi interrompido.

	![The status of the Stop-AzureService command](./media/cloud-services-nodejs-develop-deploy-app/node48.png)

2.  Para excluir o serviço, chame o seguinte cmdlet:

        PS C:\node\helloworld\WebRole1> Remove-AzureService

	Quando solicitado, insira **Y** para excluir o serviço.

	Excluir o serviço pode levar alguns minutos. Após o serviço ter sido excluído, você recebe uma mensagem indicando que o serviço foi excluído.

	![The status of the Remove-AzureService command](./media/cloud-services-nodejs-develop-deploy-app/node49.png)

	<div class="dev-callout">
	<strong>Observação</strong>
	<p>Excluir o serviço não exclui a conta de armazenamento criada quando o serviço foi inicialmente publicado e você continuará a ser cobrado pelo armazenamento usado. Para obter mais informações sobre
excluir uma conta de armazenamento, consulte <a href="http://msdn.microsoft.com/pt-br/library/windowsazure/hh531562.aspx">Como excluir uma conta de armazenamento de uma assinatura do Azure</a>.</p>
</div>


[O menu Iniciar do Windows com a entrada do Azure SDK Node.js expandida]: ./media/cloud-services-nodejs-develop-deploy-app/azure-powershell-menu.png
[mkdir]: ./media/cloud-services-nodejs-develop-deploy-app/getting-started-6.png
[nodejs.org]: http://nodejs.org/
[Uma lista de diretórios da pasta helloworld.]: ./media/cloud-services-nodejs-develop-deploy-app/getting-started-7.png
[Visão geral de como criar um serviço hospedado para o Azure]: http://msdn.microsoft.com/pt-br/library/windowsazure/jj155995.aspx
[Uma lista de diretórios da pasta WebRole1]: ./media/cloud-services-nodejs-develop-deploy-app/getting-started-8.png
[O menu exibido ao clicar duas vezes no emulador do Azure da barra de tarefas.]: ./media/cloud-services-nodejs-develop-deploy-app/getting-started-11.png
[Uma janela do navegador exibindo http://www.windowsazure.com/ com o link de avaliação gratuita destacado]: ./media/cloud-services-nodejs-develop-deploy-app/getting-started-12.png
[Uma janela do navegador exibindo a entrada liveID na página]: ./media/cloud-services-nodejs-develop-deploy-app/getting-started-13.png
[Internet Explorer exibindo a caixa de diálogo Salvar como para o arquivo publishSettings.]: ./media/cloud-services-nodejs-develop-deploy-app/getting-started-14.png

[A saída de status completo do comando Publish-AzureService]: ./media/cloud-services-nodejs-develop-deploy-app/node20.png
[Como excluir uma conta de armazenamento de uma assinatura do Azure]: https://www.windowsazure.com/pt-br/manage/services/storage/how-to-manage-a-storage-account/
[powershell-menu]: ./media/cloud-services-nodejs-develop-deploy-app/azure-powershell-start.png

<!--HONumber=35.2-->
