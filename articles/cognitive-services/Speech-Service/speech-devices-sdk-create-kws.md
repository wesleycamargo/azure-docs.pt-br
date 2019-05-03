---
title: Criar uma palavra de ativação personalizada – Serviços de Fala
titleSuffix: Azure Cognitive Services
description: Seu dispositivo está sempre escutando uma palavra de ativação (ou frase). Quando o usuário diz que a palavra de ativação, o dispositivo envia todo o áudio subsequente para a nuvem até que o usuário pare de falar. Personalizar sua palavra de ativação é uma maneira eficiente de diferenciar o seu dispositivo e fortalecer sua identidade visual.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: 2280af4bf37fdb3cd12482da855f979a9180f0ec
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65020539"
---
# <a name="create-a-custom-wake-word-by-using-the-speech-service"></a>Criar uma palavra de ativação personalizada usando o serviço de Fala

Seu dispositivo está sempre escutando uma palavra de ativação (ou frase). Por exemplo, "Ei, Cortana" é uma palavra de ativação para o assistente Cortana. Quando o usuário diz que a palavra de ativação, o dispositivo envia todo o áudio subsequente para a nuvem até que o usuário pare de falar. Personalizar sua palavra de ativação é uma maneira eficiente de diferenciar o seu dispositivo e fortalecer sua identidade visual.

Neste artigo, você aprenderá a criar uma palavra de ativação personalizada para o seu dispositivo.

## <a name="choose-an-effective-wake-word"></a>Escolher uma palavra de ativação eficaz

Considere as seguintes diretrizes ao escolher uma palavra de ativação:

* Sua palavra de ativação deve ser uma palavra ou uma frase em inglês. Não deve levar mais dois segundos para ser dita.

* Palavras com quatro a sete sílabas funcionam melhor. Por exemplo, "Hey, Computer" ("Ei Computador") é uma boa palavra de ativação. Apenas "Hey" ("Ei") seria uma ruim.

* Palavras de ativação devem seguir as regras de pronúncia comuns do inglês.

* Uma palavra única ou até mesmo inventada que siga as regras comuns de pronúncia do português pode reduzir falsos positivos. Por exemplo, “computerama” pode ser uma boa palavra de ativação.

* Não escolha uma palavra comum. Por exemplo, "comer" e "ir" são palavras que as pessoas costumam usar em conversas. Elas podem ser gatilhos falsos para o seu dispositivo.

* Evite usar uma palavra de ativação que possa ter pronúncias alternativas. Os usuários precisariam saber a pronúncia "certa" para fazer o dispositivo responder. Por exemplo, “509” poderia ser pronunciado como "five zero nine" ("cinco zero nove") ou "five hundred and nine" ("quinhentos e nove"). "R.E.I." pode ser pronunciado como “r-e-i” ou “ray”. "Live" pode ser pronunciado "/līv/" ou "/liv/".

* Não use caracteres especiais, símbolos nem dígitos. Por exemplo, "Go#" e "20 + cats" não seriam boas palavras de ativação. No entanto, "go sharp" ("fique esperto") ou "twenty plus cats" ("mais de vinte gatos") pode funcionar. Você ainda pode usar os símbolos na sua identidade visual, bem como marketing e documentação para reforçar a pronúncia adequada.

> [!NOTE]
> Se você escolher uma palavra de marca registrada como sua palavra de ativação, será preciso ser o proprietário dessa marca ou ter permissão do proprietário para usá-la. A Microsoft não é responsável por nenhum problema legal que possa surgir da sua escolha de palavra de ativação.

## <a name="create-your-wake-word"></a>Criar sua palavra de ativação

Antes de usar uma palavra de ativação personalizado com seu dispositivo, você precisará criar uma palavra de ativação com o serviço Microsoft personalizado Wake Word geração. Depois de fornecer uma palavra de ativação, produz o serviço um arquivo que você implanta seu kit de desenvolvimento para habilitar a palavra de ativação em seu dispositivo.

1. Vá para o [Portal de serviços de fala personalizado](https://aka.ms/sdsdk-speechportal) e **entrar** ou se você não tiver uma assinatura de fala escolher [ **criar uma assinatura**](https://go.microsoft.com/fwlink/?linkid=2086754)

    ![O Portal de serviços de fala personalizado](media/speech-devices-sdk/wake-word-4.png)

1. No [personalizado Wake Word](https://aka.ms/sdsdk-wakewordportal) página, digite a palavra de ativação de sua escolha e clique em **adicionar wake word**. Temos algumas [diretrizes](#choose-an-effective-wake-word) para ajudar a escolher uma palavra-chave em vigor. Atualmente, só há suporte para idioma en-US.

    ![Insira sua palavra de ativação](media/speech-devices-sdk/wake-word-5.png)

1. Três pronúncia alternativa da sua palavra de ativação será criada. Você pode escolher todos os a pronúncia que desejar. Em seguida, selecione **enviar** para gerar a palavra de ativação. Se você quiser alterar a palavra de ativação, remover o já existente em primeiro lugar, ao passar o mouse na linha de pronúncia aparecerá no ícone Excluir.

    ![Examine o word wake](media/speech-devices-sdk/wake-word-6.png)

1. Pode levar até um minuto para o modelo a ser gerado. Você será solicitado a baixar o arquivo.

    ![Baixe o word wake](media/speech-devices-sdk/wake-word-7.png)

1. Salve o arquivo .zip no computador. Você precisará esse arquivo para implantar o word ativação personalizado para o kit de desenvolvimento.

## <a name="next-steps"></a>Próximas etapas

Testar sua palavra ativação personalizado com [início rápido do SDK de dispositivos de fala](https://aka.ms/sdsdk-quickstart).
