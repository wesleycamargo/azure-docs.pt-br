---
title: 'Tutorial: Usar DMS para migrar para a Instância Gerenciada do Banco de Dados SQL do Azure | Microsoft Docs'
description: Saiba como migrar do SQL Server local para a Instância Gerenciada do Banco de Dados SQL do Azure usando o Serviço de Migração de Banco de Dados do Azure.
services: dms
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: ''
ms.service: dms
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: article
ms.date: 10/10/2018
ms.openlocfilehash: e1cce6231fbb31dac6526a01ec402533b3861a21
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/06/2018
ms.locfileid: "52956492"
---
# <a name="tutorial-migrate-sql-server-to-azure-sql-database-managed-instance-offline-using-dms"></a>Tutorial: Migrar o SQL Server para a Instância Gerenciada do Banco de Dados SQL do Azure offline usando DMS
Você pode usar o Serviço de Migração de Banco de Dados do Azure para migrar os bancos de dados de uma instância do SQL Server local para uma [Instância Gerenciada do Banco de Dados SQL do Azure](../sql-database/sql-database-managed-instance.md). Para métodos adicionais que possam exigir algum esforço manual, consulte o artigo [Migração da instância do SQL Server para a Instância Gerenciada do Banco de Dados SQL do Azure](../sql-database/sql-database-managed-instance-migrate.md).

Neste tutorial, você migra o banco de dados **Adventureworks2012** de uma instância local do SQL Server para uma Instância Gerenciada do Banco de Dados SQL do Azure usando o Serviço de Migração de Banco de Dados do Azure.

Neste tutorial, você aprenderá como:
> [!div class="checklist"]
> * Criar uma instância do Serviço de Migração de Banco de Dados do Azure.
> * Criar um projeto de migração usando o Serviço de Migração de Banco de Dados do Azure.
> * Executar a migração.
> * Monitorar a migração.
> * Baixe um relatório de migração.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

Este artigo descreve uma migração offline do SQL Server para a Instância Gerenciada do Banco de Dados SQL do Azure. Para uma migração online, confira [Migrar o SQL Server para a Instância Gerenciada do Banco de Dados SQL do Azure online usando DMS](tutorial-sql-server-managed-instance-online.md).

## <a name="prerequisites"></a>Pré-requisitos
Para concluir este tutorial, você precisará:

