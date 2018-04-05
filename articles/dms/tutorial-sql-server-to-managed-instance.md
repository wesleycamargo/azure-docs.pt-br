---
title: Usar o Serviço de Migração de Banco de Dados do Azure para migrar o SQL Server para a Instância Gerenciada do Banco de Dados SQL do Azure | Microsoft Docs
description: Saiba como migrar do SQL Server local para a Instância Gerenciada do Banco de Dados SQL do Azure usando o Serviço de Migração de Banco de Dados do Azure.
services: dms
author: edmacauley
ms.author: edmaca
manager: craigg
ms.reviewer: ''
ms.service: dms
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: article
ms.date: 03/29/2018
ms.openlocfilehash: 8abf3bae3a2274ed5514a5c621675b4c9ec27ae2
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2018
---
# <a name="migrate-sql-server-to-azure-sql-database-managed-instance"></a>Migrar o SQL Server para a Instância Gerenciada do Banco de Dados SQL do Azure
Você pode usar o serviço de migração do banco de dados do Azure para migrar os bancos de dados de uma instância do SQL Server local para o banco de dados do SQL Azure. Neste tutorial, você migra o banco de dados **Adventureworks2012** de uma instância local do SQL Server para um Banco de Dados SQL do Azure usando o Serviço de Migração de Banco de Dados do Azure.

Neste tutorial, você aprenderá como:
> [!div class="checklist"]
> * Crie uma instância do Serviço de Migração de Banco de Dados do Azure.
> * Crie um projeto de migração usando o Serviço de Migração de Banco de Dados do Azure.
> * Execute a migração.
> * Monitorar a migração.

## <a name="prerequisites"></a>pré-requisitos
Para concluir este tutorial, você precisará:

