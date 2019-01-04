---
title: Gerenciamento de recurso do Azure
titleSuffix: Language Understanding - Azure Cognitive Services
description: Neste artigo, você cria uma chave de ponto de extremidade limitada para sua conta do LUIS para fornecer tráfego ilimitado ao seu ponto de extremidade de acordo com um plano de pagamento.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 09/10/2018
ms.author: diberry
ms.openlocfilehash: 5d3e837cf644e0cb9b35a0cfc715a9dcace592e3
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/08/2018
ms.locfileid: "53082978"
---
# <a name="manage-azure-endpoint-subscription-keys"></a>Gerenciar suas chaves de assinatura do ponto de extremidade do Azure

Para testar e protótipo apenas, use a camada gratuita (F0). Para sistemas de produção, use uma camada [paga](https://aka.ms/luis-price-tier). 

> [!NOTE]
> Não use a [chave de criação](luis-concept-keys.md#authoring-key) para consultas de ponto de extremidade em produção.

<a name="create-luis-service"></a>
## <a name="create-luis-endpoint-key"></a>Criar chave de ponto de extremidade LUIS

1. Entrar no **[Microsoft Azure](https://ms.portal.azure.com/)**. 
2. Clique no sinal verde **+** de entrada no painel superior esquerdo e procure "LUIS" no marketplace, clique em **Reconhecimento Vocal** e siga as instruções em **criar experiência**  para criar uma conta de assinatura do LUIS. 

    ![Azure Search](./media/luis-azure-subscription/azure-search.png) 

3. Configure a assinatura com as configurações incluindo o nome da conta, os preços das camadas etc. 

    ![Opção de API do Azure](./media/luis-azure-subscription/azure-api-choice.png) 

4. Depois de criar o serviço do LUIS, você pode exibir as chaves de acesso geradas em **Gerenciamento de Recursos -> Chaves**.  

    ![Chaves do Azure](./media/luis-azure-subscription/azure-keys.png)

    > [!Note] 
    > Entre no site do [LUIS](luis-reference-regions.md) na sua região e [atribua a nova chave de ponto de extremidade do LUIS](luis-how-to-manage-keys.md#assign-endpoint-key). Você precisa do nome da assinatura do LUIS da etapa 3.

## <a name="change-luis-pricing-tier"></a>Alterar o tipo de preço do LUIS

1.  No [Azure](https://portal.azure.com), localize sua assinatura do LUIS. Clique na assinatura do LUIS.
    ![Localize sua assinatura do LUIS](./media/luis-usage-tiers/find.png)
2.  Clique em **Tipo de preço** para ver os tipos de preços disponíveis. 
    ![Exibir os tipos de preço](./media/luis-usage-tiers/subscription.png)
3.  Clique no tipo de preço e clique em **Selecionar** para salvar suas alterações. 
    ![Altere sua camada de pagamento do LUIS](./media/luis-usage-tiers/plans.png)
4.  Quando a alteração de preços é concluída, uma janela pop-up verifica a nova camada de preços. 
    ![Verifique sua camada de pagamento do LUIS](./media/luis-usage-tiers/updated.png)
5. Lembre-se de [atribuir essa chave do ponto de extremidade](luis-how-to-manage-keys.md#assign-endpoint-key) na página **Publicar** e usá-la em todas as consultas de ponto de extremidade. 

## <a name="exceed-pricing-tier-usage"></a>Exceder uso de camada de preços
Cada camada permite solicitações de ponto de extremidade para sua conta do LUIS em uma taxa específica. Se a taxa de solicitações for maior do que a taxa permitida de sua conta limitada por minuto ou por mês, as solicitações receberão um erro HTTP de "429: muitas solicitações".

Cada camada permite solicitações cumulativas por mês. Se o total de solicitações for maior do que a taxa permitida, as solicitações receberão um erro HTTP de "403: proibido".  

## <a name="viewing-summary-usage"></a>Exibindo uso de resumo
Você pode exibir informações de uso do LUIS no Azure. A página **Visão geral** mostra as informações de resumidas recentes, incluindo chamadas e erros. Se você fizer uma solicitação de ponto de extremidade do LUIS, acompanhe na **página Visão geral** e aguarde até cinco minutos para o uso ser exibido.

![Exibindo uso de resumo](./media/luis-usage-tiers/overview.png)

## <a name="customizing-usage-charts"></a>Como personalizar gráficos de uso
As métricas fornecem uma exibição mais detalhada dos dados.

![Métricas padrão](./media/luis-usage-tiers/metrics-default.png)

Você pode configurar os gráficos de métricas para o período de tempo e o tipo de métrica. 

![Métricas personalizadas](./media/luis-usage-tiers/metrics-custom.png)

## <a name="total-transactions-threshold-alert"></a>Alerta de limite total de transações
Se você quiser saber quando atingiu um determinado limite transação, por exemplo, 10.000 transações, crie um alerta. 

![Alertas padrão](./media/luis-usage-tiers/alert-default.png)

Adicionar um alerta de métrica para a métrica **total de chamadas** para um determinado período de tempo. Adicione endereços de email de todas as pessoas que devem receber o alerta. Adicione webhooks para todos os sistemas que devem receber o alerta. Você também poderá executar um aplicativo lógico quando o alerta for disparado. 

## <a name="next-steps"></a>Próximas etapas

Saiba como usar [versões](luis-how-to-manage-versions.md) para gerenciar as alterações ao seu aplicativo do LUIS.