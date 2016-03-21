<properties 	
	pageTitle="Azure Data Factory - Exemplos" 
	description="Fornece detalhes sobre os exemplos fornecidos com o serviço Azure Data Factory." 
	services="data-factory" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/07/2016" 
	ms.author="spelluru"/>

# Azure Data Factory - Exemplos

## Exemplos no GitHub
O [repositório GitHub Azure-DataFactory](https://github.com/azure/azure-datafactory) contém vários exemplos que ajudam você a utilizar rapidamente o serviço Azure Data Factory ou modificar os scripts e usá-los no próprio aplicativo. A pasta Samples\\JSON contém trechos de código JSON para cenários comuns.

## Exemplos no Portal do Azure
Você pode usar o bloco **Pipelines de exemplo** na home page de sua data factory para implantar os pipelines de exemplo e suas entidades associadas (conjuntos de dados e serviços vinculados) em sua data factory.

1. Crie uma nova data factory ou abra uma existente. Consulte [Introdução à Azure Data Factory](data-factory-get-started.md#CreateDataFactory) para conhecer as etapas para criar uma data factory.
2. Na folha **DATA FACTORY** para a data factory em questão, clique no bloco **Pipelines de exemplo**.

	![Bloco Pipelines de exemplo](./media/data-factory-samples/SamplePipelinesTile.png)

2. Na folha **Pipelines de exemplo**, clique no **exemplo** que você deseja implantar.
	
	![Folha Pipelines de exemplo](./media/data-factory-samples/SampleTile.png)

3. Especifique definições de configuração para o exemplo. Por exemplo, sua chave e nome da conta de armazenamento do Azure, o nome do SQL Server do Azure, banco de dados, ID de usuário e senha, etc...

	![Folha Exemplo](./media/data-factory-samples/SampleBlade.png)

4. Depois de terminar a especificação de configurações, clique em **Criar** para criar/implantar os pipelines de exemplo e serviços vinculados/tabelas usados pelos pipelines.
5. Você verá o status da implantação do bloco de exemplo em que você clicou anteriormente na folha **Pipelines de exemplo**.

	![Status da implantação](./media/data-factory-samples/DeploymentStatus.png)

6. Quando você vir a mensagem **Implantação bem-sucedida** no bloco para o exemplo, feche a folha **Pipelines de exemplo**.
5. Na folha **DATA FACTORY**, você verá que os pipelines, conjuntos de dados e serviços vinculados são adicionados à sua data factory.  

	![Folha Data Factory](./media/data-factory-samples/DataFactoryBladeAfter.png)
   
## Exemplos no Visual Studio

### Pré-requisitos

Você deve ter os seguintes itens instalados no seu computador:

- Visual Studio 2013 ou Visual Studio 2015
- Baixe o SDK do Azure para Visual Studio 2013 ou Visual Studio de 2015. Navegue até a [Página de Download do Azure](https://azure.microsoft.com/downloads/) e clique em **VS 2013** ou **VS 2015** na seção **.NET**.
- Baixe o plug-in Azure Data Factory para o Visual Studio: [VS 2013](https://visualstudiogallery.msdn.microsoft.com/754d998c-8f92-4aa7-835b-e89c8c954aa5) ou [VS 2015](https://visualstudiogallery.msdn.microsoft.com/371a4cf9-0093-40fa-b7dd-be3c74f49005). Se você estiver usando o Visual Studio 2013, você também pode atualizar o plug-in, fazendo o seguinte: no menu, clique em **Ferramentas** -> **Extensões e atualizações** -> **Online** -> **Galeria do Visual Studio** -> **Ferramentas do Microsoft Azure Data Factory** -> **Atualizar**.

### Use modelos de Data Factory

1. Clique em **Arquivo** no menu, aponte para **Novo** e clique em **Projeto**. 
2. Na caixa de diálogo **Novo Projeto**, faça o seguinte: 
	1. Selecione **DataFactory** em **Modelos**. 
	2. Selecione **Modelos de Data Factory** no painel à direita. 
	3. Insira um **nome** para o projeto. 
	4. Selecione um **local** para o projeto. 
	5. Clique em **OK**. 

	![Caixa de diálogo Novo projeto](./media/data-factory-samples/vs-new-project-adf-templates.png)
6. Na caixa de diálogo **Modelos de Data Factory**, selecione o modelo de exemplo da seção **Modelos de caso de uso** seção e clique em **Avançar**. As etapas a seguir vão orientá-lo usando o modelo **Criação de Perfil de Clientes**. As etapas são semelhantes para os outros modelos. 

	![Caixa de diálogo Modelos de Data Factory](./media/data-factory-samples/vs-data-factory-templates-dialog.png) 
7. Na caixa de diálogo **Configuração de Data Factory**, clique em **Avançar** na página **Noções básicas de Data Factory**.
8. Na página **Configurar data factory**, faça o seguinte: 
	1. Selecione **Nova Data Factory** para usar neste passo a passo. Você também pode selecionar **Usar data factory existente**.
	2. Insira um **nome** para o armazenamento de dados.
	3. Escolha a **assinatura do Azure** em que você deseja que o data factory seja criado. 
	4. Selecione o **grupo de recursos** para o data factory a ser criado.
	5. Selecione o **Oeste dos EUA** ou **Norte da Europa** como a **região**.
	6. Clique em **Próximo**. 
9. Na página **Configurar armazenamentos de dados**, especifique um **Banco de dados SQL do Azure** e **Conta de armazenamento do Azure** (ou) crie novos e clique em Avançar. 
10. Na página **Configurar computação**, selecione os padrões e clique em **Avançar**. 
11. Na página **Resumo** revise as configurações e clique em **Avançar**. 
12. Na página **Status da Implantação**, aguarde até que a implantação seja concluída e clique em **Concluir**.
13. No Gerenciador de Soluções, clique com o botão direito do mouse no projeto e clique em **Publicar**. 
19. Se a caixa de diálogo **Entrar na sua conta da Microsoft** for exibida, insira as credenciais da conta com a assinatura do Azure e clique em **entrar**.
20. Você deve ver a caixa de diálogo a seguir:

	![Caixa de diálogo Publicar](./media/data-factory-build-your-first-pipeline-using-vs/publish.png)

21. Na página **Configurar data factory**, faça o seguinte:
	1. Confirme a opção **Usar data factory existente**.
	2. Selecione a **da data factory** que tinha selecionado ao usar o modelo acima. 
	6. Clique em **Avançar** para alternar para a página **Publicar Itens**. (Pressione **TAB** para sair do campo Nome se o botão **Avançar** estiver desabilitado). 
23. Na página **Publicar Itens**, verifique se todas as entidades de Data Factories estão selecionadas e clique em **Avançar** para alternar para a página **Resumo**.     
24. Examine o resumo e clique em **Avançar** para iniciar o processo de implantação e exibir o **Status da Implantação**.
25. Na página **Status da Implantação**, você deve ver o status do processo de implantação. Clique em Concluir depois que a implantação tiver terminado. 

Confira [Criar sua primeira data factory (Visual Studio)](data-factory-build-your-first-pipeline-using-vs.md) para obter detalhes sobre como usar o Visual Studio para criar entidades de Data Factory e publicá-las no Azure.

<!---HONumber=AcomDC_0309_2016-->