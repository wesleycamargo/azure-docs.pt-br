---
title: Disponibilizar os aplicativos web e API aos seus usuários do Azure Stack | Microsoft Docs
description: Tutorial para instalar o provedor de recursos do serviço de aplicativo e criar oferece que permitem que os usuários do Azure Stack para criar aplicativos de API e da web.
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
ms.date: 10/15/2018
ms.author: jeffgilb
ms.reviewer: ''
ms.custom: mvc
ms.openlocfilehash: 0a9b87ccfd49ba04a8dff8ef48bea023ff94b222
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/16/2018
ms.locfileid: "49340697"
---
# <a name="tutorial-make-web-and-api-apps-available-to-your-azure-stack-users"></a>Tutorial: tornar da web e aplicativos de API disponíveis para os usuários do Azure Stack

Como um administrador de nuvem do Azure Stack, você pode criar ofertas que permitem aos usuários (locatários) criarem aplicativos de API e da web e funções do Azure. Dando a seus usuários acesso a esses aplicativos sob demanda, com base em nuvem, você pode salvá-los, tempo e recursos.

Para configurar isso, você irá:

> [!div class="checklist"]
> * Implantar o provedor de recursos do serviço de aplicativo
> * Criar uma oferta
> * Testar a oferta

## <a name="deploy-the-app-service-resource-provider"></a>Implantar o provedor de recursos do serviço de aplicativo

1. [Preparar o host do Kit de desenvolvimento do Azure Stack](azure-stack-app-service-before-you-get-started.md). Isso inclui implantar o provedor de recursos do SQL Server, que é necessário para a criação de alguns aplicativos.
2. [Baixe os instalador e scripts auxiliares](azure-stack-app-service-deploy.md).
3. [Execute o script auxiliar para criar os certificados necessários](azure-stack-app-service-deploy.md).
4. [Instalar o provedor de recursos do serviço de aplicativo](azure-stack-app-service-deploy.md) (levará algumas horas para instalar e para todas as funções de trabalho sejam exibidos.)
5. [Validar a instalação](azure-stack-app-service-deploy.md#validate-the-app-service-on-azure-stack-installation).

## <a name="create-an-offer"></a>Criar uma oferta

Por exemplo, você pode criar uma oferta que permite aos usuários criar sistemas de gerenciamento de conteúdo da web DNN. Ele requer o serviço do SQL Server que você já habilitou, instalando o provedor de recursos do SQL Server.

1.  [Definir uma cota](azure-stack-setting-quotas.md) e nomeie-o *AppServiceQuota*. Selecione **Microsoft. Web** para o **Namespace** campo.
2.  [Criar um plano de](azure-stack-create-plan.md). Denomine *TestAppServicePlan*, selecione o **Microsoft. SQL** service e o **cota de serviço de aplicativo** cota.

    > [!NOTE]
    > Para permitir que os usuários criem outros aplicativos, outros serviços podem ser necessárias no plano. Por exemplo, o Azure Functions requer o **Microsoft. Storage** serviço no plano, enquanto o Wordpress requer **Microsoft.MySQL**.

3.  [Criar uma oferta](azure-stack-create-offer.md), nomeie- **TestAppServiceOffer** e selecione o **TestAppServicePlan** plano.

## <a name="test-the-offer"></a>Testar a oferta

Agora que você implantou o provedor de recursos do serviço de aplicativo e criou uma oferta, você pode entrar como um usuário, assine a oferta e criar um aplicativo.

Neste exemplo, vamos criar um sistema de gerenciamento de conteúdo de DNN Platform. Primeiro, crie um banco de dados SQL e, em seguida, o aplicativo web DNN.

### <a name="subscribe-to-the-offer"></a>Assine a oferta

1. Entrar no portal do Azure Stack (https://portal.local.azurestack.external) como um locatário.
2. Selecione **obter uma assinatura** >, insira **TestAppServiceSubscription** sob **nome de exibição** > **selecione uma oferta**  >  **TestAppServiceOffer** > **criar**.

### <a name="create-a-sql-database"></a>Criar um banco de dados SQL

1. Selecione **+**  >  **dados + armazenamento** > **banco de dados SQL**.
2. Mantenha os valores padrão, exceto para os seguintes campos:

    - **Nome do banco de dados**: DNNdb
    - **Tamanho máximo em MB**: 100
    - **Assinatura**: TestAppServiceOffer
    - **Grupo de recursos**: DNN-RG

3. Selecione **configurações de logon**, insira as credenciais para o banco de dados e, em seguida, selecione **Okey**. Você usará essas credenciais posteriormente no tutorial.
4. Sob **SKU** > selecione a SKU do SQL que você criou para o servidor de hospedagem SQL > e, em seguida, selecione **Okey**.
5. Selecione **Criar**.

### <a name="create-a-dnn-app"></a>Criar um aplicativo DNN

1. Selecione **+**  >  **ver tudo** > **visualização da plataforma DNN** > **criar** .
2. Insira *DNNapp* sob **nome do aplicativo** e selecione **TestAppServiceOffer** sob **assinatura**.
3. Selecione **definir configurações obrigatórias** > **criar novo** > Insira um **plano do serviço de aplicativo** nome.
4. Selecione **tipo de preço** > **F1 gratuito** > **selecionar** > **Okey**.
5. Selecione **banco de dados** e insira as credenciais para o banco de dados do SQL que você criou anteriormente.
6. Selecione **Criar**.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu como:

> [!div class="checklist"]
> * Implantar o provedor de recursos do serviço de aplicativo
> * Criar uma oferta
> * Testar a oferta

Avance para o próximo tutorial para aprender como:

> [!div class="nextstepaction"]
> [Implantar aplicativos no Azure e o Azure Stack](user/azure-stack-solution-pipeline.md)
