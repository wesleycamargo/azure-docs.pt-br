---
title: Experimente o serviço de Fala gratuitamente
titleSuffix: Azure Cognitive Services
description: Descubra como você pode experimentar o serviço de Fala sem qualquer custo.
services: cognitive-services
author: erhopf
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: erhopf
ms.openlocfilehash: 4e29f2e77ad2b4e993fbe6a39b90325a0738f538
ms.sourcegitcommit: 62759a225d8fe1872b60ab0441d1c7ac809f9102
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/19/2018
ms.locfileid: "49471383"
---
# <a name="try-the-speech-service-for-free"></a>Experimente o serviço de Fala gratuitamente

É fácil e barato começar a usar o serviço de Fala. Uma avaliação gratuita de 30 dias permite que você descubra o que o serviço pode fazer e decida se ele é o ideal para as necessidades do seu aplicativo.

Se você precisar de mais tempo, inscreva-se em uma conta do Microsoft Azure, ela vem com US$ 200 em crédito de serviço que você pode aplicar para em uma assinatura paga do serviço de Fala por até 30 dias.

Por fim, o serviço de Fala oferece uma camada gratuita, de baixo volume e adequada para o desenvolvimento de aplicativos. Você pode manter essa assinatura gratuita, mesmo depois que o crédito do serviço expirar.

## <a name="free-trial"></a>Avaliação gratuita

A avaliação gratuita de 30 dias fornece acesso ao tipo de preço padrão por tempo limitado.

Para se inscrever em uma avaliação gratuita de 30 dias:

