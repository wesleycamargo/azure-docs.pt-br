---
title: Crie uma conta do Video Indexer conectada ao Azure | Microsoft Docs
description: Este artigo mostra como criar uma conta do Video Indexer conectada ao Azure.
services: cognitive services
documentationcenter: ''
author: juliako
manager: erikre
ms.service: cognitive-services
ms.topic: article
ms.date: 08/05/2018
ms.author: juliako
ms.openlocfilehash: 66ba79926a949371cb2280de408835862dd1a099
ms.sourcegitcommit: 8ebcecb837bbfb989728e4667d74e42f7a3a9352
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/21/2018
ms.locfileid: "41944427"
---
# <a name="create-a-video-indexer-account-connected-to-azure"></a>Crie uma conta do Video Indexer conectada ao Azure

Ao criar uma conta do Video Indexer, você pode escolher uma conta de avaliação gratuita (em que você obtém um determinado número de minutos de indexação gratuitos) ou uma opção paga (onde você não está limitado pela cota). Com o teste gratuito, o Video Indexer fornece até 600 minutos de indexação gratuita para usuários do site e até 2400 minutos de indexação gratuita para usuários da API. Com a opção paga, você cria uma conta do Video Indexer que está conectada à sua assinatura do Azure e a uma conta dos Serviços de Mídia do Azure. Você paga por minutos indexados, bem como os encargos relacionados à conta de mídia. 

Este artigo mostra como criar uma conta do Video Indexer vinculada a uma assinatura do Azure e uma conta dos Serviços de Mídia do Azure. 

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. 

    Se você ainda não tem uma assinatura do Azure, inscreva-se na [Avaliação Gratuita do Azure](https://azure.microsoft.com/free/).

* Um domínio do Azure Active Directory (AD). 

    Se você não tiver um domínio do Azure AD, crie esse domínio com sua assinatura do Azure. Para obter mais informações, consulte [Gerenciando nomes de domínio personalizados no Active Directory do Azure](../../active-directory/users-groups-roles/domains-manage.md)

* Um usuário e um membro em seu domínio do AD do Azure. Você usará esse membro ao conectar sua conta do Video Indexer ao Azure.

    Este usuário deve atender a estes critérios:

    * Seja um usuário do Azure AD com uma conta de trabalho ou escola, não uma conta pessoal, como outlook.com, live.com ou hotmail.com.
        
        ![todos os usuários do AAD](./media/create-account/all-aad-users.png)

    *  Seja um membro em sua assinatura do Azure com uma função de Proprietário ou com as funções de Administrador de Acesso e Usuário. Um usuário pode ser adicionado duas vezes, com 2 funções. Uma vez com o Contributor e uma vez com o User Access Administrator.

        ![Controle de acesso](./media/create-account/access-control-iam.png)

* Registre o provedor de recursos do EventGrid usando o portal do Azure.

    No portal do Azure, vá para **Assinaturas**> [subscription]> **ResourceProviders** > **Microsoft.EventGrid**. Se não estiver no estado "Registrado", clique em **registrar**. Demora alguns minutos para se registrar. 

    ![EventGrid](./media/create-account/event-grid.png)

## <a name="connect-to-azure"></a>Conecte-se ao Azure

1. Entre com esse usuário e clique no botão **Conectar ao Azure**:

    ![Conectar-se para o Azure](./media/create-account/connect-to-azure.png)

2. Quando a lista de assinaturas aparecer, selecione a assinatura que você deseja usar. 

    ![conectar-se o indexador de vídeo para o Azure](./media/create-account/connect-vi-to-azure-subscription.png)

3. Selecione uma região do Azure nos locais com suporte: oeste dos EUA 2, norte da Europa ou leste da Ásia.
4. Sob **conta de serviços de mídia do Azure**, escolha uma destas opções:

    * Para criar uma nova conta do Media Services, selecione **Criar novo grupo de recursos**. Forneça um nome para seu grupo de recursos.

        O Azure criará sua nova conta na sua assinatura, incluindo uma nova conta do Armazenamento do Azure. Sua nova conta de serviços de mídia tem uma configuração inicial padrão com um ponto de extremidade de fluxo contínuo e 10 unidades reservadas do S3.
    * Para usar uma conta de serviços de mídia existente, selecione **usar o recurso existente**. Na lista de contas, selecione sua conta.

        Sua conta de Serviços de Mídia deve ter a mesma região da sua conta do Indexador de Vídeo. Para minimizar a duração da indexação e a baixa taxa de transferência, ajuste o tipo e o número de Unidades reservadas para **10 S3 Reserved Units** em sua conta do Media Services.
    * Para configurar manualmente sua conexão, clique no link **Alternar para configuração manual** e forneça as informações necessárias:

    ![conectar-se o indexador de vídeo para o Azure](./media/create-account/connect-vi-to-azure-subscription-2.png)

5. Quando terminar, escolha **Connect**. Essa operação poderá demorar alguns minutos. 

    Depois que você estiver conectado ao Azure, sua nova conta do Video Indexer será exibida na lista de contas:

    ![nova conta](./media/create-account/new-account.png)

6. Navegue até sua nova conta: 

    ![Conta de indexador de vídeo](./media/create-account/vi-account.png)

## <a name="considerations"></a>Considerações

As seguintes considerações relacionadas aos Serviços de Mídia do Azure se aplicam:

* Se você se conectou a uma nova conta de Serviços de Mídia, verá uma nova conta do Grupo de Recursos, Serviços de Mídia e uma conta de Armazenamento em sua assinatura do Azure.
* Se você se conectou a uma nova conta de Serviços de Mídia, o Video Indexer definirá a mídia **Reserved Units** para 10 S3 units:

    ![Unidades reservadas para os serviços de mídia](./media/create-account/ams-reserved-units.png)

* Se você se conectou a uma conta de Serviços de Mídia existente, o Indexador de Vídeo não altera a configuração de mídia existente **Reserved Units**.

    Talvez seja necessário ajustar o tipo e o número de mídias **unidades reservadas**, de acordo com a sua carga planejada. Tenha em mente que, se sua carga for alta e você não tiver unidades ou velocidade suficiente, o processamento dos vídeos poderá resultar em falhas de tempo limite.

* Se você se conectou a uma nova conta de Serviços de Mídia, o Indexador de Vídeo inicia automaticamente um **Ponto de Extremidade de Transmissão**:

    ![Ponto de extremidade de streaming dos Serviços de Mídia](./media/create-account/ams-streaming-endpoint.png)

* Se você se conectou a uma conta de serviços de mídia existente, o Video Indexer não altera a configuração de pontos de extremidade de streaming. Se não houver **Streaming Endpoint** em execução, você não poderá assistir a vídeos dessa conta de Serviços de Mídia ou ao Video Indexer.

## <a name="use-video-indexer-apis-v2"></a>Use o indexador de vídeo APIs v2

Você pode interagir programaticamente com sua conta de avaliação e / ou com suas contas do Video Indexer conectadas ao azure seguindo as instruções em: [Use APIs](video-indexer-use-apis.md).

Você deve usar o mesmo usuário do Azure AD usado ao se conectar ao Azure.

## <a name="next-steps"></a>Próximas etapas

[Examinar os detalhes da saída JSON](video-indexer-output-json-v2.md).

