---
title: Experimente o serviço de Fala gratuitamente
description: Descubra como você pode experimentar o serviço de Fala sem qualquer custo.
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 05/17/2018
ms.author: v-jerkin
ms.openlocfilehash: ff9a258d42f25ded82545909cdeade119548148e
ms.sourcegitcommit: 7ad9db3d5f5fd35cfaa9f0735e8c0187b9c32ab1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/27/2018
ms.locfileid: "39325200"
---
# <a name="try-the-speech-service-for-free"></a>Experimente o serviço de Fala gratuitamente

É fácil e barato começar a usar o serviço de Fala. Uma avaliação gratuita de 30 dias permite que você descubra o que o serviço pode fazer e decida se ele é o ideal para as necessidades do seu aplicativo.

Se você precisar de mais tempo, inscreva-se em uma conta do Microsoft Azure, ela vem com US$ 200 em crédito de serviço que você pode aplicar para em uma assinatura paga do serviço de Fala por até 30 dias.

Por fim, o serviço de Fala oferece uma opção gratuita, de baixo volume, adequada para o desenvolvimento de aplicativos. Você pode manter essa assinatura gratuita, mesmo depois que o crédito do serviço expirar.

## <a name="free-trial"></a>Avaliação gratuita

A avaliação gratuita de 30 dias proporciona acesso ao tipo de preço padrão S0 por tempo limitado. Para se inscrever em uma avaliação gratuita de 30 dias, faça o seguinte.

