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
ms.date: 05/01/2018
ms.author: jeffgilb
ms.reviewer: ''
ms.custom: mvc
ms.openlocfilehash: 0e1eed2601946ddff6fa15f1a1f82398706c920d
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/03/2018
---
# <a name="make-sql-databases-available-to-your-azure-stack-users"></a>Disponibilizar os bancos de dados SQL para os usuários de pilha do Azure
Como um administrador de nuvem de pilha do Azure, você pode criar ofertas que permitem aos usuários (locatários) criarem bancos de dados SQL que podem ser usadas com seus aplicativos de nuvem nativo, sites e cargas de trabalho. Fornecendo esses bancos de dados personalizados, sob demanda, com base em nuvem para seus usuários, você pode salvá-los, tempo e recursos. Para configurar isso, você irá:

> [!div class="checklist"]
> * Implantar o provedor de recursos do SQL Server
> * Criar uma oferta
> * Testar a oferta

## <a name="deploy-the-sql-server-resource-provider"></a>Implantar o provedor de recursos do SQL Server

O processo de implantação é descrito detalhadamente o [bancos de dados SQL de uso no artigo de pilha do Azure](azure-stack-sql-resource-provider-deploy.md)e é composta das seguintes etapas principais:

1. [Implantar o provedor de recursos do SQL](azure-stack-sql-resource-provider-deploy.md).
2. [Verifique se a implantação](azure-stack-sql-resource-provider-deploy.md#verify-the-deployment-using-the-azure-stack-portal).
3. Fornecem a capacidade conectando-se ao servidor de hospedagem SQL.

## <a name="create-an-offer"></a>Criar uma oferta

1.  [Definir uma cota de](azure-stack-setting-quotas.md) e nomeie-o *SQLServerQuota*. Selecione **Microsoft.SQLAdapter** para o **Namespace** campo.
2.  [Criar um plano de](azure-stack-create-plan.md). Nomeie- *TestSQLServerPlan*, selecione o **Microsoft.SQLAdapter** serviço, e **SQLServerQuota** cota.

    > [!NOTE]
    > Para permitir que os usuários criem outros aplicativos, outros serviços podem ser necessárias no plano. Por exemplo, funções do Azure requer que o plano inclui o **Microsoft** de serviço, enquanto o Wordpress requer **Microsoft.MySQLAdapter**.
    > 
    >

3.  [Criar uma oferta](azure-stack-create-offer.md), nomeie-o **TestSQLServerOffer** e selecione o **TestSQLServerPlan** plano.

## <a name="test-the-offer"></a>Testar a oferta

Agora que você implantou o provedor de recursos do SQL Server e criar uma oferta, você pode entrar como um usuário, assinar a oferta e criar um banco de dados.

### <a name="subscribe-to-the-offer"></a>Assinar a oferta
1. Entre portal do Azure pilha (https://portal.local.azurestack.external) como um locatário.
2. Clique em **obter uma assinatura** e, em seguida, digite **TestSQLServerSubscription** em **nome de exibição**.
3. Clique em **selecione uma oferta** > **TestSQLServerOffer** > **criar**.
4. Clique em **mais serviços** > **assinaturas** > **TestSQLServerSubscription** > **recursos provedores de**.
5. Clique em **registrar** lado a **Microsoft.SQLAdapter** provedor.

### <a name="create-a-sql-database"></a>Criar um banco de dados SQL

1. Clique em **+**  >  **dados + armazenamento** > **banco de dados SQL**.
2. Mantenha os padrões para os campos, ou você pode usar esses exemplos:
    - **Nome do banco de dados**: SQLdb
    - **Tamanho máximo em MB**: 100
    - **Assinatura**: TestSQLOffer
    - **Grupo de recursos**: RG do SQL
3. Clique em **configurações de logon**, insira as credenciais para o banco de dados e, em seguida, clique em **Okey**.
4. Clique em **SKU** > Selecionar a SKU de SQL que você criou para o servidor de hospedagem SQL > **Okey**.
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