- Criar um VNET para o Serviço de Migração de Banco de Dados do Azure usando o modelo de implantação do Azure Resource Manager, que fornece conectividade site a site aos servidores de origem locais usando o [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) ou a [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). [Saiba mais sobre as topologias de rede para migrações da Instância Gerenciada do BD SQL do Azure usando o Serviço de Migração de Banco de Dados do Azure](https://aka.ms/dmsnetworkformi).
- Verifique se as regras do Grupo de Segurança de Rede da VNET (Rede Virtual) do Azure não bloqueiam as portas de comunicação 443, 53, 9354, 445 e 12000. Para obter mais detalhes sobre a filtragem de tráfego do NSG da Rede Virtual do Azure, consulte o artigo [Filtrar o tráfego de rede com grupos de segurança de rede](https://docs.microsoft.com/en-us/azure/virtual-network/virtual-networks-nsg).
- Configure o [Firewall do Windows para acesso ao mecanismo de banco de dados de fonte](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
- Abra o Firewall do Windows para permitir que o Serviço de Migração de Banco de Dados do Azure acesse o SQL Server de origem, que por padrão é porta TCP 1433.
- Se você estiver executando vários usando portas dinâmicas de instâncias nomeadas do SQL Server, talvez você queira habilitar o serviço navegador do SQL e permitir o acesso à porta UDP 1434 por meio de seus firewalls para que o Serviço de Migração do Banco de Dados do Azure possa se conectar a uma instância nomeada em seu código-fonte servidor.
- Se estiver usando um dispositivo de firewall na frente dos bancos de dados de origem, talvez você precise adicionar regras de firewall para permitir que o Serviço de Migração de Banco de Dados do Azure acesse os bancos de dados de origem para migração, além de arquivos por meio da porta 445 do SMB.
- Crie uma instância da Instância Gerenciada do Banco de Dados SQL do Azure seguindo os detalhes do artigo [Criar uma Instância Gerenciada do Banco de Dados SQL do Azure no portal do Azure](https://aka.ms/sqldbmi).
- Verifique se os logons usados para conectar o SQL Server de origem e a Instância Gerenciada de destino são membros da função de servidor sysadmin.
- Crie um compartilhamento de rede que pode ser usado pelo Serviço de Migração de Banco de Dados do Azure para fazer backup do banco de dados de origem.
- Verifique se a conta de serviço que executa a instância do SQL Server de origem tem privilégios de gravação no compartilhamento de rede criado.
- Anote um usuário do Windows (e a senha) que tem privilégios de controle total no compartilhamento de rede criado acima. O Serviço de Migração de Banco de Dados do Azure representa a credencial do usuário para carregar os arquivos de backup no contêiner de armazenamento do Azure para a operação de restauração.
- Crie um contêiner de blobs e recupere seu URI SAS usando as etapas descritas no artigo [Gerenciar os recursos do Armazenamento de Blobs do Azure com o Gerenciador de Armazenamento (versão prévia)](https://docs.microsoft.com/en-us/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container) e lembre-se de selecionar todas as permissões (Leitura, Gravação, Exclusão, Lista) na janela da política ao criar o URI SAS. Isso fornece ao Serviço de Migração de Banco de Dados do Azure o acesso ao contêiner da conta de armazenamento para carregar os arquivos de backup que são usados para migrar os bancos de dados para a Instância Gerenciada do Banco de Dados SQL do Azure

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registrar o provedor de recursos Microsoft.DataMigration

1.  Faça logon no portal do Azure, selecione **Todos os serviços**e, em seguida, selecione **Assinaturas**.
![Mostrar assinaturas do portal](media\tutorial-sql-server-to-managed-instance\portal-select-subscription.png)

1.  Selecione a assinatura na qual você deseja criar a instância do Serviço de Migração do Banco de Dados do Azure e, em seguida, selecione **Provedores de recursos**.
![Mostrar provedores de recursos](media\tutorial-sql-server-to-managed-instance\portal-select-resource-provider.png)

1.  Pesquise por migração e, em seguida, à direita do **Microsoft.DataMigration**, selecione **Registrar**.
![Registrar provedor de recursos](media\tutorial-sql-server-to-managed-instance\portal-register-resource-provider.png)    

## <a name="create-an-instance"></a>Escolher uma instância

1.  No portal do Azure, selecione **+ Criar um recurso**, pesquise **Serviço de Migração de Banco de Dados do Azure** e, em seguida, selecione **Serviço de Migração de Banco de Dados do Azure** na lista suspensa.

     ![Azure Marketplace](media\tutorial-sql-server-to-managed-instance\portal-marketplace.png)

1.  Na tela **Serviço de migração de banco de dados do Azure (versão prévia)**, select **Criar**.

    ![Criar uma instância do Serviço de Migração de Banco de Dados do Azure](media\tutorial-sql-server-to-managed-instance\dms-create.png)

1.  Na tela **Serviço de Migração de Banco de Dados**, especifique um nome para o serviço, a assinatura, um Grupo de recursos, uma rede virtual e o tipo de preço.

    Para obter mais informações sobre os custos e camadas de preços, consulte a [página de preços](https://aka.ms/dms-pricing). *Atualmente, o Serviço de Migração de Banco de Dados do Azure está em versão prévia e você não será cobrado.*

    **Rede:** selecione uma VNET existente ou crie uma nova, que fornece ao Serviço de Migração de Banco de Dados do Azure o acesso ao SQL Server de origem e à Instância Gerenciada do Banco de Dados SQL do Azure de destino. [Saiba mais sobre as topologias de rede para migrações da Instância Gerenciada do BD SQL do Azure usando o Serviço de Migração de Banco de Dados do Azure](https://aka.ms/dmsnetworkformi).

    Para obter mais informações sobre como criar a VNET no portal do Azure, consulte [Criar uma rede virtual com várias sub-redes usando o portal do Azure](https://aka.ms/DMSVnet).

    ![Criar o serviço DMS](media\tutorial-sql-server-to-managed-instance\dms-create-service.png)

1.  Selecione **Criar** para criar a conta.


## <a name="create-a-migration-project"></a>Criar um projeto de migração

Depois que o serviço for criado, localize-o no portal do Azure e abra-o.

1.  Selecione **+ Novo Projeto de Migração**.

1.  Na tela **Novo projeto de migração**, especifique um nome para o projeto, na caixa de texto **Tipo de servidor de origem**, selecione **SQL Server** e, em seguida, na caixa de texto **Tipo de servidor de destino**, selecione **Instância Gerenciada do Banco de Dados SQL do Azure**.

    ![Criar projeto do DMS](media\tutorial-sql-server-to-managed-instance\dms-create-project.png)

1.  Selecione **Criar** para criar o cluster.

## <a name="specify-source-details"></a>Especifique as configurações de origem

1.  Sobre o **detalhes fonte** tela, especifique os detalhes de conexão para o SQL Server de origem.

    ![Detalhes da origem](media\tutorial-sql-server-to-managed-instance\dms-source-details.png)

1.  Selecione **Salvar** e, em seguida, selecione o banco de dados **Adventureworks2012** para migração.

    ![Selecionar bancos de dados de origem](media\tutorial-sql-server-to-managed-instance\dms-source-database.png)

## <a name="specify-target-details"></a>Detalhes do destino favorito

1.  Selecione **Salvar** e, em seguida, na tela **Detalhes de destino**, especifique os detalhes de conexão para o destino, que é a Instância Gerenciada do Banco de Dados SQL do Azure pré-provisionada para a qual o banco de dados **AdventureWorks2012** será migrado.

    ![Selecionar o destino](media\tutorial-sql-server-to-managed-instance\dms-target-details.png)

1.  Selecione **Salvar**.

1.  Na tela **Resumo do projeto**, examine e verifique os detalhes associados ao projeto de migração.

## <a name="run-the-migration"></a>Execute a migração

1.  Selecione o projeto recém-salvo, selecione **+ Nova Atividade** e, em seguida, selecione **Executar migração**.

    ![Criar nova atividade](media\tutorial-sql-server-to-managed-instance\dms-create-new-activity.png)

1.  Quando solicitado, insira as credenciais para os servidores de origem e destino e, em seguida, selecione **Salvar**.

1.  Na tela **Mapear para os bancos de dados de destino**, selecione os bancos de dados de origem que você deseja migrar.

    ![Selecionar bancos de dados de origem](media\tutorial-sql-server-to-managed-instance\dms-select-source-databases.png)

1.  Selecione **Salvar** e, na tela **Definir as configurações de migração**, forneça os seguintes detalhes:

    | | |
    |--------|---------|
    |**Local de backup do servidor** | O compartilhamento de rede local no qual o Serviço de Migração de Banco de Dados do Azure pode copiar os backups de banco de dados de origem. A conta de serviço que executa a instância do SQL Server de origem deve ter privilégios de gravação nesse compartilhamento de rede. |
    |**Nome de usuário** | O nome de usuário do Windows que o Serviço de Migração de Banco de Dados do Azure pode representar e carregar os arquivos de backup no contêiner de armazenamento do Azure para a operação de restauração. |
    |**Senha** | Senha do usuário acima. |
    |**URI SAS de Armazenamento** | URI SAS que fornece ao Serviço de Migração de Banco de Dados do Azure o acesso ao contêiner da conta de armazenamento no qual o serviço carrega os arquivos de backup e que é usado para a migração de bancos de dados para a Instância Gerenciada do Banco de Dados SQL do Azure. [Saiba como obter o URI SAS do contêiner de blobs](https://docs.microsoft.com/en-us/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container).|
    
    ![Definir as configurações de migração](media\tutorial-sql-server-to-managed-instance\dms-configure-migration-settings.png)

1.  Selecione **Salvar** e, na tela Resumo de migração, na caixa de texto **Nome da atividade**, especifique um nome para a atividade de migração.

    ![Resumo do Aplicativo](media\tutorial-sql-server-to-managed-instance\dms-migration-summary.png)


## <a name="monitor-the-migration"></a>Monitorar a migração

1.  Selecione a atividade de migração para verificar o status da atividade.

1.  Após a conclusão da migração, verifique os bancos de dados de destino na Instância Gerenciada do Banco de Dados SQL do Azure de destino.

    ![Monitorar a migração](media\tutorial-sql-server-to-managed-instance\dms-monitor-migration.png)

