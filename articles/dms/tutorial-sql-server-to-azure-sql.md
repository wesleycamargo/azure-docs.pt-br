---
title: "Use o Serviço de migração de banco de dados do Azure para migrar do SQL Server para o banco de dados SQL Azure | Microsoft Docs"
description: "Saiba como migrar do SQL Server local para o SQL do Azure usando o Serviço de Migração de Banco de Dados do Azure."
services: dms
author: HJToland3
ms.author: jtoland
manager: jhubbard
ms.reviewer: 
ms.service: dms
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: article
ms.date: 11/17/2017
ms.openlocfilehash: 3938af29caec99f076452529cbc5d93cf2c8802b
ms.sourcegitcommit: a036a565bca3e47187eefcaf3cc54e3b5af5b369
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/17/2017
---
# <a name="migrate-sql-server-to-azure-sql-database"></a>Migrar SQL Server para BD SQL do Azure
Você pode usar o serviço de migração do banco de dados do Azure para migrar os bancos de dados de uma instância do SQL Server local para o banco de dados do SQL Azure. Neste artigo, você migra o banco de dados **Adventureworks2012** restaurado em uma instância local do SQL Server 2016 ou posterior para um Banco de Dados SQL do Azure usando o Microsoft Azure PowerShell.

Neste tutorial, você aprenderá como:
> [!div class="checklist"]
> * Avalie seu banco de dados local usando o Assistente de Migração de Dados.
> * Migre o esquema de exemplo usando o Assistente de Migração de Dados.
> * Crie uma instância do Serviço de Migração de Banco de Dados do Azure.
> * Crie um projeto de migração usando o Serviço de Migração de Banco de Dados do Azure.
> * Execute a migração.
> * Monitorar a migração.

## <a name="prerequisites"></a>Pré-requisitos
Para concluir este tutorial, você precisará:

