---
title: "Provisionar um tempo de execução de integração do Azure SSIS usando o portal | Microsoft Docs"
description: "Este artigo explica como criar um tempo de execução de integração do Azure SSIS usando o portal do Azure."
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: 
ms.devlang: 
ms.topic: hero-article
ms.date: 01/09/2018
ms.author: spelluru
ms.openlocfilehash: b6a795f8a26340f24f9e09aea371ba90afe50101
ms.sourcegitcommit: 7edfa9fbed0f9e274209cec6456bf4a689a4c1a6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/17/2018
---
# <a name="provision-an-azure-ssis-integration-runtime-by-using-the-data-factory-ui"></a>Provisionar um tempo de execução de integração do Azure SSIS usando a interface do usuário do Data Factory
Este tutorial fornece etapas para o provisionamento para o portal do Azure de um tempo de execução de integração (IR) do Azure-SSIS no Azure Data Factory. Em seguida, você pode usar o SSDT (SQL Server Data Tools) ou o SSMS (SQL Server Management Studio) para implantar pacotes do SSIS (SQL Server Integration Services) para esse tempo de execução no Azure. Para obter informações conceituais sobre o IR do SSIS do Azure, consulte [visão geral do Integration Runtime do Azure SSIS](concepts-integration-runtime.md#azure-ssis-integration-runtime).

Neste tutorial, você executa as seguintes etapas:

> [!div class="checklist"]
> * Criar uma fábrica de dados.
> * Criar e iniciar um tempo de execução de integração do Azure-SSIS

> [!NOTE]
> Este artigo aplica-se à versão 2 do Data Factory, que está atualmente em versão prévia. Se você estiver usando a versão 1 do serviço Data Factory, que já está disponível (GA), confira a [documentação do Data Factory versão 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).


## <a name="prerequisites"></a>pré-requisitos
- **Assinatura do Azure**. Se você não tiver uma assinatura do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar. 
- **Servidor de banco de dados SQL do Azure**. Se você ainda não tiver um servidor de banco de dados, crie um no Portal do Azure antes de começar. O Azure Data Factory cria o banco de dados do Catálogo do SSIS (SSISDB) neste servidor de banco de dados. É recomendável que você crie o servidor de banco de dados na mesma região do Azure que a do Integration Runtime. Essa configuração permite que o Integration Runtime grave logs de execução SSISDB sem cruzar regiões do Azure. 
    - Confirme se a configuração “**Permitir acesso aos serviços do Azure**” está habilitada para o servidor de banco de dados. Para saber mais, confira [Proteger seu banco de dados SQL do Azure](../sql-database/sql-database-security-tutorial.md#create-a-server-level-firewall-rule-in-the-azure-portal). Para habilitar essa configuração usando o PowerShell, veja [New-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/new-azurermsqlserverfirewallrule?view=azurermps-4.4.1).
    - Adicione o endereço IP do computador cliente ou um intervalo de endereços IP que inclua o endereço IP do computador cliente à lista de endereços IP do cliente nas configurações do firewall para o servidor de banco de dados. Para saber mais, confira [Regras de firewall no nível do servidor e no nível do banco de dados do Banco de Dados SQL do Azure](../sql-database/sql-database-firewall-configure.md). 
 
## <a name="create-a-data-factory"></a>Criar uma data factory

1. Faça logon no [Portal do Azure](https://portal.azure.com/).    
2. Clique em **Novo** no menu à esquerda, clique em **Dados + Análise** e clique em **Data Factory**. 
   
   ![Novo -> DataFactory](./media/tutorial-create-azure-ssis-runtime-portal/new-data-factory-menu.png)
3. Na página **Novo data factory**, insira **MyAzureSsisDataFactory** como o **nome**. 
      
     ![Página de novo data factory](./media/tutorial-create-azure-ssis-runtime-portal/new-azure-data-factory.png)
 
   O nome do Azure Data Factory deve ser **globalmente exclusivo**. Se você receber o erro a seguir, altere o nome do data factory (por exemplo, yournameMyAzureSsisDataFactory) e tente criar novamente. Confira o artigo [Data Factory - regras de nomenclatura](naming-rules.md) para ver as regras de nomenclatura para artefatos do Data Factory.
  
       `Data factory name “MyAzureSsisDataFactory” is not available`
3. Selecione a **assinatura** do Azure na qual você deseja criar o data factory. 
4. Para o **Grupo de Recursos**, execute uma das seguintes etapas:
     
      - Selecione **Usar existente**e selecione um grupo de recursos existente na lista suspensa. 
      - Selecione **Criar novo**e insira o nome de um grupo de recursos.   
         
      Para saber mais sobre grupos de recursos, consulte [Usando grupos de recursos para gerenciar recursos do Azure](../azure-resource-manager/resource-group-overview.md).  
4. Selecione **V2 (Versão Prévia)** para a **versão**.
5. Selecione o **local** do data factory. Somente os locais com suporte para a criação de data factories são mostrados na lista.
6. Selecione **Fixar no painel**.     
7. Clique em **Criar**.
8. No painel, você vê o seguinte bloco com status: **Implantando data factory**. 

    ![implantando bloco data factory](media/tutorial-create-azure-ssis-runtime-portal/deploying-data-factory.png)
9. Após a criação, a página do **Data Factory** será exibida conforme mostrado na imagem.
   
   ![Página inicial da data factory](./media/tutorial-create-azure-ssis-runtime-portal/data-factory-home-page.png)
10. Clique em **Criar e Monitorar** para iniciar a interface do usuário (IU) do Azure Data Factory em uma guia separada. 

## <a name="provision-an-azure-ssis-integration-runtime"></a>Provisionar um tempo de execução de integração do Azure SSIS

1. Na página de introdução, clique no bloco **Configurar o Integration Runtime do SSIS**. 

   ![Configurar o bloco do Integration Runtime SSIS](./media/tutorial-create-azure-ssis-runtime-portal/configure-ssis-integration-runtime-tile.png)
2. Na página **Configurações Gerais** da **Instalação do Integration Runtime**, siga estas etapas: 

   ![Configurações gerais](./media/tutorial-create-azure-ssis-runtime-portal/general-settings.png)

    1. Especifique um **nome** para o tempo de execução de integração.
    2. Selecione o **local** para o tempo de execução de integração. Somente os locais com suporte são exibidos.
    3. Selecione o **tamanho do nó** que é configurado com o tempo de execução do SSIS.
    4. Especifique o **número de nós** no cluster.
    5. Clique em **Próximo**. 
1. Nas **Configurações do SQL**, siga estas etapas: 

    ![Configurações do SQL](./media/tutorial-create-azure-ssis-runtime-portal/sql-settings.png)

    1. Especifique a **assinatura** do Azure que tem o servidor SQL do Azure. 
    2. Selecione o servidor SQL do Azure para o **Ponto de Extremidade de Servidor de Banco de Dados de Catálogo**.
    3. Insira o nome de usuário **administrador**.
    4. Insira a **senha** do administrador.  
    5. Selecione a **camada de serviço** para o banco de dados SSISDB. O valor padrão é Básico.
    6. Clique em **Próximo**. 
1.  Na página **Configurações Avançadas**, selecione um valor para **Máximo de Execuções em Paralelo por Nó**.   

    ![Configurações avançadas](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings.png)    
5. Esta etapa é **opcional**. Se você tiver uma rede virtual clássica (VNet) em que deseja que o tempo de execução de integração ingresse, escolha a opção **Selecionar uma rede virtual para o tempo de execução de integração do Azure SSIS ingressar e permitir que os serviços do Azure configurem permissões/configurações de rede virtual** e, em seguida, siga as etapas a seguir: 

    ![Configurações avançadas com rede virtual](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-vnet.png)    

    1. Especifique a **assinatura** com a rede virtual clássica. 
    2. Selecione a **VNet**. <br/>
    4. Selecione a **Sub-rede**.<br/> 
1. Clique em **Concluir** para iniciar a criação do tempo de execução de integração do Azure SSIS. 

    > [!IMPORTANT]
    > - Esse processo leva aproximadamente 20 minutos para ser concluída
    > - O serviço Data Factory se conecta ao Banco de Dados SQL do Azure para preparar o SSISDB (banco de dados do catálogo do SSIS). O script também configura permissões e configurações para sua VNet, se especificadas, e ingressa a nova instância de Integration Runtime do Azure-SSIS na VNet.
7. Na janela **Conexões**, alterne para **Tempos de Execução de Integração**, se necessário. Clique em **Atualizar** para atualizar o status. 

    ![Status de criação](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-creation-status.png)
8. Use os links na coluna **Ações** para monitorar, parar/iniciar, editar ou excluir o tempo de execução de integração. Use o último link para exibir o código JSON para o tempo de execução de integração. Os botões editar e excluir são habilitados somente quando o IR é interrompido. 

    ![IR do Azure SSIS - ações](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-actions.png)        
9. Clique no link **Monitorar** em **Ações**.  

    ![IR do Azure SSIS - detalhes](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-details.png)
10. Se não houver um **erro** associado ao IR do Azure SSIS, consulte o número de erros nesta página e o link para exibir detalhes sobre o erro. Por exemplo, se o catálogo do SSIS já existir no servidor de banco de dados, você verá um erro que indica a existência do banco de dados SSISDB.  
11. Clique em **Tempos de Execução de Integração** na parte superior para navegar de volta à página anterior para usar todos os tempos de execução de integração associados ao data factory.  

## <a name="azure-ssis-integration-runtimes-in-the-portal"></a>Tempos de execução de integração do Azure SSIS no portal

1. Na interface do usuário do Azure Data Factory, alterne para a guia **Editar**, clique em **Conexões** e, em seguida, alterne para a guia **Tempos de Execução de Integração** para exibir os tempos de execução de integração existentes no data factory. 
    ![Exibir IRs existentes](./media/tutorial-create-azure-ssis-runtime-portal/view-azure-ssis-integration-runtimes.png)
1. Clique em **Novo** para criar um novo IR do Azure SSIS. 

    ![Tempo de execução de integração por meio do menu](./media/tutorial-create-azure-ssis-runtime-portal/edit-connections-new-integration-runtime-button.png)
2. Para criar um tempo de execução de integração do Azure SSIS, clique em **Novo** conforme mostrado na imagem. 
3. Na janela Instalação do Integration Runtime, selecione **Pacotes SSIS existentes de lift-and-shift para execução no Azure** e, em seguida, clique em **Avançar**.

    ![Especificar o tipo de tempo de execução de integração](./media/tutorial-create-azure-ssis-runtime-portal/integration-runtime-setup-options.png)
4. Consulte a seção [Provisionar um tempo de execução de integração do Azure SSIS](#provision-an-azure-ssis-integration-runtime) para o restante das etapas para configurar um IR do Azure-SSIS. 

    
## <a name="deploy-ssis-packages"></a>Implantar pacotes do SSIS
Agora, use o SSDT (SQL Server Data Tools) ou o SSMS (SQL Server Management Studio) para implantar seus pacotes SSIS no Azure. Conecte-se ao Azure SQL Server que hospeda o SSISDB (catálogo do SSIS). O nome do SQL Server do Azure está no formato: `<servername>.database.windows.net` (para o banco de dados SQL do Azure). 

Consulte os artigos a seguir na documentação do SSIS: 

- [Implantar, executar e monitorar um pacote do SSIS no Azure](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial)   
- [Conecte-se ao catálogo do SSIS no Azure](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [Agendar a execução do pacote no Azure](/sql/integration-services/lift-shift/ssis-azure-schedule-packages)
- [Conecte-se a fontes de dados locais com a autenticação do Windows](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth) 

## <a name="next-steps"></a>Próximas etapas
Neste tutorial, você aprendeu como: 

> [!div class="checklist"]
> * Criar uma fábrica de dados.
> * Criar e iniciar um tempo de execução de integração do Azure-SSIS

Avance para o tutorial a seguir para saber mais sobre a cópia de dados do local para a nuvem: 

> [!div class="nextstepaction"]
>[Copiar dados na nuvem](tutorial-copy-data-portal.md)
