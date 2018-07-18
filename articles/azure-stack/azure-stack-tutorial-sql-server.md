---
title: Disponibilizar os bancos de dados SQL para os usuários de pilha do Azure | Microsoft Docs
description: Tutorial para instalar o provedor de recursos do SQL Server e criar oferece que permitem que os usuários do Azure pilha criar bancos de dados SQL.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/05/2018
ms.author: jeffgilb
ms.reviewer: ''
ms.custom: mvc
ms.openlocfilehash: b9ba2bb89bb0d7e16a28a165cf14530a7a10f71b
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/08/2018
ms.locfileid: "35234743"
---
# <a name="tutorial-make-sql-databases-available-to-your-azure-stack-users"></a>Tutorial: disponibilizar bancos de dados SQL para os usuários de pilha do Azure

Como um administrador de nuvem de pilha do Azure, você pode criar ofertas que permitem aos usuários (locatários) criarem bancos de dados SQL que podem ser usadas com seus aplicativos de nuvem nativo, sites e cargas de trabalho. Fornecendo esses bancos de dados personalizados, sob demanda, com base em nuvem para seus usuários, você pode salvá-los, tempo e recursos. Para configurar isso, você irá:

> [!div class="checklist"]
> * Implantar o provedor de recursos do SQL Server
> * Criar uma oferta
> * Testar a oferta

## <a name="deploy-the-sql-server-resource-provider"></a>Implantar o provedor de recursos do SQL Server

O processo de implantação é descrito detalhadamente o [bancos de dados SQL de uso no artigo de pilha do Azure](azure-stack-sql-resource-provider-deploy.md)e consiste das seguintes etapas principais:

1. [Implantar o provedor de recursos do SQL](azure-stack-sql-resource-provider-deploy.md).
2. [Verifique se a implantação](azure-stack-sql-resource-provider-deploy.md#verify-the-deployment-using-the-azure-stack-portal).
3. Fornecem a capacidade conectando-se ao servidor de hospedagem SQL. Para obter mais informações, consulte [adicionar servidores de hospedagem](azure-stack-sql-resource-provider-hosting-servers.md)

## <a name="create-an-offer"></a>Criar uma oferta

1.  [Definir uma cota de](azure-stack-setting-quotas.md) e nomeie-o *SQLServerQuota*. Selecione **Microsoft.SQLAdapter** para o **Namespace** campo.
2.  [Criar um plano de](azure-stack-create-plan.md). Nomeie- *TestSQLServerPlan*, selecione o **Microsoft.SQLAdapter** serviço, e **SQLServerQuota** cota.

    > [!NOTE]
    > Para permitir que os usuários criem outros aplicativos, outros serviços podem ser necessárias no plano. Por exemplo, funções do Azure requer o **Microsoft** serviço no plano, enquanto o Wordpress requer **Microsoft.MySQLAdapter**.

3.  [Criar uma oferta](azure-stack-create-offer.md), nomeie-o **TestSQLServerOffer** e selecione o **TestSQLServerPlan** plano.

## <a name="test-the-offer"></a>Testar a oferta

Agora que você implantou o provedor de recursos do SQL Server e criar uma oferta, você pode entrar como um usuário, assinar a oferta e criar um banco de dados.

### <a name="subscribe-to-the-offer"></a>Assinar a oferta

1. Entre portal do Azure pilha (https://portal.local.azurestack.external) como um locatário.
2. Selecione **obter uma assinatura** e, em seguida, digite **TestSQLServerSubscription** em **nome de exibição**.
3. Selecione **selecione uma oferta** > **TestSQLServerOffer** > **criar**.
4. Selecione **mais serviços** > **assinaturas** > **TestSQLServerSubscription** > **recursos provedores de**.
5. Selecione **registrar** lado a **Microsoft.SQLAdapter** provedor.

### <a name="create-a-sql-database"></a>Criar um banco de dados SQL

1. Selecione **+**  >  **dados + armazenamento** > **banco de dados SQL**.
2. Mantenha os valores padrão ou usar esses exemplos para os seguintes campos:
    - **Nome do banco de dados**: SQLdb
    - **Tamanho máximo em MB**: 100
    - **Assinatura**: TestSQLOffer
    - **Grupo de recursos**: RG do SQL
3. Selecione **configurações de logon**, insira as credenciais para o banco de dados e, em seguida, selecione **Okey**.
4. Selecione **SKU** > Selecionar a SKU de SQL que você criou para o servidor de hospedagem SQL > e, em seguida, selecione **Okey**.
5. Clique em **Criar**.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu como:

> [!div class="checklist"]
> * Implantar o provedor de recursos do SQL Server
> * Criar uma oferta
> * Testar a oferta

Avançar para o próximo tutorial para aprender como:

> [!div class="nextstepaction"]
> [Tornar disponível para os usuários da web, móveis e aplicativos de API]( azure-stack-tutorial-app-service.md)