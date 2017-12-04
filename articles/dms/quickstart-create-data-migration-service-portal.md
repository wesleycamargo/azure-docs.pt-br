---
title: "Criar uma instância do Serviço de Migração de Banco de Dados do Azure usando o Portal do Azure | Microsoft Docs"
description: "Use o Portal do Azure para criar uma instância do Serviço de Migração de Banco de Dados do Azure"
services: database-migration
author: edmacauley
ms.author: edmaca
manager: craigg
ms.reviewer: 
ms.service: database-migration
ms.workload: data-services
ms.custom: mvc
ms.topic: quickstart
ms.date: 11/28/2017
ms.openlocfilehash: 7fc4f8521afa41f21cda6576459a0794bef9ad3b
ms.sourcegitcommit: cfd1ea99922329b3d5fab26b71ca2882df33f6c2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/30/2017
---
# <a name="create-an-instance-of-the-azure-database-migration-service-by-using-the-azure-portal"></a>Crie uma instância do Serviço de Migração de Banco de Dados do Azure usando o Portal do Azure
Nesse Início Rápido, use o Portal do Azure para criar uma instância do Serviço de Migração de Banco de Dados do Azure.  Depois de criar o serviço, pode usá-lo para migrar dados do SQL Server no local para um banco de dados SQL do Azure.

Se você não tiver uma assinatura do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="log-in-to-the-azure-portal"></a>Faça logon no Portal do Azure
Primeiro, abra seu navegador da Web e navegue até o [portal do Microsoft Azure](https://portal.azure.com/). Insira suas credenciais para entrar no portal. A exibição padrão é o painel de serviço.

## <a name="register-the-resource-provider"></a>Registre o provedor de recursos
Registre o provedor de recursos Microsoft.DataMigration antes de criar sua primeira instância do Serviço de Migração de Banco de Dados.

1. No portal do Azure, selecione **Todos os serviços**e, em seguida, selecione **Assinaturas**.

2. Selecione a assinatura na qual você deseja criar a instância do Serviço de Migração do Banco de Dados do Azure e, em seguida, selecione **Provedores de recursos**.

3. Pesquise por migração e, em seguida, à direita do **Registrar**.

![Registrar provedor de recursos](media/quickstart-create-data-migration-service-portal/dms-register-provider.png)

## <a name="create-an-instance-of-the-service"></a>Crie uma instância do serviço
1. Clique em **+ Criar um recurso** para criar uma instância do Serviço de Migração de Banco de Dados do Azure, que está atualmente em versão prévia.

2. Pesquise no marketplace para "migração", selecione **Serviço de Migração de Banco de Dados do Azure** e, em seguida, na tela **Serviço de Migração de Banco de Dados do Azure (versão prévia)**, clique em **Criar**.

3. Na tela de **Serviço de Migração de Banco de Dados**: 

    - Escolha um **Nome de serviço** que seja fácil de lembrar e exclusivo para identificar sua instância de Serviço de Migração de Banco de Dados do Azure.
    - Selecione a **Assinatura** do Azure na qual quer criar a instância.
    - Crie uma nova **Rede** com um nome exclusivo.
    - Escolha o **Local** mais próximo ao seu servidor de origem ou de destino.
    - Selecione Básico: 1 vCore como **Tipo de preço**.

    ![Criar serviço de migração](media/quickstart-create-data-migration-service-portal/dms-create-service.png)
4. Selecione **Criar**.

Após alguns instantes, sua instância do Serviço de Migração de Banco de Dados do Azure é criado e estará pronto para uso. O Serviço de Migração do Banco de Dados exibe conforme mostrado na imagem seguinte:

![Serviço de migração criado](media/quickstart-create-data-migration-service-portal/dms-service-created.png)

## <a name="clean-up-resources"></a>Limpar recursos
Você pode limpar todos os recursos criados nesse início rápido excluindo o [Grupo de recursos do Azure](../azure-resource-manager/resource-group-overview.md).  Para excluir o grupo de recursos, navegue até a instância do Serviço de Migração de Banco de Dados do Azure que você criou. Selecione o nome do **Grupo de recursos** e selecione **Excluir grupo de recursos**.  Essa ação exclui todos os ativos no grupo de recursos, bem como o grupo em si.

## <a name="next-steps"></a>Próximas etapas
> [!div class="nextstepaction"]
> [Migrar SQL Server local para o Banco de Dados SQL do Azure](tutorial-sql-server-to-azure-sql.md)