---
title: Migrar do Serviço de Fala Personalizado para os Serviços de Fala
titlesuffix: Azure Cognitive Services
description: O serviço de fala personalizado agora faz parte dos serviços de fala. Alternar para os serviços de fala para se beneficiar com as atualizações de qualidade e recursos mais recentes.
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/01/2018
ms.author: panosper
ms.custom: seodec18
ms.openlocfilehash: 501555c68b7b5922bbf5a74d2a7ed5e548b900c5
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/18/2019
ms.locfileid: "57833530"
---
# <a name="migrate-from-the-custom-speech-service-to-the-speech-service"></a>Migrar do Serviço de Fala Personalizado para o Serviço de Fala

Use este artigo para migrar seus aplicativos do Serviço de Fala Personalizado para o Serviço de Fala.

O Custom Speech Service agora faz parte do Serviço de Fala. Alternar para os serviços de fala para se beneficiar com as atualizações de qualidade e recursos mais recentes.

## <a name="migration-for-new-customers"></a>Migração para novos clientes

O modelo de preços é mais simples, usando um modelo de preços baseado em hora para o Serviço de Fala.  

1. Crie um recurso do Azure em cada região onde seu aplicativo está disponível. O nome do recurso do Azure é **Fala**. Você pode usar um único recurso do Azure para os seguintes serviços na mesma região, em vez de criar recursos separados:

    * Conversão de fala em texto
    * Fala em texto personalizada
    * Conversão de texto em fala
    * Tradução de fala

2. Faça o download do [Speech SDK](speech-sdk.md).

3. Siga os guias de início rápido e as amostras de SDK para usar as APIs corretas. Se você usar as APIs REST, também precisará usar os terminais e as chaves de recursos corretos.

4. Atualize o aplicativo de cliente para usar as APIs e serviços de fala.

## <a name="migration-for-existing-customers"></a>Migração para os clientes existentes

Migre suas chaves de recurso existente para os serviços de fala no portal de serviços de fala. Use as seguintes etapas:

> [!NOTE]
> As chaves de recurso só podem ser migradas na mesma região.

1. Entre no portal [cris.ai](https://www.cris.ai) e selecione a assinatura no menu superior direito.

2. Selecione **migrar a assinatura selecionada**.

3. Digite a chave de assinatura na caixa de texto e selecione **Migrar**.

## <a name="next-steps"></a>Próximas etapas

* [Experimente gratuitamente o serviços de fala](get-started.md).
* Saiba mais sobre conceitos de [conversão de fala em texto](./speech-to-text.md).

## <a name="see-also"></a>Consulte também

* [O que é o Serviço de Fala](overview.md)
* [Documentação do SDK de fala e serviços de fala](speech-sdk.md#get-the-sdk)
