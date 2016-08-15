<properties 
	pageTitle="Implantar Aplicativos Web do Serviço de Aplicativo do Azure e do Banco de Dados de Documentos usando um Modelo do Gerenciador de Recursos do Azure | Microsoft Azure" 
	description="Saiba como implantar uma conta do Banco de Dados de Documentos, aplicativos Web do Serviço de Aplicativo do Azure e aplicativo Web de exemplo usando um modelo do Gerenciador de Recursos do Azure." 
	services="documentdb, app-service\web" 
	authors="stephbaron" 
	manager="jhubbard" 
	editor="monicar" 
	documentationCenter=""/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/02/2016" 
	ms.author="stbaro"/>

# Implantar Aplicativos Web do Serviço de Aplicativo do Azure e do Banco de Dados de Documentos usando um Modelo do Gerenciador de Recursos do Azure

Este tutorial mostra como usar um modelo do Gerenciador de Recursos do Azure para implantar e integrar o [Banco de Dados de Documentos do Microsoft Azure](https://azure.microsoft.com/services/documentdb/), um aplicativo Web do [Serviço de Aplicativo do Azure](http://go.microsoft.com/fwlink/?LinkId=529714) e um aplicativo Web de exemplo.

Usando modelos do Azure Resource Manager, é possível automatizar com facilidade a implantação e a configuração de seus recursos do Azure. Este tutorial mostra como implantar um aplicativo Web e configurar automaticamente as informações de conexão da conta do Banco de Dados de Documentos.

Depois de concluir este tutorial, você estará apto a responder às seguintes perguntas:

-	Como usar um modelo do Gerenciador de Recursos do Azure para implantar e integrar uma conta do Banco de Dados de Documentos e um aplicativo Web do Serviço de Aplicativo do Azure?
-	Como usar um modelo do Gerenciador de Recursos do Azure para implantar e integrar uma conta do Banco de Dados de Documentos, um aplicativo Web nos Aplicativos Web do Serviço de Aplicativo e um aplicativo Webdeploy?

<a id="Prerequisites"></a>
## Pré-requisitos
> [AZURE.TIP] Embora este tutorial não suponha que você tenha uma experiência anterior com modelos do Azure Resource Manager ou do JSON, caso queira modificar os modelos referenciados ou as opções de implantação, será necessário ter conhecimento em cada uma dessas áreas.

Antes de seguir as instruções neste tutorial, certifique-se de ter o seguinte:

- Uma assinatura do Azure. O Azure é uma plataforma baseada em assinatura. Para obter mais informações sobre como adquirir uma assinatura, consulte [Opções de compra](https://azure.microsoft.com/pricing/purchase-options/), [Ofertas para membros](https://azure.microsoft.com/pricing/member-offers/) ou [Avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).

##<a id="CreateDB"></a>Etapa 1: Baixar os arquivos de modelo ##
Vamos começar baixando os arquivos de modelo que usaremos neste tutorial.

1. Baixe o modelo [Criar uma conta do Banco de Dados de Documentos, Aplicativos Web e implantar uma amostra de aplicativo de demonstração](https://portalcontent.blob.core.windows.net/samples/DocDBWebsiteTodo.json) em uma pasta local (por exemplo, C:\\DocumentDBTemplates). Este modelo implantará uma conta do Banco de Dados de Documentos, um aplicativo Web do Serviço de Aplicativo e um aplicativo Web. Ele também configurará automaticamente o aplicativo Web para conectar-se à conta do Banco de Dados de Documentos.

2. Baixe o modelo [Criar uma conta do Banco de Dados de Documentos e uma amostra dos Aplicativos Web](https://portalcontent.blob.core.windows.net/samples/DocDBWebSite.json) em uma pasta local (por exemplo, C:\\DocumentDBTemplates). Este modelo implantará uma conta do Banco de Dados de Documentos e um aplicativo Web do Serviço de Aplicativo e modificará as configurações de aplicativo do site para demonstrar facilmente as informações de conexão do Banco de Dados de Documentos; no entanto, ele não inclui um aplicativo Web.

<a id="Build"></a>
##Etapa 2: Implantar a conta do Banco de Dados de Documentos, o aplicativo Web do Serviço de Aplicativo e uma amostra de aplicativo de demonstração

Agora, vamos implantar nosso primeiro modelo.

> [AZURE.TIP] O modelo não valida se o nome do aplicativo Web e o nome da conta do Banco de Dados de Documentos inseridos abaixo são a)válidos e b)disponíveis. É altamente recomendável que você verifique a disponibilidade dos nomes que planeja fornecer antes de enviar a implantação.

1. Faça logon no [Portal do Azure](https://portal.azure.com), clique em Novo e procure “Implantação de modelo”.![Captura de tela da interface do usuário da implantação do modelo](./media/documentdb-create-documentdb-website/TemplateDeployment1.png)

2. Selecione o item de implantação de Modelo e clique em **Criar** ![Captura de tela da interface do usuário da implantação do modelo](./media/documentdb-create-documentdb-website/TemplateDeployment2.png)

3.  Clique em **Editar modelo**, cole o conteúdo do arquivo de modelo DocDBWebsiteTodo.json e clique em **Salvar**.![Captura de tela da interface do usuário da implantação do modelo](./media/documentdb-create-documentdb-website/TemplateDeployment3.png)

4. Clique em **Editar parâmetros**, forneça valores para cada um dos parâmetros obrigatórios e clique em **OK**. Os parâmetros são os seguintes:

	1. SITENAME: especifica o nome do aplicativo Web do Serviço de Aplicativo e é usado para construir a URL que será usada para acessar o aplicativo Web (por exemplo, se você especificar “mydemodocdbwebapp”, a URL pela qual você acessará o aplicativo Web será mydemodocdbwebapp.azurewebsites.net).

	2. HOSTINGPLANNAME: especifica o nome do plano de hospedagem do Serviço de Aplicativo a ser criado.

	3. LOCATION: especifica o local do Azure no qual serão criados os recursos do aplicativo Web e do Banco de Dados de Documentos.

	4. DATABASEACCOUNTNAME: especifica o nome da conta do Banco de Dados de Documentos a ser criada.

	![Captura de tela da interface do usuário da implantação do modelo](./media/documentdb-create-documentdb-website/TemplateDeployment4.png)

5. Escolha um Grupo de recursos existente ou forneça um nome para criar um novo grupo de recursos e escolha um local para ele.![Captura de tela da interface do usuário da implantação do modelo](./media/documentdb-create-documentdb-website/TemplateDeployment5.png)
  
6.  Clique em **Examinar termos legais**, **Comprar** e em **Criar** para iniciar a implantação. Selecione **Fixar no painel**, para que a implantação resultante seja facilmente visível em sua home page do portal do Azure.![Captura de tela da interface do usuário da implantação do modelo](./media/documentdb-create-documentdb-website/TemplateDeployment6.png)

7.  Quando a implantação for concluída, a folha do Grupo de recursos será aberta.![Captura de tela da folha Grupo de recursos](./media/documentdb-create-documentdb-website/TemplateDeployment7.png)

8.  Para usar o aplicativo, basta navegar até a URL do aplicativo Web (no exemplo acima, a URL seria http://mydemodocdbwebapp.azurewebsites.net). Você verá o seguinte aplicativo Web:

	![Aplicativo Todo de exemplo](./media/documentdb-create-documentdb-website/image2.png)

9. Vá em frente e crie algumas tarefas no aplicativo Web. Em seguida, retorne à folha do Grupo de recursos no portal do Azure. Clique no recurso de conta do Banco de Dados de Documentos na lista de Recursos e, em seguida, clique em **Gerenciador de Consultas**.![Captura de tela da lente de Resumo com o aplicativo Web myotherdocumentdbwebapp realçado](./media/documentdb-create-documentdb-website/TemplateDeployment8.png)

10. Execute a consulta padrão, "SELECT * FROM c" e inspecione os resultados. Observe que a consulta recuperou a representação JSON dos itens de tarefas que você criou na etapa 7 acima. Fique à vontade para experimentar as consultas. Por exemplo, tente executar SELECT * FROM c WHERE c.isComplete = true para retornar todos os itens de tarefas que foram marcados como concluídos.

	![Captura de tela das lâminas Gerenciador de Consultas e Resultados mostrando os resultados da consulta](./media/documentdb-create-documentdb-website/image5.png)

11. Fique à vontade para explorar a experiência do portal do Banco de Dados de Documentos ou modificar o exemplo de aplicativo de Todo. Quando você estiver pronto, vamos implantar outro modelo.
	
<a id="Build"></a>
## Etapa 3: Implantar a conta de documento e o exemplo de aplicativo Web

Agora, vamos implantar nosso segundo modelo. Esse modelo é útil para mostrar como é possível injetar informações de conexão do Banco de Dados de Documentos como ponto de extremidade da conta e a chave mestra em um aplicativo Web como configurações de aplicativo ou como uma cadeia de conexão personalizada. Por exemplo, talvez você tenha seu próprio aplicativo Web e queira implantá-lo com uma conta do Banco de Dados de Documentos e ter as informações de conexão populadas automaticamente durante a implantação.

> [AZURE.TIP] O modelo não valida se o nome do aplicativo Web e o nome da conta do Banco de Dados de Documentos inseridos abaixo são a)válidos e b)disponíveis. É altamente recomendável que você verifique a disponibilidade dos nomes que planeja fornecer antes de enviar a implantação.

1. No [Portal do Azure](https://portal.azure.com), clique em Novo e procure “Implantação de modelo”.![Captura de tela da interface do usuário da implantação do modelo](./media/documentdb-create-documentdb-website/TemplateDeployment1.png)

2. Selecione o item de implantação de Modelo e clique em **Criar** ![Captura de tela da interface do usuário da implantação do modelo](./media/documentdb-create-documentdb-website/TemplateDeployment2.png)

3.  Clique em **Editar modelo**, cole o conteúdo do arquivo de modelo DocDBWebSite.json e clique em **Salvar**.![Captura de tela da interface do usuário da implantação do modelo](./media/documentdb-create-documentdb-website/TemplateDeployment3.png)

4. Clique em **Editar parâmetros**, forneça valores para cada um dos parâmetros obrigatórios e clique em **OK**. Os parâmetros são os seguintes:

	1. SITENAME: especifica o nome do aplicativo Web do Serviço de Aplicativo e é usado para construir a URL que será usada para acessar o aplicativo Web (por exemplo, se você especificar “mydemodocdbwebapp”, a URL pela qual você acessará o aplicativo Web será mydemodocdbwebapp.azurewebsites.net).

	2. HOSTINGPLANNAME: especifica o nome do plano de hospedagem do Serviço de Aplicativo a ser criado.

	3. LOCATION: especifica o local do Azure no qual serão criados os recursos do aplicativo Web e do Banco de Dados de Documentos.

	4. DATABASEACCOUNTNAME: especifica o nome da conta do Banco de Dados de Documentos a ser criada.

	![Captura de tela da interface do usuário da implantação do modelo](./media/documentdb-create-documentdb-website/TemplateDeployment4.png)

5. Escolha um Grupo de recursos existente ou forneça um nome para criar um novo grupo de recursos e escolha um local para ele.![Captura de tela da interface do usuário da implantação do modelo](./media/documentdb-create-documentdb-website/TemplateDeployment5.png)
  
6.  Clique em **Examinar termos legais**, **Comprar** e em **Criar** para iniciar a implantação. Selecione **Fixar no painel**, para que a implantação resultante seja facilmente visível em sua home page do portal do Azure.![Captura de tela da interface do usuário da implantação do modelo](./media/documentdb-create-documentdb-website/TemplateDeployment6.png)

7.  Quando a implantação for concluída, a folha do Grupo de recursos será aberta.![Captura de tela da folha Grupo de recursos](./media/documentdb-create-documentdb-website/TemplateDeployment7.png)

8. Clique no recurso do Aplicativo Web na lista de Recursos e, em seguida, clique em **Configurações de aplicativo** ![Captura de tela do grupo de recursos](./media/documentdb-create-documentdb-website/TemplateDeployment9.png)

9. Observe como há configurações de aplicativo presentes para o ponto de extremidade do Banco de Dados de Documentos e para cada uma das chaves mestra do Banco de Dados de Documentos.![Captura de tela das configurações do aplicativo](./media/documentdb-create-documentdb-website/TemplateDeployment10.png)

10. Sinta-se à vontade para continuar a explorar o Portal do Azure ou seguir um de nossos [exemplos](http://go.microsoft.com/fwlink/?LinkID=402386) do Banco de Dados de Documentos para criar seu próprio aplicativo do Banco de Dados de Documentos.

	
	
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
 

<!---HONumber=AcomDC_0803_2016-->