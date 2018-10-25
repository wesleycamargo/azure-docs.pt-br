---
title: Migrar do serviço de fala personalizado para o serviço de fala
titlesuffix: Azure Cognitive Services
description: Saiba como a migração do serviço de fala personalizado para o serviço de fala.
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 10/01/2018
ms.author: panosper
ms.openlocfilehash: db09a85daff553dc911d039d37c826343e93d240
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/16/2018
ms.locfileid: "49338515"
---
# <a name="migrate-from-the-custom-speech-service-to-the-speech-service"></a>Migrar do serviço de fala personalizada para o serviço de fala

Use este artigo para migrar seus aplicativos do serviço de fala personalizada para o serviço de fala.

O Custom Speech Service agora faz parte do Serviço de Fala. Mude para o serviço de fala para se beneficiar das mais recentes atualizações de qualidade e recursos.
 
## <a name="migration-for-new-customers"></a>Migração para novos clientes

O modelo de precificação é mais simples, passando para um modelo de precificação baseado em hora para o Serviço de fala.   

1. Crie um recurso do Azure em cada região onde seu aplicativo está disponível. O nome do recurso do Azure é **Fala**. Você pode usar um único recurso do Azure para os seguintes serviços na mesma região, em vez de criar recursos separados:

    * Conversão de fala em texto
    * Fala em texto personalizada
    * Conversão de texto em fala
    * Tradução de fala

2. Faça o download do [Speech SDK](speech-sdk.md). 

3. Siga os guias de início rápido e as amostras de SDK para usar as APIs corretas. Se você usar as APIs REST, também precisará usar os terminais e as chaves de recursos corretos. 

4. Atualize o aplicativo cliente para usar o serviço de fala e as APIs. 

> [!NOTE]
> * LUIS - Se você habilitou fala em Compreensão de Linguagem (LUIS), um único recurso LUIS na mesma região funcionará para LUIS, assim como todos os serviços de fala. Veja [Reconhecer intenções da documentação da fala](how-to-recognize-intents-from-speech-csharp.md).
> * A conversão de texto em texto não faz parte do serviço de fala. Ele precisa de sua própria assinatura de recurso do Azure.
  


## <a name="migration-for-existing-customers"></a>Migração para os clientes existentes

Os clientes existentes precisam migrar suas chaves de recursos existentes para o serviço de fala no portal do serviço de fala. Use as seguintes etapas: 

> [!NOTE] 
> As chaves de recurso só podem ser migradas na mesma região. 

1. Entrar para o [cris.ai](http://www.cris.ai) portal e selecione a assinatura no menu superior direito. 

2. Selecione **migrar a assinatura selecionada**.

3. Digite a chave de assinatura na caixa de texto e selecione **Migrar**.

## <a name="next-steps"></a>Próximas etapas

* [experimente o serviço de fala gratuitamente](get-started.md)
* Saiba mais [conversão de fala em texto](./speech-to-text.md) conceitos

## <a name="see-also"></a>Consulte também

* [O que é o serviço de fala](overview.md)
* [Documentação do SDK e serviço de fala](speech-sdk.md#get-the-sdk)