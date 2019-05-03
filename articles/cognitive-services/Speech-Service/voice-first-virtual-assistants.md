---
title: Personalizado primeiro voz virtuais assistentes (visualização) - serviços de fala
titleSuffix: Azure Cognitive Services
description: Uma visão geral de recursos, funcionalidades e restrições para assistentes virtuais personalizados de voz, primeiro usando o canal de fala de linha direta no Bot Framework e o Cognitive Services fala Software Development Kit (SDK).
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: travisw
ms.custom: ''
ms.openlocfilehash: f40e53c67f099fea739e45f6bdc6963ee1e235cb
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65026099"
---
# <a name="about-custom-voice-first-virtual-assistants-preview"></a>Sobre a visualização de assistentes de virtual primeiro de voz personalizada

Assistentes de virtuais personalizados usando os serviços de fala do Azure capacitam os desenvolvedores criem interfaces de conversação natural, humana para seus aplicativos e experiências. Canal de fala de linha direta da estrutura Bot aprimora esses recursos, fornecendo um ponto de coordenada e orquestrada de entrada para um bot compatível que permite a voz em voz interação com baixa latência e alta confiabilidade. Esses bots pode usar o entendimento de linguagem (LUIS da Microsoft) para a interação do idioma natural. Fala de linha direta é acessada por dispositivos que usam a fala dos serviços Software Development Kit (SDK).

   ![Diagrama conceitual de fluxo de orquestração de serviço a linha direta fala](media/voice-first-virtual-assistants/overview.png "fluxo o canal de fala")

Fala de linha direta e sua funcionalidade associada para assistentes de virtuais primeiro de voz personalizadas são um suplemento ideal para o [Virtual solução Assistant](https://docs.microsoft.com/azure/bot-service/bot-builder-virtual-assistant-introduction) e [modelo empresarial](https://docs.microsoft.com/azure/bot-service/bot-builder-enterprise-template-overview). Embora fala de linha direta pode funcionar com qualquer bot compatível, estes recursos fornecem uma linha de base reutilizável para experiências de alta qualidade Conversacionais, bem como as habilidades de suporte comuns e modelos para começar rapidamente.

## <a name="core-features"></a>Principais recursos

| Category | Recursos |
|----------|----------|
|[Palavra de ativação personalizado](speech-devices-sdk-create-kws.md) | Você pode permitir que os usuários iniciar conversas com os bots usando uma palavra-chave personalizada, como "Ei Contoso". Essa tarefa é realizada com um mecanismo de word ativação personalizado no SDK do fala, que pode ser configurado com uma palavra personalizada wake [que você pode gerar aqui](speech-devices-sdk-create-kws.md). O canal de fala de linha direta inclui a verificação de palavra no lado do serviço de ativação que aumenta a precisão da ativação de word wake versus o dispositivo sozinho.
|[Conversão de fala em texto](speech-to-text.md) | O canal de fala de linha direta inclui transcrição em tempo real de áudio em texto reconhecido usando [fala em texto](speech-to-text.md) fala dos serviços do Azure. Esse texto está disponível para seu bot e seu aplicativo cliente conforme ele é transcrita.
|[Texto em fala](text-to-speech.md) | Respostas textuais do seu bot serão ser sintetizadas usando [fala](text-to-speech.md) fala dos serviços do Azure. Este síntese será, em seguida, disponibilizado para o aplicativo cliente como um fluxo de áudio. A Microsoft oferece a capacidade de criar suas próprias [voz de TTS Neural personalizado e de alta qualidade](https://aka.ms/customneuraltts) que fornece uma voz a sua marca.
|[Fala de linha direta](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech.md) | Como um canal dentro da estrutura de Bot, fala de linha direta permite que uma conexão simples e direta entre o aplicativo cliente, um bot compatível e os recursos dos serviços de fala do Azure. Para obter mais informações sobre como configurar seu bot para usar o canal de fala de linha direta, consulte [sua página na documentação do Bot Framework](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech.md).

## <a name="sample-code"></a>Exemplo de código

Código de exemplo para a criação de um Assistente de voz-first virtual está disponível no GitHub. Esses exemplos abrangem o aplicativo cliente para se conectar ao seu bot em várias linguagens de programação populares.

* [Exemplos do Assistente de voz-first virtual (SDK)](https://aka.ms/csspeech/samples)
* [Guia de início rápido: primeiro voz assistentes virtuais (C#)](quickstart-virtual-assistant-csharp-uwp.md)
* [Guia de início rápido: assistentes voz, primeiro virtuais (Java)](quickstart-virtual-assistant-java-jre.md)

## <a name="customization"></a>Personalização

Assistentes para voz, primeiro virtuais criados usando os serviços de fala do Azure podem usar a gama completa de opções de personalização disponíveis para [fala em texto](speech-to-text.md), [texto em fala](text-to-speech.md), e [palavra-chave personalizado seleção de](speech-devices-sdk-create-kws.md).

> [!NOTE]
> Opções de personalização variam por idioma/localidade (consulte [idiomas com suporte](supported-languages.md)).

## <a name="reference-docs"></a>Documentos de Referência

* [SDK da fala](speech-sdk-reference.md)
* [Serviço de Bot do Azure](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0)

## <a name="next-steps"></a>Próximas etapas

* [Obter gratuitamente uma chave de assinatura do Serviço de Fala](get-started.md)
* [Obtenha o SDK da fala](speech-sdk.md)
* [Criar e implantar um bot básico](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-basic-deploy?view=azure-bot-service-4.0)
* [Obter a solução de assistente Virtual e modelo empresarial](https://github.com/Microsoft/AI)
