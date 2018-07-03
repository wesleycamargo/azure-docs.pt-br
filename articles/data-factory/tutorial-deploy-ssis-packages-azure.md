---
title: Provisionar o tempo de execução de integração do Azure-SSIS | Microsoft Docs
description: Saiba como provisionar o tempo de execução de integração do Azure SSIS no Azure Data Factory para que você possa implantar e executar pacotes SSIS no Azure.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: hero-article
ms.date: 06/27/2018
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: 2b9644b8cfdcca9c9ded7890e325fe9488c9511f
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/27/2018
ms.locfileid: "37061103"
---
# <a name="provision-the-azure-ssis-integration-runtime-in-azure-data-factory"></a>Criar um Integration Runtime do Azure-SSIS no Azure Data Factory
Este tutorial fornece etapas para o provisionamento para o portal do Azure de um tempo de execução de integração (IR) do Azure-SSIS no Azure Data Factory. Em seguida, você pode usar o SQL Server Data Tools ou o SQL Server Management Studio para implantar e executar pacotes do SSIS (SQL Server Integration Services) neste tempo de execução no Azure. Para obter informações conceituais sobre IRs do SSIS do Azure, consulte [visão geral do Integration Runtime do Azure SSIS](concepts-integration-runtime.md#azure-ssis-integration-runtime).

Neste tutorial, você completa as seguintes etapas:

> [!div class="checklist"]
> * Criar um data factory.
> * Provisionar um tempo de execução de integração do Azure-SSIS.

## <a name="prerequisites"></a>pré-requisitos
- **Assinatura do Azure**. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar. 
- **Servidor de banco de dados SQL do Azure**. Se você ainda não tiver um servidor de banco de dados, crie um no Portal do Azure antes de começar. O Azure Data Factory cria o Catálogo do SSIS (banco de dados SSISDB) neste servidor de banco de dados. É recomendável que você crie o servidor de banco de dados na mesma região do Azure que a do Integration Runtime. Essa configuração permite que o Integration Runtime grave logs de execução no banco de dados SSISDB sem cruzar regiões do Azure. 
- Com base no servidor de banco de dados selecionado, o SSISDB pode ser criado em seu nome como um único banco de dados, parte de um pool elástico ou em uma instância gerenciada (versão prévia) e acessível em rede pública ou ingressando em uma rede virtual. Se você usar o Banco de dados do Azure SQL com pontos de extremidade do serviço de rede virtual/Instância Gerenciada (Versão prévia) para hospedar o SSISDB ou precisar de acesso a dados locais, é preciso adicionar o Azure-SSIS IR a uma rede virtual. Confira [Criar Azure-SSIS IR em uma rede virtual](https://docs.microsoft.com/en-us/azure/data-factory/create-azure-ssis-integration-runtime). 
- Confirme se a configuração **Permitir acesso aos serviços do Azure** está habilitada para o servidor de banco de dados. Isso não é aplicável quando você usa o Banco de Dados SQL do Azure com pontos de extremidade do serviço de rede virtual/Instância Gerenciada (Versão prévia) para hospedar o SSISDB. Para saber mais, confira [Proteger seu banco de dados SQL do Azure](../sql-database/sql-database-security-tutorial.md#create-a-server-level-firewall-rule-in-the-azure-portal). Para habilitar essa configuração usando o PowerShell, veja [New-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/new-azurermsqlserverfirewallrule?view=azurermps-4.4.1). 
- Adicione o endereço IP do computador cliente ou um intervalo de endereços IP que inclua o endereço IP do computador cliente à lista de endereços IP do cliente nas configurações do firewall para o servidor de banco de dados. Para saber mais, confira [Regras de firewall no nível do servidor e no nível do banco de dados do Banco de Dados SQL do Azure](../sql-database/sql-database-firewall-configure.md). 
- Você pode se conectar ao servidor de banco de dados usando a autenticação do SQL com suas credenciais de administrador de servidor ou autenticação do Azure Active Directory (AAD) com sua Identidade de Serviço Gerenciada (MSI) do Azure Data Factory (ADF).  Para o último, você precisa adicionar o ADF MSI em um grupo do AAD com permissões de acesso para o servidor de banco de dados. Confira [Criar Azure-SSIS IR com a autenticação do AAD](https://docs.microsoft.com/en-us/azure/data-factory/create-azure-ssis-integration-runtime). 
- Confirme que o servidor de Banco de Dados SQL não tem um Catálogo SSIS (Banco de dados SSISDB). O provisionamento de um IR do Azure-SSIS não oferece suporte ao uso de um Catálogo do SSIS existente. 

> [!NOTE]
> - Você pode criar um data factory nas seguintes regiões: Leste dos EUA, Leste dos EUA 2, Sudeste Asiático e Europa Ocidental. 
> - É possível criar um IR do Azure-SSIS nas seguintes regiões: Leste dos EUA, Leste dos EUA 2, Centro dos EUA, Oeste dos EUA 2, Europa Setentrional, Europa Ocidental, Sul do Reino Unido e Leste da Austrália. 

## <a name="create-a-data-factory"></a>Criar uma data factory

1. Iniciar o navegador da Web **Microsoft Edge** ou **Google Chrome**. Atualmente, a interface do usuário do Data Factory tem suporte apenas nos navegadores da Web Microsoft Edge e Google Chrome. 
2. Entre no [portal do Azure](https://portal.azure.com/). 
3. Selecione **Novo** no menu à esquerda, selecione **Dados + Análise**e, em seguida, selecione **Data Factory**. 

   ![Seleção de Data Factory no painel "Novo"](./media/tutorial-create-azure-ssis-runtime-portal/new-data-factory-menu.png)

4. Na página **Novo data factory**, insira **MyAzureSsisDataFactory** em **Nome**. 

   ![Página de “Novo data factory”](./media/tutorial-create-azure-ssis-runtime-portal/new-azure-data-factory.png)

   O nome do Azure Data Factory deve ser *globalmente exclusivo*. Se você receber o erro a seguir, altere o nome do data factory (por exemplo, **&lt;yourname&gt;MyAzureSsisDataFactory**) e tente criar novamente. Para ver as regras de nomenclatura para artefatos do Data Factory consulte o artigo [Data Factory - regras de nomenclatura](naming-rules.md). 

   `Data factory name “MyAzureSsisDataFactory” is not available`

5. Para **Assinatura**, selecione a assinatura do Azure na qual você deseja criar o data factory. 
6. Para **Grupo de Recursos**, realize uma das seguintes etapas: 

   - Selecione **Usar existente** e selecione um grupo de recursos existente na lista. 
   - Selecione **Criar novo**e insira o nome de um grupo de recursos. 

   Para saber mais sobre grupos de recursos, consulte [Usando grupos de recursos para gerenciar recursos do Azure](../azure-resource-manager/resource-group-overview.md). 
7. Para **Versão**, selecione **V2 (Versão prévia)**. 
8. Em **Local**, selecione uma localização para o data factory. A lista mostra somente os locais com suporte para a criação de data factories. 
9. Selecione **Fixar no painel**. 
10. Selecione **Criar**. 
11. No painel, o bloco com o seguinte status é exibido **Implantando o Data Factory**: 

   ![Bloco “Implantando Data Factory”](media/tutorial-create-azure-ssis-runtime-portal/deploying-data-factory.png)

12. Após a criação, a página do **Data Factory** será exibida. 

   ![Home page do data factory](./media/tutorial-create-azure-ssis-runtime-portal/data-factory-home-page.png)

13. Selecione **Gerenciar e Monitorar** para abrir a interface do usuário (IU) do Data Factory em uma guia separada. 

## <a name="provision-an-azure-ssis-integration-runtime"></a>Provisionar um tempo de execução de integração do Azure-SSIS

1. Na página de **Introdução**, selecione o bloco **Configurar o Integration Runtime do SSIS**. 

   ![Bloco "Configurar o bloco do Integration Runtime SSIS"](./media/tutorial-create-azure-ssis-runtime-portal/configure-ssis-integration-runtime-tile.png)

2. Na página **Configurações Gerais** da **Instalação do Integration Runtime**, execute estas etapas: 

   ![Configurações gerais](./media/tutorial-create-azure-ssis-runtime-portal/general-settings.png)

   a. Para **Name**, insira o nome do seu tempo de execução de integração. 

   b. Para **Descrição**, insira a descrição do seu tempo de execução de integração. 

   c. Para **Local**, selecione o local do seu tempo de execução de integração. Somente os locais com suporte são exibidos. Recomendamos que você selecione o mesmo local do seu servidor de banco de dados para hospedar o SSISDB. 

   d. Para **Tamanho do Nó**, selecione o tamanho do nó no cluster de tempo de execução de integração. Apenas tamanhos de nós suportados são exibidos. Selecione um tamanho de nó grande (scale up), se você quiser executar muitos pacotes de computação / memória intensivos. 

   e. Para **Número do Nó**, selecione o número de nós em seu cluster de tempo de execução de integração. Somente os números de nós com suporte são exibidos. Selecione um grande cluster com muitos nós (scale out), se você deseja executar muitos pacotes em paralelo. 

   f. Para **Edition / License**, selecione a edição / licença do SQL Server para seu tempo de execução de integração: Standard ou Enterprise. Selecione Enterprise se você quiser usar os recursos avançados/premium em seu tempo de execução de integração. 

   g. Para **Economizar**, selecione a opção do Benefício Híbrido do Azure (AHB) para o tempo de execução de integração: Sim ou Não. Selecione Sim se você quiser colocar sua própria licença do SQL Server com o Software Assurance para aproveitar a redução de custos com o uso híbrido. 

   h. Clique em **Próximo**. 

3. Na página **Configurações de SQL**, execute as seguintes etapas: 

   ![Configurações do SQL](./media/tutorial-create-azure-ssis-runtime-portal/sql-settings.png)

   a. Para **Assinatura**, selecione a assinatura do Azure que tem o servidor de banco de dados para hospedar o SSISDB. 

   b. Para **Local**, selecione o local do seu servidor de banco de dados para hospedar o SSISDB. É recomendável que você selecione o mesmo local do seu tempo de execução de integração. 

   c. Para **Ponto de extremidade de servidor de banco de dados de catálogo**, selecione o ponto de extremidade do seu servidor de banco de dados para hospedar o SSISDB. Com base no servidor de banco de dados selecionado, o SSISDB pode ser criado em seu nome como um banco de dados independente, parte de um pool elástico ou em uma instância gerenciada (visualização) e acessível em rede pública ou ingressando em uma rede virtual. Para obter orientações sobre como escolher o tipo de servidor de banco de dados para hospedar o SSISDB, veja [Comparar o Banco de Dados SQL e a Instância Gerenciada (versão prévia)](create-azure-ssis-integration-runtime.md#compare-sql-database-and-managed-instance-preview). Se você selecionar o Banco de dados do Azure SQL com pontos de extremidade do serviço de rede virtual/Instância Gerenciada (Versão prévia) para hospedar o SSISDB ou precisar de acesso a dados locais, junte o Azure-SSIS IR a uma rede virtual. Veja [Criar Azure-SSIS IR em uma rede virtual](https://docs.microsoft.com/en-us/azure/data-factory/create-azure-ssis-integration-runtime). 

   d. Na caixa de seleção **Usar autenticação do AAD...**, selecione o método de autenticação para o servidor de banco de dados para hospedar o SSISDB: SQL ou Azure Active Directory (AAD) com sua Identidade de Serviço Gerenciada (MSI) do Azure Data Factory (ADF). Se você marcá-la, deverá adicionar o ADF MSI em um grupo do AAD com permissões de acesso para o servidor de banco de dados. Confira [Criar Azure-SSIS IR com a autenticação do AAD](https://docs.microsoft.com/en-us/azure/data-factory/create-azure-ssis-integration-runtime). 

   e. Para **Nome de usuário do administrador**, insira o nome de usuário de autenticação do SQL para o servidor de banco de dados para hospedar o SSISDB. 

   f. Para **Nome de usuário do administrador**, insira a senha de autenticação do SQL para o servidor de banco de dados para hospedar o SSISDB. 

   g. Para **Camada de serviço do banco de dados de catálogo**, selecione a camada de serviço para o servidor de banco de dados para hospedar o SSISDB: camada Basic/Standard/Premium ou o nome do Pool Elástico. 

   h. Clique em **Conexão de teste** e, se tiver êxito, clique em **Próximo**. 

4. Na página **Configurações avançadas**, conclua as etapas a seguir: 

   ![Configurações avançadas](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings.png)

   a. Para **Execuções paralelas máximas por nó**, selecione o número máximo de pacotes para executar simultaneamente por nó no cluster de tempo de execução de integração. Somente os números de pacotes com suporte são exibidos. Selecione um número baixo, se você quiser usar mais de um núcleo para executar um único pacote grande/ativado que seja de computação/memória intensiva. Selecione um número alto, se você quiser executar um ou mais pacotes pequenos / leves em um único núcleo. 

   b. Para o  **Custom Storage Container SAS URI**, insira opcionalmente o Uniform Resource Identifier (URI) de Assinatura de Acesso Compartilhado (SAS) do seu contêiner Azob Storage Blob onde seu script de instalação e seus arquivos associados estão armazenados, consulte [ Configuração personalizada para o Azure-SSIS IR ](https://docs.microsoft.com/en-us/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup). 

   c. Na caixa de seleção **Selecionar uma rede virtual...**, selecione se você deseja ingressar o tempo de execução de integração para uma rede virtual. Se você usar o Banco de dados do Azure SQL com pontos de extremidade do serviço de rede virtual/Instância Gerenciada (Versão prévia) para hospedar o SSISDB ou precisar de acesso a dados locais, junte o Azure-SSIS IR a uma rede virtual. Confira [Criar Azure-SSIS IR em uma rede virtual](https://docs.microsoft.com/en-us/azure/data-factory/create-azure-ssis-integration-runtime). 

5. Clique em **Concluir** para iniciar a criação do tempo de execução de integração. 

   > [!IMPORTANT]
   > Este processo leva aproximadamente de 20 a 30 minutos para ser concluído.
   >
   > O serviço Data Factory se conecta ao servidor de Banco de Dados SQL do Azure para preparar o Catálogo SSIS (banco de dados SSISDB). 
   > 
   > Quando você provisiona uma instância de uma IR do Azure-SSI, o Azure Feature Pack para SSIS e o Acesso Redistribuível também são instalados. Esses componentes fornecem conectividade para arquivos do Excel e do Access e para várias fontes de dados do Azure, além das fontes de dados com suporte dos componentes internos. Também é possível instalar outros componentes. Para obter mais informações, confira [Configuração personalizada para o tempo de execução de integração do Azure-SSIS](how-to-configure-azure-ssis-ir-custom-setup.md). 

6. Na guia **Conexões**, alterne para **Tempos de Execução de Integração**, se necessário. Selecione **Atualizar** para atualizar o status. 

   ![Status de criação, com o botão "Atualizar"](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-creation-status.png)

7. Use os links na coluna **Ações** para parar/iniciar, editar ou excluir o tempo de execução de integração. Use o último link para exibir o código JSON para o tempo de execução de integração. Os botões editar e excluir são habilitados somente quando o IR é interrompido. 

   ![Links na coluna "Ações"](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-actions.png) 

## <a name="create-an-azure-ssis-integration-runtime"></a>Criar um Integration Runtime do Azure-SSIS

1. Na interface do usuário do Azure Data Factory, alterne para a guia **Editar**, selecione **Conexões** e, em seguida, alterne para a guia **Tempos de Execução de Integração** para exibir os tempos de execução de integração existentes no data factory. 

   ![Seleções para exibir IRs existentes](./media/tutorial-create-azure-ssis-runtime-portal/view-azure-ssis-integration-runtimes.png)

2. Selecione **Novo** para criar um novo IR do Azure SSIS. 

   ![Tempo de execução de integração por meio do menu](./media/tutorial-create-azure-ssis-runtime-portal/edit-connections-new-integration-runtime-button.png)

3. Na janela **Instalação do Integration Runtime**, selecione **Pacotes SSIS existentes de lift-and-shift para execução no Azure** e, em seguida, selecione **Avançar**. 

   ![Especificar o tipo de tempo de execução de integração](./media/tutorial-create-azure-ssis-runtime-portal/integration-runtime-setup-options.png)

4. Consulte a seção [Provisionar um tempo de execução de integração do Azure SSIS](#provision-an-azure-ssis-integration-runtime) para conferir o restante das etapas de configuração de um IR do Azure-SSIS. 

## <a name="deploy-ssis-packages"></a>Implantar pacotes do SSIS
Agora, use o SSDT (SQL Server Data Tools) ou o SSMS (SQL Server Management Studio) para implantar seus pacotes SSIS no Azure. Conecte-se ao servidor de Banco de Dados SQL do Azure que hospeda o catálogo SSIS (banco de dados SSISDB). O nome do servidor de Banco de Dados SQL do Azure está no formato `<servername>.database.windows.net`. 

Consulte os artigos a seguir na documentação do SSIS: 

- [Implantar, executar e monitorar um pacote do SSIS no Azure](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial) 
- [Conectar-se ao Catálogo do SSIS no Azure](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database) 
- [Agendar a execução do pacote no Azure](/sql/integration-services/lift-shift/ssis-azure-schedule-packages) 
- [Conecte-se a fontes de dados locais com a autenticação do Windows](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth) 

## <a name="next-steps"></a>Próximas etapas
Neste tutorial, você aprendeu como: 

> [!div class="checklist"]
> * Criar um data factory.
> * Provisionar um tempo de execução de integração do Azure-SSIS.

Avance para o tutorial a seguir para saber mais sobre a cópia de dados do local para a nuvem: 

> [!div class="nextstepaction"]
> [Copiar dados na nuvem](tutorial-copy-data-portal.md)