- Fazer download e instalar o [SQL Server 2016 ou posterior](https://www.microsoft.com/sql-server/sql-server-downloads) (qualquer edição).
- Habilitar o protocolo TCP/IP, que está desabilitado por padrão durante a instalação do SQL Server Express, seguindo as instruções no artigo [Habilitar ou desabilitar um protocolo de rede de servidor](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure).
- Configurar o [Firewall do Windows para acesso ao mecanismo de banco de dados](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
- Criar uma instância do Banco de Dados SQL do Azure seguindo os detalhes no artigo [Criar um banco de dados SQL do Azure no portal do Azure](https://docs.microsoft.com/azure/sql-database/sql-database-get-started-portal).
- Fazer download e instalar o [Assistente de migração de dados](https://www.microsoft.com/download/details.aspx?id=53595) v3.3 ou posterior.
- Criar um VNET para o Serviço de Migração de Banco de Dados do Azure usando o modelo de implantação do Azure Resource Manager, que fornece conectividade site a site aos servidores de origem locais usando o [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) ou a [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).
- Garantir que as credenciais usadas para se conectar à instância de origem do SQL Server tenham as permissões [CONTROL SERVER](https://docs.microsoft.com/sql/t-sql/statements/grant-server-permissions-transact-sql).
- Garantir que as credenciais usadas para se conectar para direcionar a instância do Banco de Dados SQL do Azure tenham a permissão CONTROL DATABASE nos bancos de dados de destino do SQL do Azure.
- Abrir o firewall do Windows para permitir que o serviço de migração do banco de dados do Azure acesse a fonte do SQL Server.

## <a name="assess-your-on-premises-database"></a>Adicionar um Banco de Dados Local
Antes de você pode migrar dados de uma instância do SQL Server local para o banco de dados do SQL Azure, você precisa avaliar o banco de dados do SQL Server para os problemas de bloqueio que podem impedir que a migração. No Assistente de Migração de Dados v3.3 ou posterior, siga as etapas descritas no artigo [Realizando uma avaliação de migração do SQL Server](https://docs.microsoft.com/sql/dma/dma-assesssqlonprem) para concluir a avaliação de banco de dados local. Segue um resumo das etapas necessárias:
1.  No Assistente de Migração de Dados, selecione o ícone de novo (+) e, em seguida, selecione o **avaliação** tipo de projeto.
2.  Especifique um nome de projeto, no **tipo de servidor de origem** caixa de texto, selecione **do SQL Server**e, em seguida, no **tipo de servidor de destino** caixa de texto, selecione **SQL Azure Banco de dados**.
3.  Selecione **Criar** para criar o cluster.

    Quando você estiver avaliando o banco de dados do SQL Server de origem migrando para o banco de dados do SQL Azure, você pode escolher um ou ambos os seguintes tipos de relatórios de avaliação:
    - Determinar compatibilidade do banco de dados
    - Verificação de paridade de recursos

    Ambos os tipos de relatório são selecionados por padrão.
4.  No Assistente de Migração de Dados, sobre o **opções** tela, selecione **próximo**.
5.  No **selecione fontes** tela, o **conectar a um servidor** caixa de diálogo, forneça os detalhes de conexão ao SQL Server e, em seguida, selecione **conectar**.
6.  Na caixa de diálogo **Adicionar origens**, selecione **AdventureWorks2012**, selecione **Adicionar** e, em seguida, selecione **Iniciar Avaliação**.

    Quando a avaliação for concluída, os resultados exibem conforme mostrado no gráfico a seguir:

    ![Gerenciar a migração de dados](media\tutorial-sql-server-to-azure-sql\dma-assessments.png)

    Para o banco de dados SQL Azure, as avaliações identificam problemas de bloqueio de migração e problemas de paridade de recursos.

7.  Examine os resultados de avaliação para problemas de bloqueio de migração e paridade recurso selecionando as opções específicas.
    - A **categoria de paridade de recursos do SQL Server** fornece um conjunto abrangente de recomendações, abordagens alternativas disponíveis no Azure e etapas atenuantes para ajudá-lo a planejar o esforço em seus projetos de migração.
    - A **categoria de problemas de compatibilidade** fornece parcialmente ou recursos sem suporte que refletem os problemas de compatibilidade que podem bloquear a migração de bancos de dados do SQL Server para bancos de dados SQL do Azure locais. Recomendações também são fornecidas para ajudá-lo a resolver esses problemas.


## <a name="migrate-the-sample-schema"></a>Migrar o esquema de exemplo
Depois que você estiver familiarizado com a avaliação e satisfeito de que o banco de dados selecionado é um bom candidato à migração para o banco de dados SQL do Azure, use o Assistente de migração de dados para migrar o esquema para o banco de dados SQL do Azure.

> [!NOTE]
> Antes de criar um projeto de migração no Assistente de migração de dados, certifique-se de que você já possua um banco de dados SQL do Azure conforme mencionado nos pré-requisitos. Para fins deste tutorial, o nome do banco de dados SQL do Azure é considerado **AdventureWorksAzure**, mas você poderá renomeá-lo se desejar.

Para migrar o **AdventureWorks2012** esquema de banco de dados SQL Azure, execute as seguintes etapas:

1.  No Assistente de Migração de Dados, selecione o ícone de Novo (+) e, em seguida, em **Tipo de projeto**, selecione **Migração**.
3.  Especifique um nome de projeto, no **tipo de servidor de origem** caixa de texto, selecione **do SQL Server**e, em seguida, no **tipo de servidor de destino** caixa de texto, selecione **SQL Azure Banco de dados**.
4.  Em **escopo migração**, selecione **somente esquema**.

    Depois de executar as etapas anteriores, a interface do Assistente de Migração de Dados deve aparecer como mostrado no gráfico a seguir:
    
    ![Novo projeto do assistente de migração de dados](media\tutorial-sql-server-to-azure-sql\dma-create-project.png)

5.  Selecione **Criar** para criar o cluster.
6.  No Assistente de migração de dados, especifique os detalhes de conexão de origem para o SQL Server, selecione **conectar**e, em seguida, selecione o **AdventureWorks2012** banco de dados.

    ![Detalhes de Conexão de fonte de Assistente de Migração de Dados](media\tutorial-sql-server-to-azure-sql\dma-source-connect.png)
7.  Selecione **próximo**, em **conectar ao servidor de destino**, especifique os detalhes de conexão de destino para o banco de dados SQL do Azure, selecione **conectar**e, em seguida, selecione o banco de dados **AdventureWorksAzure** que você tinha previamente provisionado no banco de dados SQL do Azure.

    ![Detalhes de Conexão de destino Assistente de Migração de Dados](media\tutorial-sql-server-to-azure-sql\dma-target-connect.png)
8.  Selecione **próximo** para ir para o **selecionar objetos** tela, em que você pode especificar os objetos de esquema no **AdventureWorks2012** banco de dados que precisam ser implantados no Azure Banco de dados SQL.

    Por padrão, todos os discos são selecionados.

    ![Gerar Scripts SQL](media\tutorial-sql-server-to-azure-sql\dma-assessment-source.png)
9.  Selecione **script SQL gerar** para criar scripts SQL e, em seguida, examine os scripts de erros.

    ![Script de esquema](media\tutorial-sql-server-to-azure-sql\dma-schema-script.png)
10. Selecione **implantar esquema** para implantar o esquema de banco de dados do SQL Azure e, em seguida, depois que o esquema é implantado, verifique o servidor de destino para todas as anomalias.

    ![Implantar o esquema](media\tutorial-sql-server-to-azure-sql\dma-schema-deploy.png)

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registrar o provedor de recursos Microsoft.DataMigration
1. Faça logon no portal do Azure, selecione **Todos os serviços**e, em seguida, selecione **Assinaturas**.
 
   ![Mostrar assinaturas do portal](media\tutorial-sql-server-to-azure-sql\portal-select-subscription.png)
       
2. Selecione a assinatura na qual você deseja criar a instância do Serviço de Migração do Banco de Dados do Azure e, em seguida, selecione **Provedores de recursos**.
 
    ![Exibir provedores de recursos](media\tutorial-sql-server-to-azure-sql\portal-select-resource-provider.png)    
3.  Pesquise por migração e, em seguida, à direita do **Microsoft.DataMigration**, selecione **Registrar**.
 
    ![Registrar provedor de recursos](media\tutorial-sql-server-to-azure-sql\portal-register-resource-provider.png)    


## <a name="create-an-instance"></a>Escolher uma instância
1.  No portal do Azure, selecione **+ criar um recurso**, procure o serviço de migração de banco de dados do Azure e, em seguida, selecione **serviço de migração de banco de dados do Azure** na lista suspensa.

    ![Azure Marketplace](media\tutorial-sql-server-to-azure-sql\portal-marketplace.png)
2.  Na tela **Serviço de migração de banco de dados do Azure (versão prévia)**, select **Criar**.
 
    ![Criar uma instância do Serviço de Migração de Banco de Dados do Azure](media\tutorial-sql-server-to-azure-sql\dms-create.png)
  
3.  Sobre o **serviço de migração do banco de dados** tela, especifique um nome para o serviço, a assinatura, uma rede virtual e a camada de preços.

    Para obter mais informações sobre os custos e camadas de preços, consulte a [página de preços](https://aka.ms/dms-pricing).

     ![Criar uma instância do Serviço de Migração de Banco de Dados do Azure](media\tutorial-sql-server-to-azure-sql\dms-settings.png)

4.  Selecione **Criar** para criar a conta.

## <a name="create-a-migration-project"></a>Criar um projeto de migração
Depois que o serviço é criado, localizá-lo no portal do Azure e, em seguida, crie um projeto de migração.
1. Faça logon no portal do Azure, selecione **+ criar um recurso**, procure o serviço de migração de banco de dados do Azure e, em seguida, selecione **serviço de migração de banco de dados do Azure** na lista suspensa.
 
      ![Crie uma instância do Serviço de Migração de Banco de Dados do Azure](media\tutorial-sql-server-to-azure-sql\dms-search.png)
2. Sobre o **serviços de migração de banco de dados do Azure** tela, procure o nome do DMS do Azure a instância que você criou e, em seguida, selecione a instância.
 
     ![Crie uma instância do Serviço de Migração de Banco de Dados do Azure](media\tutorial-sql-server-to-azure-sql\dms-instance-search.png)
 
3. Selecione **+ Novo Projeto de Migração**.
4. Especifique um nome de projeto, no **tipo de servidor de origem** caixa de texto, selecione **do SQL Server**e, em seguida, no **tipo de servidor de destino** caixa de texto, selecione **SQL Azure Banco de dados**.

    ![Criar o Serviço de migração de banco de dados do Azure](media\tutorial-sql-server-to-azure-sql\dms-create-project.png)

5.  Selecione **Criar** para criar o cluster.


## <a name="specify-source-details"></a>Especifique as configurações de origem
1. Sobre o **detalhes fonte** tela, especifique os detalhes de conexão para o SQL Server de origem.

    ![Selecionar fonte](media\tutorial-sql-server-to-azure-sql\dms-select-source.png)

2. Selecione **salvar**e, em seguida, selecione o **AdventureWorks2012** banco de dados para migração.

    ![Selecionar fonte](media\tutorial-sql-server-to-azure-sql\dms-select-source-db.png)

## <a name="specify-target-details"></a>Detalhes do destino favorito
1. Selecione **salvar**e, em seguida, o **detalhes de destino** tela, especifique os detalhes de conexão para o destino, que é o banco de dados de SQL do Azure previamente configurado para o qual o **AdventureWorks2012**  esquema foi implantado usando o Assistente de migração de dados.

    ![Selecionar o destino](media\tutorial-sql-server-to-azure-sql\dms-select-target.png)

2. Selecione **Salvar** para salvar as alterações.
3. Sobre o **resumo de migração** tela, revise e verifique se os detalhes associados ao projeto de migração.

    ![Resumo do trabalho](media\tutorial-sql-server-to-azure-sql\dms-summary.png)

4. Selecione **Salvar**.

## <a name="run-the-migration"></a>Execute a migração
1.  Selecione o projeto recentemente salvo, selecione **+ nova atividade**e, em seguida, selecione **executar a migração de dados**.

    ![Exibir atividade](media\tutorial-sql-server-to-azure-sql\dms-new-activity.png)

2.  Quando solicitado, insira as credenciais para a servidores de origem e destino e, em seguida, selecione **salvar**.
3.  No **são mapeados para os bancos de dados de destino** tela, a origem e o banco de dados de destino para migração do mapa.

    Se o banco de dados de destino contém o mesmo nome de banco de dados do banco de dados de origem, o Azure DMS seleciona o banco de dados de destino por padrão.

    ![Mapear para bancos de dados de destino](media\tutorial-sql-server-to-azure-sql\dms-map-targets-activity.png)

4. Selecione **salvar**, no **selecionar tabelas** tela, expanda a lista de tabela e revise a lista de campos afetados.

    ![Selecionar tabelas](media\tutorial-sql-server-to-azure-sql\dms-configure-setting-activity.png)

5.  Selecione **salvar**, no **resumo de migração** tela, o **nome da atividade** texto, especifique um nome para a atividade de migração.

    Nessa tela, você também pode expandir o **escolher a opção de validação** tela que você pode usar para especificar para validar o banco de dados migrado para:
    - Esquema
    - Consistência de dados
    - Correção de consulta e desempenho

    ![Escolha a opção de validação](media\tutorial-sql-server-to-azure-sql\dms-configuration.png)

6.  Selecione **salvar**, revise o resumo para garantir que os detalhes de origem e destino correspondam especificado anteriormente.

    ![Resumo do Aplicativo](media\tutorial-sql-server-to-azure-sql\dms-run-migration.png)

7.  Selecione **executar migração** para iniciar a atividade de migração e, em seguida, selecione **atualizar** para verificar o status atual.

    ![Status da Atividade](media\tutorial-sql-server-to-azure-sql\dms-activity-status.png)

## <a name="monitor-the-migration"></a>Monitorar a migração
1. Selecione a atividade de migração para verificar o status da atividade.
2. Verifique se o banco de dados do SQL Azure de destino após a migração for concluída.

    ![Concluído](media\tutorial-sql-server-to-azure-sql\dms-completed-activity.png)
