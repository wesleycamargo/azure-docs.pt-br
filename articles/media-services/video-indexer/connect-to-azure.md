---
title: Criar uma conta do Video Indexer no portal do Azure
titlesuffix: Azure Media Services
description: Este artigo mostra como criar uma conta do Video Indexer no portal do Azure.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.topic: article
ms.date: 11/19/2018
ms.author: juliako
ms.openlocfilehash: 49e05047d58cc5b6bb5e0099c24a131a26dc8af1
ms.sourcegitcommit: beb4fa5b36e1529408829603f3844e433bea46fe
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/22/2018
ms.locfileid: "52291659"
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

    No [portal do Azure](https://portal.azure.com/), vá para **Assinaturas** > [assinatura] > **ResourceProviders** > **Microsoft.EventGrid**. Se não estiver no estado "Registrado", clique em **registrar**. Demora alguns minutos para se registrar. 

    ![EventGrid](./media/create-account/event-grid.png)

## <a name="connect-to-azure"></a>Conecte-se ao Azure

1. Navegue até o site do [Video Indexer](https://www.videoindexer.ai/) e entre.

2. Clique no botão **Conectar ao Azure**:

    ![Conectar-se para o Azure](./media/create-account/connect-to-azure.png)

3. Quando a lista de assinaturas aparecer, selecione a assinatura que você deseja usar. 

    ![conectar-se o indexador de vídeo para o Azure](./media/create-account/connect-vi-to-azure-subscription.png)

4. Selecione uma região do Azure nos locais com suporte: oeste dos EUA 2, norte da Europa ou leste da Ásia.
5. Sob **conta de serviços de mídia do Azure**, escolha uma destas opções:

    * Para criar uma nova conta do Media Services, selecione **Criar novo grupo de recursos**. Forneça um nome para seu grupo de recursos.

        O Azure criará sua nova conta na sua assinatura, incluindo uma nova conta do Armazenamento do Azure. Sua nova conta de serviços de mídia tem uma configuração inicial padrão com um ponto de extremidade de fluxo contínuo e 10 unidades reservadas do S3.
    * Para usar uma conta de serviços de mídia existente, selecione **usar o recurso existente**. Na lista de contas, selecione sua conta.

        Sua conta de Serviços de Mídia deve ter a mesma região da sua conta do Indexador de Vídeo. Para minimizar a duração da indexação e a baixa taxa de transferência, ajuste o tipo e o número de Unidades reservadas para **10 S3 Reserved Units** em sua conta do Media Services.
    * Para configurar manualmente sua conexão, clique no **Alternar para a configuração manual**. 
    
        Você pode querer configurar manualmente sua conexão, se por algum motivo a opção automática não for concluída, ou se sua configuração e instalação for diferente dos casos comuns, ou se você quiser ter total visibilidade e controle sobre as configurações. 
        
        No **Conectar o Indexador de Vídeo a uma assinatura do Azure**, forneça as seguintes informações.

        |Configuração|DESCRIÇÃO|
        |---|---|
        |Região da conta do Indexador de Vídeo|O nome da região da conta do Video Indexer. Para um melhor desempenho e custos mais baixos, é altamente recomendável especificar o nome da região em que o recurso Serviços de Mídia do Azure e a conta do Armazenamento do Azure estão localizados. |
        |Locatário do Azure Active Directory (AAD)|O nome do locatário do Azure AD, por exemplo "contoso.onmicrosoft.com". As informações do locatário podem ser recuperadas no portal do Azure. Coloque o cursor sobre o nome do usuário conectado no canto superior direito.|
        |ID da assinatura|A assinatura do Azure na qual essa conexão deve ser criada. O ID da assinatura pode ser recuperado no portal do Azure. Clique em **Todos os serviços** no painel esquerdo e procure por "inscrições". Selecione **Assinaturas** e escolha o ID desejado na lista de suas assinaturas.|
        |Nome do grupo de recursos dos Serviços de Mídia do Azur.|O nome do grupo de recursos no qual a conta do Media Services existe.|
        |Nome do recurso de serviço de mídia|O nome do recurso dos Serviços de Mídia do Azure.|
        |ID do aplicativo|O ID do aplicativo do Azure AD com permissões para a conta de serviços de mídia especificada. Para obter mais informações, consulte [autenticação de entidade de serviço de uso](../../media-services/previous/media-services-portal-get-started-with-aad.md#service-principal-authentication).|
        |Chave do aplicativo|Para obter mais informações, consulte [autenticação de entidade de serviço de uso](../../media-services/previous/media-services-portal-get-started-with-aad.md#service-principal-authentication).|

6. Quando terminar, escolha **Connect**. Essa operação poderá demorar alguns minutos. 

    Depois que você estiver conectado ao Azure, sua nova conta do Video Indexer será exibida na lista de contas:

    ![nova conta](./media/create-account/new-account.png)

7. Navegue até sua nova conta: 

    ![Conta de indexador de vídeo](./media/create-account/vi-account.png)

## <a name="considerations"></a>Considerações

As seguintes considerações relacionadas aos Serviços de Mídia do Azure se aplicam:

* Se você se conectou a uma nova conta de Serviços de Mídia, verá uma nova conta do Grupo de Recursos, Serviços de Mídia e uma conta de Armazenamento em sua assinatura do Azure.
* Se você se conectou a uma nova conta de Serviços de Mídia, o Video Indexer definirá a mídia **Reserved Units** para 10 S3 units:

    ![Unidades reservadas para os serviços de mídia](./media/create-account/ams-reserved-units.png)

* Se você se conectou a uma conta de Serviços de Mídia existente, o Indexador de Vídeo não altera a configuração de mídia existente **Reserved Units**.

    Talvez seja necessário ajustar o tipo e o número de mídias **unidades reservadas**, de acordo com a sua carga planejada. Tenha em mente que, se sua carga for alta e você não tiver unidades ou velocidade suficiente, o processamento dos vídeos poderá resultar em falhas de tempo limite.

* Se você se conectou a uma nova conta de Serviços de Mídia, o Video Indexer inicia automaticamente nele o **Ponto de extremidade de streaming** padrão:

    ![Ponto de extremidade de streaming dos Serviços de Mídia](./media/create-account/ams-streaming-endpoint.png)

* Se você se conectou a uma conta de serviços de mídia existente, o Video Indexer não altera a configuração de Ponto de extremidade de streaming padrão. Se não houver **Streaming Endpoint** em execução, você não poderá assistir a vídeos dessa conta de Serviços de Mídia ou ao Video Indexer.

## <a name="next-steps"></a>Próximas etapas

Você pode interagir programaticamente com sua conta de avaliação e / ou com suas contas do Video Indexer conectadas ao azure seguindo as instruções em: [Use APIs](video-indexer-use-apis.md).

Você deve usar o mesmo usuário do Azure AD usado ao se conectar ao Azure.


