---
title: Migrar tipos de preço dos pontos de extremidade a partir do Serviço de Fala Personalizado no Azure | Microsoft Docs
description: Saiba como migrar as implantações das camadas S0 e S1 para S2 dos pontos de extremidade do Serviço de Fala Personalizado nos Serviços Cognitivos.
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: article
ms.date: 07/05/2017
ms.author: panosper
ROBOTS: NOINDEX
ms.openlocfilehash: 44411e0c92f4e24e274761821dd99efbe60c5f5d
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46964966"
---
# <a name="migrate-deployments-to-the-new-pricing-model"></a>Migrar implantações para o novo modelo de preços
A partir de julho de 2017, o Serviço de Fala Personalizado oferece um [novo modelo de preços](https://azure.microsoft.com/pricing/details/cognitive-services/custom-speech-service/). O novo modelo é *mais fáceis de entender*, *mais simples de calcular os custos* e *mais flexível* em termos de dimensionamento. Para dimensionar, a Microsoft introduziu o conceito de uma unidade de escala. Cada unidade de escala pode lidar com cinco solicitações simultâneas. O dimensionamento de solicitações simultâneas no modelo antigo foi definido em 5 solicitações simultâneas para a camada S0 e 12 solicitações simultâneas para a camada S1. Abrimos esses limites para oferecer a você maior flexibilidade aos seus requisitos de caso de uso.

Se você executar uma camada S0 ou S1 antiga, é recomendável migrar suas implantações existentes para a nova camada S2. A nova camada S2 abrange os níveis S0 e S1. Você pode ver as opções disponíveis na figura a seguir:

![Página "Escolher seu tipo de preço"](../../../media/cognitive-services/custom-speech-service/custom-speech-pricing-tier.png)

A Microsoft lida com a migração de uma maneira semiautomática. Primeiro, você inicializa a migração selecionando o novo tipo de preço. Em seguida, migramos sua implantação automaticamente.

O mapeamento das camadas antigas para as unidades de escala é mostrado na tabela a seguir:

| Camada | Solicitações simultâneas (antigo modelo) | Migração | Solicitações simultâneas |
|----- | ----- | ---- | ---- |
| S0 |  5   |   => **S2** com 1 unidade de escala |   5 |
| S1 |  12  |   => **S2** com 3 unidades de escala |  15 |

Para migrar para a nova camada, faça o seguinte:

## <a name="step-1-check-your-existing-deployment"></a>Etapa 1: Verifique a implantação existente
Vá para o [portal do Serviço de Fala Personalizado](http://cris.ai) e verifique as implantações existentes. Em nosso exemplo, há duas implantações. Uma implantação é executada na camada S0 e a outra é executada na camada de S1. As implantações são mostradas na coluna **Opções de Implantação** da tabela a seguir:

![Página de Implantações](../../../media/cognitive-services/custom-speech-service/custom-speech-deployments.png)

## <a name="step-2-select-your-new-pricing-tier-in-the-azure-portal"></a>Etapa 2: Selecione o novo tipo de preço no portal do Azure
1. Abra uma nova guia do navegador e entre no [portal do Azure](http://ms.portal.azure.com/). 

2. No painel **Serviços Cognitivos**, na lista **Assinaturas**, selecione sua assinatura de fala personalizada. 

3. No painel da assinatura, selecione **Tipo de preço**.

    ![Link do “Tipo de preço”](../../../media/cognitive-services/custom-speech-service/custom-speech-update-tier.png)

4. Na página **Escolher seu tipo de preço**, selecione **S2 Standard**. Essa camada de preços é o novo tipo de preço simplificado e mais flexível.

5. Escolha o botão **Selecionar**.

    ![Página "Escolher seu tipo de preço"](../../../media/cognitive-services/custom-speech-service/custom-speech-update-pricing.png)

## <a name="step-3-check-the-migration-status-in-the-custom-speech-service-portal"></a>Etapa 3: Verifique o status de migração no portal do Serviço de Fala Personalizado
Volte para o portal do Serviço de Fala Personalizado e verifique suas implantações. (Se a janela do navegador já estiver aberta, atualize-a.) 

O status da implantação relacionada deve ter trocado para *Processando*. Você também pode validar a migração verificando a coluna **Opções de Implantação**. Nela, você pode encontrar informações sobre as unidades de escala e o registro em log. As unidades de escala devem refletir o tipo de preço anterior. O log deve ser ativado, como mostrado na tabela:

![Coluna das Opções de Implantação](../../../media/cognitive-services/custom-speech-service/custom-speech-deployments-new.png)


> [!NOTE]
> Se você tiver problemas durante a migração, entre em contato conosco.
>

## <a name="next-steps"></a>Próximas etapas
Para ver mais tutoriais, consulte:
* [Criar um modelo acústico personalizado](cognitive-services-custom-speech-create-acoustic-model.md)
* [Criar um modelo de linguagem personalizado](cognitive-services-custom-speech-create-language-model.md)
* [Criar um ponto de extremidade de conversão de fala em texto personalizado](cognitive-services-custom-speech-create-endpoint.md)
