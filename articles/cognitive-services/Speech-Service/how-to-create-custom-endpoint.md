---
title: Criar um ponto de extremidade de fala personalizado com os serviços de fala no Azure | Microsoft Docs
description: Saiba como criar um ponto de extremidade de fala em texto personalizado usando os serviços de fala do Azure.
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.subservice: custom-speech
ms.topic: article
ms.date: 03/12/2018
ms.author: panosper
ms.openlocfilehash: 1f7a84d187ba6279caad4926d54bfc56254152af
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60539019"
---
# <a name="create-a-custom-speech-to-text-endpoint"></a>Criar um ponto de extremidade de conversão de fala em texto personalizado 

Depois de criar modelos de acústicos personalizados ou modelos de linguagem, você pode implantá-los em um ponto de extremidade de conversão de fala em texto personalizado.

## <a name="create-an-endpoint"></a>Criar um ponto de extremidade
Para criar um ponto de extremidade personalizado, selecione **Pontos de extremidade** no menu **Fala Personalizada** na parte superior da página. Essa ação leva você para a página **Pontos de extremidade**, que contém uma tabela de pontos de extremidade personalizados atuais. Se você ainda não tiver criado nenhum ponto de extremidade, a tabela está vazia. A localidade atual é refletida no título da tabela.

Para criar uma implantação de um idioma diferente, selecione **Alterar localidade**. Para obter mais informações sobre os idiomas com suporte.

Para criar um novo ponto de extremidade, selecione **Criar novo**. No painel **Criar ponto de extremidade**, preencha as informações nas caixas **Nome** e **Descrição** da sua implantação personalizada.

Na caixa de combinação **Assinatura**, escolha a assinatura que você quer usar. Se for uma assinatura S0 (pagante), você poderá selecionar solicitações simultâneas.

Você também pode selecionar se o log de conteúdo está ativado ou desativada. Ou seja, você está selecionando se o tráfego de ponto de extremidade é armazenado. Se não estiver selecionado, o armazenamento de tráfego será suprimida. Para todo o conteúdo registrado, você pode encontrar links de download na exibição Ponto de extremidade-> Detalhes

Na lista **Modelo acústico**, selecione o modelo acústico que desejar, e, na lista **modelo de linguagem**, selecione o modelo de linguagem que você deseja. As opções para modelos acústico e de linguagem sempre incluem os modelos base Microsoft. A seleção do modelo base limita as combinações. Não é possível misturar modelos de base de conversação com pesquisa e ditam modelos base.

> [!NOTE]
> Certifique-se de aceitar os termos de uso e informações sobre preços, marcando a caixa de seleção.
>

Depois de selecionar os modelos acústico e de linguagem, selecione **Criar**. Essa ação retorna para a página **Implantações**. A tabela agora inclui uma entrada que corresponde ao novo ponto de extremidade. O status do ponto de extremidade reflete o estado atual enquanto ele está sendo criado. Pode levar até 30 minutos para criar uma instância de um novo ponto de extremidade com seus modelos personalizados. Quando o status da implantação é *Concluído*, o ponto de extremidade está pronto para uso.

Quando a implantação estiver pronta, o nome de ponto de extremidade se tornará um link. Selecionar o link exibe a página **Informações de ponto de extremidade**, que exibe as URLs de seu ponto de extremidade personalizado para usar com uma solicitação HTTP ou a Biblioteca de Clientes de Fala dos Serviços Cognitivos da Microsoft, que usa soquetes da Web.

## <a name="next-steps"></a>Próximos passos

Para ver mais tutoriais, consulte:
- [Obter sua assinatura de avaliação de Serviços de Fala](https://azure.microsoft.com/try/cognitive-services/)
- [Crie modelos acústicos personalizados](how-to-customize-acoustic-models.md)
- [Crie modelos de linguagem personalizados](how-to-customize-language-model.md)
