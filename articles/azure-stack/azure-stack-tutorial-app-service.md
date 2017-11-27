---
title: "Tornar disponível para os usuários do Azure pilha da web e aplicativos de API | Microsoft Docs"
description: "Tutorial para instalar o provedor de recursos do serviço de aplicativo e criar oferece que dar aos usuários de pilha do Azure, a capacidade de criar a web e aplicativos de API."
services: azure-stack
documentationcenter: 
author: ErikjeMS
manager: 
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 7/03/2017
ms.author: erikje
ms.custom: mvc
ms.openlocfilehash: 42d94eea8b9e4db611b821cd84e7d02f1d226293
ms.sourcegitcommit: 5bced5b36f6172a3c20dbfdf311b1ad38de6176a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/27/2017
---
# <a name="make-web-and-api-apps-available-to-your-azure-stack-users"></a>Tornar disponível para os usuários do Azure pilha da web e aplicativos de API

Como um administrador de nuvem de pilha do Azure, você pode criar ofertas que permitem aos usuários (locatários) criarem aplicativos web e funções do Azure e de API. Fornecendo acesso a esses aplicativos sob demanda, com base em nuvem para seus usuários, você pode salvá-los, tempo e recursos. Para configurar isso, você irá:

> [!div class="checklist"]
> * Implantar o provedor de recursos do serviço de aplicativo
> * Criar uma oferta
> * Testar a oferta

## <a name="deploy-the-app-service-resource-provider"></a>Implantar o provedor de recursos do serviço de aplicativo

1. [Preparar o host do Kit de desenvolvimento de pilha do Azure](azure-stack-app-service-before-you-get-started.md). Isso inclui a implantação do provedor de recursos do SQL Server, é necessário para a criação de alguns aplicativos.
2. [Baixe os scripts do instalador e auxiliar](azure-stack-app-service-deploy.md).
3. [Execute o script de auxiliar para criar certificados necessários](azure-stack-app-service-deploy.md).
4. [Instalar o provedor de recursos do serviço de aplicativo](azure-stack-app-service-deploy.md) (levará algumas horas para instalar e para todas as funções de trabalho aparecer).
5. [Validar a instalação](azure-stack-app-service-deploy.md#validate-the-app-service-on-azure-stack-installation).

## <a name="create-an-offer"></a>Criar uma oferta

Por exemplo, você pode criar uma oferta que permite aos usuários criar sistemas de gerenciamento de conteúdo da web DNN. Ele requer o serviço do SQL Server que você tiver habilitado ao instalar o provedor de recursos do SQL Server.

1.  [Definir uma cota de](azure-stack-setting-quotas.md) e nomeie-o *AppServiceQuota*. Selecione **Microsoft** para o **Namespace** campo.
2.  [Criar um plano de](azure-stack-create-plan.md). Nomeie- *TestAppServicePlan*, selecione o **Microsoft.SQL** serviço, e **cota de serviço de aplicativo** cota.

    > [!NOTE]
    > Para permitir que os usuários criem outros aplicativos, outros serviços podem ser necessárias no plano. Por exemplo, funções do Azure requer que o plano inclui o **Microsoft** de serviço, enquanto o Wordpress requer **Microsoft.MySQL**.
    > 
    >

3.  [Criar uma oferta](azure-stack-create-offer.md), nomeie-o **TestAppServiceOffer** e selecione o **TestAppServicePlan** plano.

## <a name="test-the-offer"></a>Testar a oferta

Agora que você implantou o provedor de recursos do serviço de aplicativo e criar uma oferta, você pode entrar como um usuário, assinar a oferta e criar um aplicativo. Neste exemplo, vamos criar um sistema de gerenciamento de conteúdo DNN plataforma. Você deve primeiro criar um banco de dados do SQL e, em seguida, o aplicativo web DNN.

### <a name="subscribe-to-the-offer"></a>Assinar a oferta
1. Entre no portal do Azure pilha (https://portal.local.azurestack.external) como um locatário.
2. Clique em **obter uma assinatura** > tipo **TestAppServiceSubscription** em **nome de exibição** > **selecione uma oferta**  >  **TestAppServiceOffer** > **criar**.

### <a name="create-a-sql-database"></a>Criar um banco de dados SQL

1. Clique em  **+**   >  **dados + armazenamento** > **banco de dados SQL**.
2. Mantenha os padrões para os campos, exceto da seguinte maneira:
    - **Nome do banco de dados**: DNNdb
    - **Tamanho máximo em MB**: 100
    - **Assinatura**: TestAppServiceOffer
    - **Grupo de recursos**: DNN RG
3. Clique em **configurações de logon**, insira as credenciais para o banco de dados e, em seguida, clique em **Okey**. Você usará essas credenciais nas próximas etapas.
4. Clique em **SKU** > Selecionar a SKU de SQL que você criou para o servidor de hospedagem SQL > **Okey**.
5. Clique em **Criar**.

### <a name="create-a-dnn-app"></a>Criar um aplicativo DNN    

1. Clique em  **+**   >  **ver todos os** > **visualização da plataforma DNN** > **criar**.
2. Tipo *DNNapp* em **nome do aplicativo** e selecione **TestAppServiceOffer** em **assinatura**.
3. Clique em **definir as configurações necessárias** > **criar novo** > tipo uma **plano de serviço de aplicativo** nome.
4. Clique em **preço** > **F1 livre** > **selecione** > **Okey**.
5. Clique em **banco de dados** e insira as informações do banco de dados do SQL que você criou anteriormente.
6. Clique em **Criar**.

Neste tutorial, você aprendeu como:

> [!div class="checklist"]
> * Implantar o provedor de recursos do serviço de aplicativo
> * Criar uma oferta
> * Testar a oferta

Avançar para o próximo tutorial para aprender como:

> [!div class="nextstepaction"]
> [Implantar aplicativos do Azure e o Azure pilha](user/azure-stack-solution-pipeline.md)