1. Acesse [Experimentar os Serviços Cognitivos](https://azure.microsoft.com/try/cognitive-services/).

1. Selecione a guia **APIs de Fala**.

   ![Guia Serviços de Fala](media/index/try-speech-api-free-trial1.png)
   
1. Em **Serviços de fala**, selecione o botão **Obter Chave de API**.

   ![Chave de API](media/index/try-speech-api-free-trial2.png)

1. Aceite os termos e selecione sua localidade no menu suspenso.

   ![Concordar com os termos](media/index/try-speech-api-free-trial3.png)

1. Entre usando sua conta Microsoft, do Facebook, do LinkedIn ou do GitHub.

    Inscreva-se em uma conta gratuita Microsoft no [portal de conta Microsoft](https://account.microsoft.com/account). Para começar, clique em **Entrar com a conta da Microsoft** e, quando solicitado a entrar, clique em **Criar uma.** Siga as etapas para criar e confirmar sua nova conta Microsoft.

Depois de entrar em Experimentar os Serviços Cognitivos, sua avaliação gratuita será iniciada. A página da Web exibida lista todos os Serviços Cognitivos do Azure para os quais você tem uma assinatura de avaliação. Duas chaves de assinatura estão listadas ao lado de **Serviços de Fala**. Use uma das chaves em seus aplicativos.

> [!NOTE]
> Todas as assinaturas de avaliação gratuita estão na região Oeste dos EUA. Ao fazer solicitações, use o ponto de extremidade `westus`.

## <a name="new-azure-account"></a>Nova conta do Azure

As novas contas do Azure recebem um crédito de serviço de US$ 200 que fica disponível por até 30 dias. Use esse crédito para explorar ainda mais o serviço de Fala ou para iniciar o desenvolvimento de aplicativos.

Para se inscrever em uma nova conta do Azure, acesse a [página de inscrição do Azure](https://azure.microsoft.com/free/ai/), clique em **Iniciar Gratuitamente** e crie uma conta do Azure usando sua conta Microsoft.

Inscreva-se em uma conta gratuita Microsoft no [portal de conta Microsoft](https://account.microsoft.com/account). Para começar, clique em **Entrar com a conta da Microsoft** e, quando solicitado a entrar, clique em **Criar uma.** Siga as etapas para criar e confirmar sua nova conta Microsoft.

Depois de criar sua conta do Azure, siga as etapas na próxima seção para iniciar uma assinatura do serviço de Fala.

## <a name="create-a-speech-resource-in-azure"></a>Criar um recurso de Fala no Azure

Para adicionar um recurso de serviço de Fala (camada gratuita ou paga) à sua conta do Azure:

1. Entre no [portal do Azure](https://portal.azure.com/) usando sua conta Microsoft.

1. Selecione **Criar um recurso** na parte superior esquerda do portal.

    ![Criar um recurso](media/index/try-speech-api-create-speech1.png)

1. Na janela **Nova**, pesquise **fala**.

1. Nos resultados da pesquisa, selecione **Fala**.

    ![Selecionar Fala](media/index/try-speech-api-create-speech2.png)

1. Em **Fala**, selecione o botão **Criar**.

    ![Selecionar o botão Criar](media/index/try-speech-api-create-speech3.png)

1. Em **Criar**, insira:

    * Um nome para o novo recurso. O nome ajuda a distinguir entre várias assinaturas do mesmo serviço.
    * Escolha a assinatura do Azure a qual o novo recurso está associado para determinar como os valores serão cobrados.
    * Escolha a região onde o recurso será usado. No momento, o serviço de Fala está disponível nas regiões Ásia Oriental, Europa Setentrional e Oeste dos EUA.
    * Escolha um tipo de preço gratuito ou pago. Clique em **Exibir detalhes completos de preço** para obter informações completas sobre preço e cotas de uso para cada tipo.
    * Crie um grupo de recursos para esta assinatura de Fala ou atribua a assinatura a um grupo de recursos existente. Os grupos de recurso ajudam você a manter suas diversas assinaturas do Azure organizadas.
    * Para ter acesso conveniente à sua assinatura futuramente, marque a caixa de seleção **Fixar no painel**.
    * Selecione **Criar.**

    ![Selecionar o botão Criar](media/index/try-speech-api-create-speech4.png)

    São necessários alguns instantes para criar e implantar o novo recurso de Fala. Selecione **Início Rápido** para ver informações sobre o novo recurso.

    ![Painel Início Rápido](media/index/try-speech-api-create-speech5.png)

1. Em **Início Rápido**, clique no link **Chaves** na etapa 1 para exibir suas chaves de assinatura. Cada assinatura tem duas chaves; você pode usar uma das chaves em seu aplicativo. Selecione o botão ao lado de cada chave para copiá-la para a área de transferência e colá-la no código.

> [!NOTE]
> Você pode criar um número ilimitado de assinaturas da camada Standard em uma ou várias regiões. No entanto, você só pode criar uma assinatura da camada gratuita. As implantações de modelo na camada gratuita que permanecerem inutilizadas por 7 dias serão desativadas automaticamente.

## <a name="switch-to-a-new-subscription"></a>Alternar para uma nova assinatura

Para alternar de uma assinatura para outra, por exemplo, quando a avaliação gratuita expirar ou quando você publicar seu aplicativo, substitua a região e a chave de assinatura no código pela região e pela chave de assinatura do novo recurso do Azure.

> [!NOTE]
> As chaves de avaliação gratuita são criadas na região Oeste dos EUA (`westus`). Uma assinatura criada por meio do painel do Azure pode estar em alguma outra região, se você assim escolher.

* Se o aplicativo usar um [SDK de Fala](speech-sdk.md), forneça o código da região, como `westus`, ao criar uma configuração de fala.
* Se o aplicativo usar uma das [APIs REST](rest-apis.md) do serviço de Fala, a região fará parte do URI do ponto de extremidade usado ao fazer solicitações.

As chaves criadas para uma região são válidas somente nessa região. A tentativa de usá-las com outras regiões resultará em erros de autenticação.

## <a name="next-steps"></a>Próximas etapas

Conclua um de nossos inícios rápidos de 10 minutos ou confira nossas amostras de SDK:

> [!div class="nextstepaction"]
> [Início Rápido: Reconhecer a fala em C#](quickstart-csharp-dotnet-windows.md)
> [Amostras de SDK de Fala](speech-sdk.md#get-the-samples)
