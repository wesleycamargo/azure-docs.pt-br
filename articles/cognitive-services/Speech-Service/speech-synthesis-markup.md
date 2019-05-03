---
title: Speech Synthesis Markup Language (SSML) - serviços de fala
titleSuffix: Azure Cognitive Services
description: Usando o Speech Synthesis Markup Language para controlar pronúncia e prosódia em texto para fala.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/13/2018
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: 9871e0106ee6caf11c5a1e24459fbd2044f5f3d7
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65021449"
---
# <a name="speech-synthesis-markup-language-ssml"></a>Linguagem de marcação de síntese de fala (SSML)

A SSML (Linguagem de Marcação de Sintetização de Voz) é uma linguagem de marcação baseada em XML que fornece uma maneira de controlar a pronúncia e a *prosódia* da conversão de texto em fala. Prosody refere-se ao ritmo e tom de fala — suas músicas, se desejar. É possível especificar palavras foneticamente, fornecer dicas para interpretar números, inserir pausas, densidade de controle, volume e velocidade, e muito mais. Para obter mais informações, consulte [Speech Synthesis Markup Language (SSML) Version 1.0](https://www.w3.org/TR/2009/REC-speech-synthesis-20090303/) (SSML (Speech Synthesis Markup Language) Versão 1.0). 

Para obter uma lista completa de vozes (neurais e padrão), localidades e idiomas com suporte, consulte [suporte para idioma](language-support.md#text-to-speech).

As seções a seguir fornecem exemplos de tarefas comuns de sintetização de fala.

## <a name="adjust-speaking-style-for-neural-voices"></a>Ajustar o estilo de fala para vozes neurais

Você pode usar SSML para ajustar o estilo de fala ao usar uma das vozes neurais.

Por padrão, o serviço de texto em fala sintetiza texto em um estilo neutro. As vozes neurais estendem SSML com um `<mstts:express-as>` estilos do elemento que converte o texto em fala sintetizada ao falar diferentes. Atualmente, as marcas de estilo somente são compatíveis com essas vozes:

* `en-US-JessaNeural` 
* `zh-CN-XiaoxiaoNeural`.

Alterações de estilo de fala pode ser aplicada no nível de frase. Os estilos variam de acordo com a voz. Se não há suporte para um tipo de estilo, o serviço retornará a falas sintetizadas como o estilo de padrão neutro.

| Voz | Estilo | DESCRIÇÃO | 
|-----------|-----------------|----------|
| `en-US-JessaNeural` | type=`cheerful` | Expressa emoção que está feliz e positivo |
| | type=`empathy` | Expressa uma noção de se preocupar e Noções básicas sobre |
| `zh-CN-XiaoxiaoNeural` | type=`newscast` | Expressa um tom formal, semelhante a difusões de notícias |
| | type=`sentiment ` | Transmite uma mensagem tocando nas ou uma história |

```xml
<speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xmlns:mstts="https://www.w3.org/2001/mstts" xml:lang="en-US">
<voice name='en-US-JessaNeural'>
<mstts:express-as type="cheerful"> 
    That'd be just amazing! 
</mstts:express-as></voice></speak>
```

## <a name="add-a-break"></a>Adicionar uma quebra
```xml
<speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
<voice  name='en-US-Jessa24kRUS'>
    Welcome to Microsoft Cognitive Services <break time="100ms" /> Text-to-Speech API.
</voice> </speak>
```

## <a name="change-speaking-rate"></a>Alterar taxa de fala

Taxa de fala pode ser aplicada a vozes padrão no word ou no nível da frase. Ao passo que a taxa de fala só pode ser aplicada a vozes neurais no nível de frase.

```xml
<speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
<voice  name='en-US-Guy24kRUS'>
<prosody rate="+30.00%">
    Welcome to Microsoft Cognitive Services Text-to-Speech API.
</prosody></voice> </speak>
```

## <a name="pronunciation"></a>Pronúncia
```xml
<speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
<voice  name='en-US-Jessa24kRUS'>
    <phoneme alphabet="ipa" ph="t&#x259;mei&#x325;&#x27E;ou&#x325;"> tomato </phoneme>
</voice> </speak>
```

## <a name="change-volume"></a>Alterar volume

Alterações de volume podem ser aplicadas à vozes padrão no word ou no nível da frase. Enquanto as alterações de volume só podem ser aplicadas para vozes neurais no nível de frase.

```xml
<speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
<voice  name='en-US-Jessa24kRUS'>
<prosody volume="+20.00%">
    Welcome to Microsoft Cognitive Services Text-to-Speech API.
</prosody></voice> </speak>
```

## <a name="change-pitch"></a>Alterar densidade

Alterações de tom podem ser aplicadas à vozes padrão no word ou no nível da frase. Enquanto as alterações de tom só podem ser aplicadas para vozes neurais no nível de frase.

```xml
<speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
    <voice  name='en-US-Guy24kRUS'>
    Welcome to <prosody pitch="high">Microsoft Cognitive Services Text-to-Speech API.</prosody>
</voice> </speak>
```

## <a name="change-pitch-contour"></a>Alterar contorno de densidade

> [!IMPORTANT]
> Alterações de delimitação de densidade não são compatíveis com as vozes neurais.

```xml
<speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
<voice  name='en-US-Jessa24kRUS'>
<prosody contour="(80%,+20%) (90%,+30%)" >
    Good morning.
</prosody></voice> </speak>
```

## <a name="use-multiple-voices"></a>Usar várias vozes
```xml
<speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
<voice  name='en-US-Jessa24kRUS'>
    Good morning!
</voice>
<voice  name='en-US-Guy24kRUS'>
    Good morning to you too Jessa!
</voice> </speak>
```

## <a name="next-steps"></a>Próximas etapas

* [Suporte de linguagem: vozes, localidades, idiomas](language-support.md)
