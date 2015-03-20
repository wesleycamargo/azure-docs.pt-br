<properties 
	pageTitle="Implantar o Banco de Dados de Documentos e um Site do Azure usando um Modelo do Gerenciador de Recursos do Azure | Azure" 
	description="Saiba como implantar uma conta do Banco de Dados de Documentos, Site do Azure e aplicativo Web de exemplo usando um modelo do Gerenciador de Recursos do Azure." 
	services="documentdb, websites" 
	authors="stephbaron" 
	manager="johnmac" 
	editor="monicar" 
	documentationCenter=""/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/09/2015" 
	ms.author="stbaro"/>

# Implantar o Banco de Dados de Documentos e um Site do Azure usando um Modelo do Gerenciador de Recursos do Azure #

Este tutorial mostra como usar um modelo do Gerenciador de Recursos do Azure para implantar e integrar o [Banco de Dados de Documentos do Microsoft Azure](http://azure.microsoft.com/services/documentdb/), um [Site do Azure](http://azure.microsoft.com/services/websites/) e um aplicativo Web de exemplo.

Depois de concluir este tutorial, você poderá responder às seguintes perguntas:  

-	Como usar um modelo do Gerenciador de Recursos do Azure para implantar e integrar uma conta do Banco de Dados de Documentos e um Site do Azure?
-	Como usar um modelo do Gerenciador de Recursos do Azure para implantar e integrar uma conta do Banco de Dados de Documentos, um Site do Azure e um aplicativo Webdeploy?

##<a id="Prerequisites"></a>Pré-requisitos ##
> [AZURE.TIP] Embora este tutorial não suponha uma experiência anterior com modelos do Gerenciador de Recursos do Azure, JSON ou Azure PowerShell, caso você queira modificar os modelos referenciados ou as opções de implantação, será necessário ter conhecimento em cada uma dessas áreas.

Antes de seguir as instruções neste tutorial, certifique-se de ter o seguinte:

- Uma assinatura do Azure. O Azure é uma plataforma baseada em assinatura.  Para obter mais informações sobre como adquirir uma assinatura, consulte [Opções de compra](http://azure.microsoft.com/pricing/purchase-options/), [Ofertas para membros](http://azure.microsoft.com/pricing/member-offers/) ou [Avaliação gratuita](http://azure.microsoft.com/pricing/free-trial/).
- Uma Conta de Armazenamento do Azure. Para obter instruções, consulte [Sobre Contas de Armazenamento do Azure](../storage-whatis-account/).
- Uma estação de trabalho com o PowerShell do Azure. Para obter instruções, consulte [Instalar e configurar o PowerShell do Azure](http://azure.microsoft.com/documentation/articles/install-configure-powershell/).

##<a id="CreateDB"></a>Etapa 1: Baixe e extraia os arquivos de exemplo ##
Vamos começar baixando os arquivos de exemplo que usaremos neste tutorial.

1. Baixe o [Criar uma conta do Banco de Dados de Documentos, site e implantar um exemplo de aplicativo de demonstração](https://portalcontent.blob.core.windows.net/samples/CreateDocDBWebsiteTodo.zip) em uma pasta local (por exemplo, C:\DocumentDBTemplates) e extraia os arquivos.  Este exemplo implantará um aplicativo Web, um Site do Azure e uma conta do Banco de Dados de Documentos.  Ele também configurará automaticamente o aplicativo Web para conectar-se à conta do Banco de Dados de Documentos.

2. Baixe o [Criar um exemplo de site e conta do Banco de Dados de Documentos](https://portalcontent.blob.core.windows.net/samples/CreateDocDBWebSite.zip) em uma pasta local (por exemplo, C:\DocumentDBTemplates) e extraia os arquivos.  Este exemplo implantará uma conta do Banco de Dados de Documentos, um Site do Azure e modificará a configuração do site para demonstrar facilmente informações de conexão do Banco de Dados de Documentos, mas não inclui um aplicativo Web.  

> [AZURE.TIP] Observe que, dependendo das configurações de segurança do seu computador, talvez seja necessário desbloquear os arquivos extraídos clicando com o botão direito em **Propriedades** e em **Desbloquear**.

![Screenshot of the Properties window with the Unblock button highlighted](./media/documentdb-create-documentdb-website/image1.png)


##<a id="Build"></a>Etapa 2: Implantar a conta de Documentos, o site e o exemplo de aplicativo de demonstração ##

Agora, vamos implantar nosso primeiro modelo.

> [AZURE.TIP] O modelo não valida se o nome do site e o nome da conta do Banco de Dados de Documentos inseridos abaixo são a)válidos e b)disponíveis.  É altamente recomendável que você verifique a disponibilidade dos nomes que planeja fornecer antes de executar o script de implantação do PowerShell.

1. Abra o Microsoft Azure PowerShell e navegue até a pasta na qual você baixou e extraiu o [Criar uma conta do Banco de Dados de Documentos, site e implantar um exemplo de aplicativo de demonstração](https://portalcontent.blob.core.windows.net/samples/CreateDocDBWebsiteTodo.zip) (por exemplo, C:\DocumentDBTemplates\CreateDocDBWebsiteTodo).


2. Vamos executar o script PowerShell CreateDocDBWebsiteTodo.ps1.  O script usa os seguintes parâmetros obrigatórios:
	- WebsiteName: especifica o nome do site e é usado para construir a URL usada para acessar o site (por exemplo, se você especificar "mydemodocdbwebsite", a URL pela qual você acessará o site será mydemodocdbwebsite.azurewebsites.net).

	- ResourceGroupName: especifica o nome do Grupo de Recursos do Azure para implantar. Se o Grupo de Recursos especificado não existir, ele será criado.

	- docDBAccountName: especifica o nome da conta do Banco de Dados de Documentos a ser criada.

	- location: especifica o local do Azure onde criar os recursos de site da Web e do Banco de Dados de Documentos.  Os valores válidos são Ásia Oriental, Sudeste da Ásia, Leste dos EUA, Oeste dos EUA, Norte da Europa, Europa Ocidental (observe que o valor do local fornecido diferencia maiúsculas de minúsculas).


3. Aqui está um exemplo de comando para executar o script:

    	PS C:\DocumentDBTemplates\CreateDocDBWebsiteTodo> .\CreateDocDBWebsiteTodo.ps1 -WebSiteName "mydemodocdbwebsite" -ResourceGroupName "myDemoResourceGroup" -docDBAccountName "mydemodocdbaccount" -location "West US"

	> [AZURE.TIP] Observe que será solicitado inserir seu nome de usuário da conta do Azure e a senha como parte da execução do script.  A implantação completa levará entre 10 e 15 minutos para ser concluída.  	

4. E aqui está um exemplo da saída resultante: 

		VERBOSE: 1:06:00 PM - Created resource group 'myDemoResourceGroup' in location westus'
		VERBOSE: 1:06:01 PM - Template is valid.
		VERBOSE: 1:06:01 PM - Create template deployment 'Microsoft.DocumentDBWebSiteTodo'.
		VERBOSE: 1:06:08 PM - Resource Microsoft.DocumentDb/databaseAccounts 'mydemodocdbaccount' provisioning status is running
		VERBOSE: 1:06:10 PM - Resource Microsoft.Web/serverFarms 'mydemodocdbwebsite' provisioning status is succeeded
		VERBOSE: 1:06:14 PM - Resource microsoft.insights/alertrules 'CPUHigh mydemodocdbwebsite' provisioning status is succeeded
		VERBOSE: 1:06:16 PM - Resource microsoft.insights/autoscalesettings 'mydemodocdbwebsite-myDemoResourceGroup' provisioning status is succeeded
		VERBOSE: 1:06:16 PM - Resource microsoft.insights/alertrules 'LongHttpQueue mydemodocdbwebsite' provisioning status is succeeded
		VERBOSE: 1:06:21 PM - Resource Microsoft.Web/Sites 'mydemodocdbwebsite' provisioning status is succeeded
		VERBOSE: 1:06:23 PM - Resource microsoft.insights/alertrules 'ForbiddenRequests mydemodocdbwebsite' provisioning status is succeeded
		VERBOSE: 1:06:25 PM - Resource microsoft.insights/alertrules 'ServerErrors mydemodocdbwebsite' provisioning status is succeeded
		VERBOSE: 1:06:25 PM - Resource microsoft.insights/components 'mydemodocdbwebsite' provisioning status is succeeded
		VERBOSE: 1:16:22 PM - Resource Microsoft.DocumentDb/databaseAccounts 'mydemodocdbaccount' provisioning status is succeeded
		VERBOSE: 1:16:22 PM - Resource Microsoft.DocumentDb/databaseAccounts 'mydemodocdbaccount' provisioning status is succeeded
		VERBOSE: 1:16:24 PM - Resource Microsoft.Web/Sites/config 'mydemodocdbwebsite/web' provisioning status is succeeded
		VERBOSE: 1:16:27 PM - Resource Microsoft.Web/Sites/Extensions 'mydemodocdbwebsite/MSDeploy' provisioning status is running
		VERBOSE: 1:16:35 PM - Resource Microsoft.Web/Sites/Extensions 'mydemodocdbwebsite/MSDeploy' provisioning status is succeeded

		ResourceGroupName : myDemoResourceGroup
		Location          : westus
		Resources         : {mydemodocdbaccount, CPUHigh mydemodocdbwebsite, ForbiddenRequests mydemodocdbwebsite, LongHttpQueue mydemodocdbwebsite...}
		ResourcesTable    :
                    Name                                    Type                                   Location
                    ======================================  =====================================  =========
                    mydemodocdbaccount                      Microsoft.DocumentDb/databaseAccounts  westus
                    CPUHigh mydemodocdbwebsite              microsoft.insights/alertrules          eastus
                    ForbiddenRequests mydemodocdbwebsite    microsoft.insights/alertrules          eastus
                    LongHttpQueue mydemodocdbwebsite        microsoft.insights/alertrules          eastus
                    ServerErrors mydemodocdbwebsite         microsoft.insights/alertrules          eastus
                    mydemodocdbwebsite-myDemoResourceGroup  microsoft.insights/autoscalesettings   eastus
                    mydemodocdbwebsite                      microsoft.insights/components          centralus
                    mydemodocdbwebsite                      Microsoft.Web/serverFarms              westus
                    mydemodocdbwebsite                      Microsoft.Web/sites                    westus

		ProvisioningState : Succeeded


5. Antes de examinarmos o nosso aplicativo de exemplo, vamos entender o que a implantação do modelo realizou:

	- Um site do Azure foi criado.

	- Uma conta do Banco de Dados de Documentos do Azure foi criada.

	- Um pacote de implantação da Web foi implantado para o site do Azure

	- A configuração do Site do Azure foi modificada de modo que o ponto de extremidade do Banco de Dados de Documentos do Azure e a chave primária mestra foram reproduzidos como configurações de aplicativo.

	- Uma série de regras de monitoramento padrão foi criada.

	
6. Para usar o aplicativo, basta navegar para a URL do site (no exemplo acima, a URL seria http://mydemodocdbwebsite.azurewebsites.net).  Você verá o seguinte aplicativo Web:

	![Sample Todo application](./media/documentdb-create-documentdb-website/image2.png)

7. Prossiga e crie algumas tarefas e, em seguida, vamos abrir o [Portal de Visualização do Azure](https://portal.azure.com).

8. Escolha esta opção de procurar grupos de recursos e selecione o grupo de recursos criado durante a implantação (no exemplo acima, myDemoResourceGroup).

	![Screenshot of the Azure Preview portal with the myDemoResourceGroup highlighted](./media/documentdb-create-documentdb-website/image3.png)
9.  Observe como o mapa de recursos na lente Resumo mostra todos os recursos relacionados (conta do Banco de Dados de Documentos, Site e Monitoramento).

	![Screenshot of the Summary lens](./media/documentdb-create-documentdb-website/image4.png)
10.  Clique em sua conta do Banco de Dados de Documentos e inicie o Gerenciador de Consultas (na parte inferior da folha da conta).

	![Screenshot of the Resource Group and Account blades with the Query Explorer tile highlighted](./media/documentdb-create-documentdb-website/image8.png)

11. Execute a consulta padrão, "SELECT * FROM" e inspecione os resultados.  Observe que a consulta recuperou a representação JSON dos itens de tarefas que você criou na etapa 7 acima.  Fique à vontade para experimentar as consultas. Por exemplo, tente executar SELECT * FROM c WHERE c.isComplete = true para retornar todos os itens de tarefas que foram marcados como concluídos.


	![Screenshot of the Query Explorer and Results blades showing the query results](./media/documentdb-create-documentdb-website/image5.png)
12. Fique à vontade para explorar a experiência do portal do Banco de Dados de Documentos ou modificar o exemplo de aplicativo de Todo.  Quando você estiver pronto, vamos implantar outro modelo.
	 
##<a id="Build"></a>Etapa 3: Implantar a conta de documento e o exemplo de site ##

Agora, vamos implantar nosso segundo modelo.

> [AZURE.TIP] O modelo não valida se o nome do site e o nome da conta do Banco de Dados de Documentos inseridos abaixo são a)válidos e b)disponíveis.  É altamente recomendável que você verifique a disponibilidade dos nomes que planeja fornecer antes de executar o script de implantação do PowerShell.

1. Abra o Microsoft Azure PowerShell e navegue até a pasta na qual você baixou e extraiu o [Criar uma conta do Banco de Dados de Documentos e exemplo de site](https://portalcontent.blob.core.windows.net/samples/CreateDocDBWebSite.zip) (por exemplo, C:\DocumentDBTemplates\CreateDocDBWebsite).


2. Vamos executar o script PowerShell CreateDocDBWebsite.ps1.  O script usa os mesmos parâmetros que o primeiro modelo implantado, ou seja:
	- WebsiteName: especifica o nome do site e é usado para construir a URL usada para acessar o site (por exemplo, se você especificar "myotherdocumentdbwebsite", a URL pela qual você acessará o site será myotherdocumentdbwebsite.azurewebsites.net).

	- ResourceGroupName: especifica o nome do Grupo de Recursos do Azure para implantar.  Se o Grupo de Recursos especificado não existir, ele será criado.

	- docDBAccountName: especifica o nome da conta do Banco de Dados de Documentos a ser criada.

	- 	location: especifica o local do Azure onde criar os recursos de site da Web e do Banco de Dados de Documentos.  Os valores válidos são Ásia Oriental, Sudeste da Ásia, Leste dos EUA, Oeste dos EUA, Norte da Europa, Europa Ocidental (observe que o valor do local fornecido diferencia maiúsculas de minúsculas).

3. Aqui está um exemplo de comando para executar o script:

    	PS C:\DocumentDBTemplates\CreateDocDBWebSite> .\CreateDocDBWebSite.ps1 -WebSiteName "myotherdocumentdbwebsite" -ResourceGroupName "myOtherDemoResourceGroup" -docDBAccountName "myotherdocumentdbdemoaccount" -location "East US"

	> [AZURE.TIP] Observe que será solicitado inserir seu nome de usuário da conta do Azure e a senha como parte da execução do script.  A implantação completa levará entre 10 e 15 minutos para ser concluída.  	

4. A saída da implantação será muito semelhante ao primeiro exemplo de modelo. 
5. Antes de abrirmos o portal do Azure, vamos entender o que essa implantação de modelo realizou:

	- Um site do Azure foi criado.

	- Uma conta do Banco de Dados de Documentos do Azure foi criada.

	- 	A configuração de site do Azure foi modificada de modo que o ponto de extremidade do Banco de Dados de Documentos do Azure, a chave mestra primária e secundária foram reproduzidos como configurações de aplicativo.

	- 	Uma série de regras de monitoramento padrão foi criada.

6. Vamos abrir o [portal de Visualização do Azure](https://portal.azure.com), optar por procurar grupos de recursos e selecionar o grupo de recursos criado durante a implantação (no exemplo acima, myOtherDemoResourceGroup).
7. Na lente Resumo, clique no site do Azure que acabou de ser implantado.

	![Screenshot of the Summary lens with the myotherdocumentdwebsite website highlighted](./media/documentdb-create-documentdb-website/image6.png)
8. Na folha Site, clique em **Todas as configurações**, em **Configurações do aplicativo** e observe como há configurações de aplicativos presentes para o ponto de extremidade do Banco de Dados de Documentos e cada uma das chaves mestras do Banco de Dados de Documentos.

	![Screenshot of the Website, Settings, and Site settings blades](./media/documentdb-create-documentdb-website/image7.png)
9. Fique à vontade para continuar a explorar o portal do Azure ou siga um dos nosso [exemplos](http://go.microsoft.com/fwlink/?LinkID=402386) do Banco de Dados de Documentos para criar seu próprio aplicativo do Banco de Dados de Documentos.

	
	

##<a name="NextSteps"></a>Próximas etapas

Parabéns! Você implantou um Banco de Dados de Documentos, Sites do Azure e um aplicativo Web de exemplo usando modelos do Gerenciador de Recursos do Azure.

- Para saber mais sobre o Banco de Dados de Documentos, clique [aqui](http://azure.com/docdb).
- Para saber mais sobre os Sites do Azure, clique [aqui](http://go.microsoft.com/fwlink/?LinkId=325362).
- Para saber mais sobre modelos do Gerenciador de Recursos do Azure, clique [aqui](https://msdn.microsoft.com/library/azure/dn790549.aspx).

<!--HONumber=47-->
