---
title: Criar um ponto de extremidade de fala personalizado com o Serviço de Fala Personalizada no Azure | Microsoft Docs
description: Saiba como criar um ponto de extremidade de conversão de fala em texto personalizado com o Serviço de Fala Personalizada em Serviços Cognitivos.
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: article
ms.date: 07/08/2017
ms.author: panosper
ms.openlocfilehash: 7b7bbf89f04e4c8b12df46c2a7100a08001eb7d1
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/16/2018
ms.locfileid: "49340794"
---
# <a name="create-a-custom-speech-to-text-endpoint"></a>Criar um ponto de extremidade de conversão de fala em texto personalizado 

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-custom-speech-deprecation-note.md)]

Depois de criar modelos de acústicos personalizados ou modelos de linguagem, você pode implantá-los em um ponto de extremidade de conversão de fala em texto personalizado. 

## <a name="create-an-endpoint"></a>Criar um ponto de extremidade
Para criar um novo ponto de extremidade personalizado, selecione **Implantações** no menu **Fala Personalizado** na parte superior da página. Essa ação leva você para a página **Implantações**, que contém uma tabela de pontos de extremidade personalizados atuais. Se você ainda não tiver criado nenhum ponto de extremidade, a tabela está vazia. A localidade atual é refletida no título da tabela. 

Para criar uma implantação de um idioma diferente, selecione **Alterar localidade**. Para obter mais informações sobre idiomas com suporte, consulte [Localidades com suporte no Serviço de Fala Personalizada](cognitive-services-custom-speech-change-locale.md).

Para criar um novo ponto de extremidade, selecione **Criar novo**. No painel **Criar implantação**, preencha as caixas **Nome** e **Descrição** da sua implantação personalizada.

Na caixa de combinação **Assinatura**, escolha a assinatura que você quer usar. Se for uma assinatura S2, você pode selecionar unidades de escala e o log de conteúdo. Para obter mais informações sobre unidades de escala e registro em log, consulte [Medidores e cotas do Serviço de Fala Personalizado](../cognitive-services-custom-speech-meters.md).

A tabela a seguir mostra como unidades de escala são mapeados para solicitações simultâneas disponíveis:

| Unidades da escala | Número de solicitações simultâneas |
| ------ | ----- |
| 0 | 1 |
| 1 | 5 |
| 2 | 10 |
| 3 | 15 |
| n | 5 * n |

Você também pode selecionar se o log de conteúdo está ativado ou desativada. Ou seja, você está selecionando se o tráfego de ponto de extremidade é armazenado para uso interno da Microsoft. Se não estiver selecionado, o armazenamento de tráfego será suprimida. Suprimir resultados do log de conteúdo em custos adicionais. Consulte a [página de informações de preços](https://azure.microsoft.com/pricing/details/cognitive-services/custom-speech-service/) para obter detalhes.

> [!NOTE]
> Log de conteúdo é chamado "Não rastrear" na página de preços.
>


Além disso, a Microsoft fornece uma estimativa aproximada de custos para que você está ciente de que o impacto sobre os custos de unidades de escala e o log de conteúdo. Essa estimativa é uma estimativa aproximada e pode diferir os custos reais.

> [!NOTE]
> Essas configurações não estão disponíveis com as assinaturas do F0 (camada gratuita).
>

Na lista **Modelo acústico**, selecione o modelo acústico que desejar, e, na lista **modelo de linguagem**, selecione o modelo de linguagem que você deseja. As opções para modelos acústico e de linguagem sempre incluem os modelos base Microsoft. A seleção do modelo base limita as combinações. Não é possível misturar modelos de base de conversação com pesquisa e ditam modelos base.

![A página Criar implantação](../../../media/cognitive-services/custom-speech-service/custom-speech-deployment-create2.png)

> [!NOTE]
> Certifique-se de aceitar os termos de uso e informações sobre preços, marcando a caixa de seleção.
>

Depois de selecionar os modelos acústico e de linguagem, selecione **Criar**. Essa ação retorna para a página **Implantações**. A tabela agora inclui uma entrada que corresponde ao novo ponto de extremidade. O status do ponto de extremidade reflete o estado atual enquanto ele está sendo criado. Pode levar até 30 minutos para criar uma instância de um novo ponto de extremidade com seus modelos personalizados. Quando o status da implantação é *Concluído*, o ponto de extremidade está pronto para uso.

![A página de Implantações](../../../media/cognitive-services/custom-speech-service/custom-speech-deployment-ready.png)

Quando a implantação estiver pronta, o nome de implantação se torna um link. Selecionar o link exibe a página **Informações de implantação**, que exibe as URLs de seu ponto de extremidade personalizado para usar com uma solicitação HTTP ou a Biblioteca de Clientes do Serviços Cognitivos da Microsoft, que usa soquetes da web.

![A página Informações de implantação](../../../media/cognitive-services/custom-speech-service/custom-speech-deployment-info2.png)

## <a name="next-steps"></a>Próximas etapas

Para ver mais tutoriais, consulte:
* [Usar um ponto de extremidade personalizado de conversão de fala em texto](cognitive-services-custom-speech-use-endpoint.md)
* [Criar um modelo acústico personalizado](cognitive-services-custom-speech-create-acoustic-model.md)
* [Criar um modelo de linguagem personalizado](cognitive-services-custom-speech-create-language-model.md)
