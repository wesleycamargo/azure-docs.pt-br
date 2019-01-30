---
title: Disponibilizar os bancos de dados SQL para os usuários do Azure Stack | Microsoft Docs
description: Tutorial para instalar o provedor de recursos do SQL Server e criar oferece que permitem que os usuários do Azure Stack criar bancos de dados SQL.
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
ms.date: 11/05/2018
ms.author: jeffgilb
ms.reviewer: quying
ms.lastreviewed: 11/05/2018
ms.custom: mvc
ms.openlocfilehash: 983e8b279261d3ff8e5d24c8e3a6f61c5a787e5b
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/30/2019
ms.locfileid: "55240804"
---
# <a name="tutorial-make-sql-databases-available-to-your-azure-stack-users"></a>Tutorial: tornar bancos de dados SQL disponível para seus usuários do Azure Stack

Como um administrador de nuvem do Azure Stack, você pode criar ofertas que permitem aos usuários (locatários) criarem bancos de dados SQL que eles podem usar com seus aplicativos nativos de nuvem, sites e cargas de trabalho. Fornecendo esses bancos de dados personalizados, sob demanda, com base em nuvem para seus usuários, você pode salvá-los, tempo e recursos. Para configurar isso, você irá:

> [!div class="checklist"]
> * Implantar o provedor de recursos do SQL Server
> * Criar uma oferta
> * Testar a oferta

## <a name="deploy-the-sql-server-resource-provider"></a>Implantar o provedor de recursos do SQL Server

O processo de implantação é descrito detalhadamente na [bancos de dados SQL de uso no artigo do Azure Stack](azure-stack-sql-resource-provider-deploy.md)e consiste as seguintes etapas principais:

1. [Implantar o provedor de recursos do SQL](azure-stack-sql-resource-provider-deploy.md).
2. [Verificar a implantação](azure-stack-sql-resource-provider-deploy.md#verify-the-deployment-using-the-azure-stack-portal).
3. Forneça capacidade conectando-se ao servidor de hospedagem SQL. Para obter mais informações, consulte [adicionar servidores de hospedagem](azure-stack-sql-resource-provider-hosting-servers.md)

## <a name="create-an-offer"></a>Criar uma oferta

1.  [Definir uma cota](azure-stack-setting-quotas.md) e nomeie-o *SQLServerQuota*. Selecione **Microsoft.SQLAdapter** para o **Namespace** campo.
2.  [Criar um plano de](azure-stack-create-plan.md). Denomine *TestSQLServerPlan*, selecione o **Microsoft.SQLAdapter** serviço, e **SQLServerQuota** cota.

    > [!NOTE]
    > Para permitir que os usuários criem outros aplicativos, outros serviços podem ser necessárias no plano. Por exemplo, o Azure Functions requer o **Microsoft. Storage** serviço no plano, enquanto o Wordpress requer **Microsoft.MySQLAdapter**.

3.  [Criar uma oferta](azure-stack-create-offer.md), nomeie- **TestSQLServerOffer** e selecione o **TestSQLServerPlan** plano.

## <a name="test-the-offer"></a>Testar a oferta

Agora que você implantou o provedor de recursos do SQL Server e criado uma oferta, você pode entrar como um usuário, assine a oferta e criar um banco de dados.

### <a name="subscribe-to-the-offer"></a>Assine a oferta

1. Entrar no portal do Azure Stack (https://portal.local.azurestack.external) como um locatário.
2. Selecione **obter uma assinatura** e, em seguida, insira **TestSQLServerSubscription** sob **nome de exibição**.
3. Selecione **selecionar uma oferta** > **TestSQLServerOffer** > **criar**.
4. Selecione **todos os serviços** > **assinaturas** > **TestSQLServerSubscription** > **recursos provedores de**.
5. Selecione **registre** ao lado de **Microsoft.SQLAdapter** provedor.

### <a name="create-a-sql-database"></a>Criar um banco de dados SQL

1. Selecione **+**  >  **dados + armazenamento** > **banco de dados SQL**.
2. Mantenha os valores padrão ou usar esses exemplos para os seguintes campos:
    - **Nome do banco de dados**: SQLdb
    - **Tamanho máximo em MB**: 100
    - **Assinatura**: TestSQLOffer
    - **Grupo de Recursos**: SQL-RG
3. Selecione **configurações de logon**, insira as credenciais para o banco de dados e, em seguida, selecione **Okey**.
4. Selecione **SKU** > selecione a SKU do SQL que você criou para o servidor de hospedagem SQL > e, em seguida, selecione **Okey**.
5. Selecione **Criar**.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu como:

> [!div class="checklist"]
> * Implantar o provedor de recursos do SQL Server
> * Criar uma oferta
> * Testar a oferta

Avance para o próximo tutorial para aprender como:

> [!div class="nextstepaction"]
> [Tornar disponível para seus usuários da web, móveis e aplicativos de API]( azure-stack-tutorial-app-service.md)