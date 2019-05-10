---
title: Introdução a conversão de fala personalizado – serviços de fala
titlesuffix: Azure Cognitive Services
description: Conversão de fala personalizada é um conjunto de ferramentas online que permitem que você avalie e aumentar a precisão da conversão de fala em texto da Microsoft para seus aplicativos, ferramentas e produtos. Para começar a usar são necessários um punhado de arquivos de áudio de teste. Siga os links abaixo para começar a criar uma experiência personalizada de fala em texto.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: erhopf
ms.openlocfilehash: ab33feb1ffdbced193afaba8f52719b3c215652f
ms.sourcegitcommit: 8fc5f676285020379304e3869f01de0653e39466
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/09/2019
ms.locfileid: "65511095"
---
# <a name="what-is-custom-speech"></a>O que é a conversão de fala personalizado?

[Conversão de fala personalizada](https://aka.ms/custom-speech) é um conjunto de ferramentas online que permitem que você avalie e aumentar a precisão da conversão de fala em texto da Microsoft para seus aplicativos, ferramentas e produtos. Para começar a usar são necessários um punhado de arquivos de áudio de teste. Siga os links abaixo para começar a criar uma experiência personalizada de fala em texto.

## <a name="whats-in-custom-speech"></a>O que é fala personalizado?

Antes de fazer qualquer coisa com a conversão de fala personalizado, será necessário uma conta do Azure e uma assinatura dos serviços de fala. Depois que você tem uma conta, você pode preparar seus dados, treinar e testar seus modelos, inspecione a qualidade do reconhecimento, avaliar a precisão e, por fim, implantar e usar o modelo de fala em texto personalizado.

O diagrama destaca as partes que compõem o portal de fala personalizado. Use os links abaixo para saber mais sobre cada etapa.

![Realça os diferentes componentes que compõem o portal de fala personalizado.](./media/custom-speech/custom-speech-overview.png)

1. [Inscrever-se e crie um projeto](#set-up-your-azure-account) - criar uma conta do Azure e assinar os serviços de fala. Esta assinatura unificada fornece acesso a fala em texto, texto em fala, tradução de fala e o portal de fala personalizado. Em seguida, usando sua assinatura dos serviços de fala, crie seu primeiro projeto de fala personalizado.

2. [Carregar dados de teste](how-to-custom-speech-test-data.md) -carregar dados de teste (arquivos de áudio), para avaliar fala em texto da Microsoft oferta para seus aplicativos, ferramentas e produtos.

3. [Inspecione a qualidade do reconhecimento](how-to-custom-speech-inspect-data.md) -usar o portal de fala personalizado para reproduzir áudio carregado e inspecionar a qualidade do reconhecimento de fala de seus dados de teste. Para medidas quantitativas, consulte [inspecionar dados](how-to-custom-speech-inspect-data.md).

4. [Avaliar a precisão](how-to-custom-speech-evaluate-data.md) -avaliar a precisão do modelo de fala em texto. O portal de fala personalizado fornecerá uma *taxa de erros do Word*, que pode ser usado para determinar se é necessário treinamento adicional. Se você estiver satisfeito com a precisão, você pode usar as APIs de serviço de fala diretamente. Se você quiser aumentar a precisão por uma média relativa de 5 a 20%, use o **treinamento** guia no portal para carregar dados de treinamento adicionais, como transcrições rotulada como humanos e de texto relacionado.

5. [Treinar o modelo](how-to-custom-speech-train-model.md) - melhorar a precisão do seu modelo de fala em texto, fornecendo Transcrições gravadas (10-1.000 horas) e relacionadas a texto (< 200 MB), juntamente com seu áudio dados de teste. Esses dados ajudam a treinar o modelo de fala em texto. Após o treinamento, teste novamente, e se você estiver satisfeito com o resultado, você pode implantar seu modelo.

6. [Implantar o modelo](how-to-custom-speech-deploy-model.md) – criar um ponto de extremidade personalizado para seu modelo de fala em texto e usá-lo em seus aplicativos, ferramentas ou produtos.

## <a name="set-up-your-azure-account"></a>Configurar sua conta do Azure

Uma assinatura de serviços de fala é necessária antes que você pode usar o portal de fala personalizado para criar um modelo personalizado. Siga estas instruções para criar uma assinatura de serviços de fala padrão: [Criar uma assinatura de fala](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started#new-azure-account).

> [!NOTE]
> Não se esqueça de criar assinaturas de (S0) padrão, não há suporte para assinaturas de avaliação gratuita (F0).

Depois de criar uma conta do Azure e uma assinatura dos serviços de fala, você precisará entrar no portal de fala personalizado e conecte-se a sua assinatura.

1. Obtenha sua chave de assinatura de serviços de fala do portal do Azure.
2. Entrar para o [portal de fala personalizado](https://aka.ms/custom-speech).
3. Selecione a assinatura que você precisa para trabalhar em e criar um projeto de fala.
4. Se você quiser modificar sua assinatura, use o **engrenagem** ícone localizado no painel de navegação superior.

## <a name="how-to-create-a-project"></a>Como criar um projeto

Como dados, modelos, testes e pontos de extremidade são organizados em de conteúdo **projetos** no portal de fala personalizado. Cada projeto é específico para um domínio e o idioma/país. Por exemplo, você pode criar um projeto para call centers de clientes que usam o inglês dos Estados Unidos.

Para criar seu primeiro projeto, selecione a **fala de fala para texto/personalizada**, em seguida, clique em **novo projeto**. Siga as instruções fornecidas pelo Assistente para criar seu projeto. Depois de criar um projeto, você deverá ver quatro guias: **Dados**, **testando**, **treinamento**, e **implantação**. Use os links fornecidos na [próximas etapas](#next-steps) para aprender a usar cada guia.

## <a name="next-steps"></a>Próximas etapas

* [Preparar e testar seus dados](how-to-custom-speech-test-data.md)
* [Inspecionar seus dados](how-to-custom-speech-inspect-data.md)
* [Avaliar seus dados](how-to-custom-speech-evaluate-data.md)
* [Treinar seu modelo](how-to-custom-speech-train-model.md)
* [Implantar seu modelo](how-to-custom-speech-deploy-model.md)
