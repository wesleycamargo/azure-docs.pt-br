<properties 
	pageTitle="Guia de introdução ao Node.js - Tutorial do Azure" 
	description="Saiba como criar um aplicativo Web simples do Node.js e implantá-lo em um serviço de nuvem do Azure." 
	services="cloud-services" 
	documentationCenter="nodejs" 
	authors="MikeWasson" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="cloud-services" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="nodejs" 
	ms.topic="hero-article" 
	ms.date="02/24/2015" 
	ms.author="mwasson"/>


# Criar e implantar um aplicativo Node.jc para um Serviço de Nuvem do Windows Azure

> [AZURE.SELECTOR]
- [Node.js](cloud-services-nodejs-develop-deploy-app.md)
- [.NET](cloud-services-dotnet-get-started.md)

Este tutorial mostra como criar um aplicativo simples do Node.js em execução em um Serviço de Nuvem do Azure. Os Serviços de Nuvem são os blocos de construção de aplicativos de nuvem escalonáveis no Azure. Eles permitem a separação e o gerenciamento independente e o dimensionamento dos componentes de front-end e back-end de seu aplicativo.  Os serviços de nuvemfornecem uma máquina virtual exclusiva robusta para hospedar cada função confiável.

Para obter mais informações sobre serviços de nuvem e como eles são comparados aos Sites do Azure e máquinas virtuais, consulte [Comparação de Sites do Azure, Serviços de Nuvem e Máquinas Virtuais](http://azure.microsoft.com/documentation/articles/choose-web-site-cloud-service-vm/).

>[AZURE.TIP] Procurando desenvolver um site simples? Se o seu cenário envolver apenas um site de front-end simples, considere <a href="/documentation/articles/web-sites-nodejs-develop-deploy-mac/">usar um Site do Azure leve.</a> Você pode atualizar facilmente para um Serviço de Nuvem conforme o site cresce e suas necessidades mudam.


Seguindo este tutorial, você irá criar um aplicativo da Web simples hospedado dentro de uma função Web. Você usará o emulador de computação para testar o aplicativo localmente e, em seguida, o implantará usando ferramentas de linha de comando do PowerShell.

O aplicativo é um aplicativo simples "hello world":

<p><img src="https://wacomdpsstablestorage.blob.core.windows.net/articlesmedia/demo-ppe.windowsazure.com/documentation/articles/cloud-services-nodejs-develop-deploy-app/20140107035927/node21.png" alt="Uma janela do navegador exibindo a página hello world. A URL indica que a página está hospedada no Azure.">
</p>

## Pré-requisitos

> [AZURE.NOTE] Este tutorial usa o PowerShell do Azure, que requer o Windows.

- Instale o SDK do Azure para o Node.js: <a href="http://go.microsoft.com/fwlink/?LinkId=254279">Windows installer</a> 

- Instalar e configurar o [Powershell do Azure](install-configure-powershell.md).


## Criar um projeto de Serviço de Nuvem do Azure

Execute as tarefas a seguir para criar um novo projeto do Serviço de Nuvem do Azure, juntamente com a estrutura básica do Node.js:


1. Execute o **PowerShell do Azure** como administrador. (No **Menu Iniciar** ou na **Tela Iniciar**, pesquise **PowerShell do Azure**.)

2.  Insira o seguinte cmdlet do PowerShell para criar o projeto:

        New-AzureServiceProject helloworld

	![The result of the New-AzureService helloworld command](./media/cloud-services-nodejs-develop-deploy-app/node9.png)

	O cmdlet **New-AzureServiceProject** gera uma estrutura básica para publicar um aplicativo do Node.js para um Serviço de Nuvem. Ele contém arquivos de configuração necessários para publicar no Azure. O cmdlet também altera o diretório de trabalho para o diretório de serviço.

	O cmdlet cria os seguintes arquivos:

	-   **ServiceConfiguration.Cloud.cscfg**,
        **ServiceConfiguration.Local.cscfg** and **ServiceDefinition.csdef**: 
        Arquivos específicos do Azure necessários para publicar seu aplicativo.
		Para obter mais informações, consulte
        [Visão geral da criação de um serviço hospedado para o Azure][].

	-   **deploymentSettings.json**: armazena configurações locais que são usadas peloscmdlets de implantação do Azure PowerShell.

4.  Digite o seguinte comando para adicionar uma nova função da Web:

        Add-AzureNodeWebRole
	
	![The output of the Add-AzureNodeWebRole command.](./media/cloud-services-nodejs-develop-deploy-app/node11.png)

	O **Add-AzureNodeWebRole** cria um aplicativo básico do Node.js. Ele também modifica os arquivos **.csfg** e **.csdef** para adicionar entradas de configuração para a nova função.

	> [AZURE.NOTE] Se você não especificar um nome de função, um nome padrão será usado. Você pode fornecer um nome como o primeiro parâmetro do cmdlet: `Add-AzureNodeWebRole MyRole`


O aplicativo do Node.js é definido no arquivo **server.js**, localizado no diretório da função Web (**WebRole1** por padrão). Eis o código:

	var http = require('http');
	var port = process.env.port || 1337;
	http.createServer(function (req, res) {
	    res.writeHead(200, { 'Content-Type': 'text/plain' });
	    res.end('Hello World\n');
	}).listen(port);

Esse código é basicamente o mesmo código do exemplo "Hello World" no site [nodejs.org][], com a exceção de que ele usa o número de porta atribuído pelo ambiente de nuvem.


## Executar o aplicativo localmente no emulador

Uma das ferramentas instaladas pelo SDK do Azure é o emulador
de computação do Azure, que permite que você teste seu aplicativo localmente. A
emulador de computação simula o ambiente em que seu aplicativo rodará
quando for implantado na nuvem. 

1.  Insira o seguinte cmdlet do PowerShell do Azure para executar o serviço no emulador:

        Start-AzureEmulator -Launch

	Use o parâmetro **-Launch** para abrir automaticamente uma janela do navegador quando a função Web estiver em execução no emulador. A janela do navegador deve exibir "Hello World", conforme mostrado na captura de tela abaixo. 

	![A web browser displaying the Hello World web page](./media/cloud-services-nodejs-develop-deploy-app/node14.png)

2.  Para parar o emulador de computação, use o cmdlet **Stop-AzureEmulator**:
	
		Stop-AzureEmulator

## Implantar o aplicativo no Azure

	[AZURE.INCLUDE [create-account-note](../includes/create-account-note.md)]


### Baixar as configurações de publicação do Azure

Para implantar seu aplicativo do Azure, você deve primeiro baixar as definições de publicação para sua assinatura do Azure. 

1.  Execute o seguinte cmdlet do PowerShell do Azure:

        Get-AzurePublishSettingsFile

	Isso irá usar o navegador para navegar para a página de download de configurações de publicação. Você precisará fazer logon com uma conta da Microsoft. Se fizer, use a conta associada com sua assinatura do Azure.

	Salve o perfil baixado para um local de arquivo, que você pode acessar facilmente.

2.  Execute o seguinte cmdlet para importar o perfil de publicação que você baixou:

        Import-AzurePublishSettingsFile [caminho do arquivo]


	> [AZURE.NOTE] Depois de importar as configurações de publicação, considere a exclusão do arquivo .publishsettings baixado, pois ele contém informações que podem permitir que alguém acesse sua conta.
    

### Publicar o aplicativo

Para publicar, execute o cmdlet **Publish-AzureServiceProject** da seguinte maneira:

    Publish-AzureServiceProject -ServiceName NodeHelloWorld -Location "East US" -Launch

- **-ServiceName** especifica o nome para a implantação. Esse deve ser um nome exclusivo, caso contrário, o processo de publicação falhará.

- **-Location** especifica o data center em que o aplicativo será hospedado. Para ver uma lista dos centros de dados disponíveis, use o cmdlet **Get-AzureLocation**.

- **-Launch** abre uma janela do navegador e navega para o serviço hospedado após a conclusão da implantação.

Após a publicação for bem-sucedida, você verá uma resposta semelhante à seguinte:

![The output of the Publish-AzureService command](./media/cloud-services-nodejs-develop-deploy-app/node19.png)

> [AZURE.NOTE]
> Pode levar de 5 a 7 minutos para o aplicativo ser implantado e tornar-se disponível quando for publicado pela primeira vez.

Depois que a implantação for concluída, uma janela do navegador será aberta e irá navegar para o serviço de nuvem.


![A browser window displaying the hello world page. The URL indicates the page is hosted on Azure.](./media/cloud-services-nodejs-develop-deploy-app/node21.png)

Seu aplicativo está sendo executado no Azure!

O cmdlet **Publish-AzureServiceProject** executa as seguintes etapas:

1.  Cria um pacote a ser implantado. O pacote contém todos os arquivos em sua pasta de aplicativos.

2.  Cria uma nova **conta de armazenamento** se não existir. A conta de armazenamento do Azure é usada para armazenar o pacote de aplicativos durante a implantação. Você pode excluir com segurança a conta de armazenamento após a conclusão da implantação.

3.  Cria um novo **serviço de nuvem** se não existir. Um **serviço de nuvem** é o contêiner no qual seu aplicativo é hospedado quando é implantado no Azure. Para obter mais informações, consulte [Visão geral da criação de um serviço hospedado para o Azure][].

4.  Publica o pacote de implantação do Azure.



## Parando e excluindo seu aplicativo

Depois de implantar seu aplicativo, convém desativá-lo para que você possa evitar custos extras. O Azure cobra as instâncias de função web por hora de acordo com o tempo consumido do servidor. O tempo do servidor é consumido quando seu aplicativo é implantado, mesmo se as instâncias não estiverem sendo executadas e estiverem no estado parado.

1.  Na janela do Windows PowerShell, interrompa a implantação do serviço criada na seção anterior com o seguinte cmdlet:

        Stop-AzureService

	Interromper o serviço pode levar alguns minutos. Quando o serviço for interrompido, você recebe uma mensagem indicando que foi interrompido.

	![The status of the Stop-AzureService command](./media/cloud-services-nodejs-develop-deploy-app/node48.png)

2.  Para excluir o serviço, chame o seguinte cmdlet:

        Remove-AzureService

	Quando solicitado, insira **Y** para excluir o serviço.

	Excluir o serviço pode levar alguns minutos. Após o serviço ter sido excluído, você recebe uma mensagem indicando que o serviço foi excluído.

	![The status of the Remove-AzureService command](./media/cloud-services-nodejs-develop-deploy-app/node49.png)

	> [AZURE.NOTE] Excluir o serviço não exclui a conta de armazenamento criada quando o serviço foi inicialmente publicado e você continuará a ser cobrado pelo armazenamento usado. Para obter mais informações sobre como excluir uma conta de armazenamento, consulte [Como excluir uma conta de armazenamento de uma assinatura do Azure](http://msdn.microsoft.com/library/windowsazure/hh531562.aspx).


[O menu Iniciar do Windows com a entrada do Azure SDK Node.js expandida]: ./media/cloud-services-nodejs-develop-deploy-app/azure-powershell-menu.png
[mkdir]: ./media/cloud-services-nodejs-develop-deploy-app/getting-started-6.png
[nodejs.org]: http://nodejs.org/
[Uma lista de diretórios da pasta helloworld.]: ./media/cloud-services-nodejs-develop-deploy-app/getting-started-7.png
[Visão geral de como criar um serviço hospedado para o Azure]: http://msdn.microsoft.com/library/windowsazure/jj155995.aspx
[Visão geral da criação de um serviço hospedado para o Azure]: http://msdn.microsoft.com/library/windowsazure/jj155995.aspx
[Uma lista de diretórios da pasta WebRole1]: ./media/cloud-services-nodejs-develop-deploy-app/getting-started-8.png
[O menu exibido ao clicar duas vezes no emulador do Azure da barra de tarefas.]: ./media/cloud-services-nodejs-develop-deploy-app/getting-started-11.png
[Uma janela do navegador exibindo http://www.windowsazure.com/ com o link de avaliação gratuita destacado]: ./media/cloud-services-nodejs-develop-deploy-app/getting-started-12.png
[Uma janela do navegador exibindo a entrada liveID na página]: ./media/cloud-services-nodejs-develop-deploy-app/getting-started-13.png
[Internet Explorer exibindo a caixa de diálogo Salvar como para o arquivo publishSettings.]: ./media/cloud-services-nodejs-develop-deploy-app/getting-started-14.png

[A saída de status completo do comando Publish-AzureService]: ./media/cloud-services-nodejs-develop-deploy-app/node20.png
[Como excluir uma conta de armazenamento de uma assinatura do Azure]: https://www.windowsazure.com/manage/services/storage/how-to-manage-a-storage-account/
[powershell-menu]: ./media/cloud-services-nodejs-develop-deploy-app/azure-powershell-start.png

<!--HONumber=52-->