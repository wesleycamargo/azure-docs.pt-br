---
title: Migrar do Serviço de Fala Personalizado para os Serviços de Fala
titlesuffix: Azure Cognitive Services
description: O Custom Speech Service agora faz parte do Serviço de Fala. Mude para o serviço de fala para se beneficiar das mais recentes atualizações de qualidade e recursos.
services: cognitive-services
author: PanosPeriorellis
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: conceptual
ms.date: 10/01/2018
ms.author: panosper
ms.custom: seodec18
ms.openlocfilehash: b5879fabba70308c33101699dae4443e6b1b7070
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/08/2018
ms.locfileid: "53084525"
---
# <a name="migrate-from-the-custom-speech-service-to-the-speech-service"></a>Migrar do Serviço de Fala Personalizado para o Serviço de Fala

Use este artigo para migrar seus aplicativos do Serviço de Fala Personalizado para o Serviço de Fala.

O Custom Speech Service agora faz parte do Serviço de Fala. Mude para o serviço de fala para se beneficiar das mais recentes atualizações de qualidade e recursos.

## <a name="migration-for-new-customers"></a>Migração para novos clientes

O modelo de preços é mais simples, usando um modelo de preços baseado em hora para o Serviço de Fala.  

1. Crie um recurso do Azure em cada região onde seu aplicativo está disponível. O nome do recurso do Azure é **Fala**. Você pode usar um único recurso do Azure para os seguintes serviços na mesma região, em vez de criar recursos separados:

    * Conversão de fala em texto
    * Fala em texto personalizada
    * Conversão de texto em fala
    * Tradução de fala

2. Faça o download do [Speech SDK](speech-sdk.md).

3. Siga os guias de início rápido e as amostras de SDK para usar as APIs corretas. Se você usar as APIs REST, também precisará usar os terminais e as chaves de recursos corretos.

4. Atualize o aplicativo cliente para usar o Serviço de Fala e as APIs.

> [!NOTE]
> * Se você habilitar a fala no Reconhecimento vocal (LUIS), um único recurso LUIS na mesma região funcionará para LUIS, assim como todos os serviços de fala. Para mais informações, consulte [Reconhecer intenções da fala](how-to-recognize-intents-from-speech-csharp.md).
> * A conversão de texto em texto não faz parte do Serviço de Fala. Essa funcionalidade requer sua própria assinatura de recursos do Azure.
 


## <a name="migration-for-existing-customers"></a>Migração para os clientes existentes

Migre suas chaves de recursos existentes para o Serviço de Fala no portal do Serviço de Fala. Use as seguintes etapas:

> [!NOTE]
> As chaves de recurso só podem ser migradas na mesma região.

1. Entre no portal [cris.ai](http://www.cris.ai) e selecione a assinatura no menu superior direito.

2. Selecione **migrar a assinatura selecionada**.

3. Digite a chave de assinatura na caixa de texto e selecione **Migrar**.

## <a name="next-steps"></a>Próximas etapas

* [Experimentar o Serviço de Fala gratuitamente](get-started.md).
* Saiba mais sobre conceitos de [conversão de fala em texto](./speech-to-text.md).

## <a name="see-also"></a>Consulte também

* [O que é o Serviço de Fala](overview.md)
* [Documentação do SDK e do Serviço de Fala](speech-sdk.md#get-the-sdk)
