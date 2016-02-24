<properties 
	pageTitle="Implantar Aplicativos Web do Serviço de Aplicativo do Azure e do Banco de Dados de Documentos usando um Modelo do Gerenciador de Recursos do Azure | Microsoft Azure" 
	description="Saiba como implantar uma conta do Banco de Dados de Documentos, aplicativos Web do Serviço de Aplicativo do Azure e aplicativo Web de exemplo usando um modelo do Gerenciador de Recursos do Azure." 
	services="documentdb, app-service\web" 
	authors="ryancrawcour" 
	manager="jhubbard" 
	editor="monicar" 
	documentationCenter=""/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/01/2016" 
	ms.author="ryancraw"/>

# Implantar Aplicativos Web do Serviço de Aplicativo do Azure e do Banco de Dados de Documentos usando um Modelo do Gerenciador de Recursos do Azure #

Este tutorial mostra como usar um modelo do Gerenciador de Recursos do Azure para implantar e integrar o [Banco de Dados de Documentos do Microsoft Azure](https://azure.microsoft.com/services/documentdb/), um aplicativo Web do [Serviço de Aplicativo do Azure](http://go.microsoft.com/fwlink/?LinkId=529714) e um aplicativo Web de exemplo.

Depois de concluir este tutorial, você poderá responder às seguintes perguntas:

-	Como usar um modelo do Gerenciador de Recursos do Azure para implantar e integrar uma conta do Banco de Dados de Documentos e um aplicativo Web do Serviço de Aplicativo do Azure?
-	Como usar um modelo do Gerenciador de Recursos do Azure para implantar e integrar uma conta do Banco de Dados de Documentos, um aplicativo Web nos Aplicativos Web do Serviço de Aplicativo e um aplicativo Webdeploy?

<a id="Prerequisites"></a>
## Pré-requisitos ##
> [AZURE.TIP] Embora este tutorial não suponha uma experiência anterior com modelos do Gerenciador de Recursos do Azure, JSON ou Azure PowerShell, caso você queira modificar os modelos referenciados ou as opções de implantação, será necessário ter conhecimento em cada uma dessas áreas.

Antes de seguir as instruções neste tutorial, certifique-se de ter o seguinte:

- Uma assinatura do Azure. O Azure é uma plataforma baseada em assinatura. Para obter mais informações sobre como adquirir uma assinatura, consulte [Opções de compra](https://azure.microsoft.com/pricing/purchase-options/), [Ofertas para membros](https://azure.microsoft.com/pricing/member-offers/) ou [Avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).
- Uma Conta de Armazenamento do Azure. Para obter instruções, consulte [Sobre Contas de Armazenamento do Azure](../storage/storage-create-storage-account.md).
- Uma estação de trabalho com o Azure PowerShell 0.9.8. Para obter instruções, consulte [Instalar e configurar o PowerShell do Azure](../powershell-install-configure.md). Este tutorial ainda não foi atualizado para o Azure PowerShell 1.0 Preview. 

##<a id="CreateDB"></a>Etapa 1: Baixe e extraia os arquivos de exemplo ##
Vamos começar baixando os arquivos de exemplo que usaremos neste tutorial.

1. Baixe [Criar uma conta do Banco de Dados de Documentos, Aplicativos Web e implantar um exemplo de aplicativo de demonstração](https://portalcontent.blob.core.windows.net/samples/CreateDocDBWebsiteTodo.zip) para uma pasta local (por exemplo, C:\\DocumentDBTemplates) e extraia os arquivos. Este exemplo implantará um aplicativo Web, um aplicativo Web do Serviço de Aplicativo e uma conta do Banco de Dados de Documentos. Ele também configurará automaticamente o aplicativo Web para conectar-se à conta do Banco de Dados de Documentos.

2. Baixe [Criar um exemplo de Aplicativo Web e conta do Banco de Dados de Documentos](https://portalcontent.blob.core.windows.net/samples/CreateDocDBWebSite.zip) em uma pasta local (por exemplo, C:\\DocumentDBTemplates) e extraia os arquivos. Este exemplo implantará uma conta do Banco de Dados de Documentos, um aplicativo Web do Serviço de Aplicativo e modificará a configuração do site para demonstrar facilmente informações de conexão do Banco de Dados de Documentos, mas não inclui um aplicativo Web.

> [AZURE.TIP] Observe que, dependendo das configurações de segurança do seu computador, talvez seja necessário desbloquear os arquivos extraídos clicando com o botão direito em **Propriedades** e em **Desbloquear**.

![Captura de tela da janela Propriedades com o botão Desbloquear realçado](./media/documentdb-create-documentdb-website/image1.png)

<a id="Build"></a>
##Etapa 2: Implantar a conta de Documentos, o aplicativo Web do Serviço de Aplicativo e o exemplo de aplicativo de demonstração ##

Agora, vamos implantar nosso primeiro modelo.

> [AZURE.TIP] O modelo não valida se o nome do aplicativo Web e o nome da conta do Banco de Dados de Documentos inseridos abaixo são a)válidos e b)disponíveis. É altamente recomendável que você verifique a disponibilidade dos nomes que planeja fornecer antes de executar o script de implantação do PowerShell.

1. Abra o Microsoft Azure PowerShell e navegue até a pasta na qual você baixou e extraiu o [Criar uma conta do Banco de Dados de Documentos, aplicativo Web do Serviço de Aplicativo e implantar um exemplo de aplicativo de demonstração](https://portalcontent.blob.core.windows.net/samples/CreateDocDBWebsiteTodo.zip) (por exemplo, C:\\DocumentDBTemplates\\CreateDocDBWebsiteTodo).


2. Vamos executar o script PowerShell CreateDocDBWebsiteTodo.ps1. O script usa os seguintes parâmetros obrigatórios:
	- WebsiteName: especifica o nome do aplicativo Web do Serviço de Aplicativo e é usado para construir a URL usada para acessar o aplicativo Web (por exemplo, se você especificar "mydemodocdbwebapp", a URL pela qual você acessará o site será mydemodocdbwebapp.azurewebsites.net).

	- ResourceGroupName: especifica o nome do Grupo de Recursos do Azure para implantar. Se o Grupo de Recursos especificado não existir, ele será criado.

	- docDBAccountName: especifica o nome da conta do Banco de Dados de Documentos a ser criada.

	- location: especifica o local do Azure onde criar os recursos do aplicativo Web e do Banco de Dados de Documentos. Os valores válidos são Ásia Oriental, Sudeste da Ásia, Leste dos EUA, Oeste dos EUA, Norte da Europa, Europa Ocidental (observe que o valor do local fornecido diferencia maiúsculas de minúsculas).


3. Aqui está um exemplo de comando para executar o script:

    	PS C:\DocumentDBTemplates\CreateDocDBWebAppTodo> .\CreateDocDBWebsiteTodo.ps1 -WebSiteName "mydemodocdbwebapp" -ResourceGroupName "myDemoResourceGroup" -docDBAccountName "mydemodocdbaccount" -location "West US"

	> [AZURE.TIP] Observe que será solicitado inserir seu nome de usuário da conta do Azure e a senha como parte da execução do script. A implantação completa levará entre 10 e 15 minutos para ser concluída.

4. E aqui está um exemplo da saída resultante:

		VERBOSE: 1:06:00 PM - Created resource group 'myDemoResourceGroup' in location westus'
		VERBOSE: 1:06:01 PM - Template is valid.
		VERBOSE: 1:06:01 PM - Create template deployment 'Microsoft.DocumentDBWebSiteTodo'.
		VERBOSE: 1:06:08 PM - Resource Microsoft.DocumentDb/databaseAccounts 'mydemodocdbaccount' provisioning status is running
		VERBOSE: 1:06:10 PM - Resource Microsoft.Web/serverFarms 'mydemodocdbwebapp' provisioning status is succeeded
		VERBOSE: 1:06:14 PM - Resource microsoft.insights/alertrules 'CPUHigh mydemodocdbwebapp' provisioning status is succeeded
		VERBOSE: 1:06:16 PM - Resource microsoft.insights/autoscalesettings 'mydemodocdbwebapp-myDemoResourceGroup' provisioning status is succeeded
		VERBOSE: 1:06:16 PM - Resource microsoft.insights/alertrules 'LongHttpQueue mydemodocdbwebapp' provisioning status is succeeded
		VERBOSE: 1:06:21 PM - Resource Microsoft.Web/Sites 'mydemodocdbwebapp' provisioning status is succeeded
		VERBOSE: 1:06:23 PM - Resource microsoft.insights/alertrules 'ForbiddenRequests mydemodocdbwebapp' provisioning status is succeeded
		VERBOSE: 1:06:25 PM - Resource microsoft.insights/alertrules 'ServerErrors mydemodocdbwebapp' provisioning status is succeeded
		VERBOSE: 1:06:25 PM - Resource microsoft.insights/components 'mydemodocdbwebapp' provisioning status is succeeded
		VERBOSE: 1:16:22 PM - Resource Microsoft.DocumentDb/databaseAccounts 'mydemodocdbaccount' provisioning status is succeeded
		VERBOSE: 1:16:22 PM - Resource Microsoft.DocumentDb/databaseAccounts 'mydemodocdbaccount' provisioning status is succeeded
		VERBOSE: 1:16:24 PM - Resource Microsoft.Web/Sites/config 'mydemodocdbwebapp/web' provisioning status is succeeded
		VERBOSE: 1:16:27 PM - Resource Microsoft.Web/Sites/Extensions 'mydemodocdbwebapp/MSDeploy' provisioning status is running
		VERBOSE: 1:16:35 PM - Resource Microsoft.Web/Sites/Extensions 'mydemodocdbwebapp/MSDeploy' provisioning status is succeeded

		ResourceGroupName : myDemoResourceGroup
		Location          : westus
		Resources         : {mydemodocdbaccount, CPUHigh mydemodocdbwebapp, ForbiddenRequests mydemodocdbwebapp, LongHttpQueue mydemodocdbwebapp...}
		ResourcesTable    :
                    Name                                    Type                                   Location
                    ======================================  =====================================  =========
                    mydemodocdbaccount                      Microsoft.DocumentDb/databaseAccounts  westus
                    CPUHigh mydemodocdbwebapp              microsoft.insights/alertrules          eastus
                    ForbiddenRequests mydemodocdbwebapp    microsoft.insights/alertrules          eastus
                    LongHttpQueue mydemodocdbwebapp        microsoft.insights/alertrules          eastus
                    ServerErrors mydemodocdbwebapp         microsoft.insights/alertrules          eastus
                    mydemodocdbwebapp-myDemoResourceGroup  microsoft.insights/autoscalesettings   eastus
                    mydemodocdbwebapp                      microsoft.insights/components          centralus
                    mydemodocdbwebapp                      Microsoft.Web/serverFarms              westus
                    mydemodocdbwebapp                      Microsoft.Web/sites                    westus

		ProvisioningState : Succeeded


5. Antes de examinarmos o nosso aplicativo de exemplo, vamos entender o que a implantação do modelo realizou:

	- Um aplicativo Web do Serviço de Aplicativo foi criado.

	- Uma conta do Banco de Dados de Documentos foi criada.

	- Um pacote de implantação da Web foi implantado para o aplicativo Web do Serviço de Aplicativo

	- A configuração do aplicativo Web foi modificada de modo que o ponto de extremidade do Banco de Dados de Documentos e a chave primária mestra foram reproduzidas como configurações de aplicativo.

	- Uma série de regras de monitoramento padrão foi criada.

	
6. Para usar o aplicativo, basta navegar para a URL do aplicativo Web (no exemplo acima, a URL seria http://mydemodocdbwebapp.azurewebsites.net)). Você verá o seguinte aplicativo Web:

	![Aplicativo Todo de exemplo](./media/documentdb-create-documentdb-website/image2.png)

7. Continue e crie algumas tarefas e, em seguida, vamos abrir o [Portal do Microsoft Azure](https://portal.azure.com).

8. Escolha esta opção de procurar grupos de recursos e selecione o grupo de recursos criado durante a implantação (no exemplo acima, myDemoResourceGroup).

	![Captura de tela do Portal Clássico do Azure com myDemoResourceGroup realçado](./media/documentdb-create-documentdb-website/image3.png)
9.  Observe como o mapa de recursos na lente Resumo mostra todos os recursos relacionados (conta do Banco de Dados de Documentos, aplicativo Web do Serviço de Aplicativo e Monitoramento).

	![Captura de tela de lente de Resumo](./media/documentdb-create-documentdb-website/image4.png)
10.  Clique em sua conta do Banco de Dados de Documentos e inicie o Gerenciador de Consultas (na parte inferior da folha da conta).

	![Captura de tela das lâminas Grupo de Recursos e Conta com o bloco Gerenciador de Consultas realçado](./media/documentdb-create-documentdb-website/image8.png)

11. Execute a consulta padrão, "SELECT * FROM" e inspecione os resultados. Observe que a consulta recuperou a representação JSON dos itens de tarefas que você criou na etapa 7 acima. Fique à vontade para experimentar as consultas. Por exemplo, tente executar SELECT * FROM c WHERE c.isComplete = true para retornar todos os itens de tarefas que foram marcados como concluídos.


	![Captura de tela das lâminas Gerenciador de Consultas e Resultados mostrando os resultados da consulta](./media/documentdb-create-documentdb-website/image5.png)
12. Fique à vontade para explorar a experiência do portal do Banco de Dados de Documentos ou modificar o exemplo de aplicativo de Todo. Quando você estiver pronto, vamos implantar outro modelo.
	
<a id="Build"></a>
## Etapa 3: Implantar a conta de documento e o exemplo de aplicativo Web ##

Agora, vamos implantar nosso segundo modelo.

> [AZURE.TIP] O modelo não valida se o nome do aplicativo Web e o nome da conta do Banco de Dados de Documentos inseridos abaixo são a)válidos e b)disponíveis. É altamente recomendável que você verifique a disponibilidade dos nomes que planeja fornecer antes de executar o script de implantação do PowerShell.

1. Abra o Microsoft Azure PowerShell e navegue até a pasta na qual você baixou e extraiu [Criar uma conta do Banco de Dados de Documentos e exemplo de aplicativo Web](https://portalcontent.blob.core.windows.net/samples/CreateDocDBWebSite.zip) (por exemplo, C:\\DocumentDBTemplates\\CreateDocDBWebsite).


2. Vamos executar o script PowerShell CreateDocDBWebsite.ps1. O script usa os mesmos parâmetros que o primeiro modelo implantado, ou seja:
	- WebsiteName: especifica o nome do aplicativo Web do Serviço de Aplicativo e é usado para construir a URL usada para acessar o aplicativo Web (por exemplo, se você especificar "myotherdocumentdbwebapp", a URL pela qual você acessará o site será myotherdocumentdbwebapp.azurewebsites.net).

	- ResourceGroupName: especifica o nome do Grupo de Recursos do Azure para implantar. Se o Grupo de Recursos especificado não existir, ele será criado.

	- docDBAccountName: especifica o nome da conta do Banco de Dados de Documentos a ser criada.

	- 	location: especifica o local do Azure onde criar os recursos do aplicativo Web e do Banco de Dados de Documentos. Os valores válidos são Ásia Oriental, Sudeste da Ásia, Leste dos EUA, Oeste dos EUA, Norte da Europa, Europa Ocidental (observe que o valor do local fornecido diferencia maiúsculas de minúsculas).

3. Aqui está um exemplo de comando para executar o script:

    	PS C:\DocumentDBTemplates\CreateDocDBWebSite> .\CreateDocDBWebSite.ps1 -WebSiteName "myotherdocumentdbwebapp" -ResourceGroupName "myOtherDemoResourceGroup" -docDBAccountName "myotherdocumentdbdemoaccount" -location "East US"

	> [AZURE.TIP] Observe que será solicitado inserir seu nome de usuário da conta do Azure e a senha como parte da execução do script. A implantação completa levará entre 10 e 15 minutos para ser concluída.

4. A saída da implantação será muito semelhante ao primeiro exemplo de modelo.
5. Antes de abrirmos o Portal do Azure, vamos entender o que essa implantação de modelo realizou:

	- Um aplicativo Web do Serviço de Aplicativo foi criado.

	- Uma conta do Banco de Dados de Documentos foi criada.

	- 	A configuração do aplicativo Web foi modificada de modo que o ponto de extremidade do Banco de Dados de Documentos do Azure, a chave mestra primária e secundária foram reproduzidos como configurações de aplicativo.

	- 	Uma série de regras de monitoramento padrão foi criada.

6. Vamos abrir o [Portal do Azure](https://portal.azure.com), optar por procurar Grupos de Recursos e selecionar o Grupo de Recursos criado durante a implantação (no exemplo acima, myOtherDemoResourceGroup).
7. Na lente Resumo, clique no aplicativo Web que acabou de ser implantado.

	![Captura de tela da lente de Resumo com o aplicativo Web myotherdocumentdbwebapp realçado](./media/documentdb-create-documentdb-website/image6.png)
8. Na lâmina do aplicativo Web, clique em **Todas as configurações**, em **Configurações do Aplicativo** e observe como há configurações de aplicativos presentes para o ponto de extremidade do Banco de Dados de Documentos e cada uma das chaves mestras do Banco de Dados de Documentos.

	![Captura de tela das lâminas de aplicativo Web, Configurações e configurações do aplicativo](./media/documentdb-create-documentdb-website/image7.png)
9. Sinta-se à vontade para continuar a explorar o Portal do Azure ou seguir um de nossos [exemplos](http://go.microsoft.com/fwlink/?LinkID=402386) do Banco de Dados de Documentos para criar seu próprio aplicativo do Banco de Dados de Documentos.

	
	
<a name="NextSteps"></a>
## Próximas etapas

Parabéns! Você implantou um Banco de Dados de Documentos, aplicativo Web do Serviço de Aplicativo e aplicativo Web de exemplo usando modelos do Gerenciador de Recursos do Azure.

- Para saber mais sobre o Banco de Dados de Documentos, clique [aqui](http://azure.com/docdb).
- Para saber mais sobre os aplicativos Web do Serviço de Aplicativo do Azure, clique [aqui](http://go.microsoft.com/fwlink/?LinkId=325362).
- Para saber mais sobre modelos do Gerenciador de Recursos do Azure, clique [aqui](https://msdn.microsoft.com/library/azure/dn790549.aspx).


## O que mudou
* Para obter um guia sobre a alteração de Sites para o Serviço de Aplicativo, consulte: [Serviço de Aplicativo do Azure e seu impacto sobre os serviços do Azure existentes](http://go.microsoft.com/fwlink/?LinkId=529714)
* Para obter um guia sobre a alteração do portal antigo para o novo portal, veja: [Referência para navegar no Portal Clássico do Azure](http://go.microsoft.com/fwlink/?LinkId=529715)

>[AZURE.NOTE] Se você deseja começar a usar o Serviço de Aplicativo do Azure antes de se inscrever em uma conta do Azure, vá até [Experimentar o Serviço de Aplicativo](http://go.microsoft.com/fwlink/?LinkId=523751), em que você pode criar imediatamente um aplicativo Web inicial de curta duração no Serviço de Aplicativo. Nenhum cartão de crédito é exigido, sem compromissos.
 

<!---HONumber=AcomDC_0204_2016-->