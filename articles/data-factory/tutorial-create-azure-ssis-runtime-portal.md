---
title: "Provisionar um tempo de execução de integração do SSIS usando o Azure Data Factory | Microsoft Docs"
description: "Este artigo explica como criar um tempo de execução de integração do Azure SSIS usando o Azure Data Factory."
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
ms.date: 01/29/2018
ms.author: spelluru
ms.openlocfilehash: abb5fd4953709198f6071bbdd4a1dc86900f0183
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/09/2018
---
# <a name="provision-an-azure-ssis-integration-runtime-by-using-the-azure-data-factory-ui"></a>Provisionar um tempo de execução de integração do Azure SSIS usando a interface do usuário do Azure Data Factory
Este tutorial fornece etapas para o provisionamento para o portal do Azure de um tempo de execução de integração (IR) do Azure-SSIS no Azure Data Factory. Em seguida, você pode usar o SQL Server Data Tools ou o SQL Server Management Studio para implantar pacotes do SSIS (SQL Server Integration Services) para esse tempo de execução no Azure. Para obter informações conceituais sobre IRs do SSIS do Azure, consulte [visão geral do Integration Runtime do Azure SSIS](concepts-integration-runtime.md#azure-ssis-integration-runtime).

Neste tutorial, você completa as seguintes etapas:

> [!div class="checklist"]
> * Criar uma fábrica de dados.
> * Provisionar um tempo de execução de integração do Azure-SSIS.

> [!NOTE]
> Este artigo aplica-se à versão 2 do Data Factory, que está atualmente em versão prévia. Se estiver usando a versão 1 do serviço do Data Factory, que está em disponibilidade geral (GA), confira [Introdução ao Data Factory versão 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).


## <a name="prerequisites"></a>pré-requisitos
- **Assinatura do Azure**. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar. 
- **Servidor de banco de dados SQL do Azure**. Se você ainda não tiver um servidor de banco de dados, crie um no Portal do Azure antes de começar. O Azure Data Factory cria o Catálogo do SSIS (banco de dados SSISDB) neste servidor de banco de dados. É recomendável que você crie o servidor de banco de dados na mesma região do Azure que a do Integration Runtime. Essa configuração permite que o Integration Runtime grave logs de execução no banco de dados SSISDB sem cruzar regiões do Azure. 
   - Confirme se a configuração **Permitir acesso aos serviços do Azure** está habilitada para o servidor de banco de dados. Para saber mais, confira [Proteger seu banco de dados SQL do Azure](../sql-database/sql-database-security-tutorial.md#create-a-server-level-firewall-rule-in-the-azure-portal). Para habilitar essa configuração usando o PowerShell, veja [New-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/new-azurermsqlserverfirewallrule?view=azurermps-4.4.1).
   - Adicione o endereço IP do computador cliente ou um intervalo de endereços IP que inclua o endereço IP do computador cliente à lista de endereços IP do cliente nas configurações do firewall para o servidor de banco de dados. Para saber mais, confira [Regras de firewall no nível do servidor e no nível do banco de dados do Banco de Dados SQL do Azure](../sql-database/sql-database-firewall-configure.md).
   - Confirme que o servidor de Banco de Dados SQL não tem um Catálogo SSIS (Banco de dados SSISDB). O provisionamento de um IR do Azure-SSIS não oferece suporte ao uso de um Catálogo do SSIS existente.
 
## <a name="create-a-data-factory"></a>Criar uma data factory

1. Iniciar o navegador da Web **Microsoft Edge** ou **Google Chrome**. Atualmente, a interface de usuário do Data Factory tem suporte apenas nos navegadores da Web Microsoft Edge e Google Chrome.
2. Entre no [portal do Azure](https://portal.azure.com/).    
3. Selecione **Novo** no menu à esquerda, selecione **Dados + Análise**e, em seguida, selecione **Data Factory**. 
   
   ![Seleção de Data Factory no painel "Novo"](./media/tutorial-create-azure-ssis-runtime-portal/new-data-factory-menu.png)
3. Na página **Novo data factory**, insira **MyAzureSsisDataFactory** em **Nome**. 
      
   ![Página de “Novo data factory”](./media/tutorial-create-azure-ssis-runtime-portal/new-azure-data-factory.png)
 
   O nome do Azure Data Factory deve ser *globalmente exclusivo*. Se você receber o erro a seguir, altere o nome do data factory (por exemplo, **&lt;yourname&gt;MyAzureSsisDataFactory**) e tente criar novamente. Para ver as regras de nomenclatura para artefatos do Data Factory consulte o artigo [Data Factory - regras de nomenclatura](naming-rules.md).
  
   `Data factory name “MyAzureSsisDataFactory” is not available`
4. Para **Assinatura**, selecione a assinatura do Azure na qual você deseja criar o data factory. 
5. Para **Grupo de Recursos**, realize uma das seguintes etapas:
     
   - Selecione **Usar existente** e selecione um grupo de recursos existente na lista. 
   - Selecione **Criar novo**e insira o nome de um grupo de recursos.   
         
   Para saber mais sobre grupos de recursos, consulte [Usando grupos de recursos para gerenciar recursos do Azure](../azure-resource-manager/resource-group-overview.md).  
6. Para **Versão**, selecione **V2 (Versão prévia)**.
7. Em **Local**, selecione uma localização para o data factory. A lista mostra somente os locais com suporte para a criação de data factories.
8. Selecione **Fixar no painel**.     
9. Selecione **Criar**.
10. No painel, o bloco com o seguinte status é exibido **Implantando o Data Factory**: 

   ![Bloco “Implantando Data Factory”](media/tutorial-create-azure-ssis-runtime-portal/deploying-data-factory.png)
11. Após a criação, a página do **Data Factory** será exibida.
   
   ![Home page do data factory](./media/tutorial-create-azure-ssis-runtime-portal/data-factory-home-page.png)
12. Selecione **Gerenciar e Monitorar** para abrir a interface do usuário (IU) do Data Factory em uma guia separada. 

## <a name="provision-an-azure-ssis-integration-runtime"></a>Provisionar um tempo de execução de integração do Azure-SSIS

1. Na página de **Introdução**, selecione o bloco **Configurar o Integration Runtime do SSIS**. 

   ![Bloco "Configurar o bloco do Integration Runtime SSIS"](./media/tutorial-create-azure-ssis-runtime-portal/configure-ssis-integration-runtime-tile.png)
2. Na página **Configurações Gerais** da **Instalação do Integration Runtime**, execute estas etapas: 

   ![Configurações gerais](./media/tutorial-create-azure-ssis-runtime-portal/general-settings.png)

   a. Para **Nome**, especifique um nome para o tempo de execução de integração.

   b. Para **Local**, selecione o local do tempo de execução de integração. Somente os locais com suporte são exibidos.

   c. Para **Tamanho do Nó**, selecione o tamanho do nó que será configurado com o tempo de execução do SSIS.

   d. Para **Número de Nós**, especifique o número de nós no cluster.
   
   e. Selecione **Avançar**. 
3. Na página **Configurações de SQL**, execute as seguintes etapas: 

   ![Configurações do SQL](./media/tutorial-create-azure-ssis-runtime-portal/sql-settings.png)

   a. Para **Assinatura**, especifique a assinatura do Azure que tem o servidor de banco de dados do Azure.

   b. Para o **Ponto de Extremidade de Servidor de Banco de Dados de Catálogo**, selecione o servidor de banco de dados do Azure.

   c. Para **Nome de Usuário do Administrador**, insira o nome de usuário do administrador.

   d. Para **Senha do Administrador**, insira a senha do administrador.

   e. Para **Nível do Servidor de Banco de Dados de Catálogo**, selecione a tipo de serviço para o banco de dados SSISDB. O valor padrão é **Básico**.

   f. Selecione **Avançar**. 
4. Na página **Configurações Avançadas**, selecione um valor para **Máximo de Execuções em Paralelo por Nó**.   

   ![Configurações avançadas](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings.png)    
5. Esta etapa é *opcional*. Se você tiver uma rede virtual (criada por meio do modelo de implantação clássica ou pelo Azure Resource Manager) em que deseja que o tempo de execução de integração ingresse, marque a caixa de seleção **Selecionar uma VNet para o tempo de execução de integração do Azure SSIS ingressar e permitir que os serviços do Azure configurem permissões/configurações de VNet**. Em seguida, conclua as seguintes etapas: 

   ![Configurações avançadas com uma rede virtual](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-vnet.png)    

   a. Para **Assinatura**, especifique a assinatura que tem a rede virtual.

   b. Para **Nome da VNet**, selecione o nome da rede virtual.

   c. Para **Nome da Sub-rede**, selecione o nome da sub-rede na rede virtual. 
6. Selecione **Concluir** para iniciar a criação do tempo de execução de integração do Azure SSIS. 

   > [!IMPORTANT]
   > Esse processo leva aproximadamente 20 minutos para ser concluída.
   >
   > O serviço Data Factory se conecta ao Banco de Dados SQL do Azure para preparar o Catálogo SSIS (banco de dados SSISDB). O script também configura permissões e configurações para sua rede virtual, se for especificado. E ingressa a nova instância do tempo de execução de integração do Azure SSIS à rede virtual.
   > 
   > Quando você provisiona uma instância de uma IR do Azure-SSI, o Azure Feature Pack para SSIS e o Acesso Redistribuível também são instalados. Esses componentes fornecem conectividade para arquivos do Excel e do Access e para várias fontes de dados do Azure, além das fontes de dados com suporte dos componentes internos. No momento, não é possível instalar componentes de terceiros para SSIS. (Essa restrição inclui componentes de terceiros da Microsoft, como os componentes do Oracle e Teradata da Attunity e os componentes SAP BI).

7. Na guia **Conexões**, alterne para **Tempos de Execução de Integração**, se necessário. Selecione **Atualizar** para atualizar o status. 

   ![Status de criação, com o botão "Atualizar"](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-creation-status.png)
8. Use os links na coluna **Ações** para parar/iniciar, editar ou excluir o tempo de execução de integração. Use o último link para exibir o código JSON para o tempo de execução de integração. Os botões editar e excluir são habilitados somente quando o IR é interrompido. 

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
Agora, use o SQL Server Data Tools ou o SQL Server Management Studio para implantar seus pacotes SSIS no Azure. Conecte-se ao servidor de banco de dados do Azure que hospeda o catálogo SSIS (banco de dados SSISDB). O nome do servidor de banco de dados do Azure está no formato `<servername>.database.windows.net` (para o banco de dados SQL do Azure). 

Consulte os artigos a seguir na documentação do SSIS: 

- [Implantar, executar e monitorar um pacote do SSIS no Azure](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial)   
- [Conectar-se ao Catálogo do SSIS no Azure](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [Agendar a execução do pacote no Azure](/sql/integration-services/lift-shift/ssis-azure-schedule-packages)
- [Conecte-se a fontes de dados locais com a autenticação do Windows](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth) 

## <a name="next-steps"></a>Próximas etapas
Neste tutorial, você aprendeu como: 

> [!div class="checklist"]
> * Criar uma fábrica de dados.
> * Provisionar um tempo de execução de integração do Azure-SSIS.

Avance para o tutorial a seguir para saber mais sobre a cópia de dados do local para a nuvem: 

> [!div class="nextstepaction"]
> [Copiar dados na nuvem](tutorial-copy-data-portal.md)
