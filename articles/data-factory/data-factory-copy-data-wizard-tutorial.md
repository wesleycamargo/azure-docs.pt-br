<properties 
	pageTitle="Tutorial: criar um pipeline usando o Assistente de Cópia" 
	description="Neste tutorial, você cria um pipeline do Azure Data Factory com uma Atividade de Cópia usando o Assistente de Cópia com suporte do Data Factory" 
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
	ms.topic="get-started-article" 
	ms.date="08/01/2016" 
	ms.author="spelluru"/>

# Tutorial: Criar um pipeline com a Atividade de Cópia usando o Assistente de Cópia do Data Factory
> [AZURE.SELECTOR]
- [Visão geral do tutorial](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
- [Como usar o Editor do Data Factory](data-factory-copy-activity-tutorial-using-azure-portal.md)
- [Usando o PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
- [Como usar o Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
- [Usando a API REST](data-factory-copy-activity-tutorial-using-rest-api.md)
- [Usando o Assistente de Cópia](data-factory-copy-data-wizard-tutorial.md)

Neste tutorial, você usa o Assistente de Cópia do Data Factory para criar um pipeline com uma Atividade de Cópia em um data factory. Primeiro, você cria um data factory usando o portal do Azure e usa o Assistente de Cópia para criar serviços vinculados de Data Factory, conjuntos de dados e um pipeline com uma Atividade de Cópia que copia dados de um armazenamento de blobs do Azure para um banco de dados SQL do Azure.

> [AZURE.IMPORTANT] Leia o artigo [Visão geral do tutorial](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) e conclua as etapas de pré-requisito antes de executar este tutorial.

## Criar um data factory
Nesta etapa, você usa o Portal do Azure para criar um data factory do Azure denominado **ADFTutorialDataFactory**.

1.	Após fazer logon no [portal do Azure](https://portal.azure.com), clique em **NOVO** no canto inferior esquerdo, selecione **Análises de dados** na folha **Criar** e clique em **Data Factory** na folha **Análises de dados**.

	![Novo -> DataFactory](./media/data-factory-copy-data-wizard-tutorial/new-data-factory-menu.png)

6. Na folha **Nova data factory**:
	1. Digite **ADFTutorialDataFactory** para o **nome**.
	
  		![Folha Nova data factory](./media/data-factory-copy-data-wizard-tutorial/getstarted-new-data-factory.png)
	2. Clique em **NOME DO GRUPO DE RECURSOS** e faça o seguinte:
		1. Clique em **Criar um novo grupo de recursos**.
		2. Na folha **Criar grupo de recursos**, digite **ADFTutorialResourceGroup** para o **nome** do grupo de recursos e clique em **OK**.

			![Criar grupo de recursos](./media/data-factory-copy-data-wizard-tutorial/create-new-resource-group.png)

		Algumas das etapas neste tutorial supõem que você usa o nome: **ADFTutorialResourceGroup** para o grupo de recursos. Para saber mais sobre grupos de recursos, consulte [Usando grupos de recursos para gerenciar recursos do Azure](../resource-group-overview.md).
7. Na folha **Nova data factory**, observe que a opção **Adicionar ao Quadro Inicial** está selecionada.
8. Clique em **Criar** na folha **Nova data factory**.

	O nome da data factory do Azure deve ser globalmente exclusivo. Se você receber o erro: **O nome da data factory "ADFTutorialDataFactory" não está disponível**, altere o nome da data factory (por exemplo, yournameADFTutorialDataFactory) e tente criá-la novamente. Consulte o tópico [Data Factory - regras de nomenclatura](data-factory-naming-rules.md) para ver as regras de nomenclatura para artefatos de Data Factory.
	 
	![Nome da data factory indisponível](./media/data-factory-copy-data-wizard-tutorial/getstarted-data-factory-not-available.png)
	
	> [AZURE.NOTE] O nome do data factory pode ser registrado futuramente como um nome DNS e tornar-se publicamente visível.

9. Clique no hub **NOTIFICAÇÕES** à esquerda e procure as notificações do processo de criação. Clique em **X** para fechar a folha **NOTIFICAÇÕES** se ela estiver aberta.
10. Depois que a criação estiver concluída, você verá a folha **DATA FACTORY**, conforme mostrado abaixo.

    ![Página inicial da data factory](./media/data-factory-copy-data-wizard-tutorial/getstarted-data-factory-home-page.png)

## Criar um pipeline

1. Na home page do Data Factory, clique no bloco **Copiar dados** para iniciar o **Assistente de Cópia**.

	> [AZURE.NOTE] Se você vir que o navegador da Web está bloqueado em "Autorizando...", desabilite/desmarque a configuração **Bloquear cookies de terceiros e dados de site** (ou) mantenha-a habilitada, crie uma exceção para **login.microsoftonline.com** e tente iniciar o assistente novamente.
2. Na página **Propriedades**:
	1. Insira **CopyFromBlobToAzureSql** para o **Nome da tarefa**
	2. Insira uma **descrição** (opcional).
	3. Anote a **Data/hora de início** e a **Data/hora de término**. Altere a **Data/hora de término** para ser no dia posterior à **Data/hora de início**.
	3. Clique em **Avançar**.

	![Ferramenta de Cópia - página Propriedades](./media/data-factory-copy-data-wizard-tutorial/copy-tool-properties-page.png)
3. Na página **Repositório de dados de origem**, clique no bloco **Armazenamento de Blobs do Azure**. Use essa página para especificar o repositório de dados de origem para a tarefa de cópia. Você pode usar um serviço de repositório de dados vinculado existente ou especificar um novo repositório de dados. Para usar um serviço vinculado existente, clique em **DE SERVIÇOS VINCULADOS EXISTENTES** e selecione o serviço vinculado correto.

	![Ferramenta de Cópia - página de repositório de dados de origem](./media/data-factory-copy-data-wizard-tutorial/copy-tool-source-data-store-page.png)
5. Na página **Especificar a conta de armazenamento de Blobs do Azure**:
	1. Insira **AzureStorageLinkedService** para o **Nome do serviço vinculado**.
	2. Confirme as **Assinaturas do Azure** para o **Método de seleção de conta**.
	3. Selecione uma **Conta de armazenamento do Azure** na lista de contas de armazenamento do Azure disponíveis na assinatura selecionada. Você também pode inserir as configurações de conta de armazenamento manualmente selecionando a opção **Inserir manualmente** para o **Método de seleção de conta** e clicando em **Avançar**.

	![Ferramenta de Cópia - especifique a conta de armazenamento de Blobs do Azure](./media/data-factory-copy-data-wizard-tutorial/copy-tool-specify-azure-blob-storage-account.png)
6. Na página **Escolher o arquivo de entrada ou a pasta**:
	1. Navegue até a pasta **adftutorial**.
	2. Selecione **emp.txt** e clique em **Escolher**
	3. Clique em **Avançar**.

	![Ferramenta de Cópia - escolha a pasta ou o arquivo de entrada](./media/data-factory-copy-data-wizard-tutorial/copy-tool-choose-input-file-or-folder.png)
7. Na página **Configurações de formato de arquivo**, selecione valores **padrão** e clique em **Avançar**.

	![Ferramenta de Cópia - configurações de formato de arquivo](./media/data-factory-copy-data-wizard-tutorial/copy-tool-file-format-settings.png)
8. Na página Repositório de dados de destino, clique no bloco **Banco de Dados SQL do Azure** e clique em **Avançar**.
9. Na página **Especificar o banco de dados SQL do Azure**:
	1. Insira **AzureSqlLinkedService** para o campo **Nome do serviço vinculado**.
	2. Confirme se o **Método de seleção de servidor/banco de dados** está definido como **assinaturas do Azure**.
	3. Selecione **Nome do servidor** e **Banco de Dados**.
	4. Insira o **Nome de usuário** e a **Senha**.
	5. Clique em **Avançar**.
9. Na página **Mapeamento de tabela**, selecione **emp** para o campo **Destino** na lista suspensa e clique em **seta para baixo** (opcional) para ver o esquema e visualizar os dados.

	![Ferramenta de Cópia - mapeamento de tabela](./media/data-factory-copy-data-wizard-tutorial/copy-tool-table-mapping-page.png)
10. Na página **Mapeamento de esquema**, clique em **Avançar**.
11. Examine as informações na página **Resumo** e clique em **Concluir**. Isso cria dois serviços vinculados, dois conjuntos de dados (entrada e saída) e um pipeline no data factory (de onde você iniciou o Assistente de Cópia).
12. Na página **Implantação bem-sucedida**, clique em **Clique aqui para monitorar o pipeline de cópia**.

	![Ferramenta de Cópia - implantação bem-sucedida](./media/data-factory-copy-data-wizard-tutorial/copy-tool-deployment-succeeded.png)
13. Use as instruções de [Monitorar e gerenciar o pipeline usando o Aplicativo de Monitoramento](data-factory-monitor-manage-app.md) para saber mais sobre como monitorar o pipeline que você acabou de criar.

	![Aplicativo de Monitoramento](./media/data-factory-copy-data-wizard-tutorial/monitoring-app.png)
 

## Consulte também
| Tópico | Descrição |
| :---- | :---- |
| [Atividades de movimentação de dados](data-factory-data-movement-activities.md) | Este artigo fornece informações detalhadas sobre a Atividade de Cópia utilizada neste tutorial. |
| [Agendamento e execução](data-factory-scheduling-and-execution.md) | Este artigo explica os aspectos de agendamento e execução do modelo de aplicativo do Azure Data Factory. |
| [Pipelines](data-factory-create-pipelines.md) | Este artigo o ajuda a compreender pipelines e atividades no Azure Data Factory e como aproveitá-los para construir fluxos de trabalho orientados a dados de ponta a ponta para seu cenário ou negócio. |
| [Conjunto de dados](data-factory-create-datasets.md) | Este artigo o ajuda a entender os conjuntos de dados no Azure Data Factory.
| [Monitorar e gerenciar pipelines usando o Aplicativo de Monitoramento](data-factory-monitor-manage-app.md) | Este artigo descreve como monitorar, gerenciar e depurar seus pipelines usando o Aplicativo de Monitoramento e Gerenciamento. 

<!---HONumber=AcomDC_0817_2016-->