- Criar um VNET para o Serviço de Migração de Banco de Dados do Azure usando o modelo de implantação do Azure Resource Manager, que fornece conectividade site a site aos servidores de origem locais usando o [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) ou a [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). [Saiba mais sobre as topologias de rede para migrações da Instância Gerenciada do BD SQL do Azure usando o Serviço de Migração de Banco de Dados do Azure](https://aka.ms/dmsnetworkformi).
- Verificar se as regras do Grupo de Segurança de Rede da VNET (Rede Virtual) do Azure não bloqueiam as portas de comunicação 443, 53, 9354, 445 e 12000. Para obter mais detalhes sobre a filtragem de tráfego do NSG da Rede Virtual do Azure, consulte o artigo [Filtrar o tráfego de rede com grupos de segurança de rede](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg).
- Configure o [Firewall do Windows para acesso ao mecanismo de banco de dados de fonte](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
- Abra o Firewall do Windows para permitir que o Serviço de Migração de Banco de Dados do Azure acesse o SQL Server de origem, que por padrão é porta TCP 1433.
- Caso esteja executando várias instâncias nomeadas do SQL Server usando portas dinâmicas, talvez seja preciso habilitar o serviço do SQL Browser e permitir o acesso à porta UDP 1434 por meio de seus firewalls para que o Serviço de Migração de Banco de Dados do Azure possa se conectar a uma instância nomeada em seu servidor de origem.
- Se estiver usando um dispositivo de firewall na frente dos bancos de dados de origem, talvez seja preciso adicionar regras de firewall para permitir que o Serviço de Migração de Banco de Dados do Azure acesse os bancos de dados de origem para migração, além de arquivos por meio da porta 445 do SMB.
- Crie uma Instância Gerenciada do Banco de Dados SQL do Azure seguindo os detalhes do artigo [Criar uma Instância Gerenciada do Banco de Dados SQL do Azure no portal do Azure](https://aka.ms/sqldbmi).
- Verificar se os logons usados para conectar o SQL Server de origem e a Instância Gerenciada de destino são membros da função de servidor sysadmin.
- Criar um compartilhamento de rede que pode ser usado pelo Serviço de Migração de Banco de Dados do Azure para fazer backup do banco de dados de origem.
- Certifique-se de que a conta de serviço que executa a instância do SQL Server de origem tem privilégios de gravação no compartilhamento de rede que você criou e que a conta de computador do servidor de origem tem acesso de leitura/gravação para o mesmo compartilhamento.
- Anote um usuário do Windows (e a senha) que tem privilégios de controle total no compartilhamento de rede criado anteriormente. O Serviço de Migração de Banco de Dados do Azure representa a credencial do usuário para carregar os arquivos de backup no contêiner de armazenamento do Azure para a operação de restauração.
- Crie um contêiner de blobs e recupere seu URI SAS usando as etapas descritas no artigo [Gerenciar os recursos do Armazenamento de Blobs do Azure com o Gerenciador de Armazenamento](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container) e lembre-se de selecionar todas as permissões (Leitura, Gravação, Exclusão, Lista) na janela da política ao criar o URI SAS. Esse detalhe fornece ao Serviço de Migração de Banco de Dados do Azure acesso ao contêiner da conta de armazenamento para carregar os arquivos de backup que são usados para migrar os bancos de dados para a Instância Gerenciada do Banco de Dados SQL do Azure.

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registrar o provedor de recursos Microsoft.DataMigration

1. Entre no portal do Azure, selecione **Todos os serviços** e selecione **Assinaturas**.

    ![Mostrar assinaturas do portal](media/tutorial-sql-server-to-managed-instance/portal-select-subscriptions.png)        

2. Selecione a assinatura na qual você deseja criar a instância do Serviço de Migração do Banco de Dados do Azure e, em seguida, selecione **Provedores de recursos**.

    ![Exibir provedores de recursos](media/tutorial-sql-server-to-managed-instance/portal-select-resource-provider.png)

3. Pesquise por migração e, em seguida, à direita do **Microsoft.DataMigration**, selecione **Registrar**.

    ![Registrar provedor de recursos](media/tutorial-sql-server-to-managed-instance/portal-register-resource-provider.png)   

## <a name="create-an-azure-database-migration-service-instance"></a>Criar uma instância do Serviço de Migração de Banco de Dados do Azure

1. No portal do Azure, selecione + **Criar um recurso**, pesquise **Serviço de Migração de Banco de Dados do Azure** e, em seguida, selecione **Serviço de Migração de Banco de Dados do Azure** na lista suspensa.

     ![Azure Marketplace](media/tutorial-sql-server-to-managed-instance/portal-marketplace.png)

2. Na tela **Serviço de Migração de Banco de Dados do Azure**, selecione **Criar**.

    ![Criar uma instância do Serviço de Migração de Banco de Dados do Azure](media/tutorial-sql-server-to-managed-instance/dms-create1.png)

3. Na tela **Criar Serviço de Migração**, especifique um nome para o serviço, a assinatura e um grupo de recurso novo ou existente.

4.  Selecione o local no qual você deseja criar a instância do DMS.

5. Escolha uma rede virtual existente (VNET) ou criar uma.
 
    A VNET fornece ao Serviço de Migração de Banco de Dados do Azure acesso ao SQL Server de origem e à Instância Gerenciada do Banco de Dados SQL do Azure de destino.

    Para mais informações sobre como criar uma VNET no portal do Azure, consulte o artigo [Criar uma rede virtual usando o portal do Azure](https://aka.ms/DMSVnet).

    Para obter detalhes adicionais, consulte o artigo [Topologias de rede para migrações da Instância Gerenciada do Banco de Dados SQL do Azure usando o Serviço de Migração de Banco de Dados do Azure](https://aka.ms/dmsnetworkformi).

6. Selecione um tipo de preço.

    Para obter mais informações sobre os custos e camadas de preços, consulte a [página de preços](https://aka.ms/dms-pricing).
   
    ![Criar o serviço DMS](media/tutorial-sql-server-to-managed-instance/dms-create-service2.png)

7.  Selecione **Criar** para criar a conta.

## <a name="create-a-migration-project"></a>Criar um projeto de migração

Depois que uma instância do serviço é criada, localize-a no portal do Azure, abra-a e, em seguida, crie um novo projeto de migração.

1. Faça logon no portal do Azure, selecione **+ criar um recurso**, procure o serviço de migração de banco de dados do Azure e, em seguida, selecione **serviço de migração de banco de dados do Azure** na lista suspensa.

    ![Crie uma instância do Serviço de Migração de Banco de Dados do Azure](media/tutorial-sql-server-to-managed-instance/dms-search.png)

2. Na tela **Serviço de Migração de Banco de Dados do Azure**, procure o nome da instância que você criou e, em seguida, selecione-a.
 
3. Selecione + **Novo Projeto de Migração**.

4. Na tela **Novo projeto de migração**, especifique um nome para o projeto, na caixa de texto **Tipo de servidor de origem**, selecione **SQL Server**; na caixa de texto **Tipo de servidor de destino**, selecione **Instância Gerenciada do Banco de Dados SQL do Azure** e, em **Escolher tipo de atividade**, selecione **Migração de dados offline**.

   ![Criar projeto do DMS](media/tutorial-sql-server-to-managed-instance/dms-create-project2.png)

5. Selecione **Criar** para criar o cluster.

## <a name="specify-source-details"></a>Especifique as configurações de origem

1. Na tela **Detalhe de origem de migração**, especifique os detalhes da conexão do SQL Server de origem.

2. Caso não tenha instalado um certificado confiável no servidor, selecione a caixa de seleção **Certificado de servidor confiável**.

    Quando não houver um certificado confiável instalado, o SQL Server gerará um certificado autoassinado quando a instância for iniciada. Esse certificado é usado para criptografar as credenciais das conexões de cliente.

    > [!CAUTION]
    > As conexões SSL que são criptografadas usando um certificado autoassinado não fornecem alta segurança. Elas são suscetíveis a ataques “man-in-the-middle”. Você não deve confiar no SSL usando certificados autoassinados em um ambiente de produção ou em servidores conectados à Internet.

   ![Detalhes da origem](media/tutorial-sql-server-to-managed-instance/dms-source-details1.png)

3. Clique em **Salvar**.

4. Na tela **Selecionar bancos de dados de origem**, selecione o banco de dados **Adventureworks2012** para migração.

   ![Selecionar bancos de dados de origem](media/tutorial-sql-server-to-managed-instance/dms-source-database1.png)

5. Clique em **Salvar**.

## <a name="specify-target-details"></a>Detalhes do destino favorito

1.  Na tela de **Detalhes de destino da migração**, especifique os detalhes de conexão do destino, que é a Instância Gerenciada do Banco de Dados SQL do Azure para a qual o banco de dados **AdventureWorks2012** está sendo migrado.

    Se você ainda não tiver provisionado a Instância Gerenciada do Banco de Dados SQL do Azure, selecione **Não** para um link para obter ajuda no provisionamento da instância. Você pode continuar assim mesmo com a criação do projeto e, em seguida, quando a Instância Gerenciada do Banco de Dados SQL do Azure estiver pronta, retorne a este projeto específico para executar a migração.   
 
       ![Selecionar o destino](media/tutorial-sql-server-to-managed-instance/dms-target-details2.png)

2.  Clique em **Salvar**.

## <a name="select-source-databases"></a>Selecionar bancos de dados de origem

1. Na tela **Selecionar bancos de dados de destino**, selecione o banco de dados de origem que você deseja migrar.

    ![Selecionar bancos de dados de origem](media/tutorial-sql-server-to-managed-instance/select-source-databases.png)

2. Clique em **Salvar**.

## <a name="select-logins"></a>Selecionar logons
 
1. Na tela **Selecionar logons**, selecione os logons que deseja migrar.

    >[!NOTE]
    >Esta versão só é compatível com a migração de logons do SQL.

    ![Selecionar logons](media/tutorial-sql-server-to-managed-instance/select-logins.png)

2. Clique em **Salvar**.
 
## <a name="configure-migration-settings"></a>Definir as configurações de migração
 
1. Na tela **Definir as configurações de migração**, forneça os seguintes detalhes:

    | | |
    |--------|---------|
    |**Escolha a opção de backup de origem** | Escolha a opção **Fornecerei os arquivos de backup mais recentes** quando você já tiver arquivos de backup completos disponíveis para uso pelo DMS para a migração de banco de dados. Escolha a opção **Permitirei que o Serviço de Migração de Banco de Dados do Azure crie arquivos de backup** quando desejar que o DMS faça o backup completo do banco de dados de origem primeiro e use-o para a migração. |
    |**Compartilhamento do local da rede** | O compartilhamento de rede do SMB local no qual o Serviço de Migração de Banco de Dados do Azure pode copiar os backups de banco de dados de origem. A conta de serviço que executa a instância do SQL Server de origem deve ter privilégios de gravação nesse compartilhamento de rede. Forneça um FQDN ou endereços IP do servidor no compartilhamento de rede, por exemplo, “\\\servername.domainname.com\backupfolder' ou '\\\IP address\backupfolder”.|
    |**Nome de usuário** | Certifique-se de que o usuário do Windows possui privilégios de controle total no compartilhamento de rede fornecido acima. O Serviço de Migração de Banco de Dados do Azure representará a credencial do usuário para carregar os arquivos de backup no contêiner de armazenamento do Azure para a operação de restauração. Se os bancos de dados habilitados para TDE forem selecionados para migração, o usuário do Windows acima deve ser a conta de administrador interno e [Controle de Conta de Usuário](https://docs.microsoft.com/windows/security/identity-protection/user-account-control/user-account-control-overview) deve ser desabilitado para o Serviço de Migração de Banco de Dados carregar e excluir os arquivos de certificados). |
    |**Senha** | Senha do usuário. |
    |**Configurações da conta de armazenamento** | O URI SAS que fornece ao Serviço de Migração do Banco de Dados do Azure acesso ao contêiner da conta de armazenamento no qual o serviço carrega os arquivos de backup e que é usado para a migração de bancos de dados para a Instância Gerenciada do Banco de Dados SQL do Azure. [Saiba como obter o URI SAS do contêiner de blobs](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container).|
    |**Configurações de TDE** | Se você estiver migrando bancos de dados de origem com a criptografia Transparent Data Encryption (TDE) habilitada, você precisará ter privilégios de gravação na Instância Gerenciada do Banco de Dados SQL do Azure de destino.  Selecione a assinatura onde foi provisionada a Instância Gerenciada do Banco de Dados SQL do Azure no menu suspenso.  Selecione a **Instância Gerenciada do Banco de Dados SQL do Azure** de destino no menu suspenso. |
    
    ![Definir as configurações de migração](media/tutorial-sql-server-to-managed-instance/dms-configure-migration-settings3.png)

2. Clique em **Salvar**.
 
## <a name="review-the-migration-summary"></a>Análise do resumo da migração

1. Na tela **Resumo de migração**, na caixa de texto **Nome da atividade**, especifique um nome para a atividade de migração.

2. Expanda a seção **Opção de validação** para exibir a tela **Escolher a opção de validação**, especifique se é preciso validar os bancos de dados migrados para correção do esquema e, em seguida, selecione **Salvar**.

3. Analise e verifique os detalhes associados ao projeto de migração.
 
    ![Resumo do projeto de migração](media/tutorial-sql-server-to-managed-instance/dms-project-summary2.png)

4.  Clique em **Salvar**.   

## <a name="run-the-migration"></a>Execute a migração

- Selecione **Executar migração**.

  A janela de atividade de migração aparece e o status da atividade está **Pendente**.

## <a name="monitor-the-migration"></a>Monitorar a migração

1. Na tela da atividade de migração, selecione **Atualizar** para atualizar a exibição.
 
   ![Atividade de migração em andamento](media/tutorial-sql-server-to-managed-instance/dms-monitor-migration1.png)

    É possível expandir ainda mais as categorias de logon e banco de dados para monitorar o status da migração dos respectivos objetos de servidor.

   ![Atividade de migração em andamento](media/tutorial-sql-server-to-managed-instance/dms-monitor-migration-extend.png)

2. Após a conclusão da migração, selecione **Baixar relatório** para obter um relatório que lista os detalhes associados ao processo de migração.
 
3. Verifique se o banco de dados de destino está no ambiente da Instância Gerenciada do Banco de Dados SQL do Azure de destino.

## <a name="next-steps"></a>Próximas etapas

- Para obter um tutorial mostrando como migrar um banco de dados para uma Instância Gerenciada usando o comando T-SQL RESTORE, consulte [Restaurar um backup para uma Instância Gerenciada usando o comando restaurar](../sql-database/sql-database-managed-instance-restore-from-backup-tutorial.md).
- Para obter informações sobre a Instância Gerenciada, consulte [O que é uma Instância Gerenciada](../sql-database/sql-database-managed-instance.md).
- Para obter informações sobre como se conectar a aplicativos a uma Instância Gerenciada, confira [Conectar aplicativos](../sql-database/sql-database-managed-instance-connect-app.md).