1. Vá até a página [Experimentar os Serviços Cognitivos](https://azure.microsoft.com/try/cognitive-services/).

1. Alterne para a guia Fala e clique no botão **Obter chave de API** ao lado de "Serviços de Fala".

   ![Guia Serviços de Fala](media/index/try-speech-api-free-trial1.png)<br>
   ![Chave de API](media/index/try-speech-api-free-trial2.png)

3. Aceite os termos e selecione sua localidade no menu suspenso.

   ![Concordar com os termos](media/index/try-speech-api-free-trial3.png)

4. Entre usando sua conta Microsoft, do Facebook, do LinkedIn ou do GitHub. Ou você pode se inscrever para uma conta Microsoft gratuita:

    * Acesse o [portal de contas da Microsoft](https://account.microsoft.com/account).
    * Clique em **Entrar com a conta da Microsoft**.

    ![Entrar](media/index/try-speech-api-free-trial4.png)

    * Quando receber a solicitação de entrada, clique em "Criar uma".

    ![Criar nova conta](media/index/try-speech-api-free-trial5.png)

    * Nas etapas a seguir, insira seu endereço de email ou número de telefone, atribua uma senha e siga as instruções para verificar sua nova conta Microsoft.

Após a entrada, começa a sua avaliação gratuita. A página da Web exibida lista todos os Serviços Cognitivos para os quais você tem uma assinatura de avaliação. Duas chaves de assinatura estão listadas ao lado de "Serviços de Fala". Você pode usar uma das chaves em seus aplicativos.

> [!NOTE]
> Todas as assinaturas de avaliação gratuita estão na região Oeste dos EUA. Use o ponto de extremidade correspondente à sua região ao fazer solicitações.

## <a name="new-azure-account"></a>Nova conta do Azure

As novas contas do Azure recebem um crédito de serviço de $200 que dura até 30 dias. Esse crédito pode ser usado para explorar ainda mais o serviço de Fala ou iniciar o desenvolvimento de aplicativos.

Para se inscrever para uma nova conta do Azure, execute estas etapas.

1. Acesse a [página de inscrição do Azure](https://azure.microsoft.com/free/ai/). 

1. Clique em **Iniciar gratuitamente**.

    ![Início gratuito](media/index/try-speech-api-new-azure1.png)

3. Entre com sua conta Microsoft. Se você não tiver uma:

    * Acesse o [portal de contas da Microsoft](https://account.microsoft.com/account).
    * Clique em **Entrar com a conta da Microsoft**.
    * Quando receber a solicitação de entrada, clique em "Criar uma".
    * Nas etapas a seguir, insira seu endereço de email ou número de telefone, atribua uma senha e siga as instruções para verificar sua nova conta Microsoft.

1. Insira o restante das informações solicitadas para se inscrever para uma conta. Especifique seu nome e país e forneça um endereço de email e número de telefone.

    ![Inserir informações](media/index/try-speech-api-new-azure2.png)

    Verifique sua identidade pelo telefone e fornecendo um número de cartão de crédito, depois, aceite o contrato de usuário do Azure. Seu cartão de crédito não será cobrado.

    ![Aceitar o contrato](media/index/try-speech-api-new-azure3.png)

Sua conta gratuita do Azure foi criada. Execute as etapas na próxima seção para iniciar uma assinatura do serviço de Fala.

## <a name="create-a-speech-resource-in-azure"></a>Criar um recurso de Fala no Azure

Para adicionar um recurso do serviço de Fala à sua conta do Azure, execute estas etapas.

1. Entre no [portal do Azure](https://ms.portal.azure.com/) usando sua conta Microsoft.

1. Clique em **Criar um recurso** (o ícone **+** verde) na parte superior esquerda do portal.

    ![Criar recurso](media/index/try-speech-api-create-speech1.png)

1. Na janela Nova, procure por Fala.

    ![Clique em Fala](media/index/try-speech-api-create-speech2.png)

1. Nos resultados da pesquisa, clique em "Fala (versão prévia)".

1. Clique no botão **Criar** na parte inferior do painel do serviço de Fala.

    ![Clicar em Criar](media/index/try-speech-api-create-speech3.png)

1. No painel Criar, insira:

    * Um nome para o novo recurso. O nome ajuda a distinguir entre várias assinaturas do mesmo serviço.
    * Escolha a assinatura do Azure a qual o novo recurso está associado para determinar como os valores serão cobrados.
    * Escolha a região onde o recurso será usado. No momento, o serviço de Fala está disponível nas regiões Ásia Oriental, Europa Setentrional e Oeste dos EUA.
    * Escolha o tipo de preços, F0 (assinatura gratuita limitada) ou S0 (assinatura padrão). Clique em **Exibir detalhes de preços completos** para saber mais sobre preço e cotas de uso de cada tipo.
    * Crie um novo grupo de recursos para esta assinatura de Fala ou a atribua a um grupo existente. Os grupos de recurso ajudam você a manter suas diversas assinaturas do Azure organizadas.
    * Para ter um acesso conveniente à sua assinatura futuramente, marque a caixa de seleção **Fixar no painel**.
    * Clicar em **Criar.**

    ![Clique em criar no painel](media/index/try-speech-api-create-speech4.png)

    Talvez demore um pouco para criar e implantar o novo recurso de Fala. O painel Início Rápido aparece com informações sobre o novo recurso.

    ![Painel Início Rápido](media/index/try-speech-api-create-speech5.png)

1. Clique no link **Chaves** na Etapa 1 no painel Início Rápido para exibir suas chaves de assinatura. Cada assinatura tem duas chaves; você pode usar uma delas em seu aplicativo. Clique no botão ao lado de cada chave para copiá-la na área de transferência e colá-la em seu código.

> [!NOTE]
> Você pode criar várias assinaturas de tipo standard em uma ou várias regiões. No entanto, só pode criar uma assinatura de tipo gratuito.

## <a name="next-steps"></a>Próximas etapas

Faça uma de nossas iniciações rápidas de 10 minutos ou confira nossas amostras de SDK.

> [!div class="nextstepaction"]
> [Início rápido: reconhecer fala em C#](quickstart-csharp-dotnet-windows.md)
> [Exemplos de SDK de fala](speech-sdk.md#get-the-samples)
