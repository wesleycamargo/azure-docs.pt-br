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
ms.date: 11/17/2017
ms.openlocfilehash: 9faac0716334d627cdde4c0ef16262670333b5d4
ms.sourcegitcommit: a036a565bca3e47187eefcaf3cc54e3b5af5b369
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/17/2017
---
# <a name="create-an-instance-of-the-azure-database-migration-service-by-using-the-azure-portal"></a>Crie uma instância do Serviço de Migração de Banco de Dados do Azure usando o Portal do Azure
Nesse início rápido, use o Portal do Azure para criar uma instância do Serviço de Migração de Banco de Dados do Azure.  Depois de criar o serviço, será possível usá-lo para migrar dados do SQL Server no local para um banco de dados SQL do Azure.

Se você não tiver uma assinatura do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="log-in-to-the-azure-portal"></a>Faça logon no Portal do Azure
Primeiro, abra seu navegador da Web e navegue até o [portal do Microsoft Azure](https://portal.azure.com/). Insira suas credenciais para entrar no portal. A exibição padrão é o painel de serviço.

## <a name="register-the-resource-provider"></a>Registre o provedor de recursos
Você precisa registrar o provedor de recursos Microsoft.DataMigration antes de criar seu primeiro Serviço de Migração de Banco de Dados.

1. No portal do Azure, selecione **Todos os serviços**e, em seguida, selecione **Assinaturas**.

1. Selecione a assinatura na qual você deseja criar a instância do Serviço de Migração do Banco de Dados do Azure e, em seguida, selecione **Provedores de recursos**.

1. Pesquise por migração e, em seguida, à direita do **Registrar**.

![Registrar provedor de recursos](media/quickstart-create-data-migration-service-portal/dms-register-provider.png)

## <a name="create-azure-database-migration-service"></a>Criar o Serviço de Migração de Banco de Dados do Azure
1. Clique em **+** para criar um novo serviço.  Serviço de migração de banco de dados em versão prévia.  

1. No marketplace, pesquise por "migração", selecione "Serviço de migração de banco de dados (versão prévia)" e clique em **criar**.

    ![Criar serviço de migração](media/quickstart-create-data-migration-service-portal/dms-create-service.png)

    - Escolha um **Nome de serviço** que seja fácil de lembrar e exclusivo para identificar sua instância de Serviço de Migração de Banco de Dados do Azure.
    - Selecione sua **Assinatura** do Azure na qual você deseja criar o Serviço de migração de banco de dados.
    - Crie uma nova **Rede** com um nome exclusivo.
    - Escolha o **Local** mais próximo ao seu servidor de origem ou de destino.
    - Selecione Básico: 1 vCore como **Tipo de preço**.

1. Clique em **Criar**.

Após alguns instantes, seu Serviço de Migração de Banco de Dados do Azure será criado e estará pronto para uso.  Você verá o Serviço de migração do banco de dados conforme mostrado na imagem.

![Serviço de migração criado](media/quickstart-create-data-migration-service-portal/dms-service-created.png)

## <a name="clean-up-resources"></a>Limpar recursos
Você pode limpar todos os recursos criados no início rápido excluindo o [Grupo de recursos do Azure](../azure-resource-manager/resource-group-overview.md).  Para excluir o grupo de recursos, navegue até o Serviço de migração de banco de dados que você criou, clique no nome do **Grupo de recursos** e selecione **Excluir grupo de recursos**.  Essa ação exclui todos os ativos no grupo de recursos, bem como o grupo em si.

## <a name="next-steps"></a>Próximas etapas
> [!div class="nextstepaction"]
> [Migrar SQL Server local para BD SQL do Azure](tutorial-sql-server-to-azure-sql.md)