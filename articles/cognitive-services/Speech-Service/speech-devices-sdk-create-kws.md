---
title: Criando uma palavra de ativação personalizada | Microsoft Docs
description: Criando uma palavra de ativação personalizada para o SDK de Dispositivos de Fala.
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
manager: noellelacharite
ms.service: cognitive-services
ms.technology: speech
ms.topic: article
ms.date: 04/28/2018
ms.author: v-jerkin
ms.openlocfilehash: 2575ed24bb931ca4da05dd6663b976406af590e6
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35364780"
---
# <a name="create-a-custom-wake-word-using-speech-service"></a>Criar uma palavra de ativação personalizada usando o serviço de Fala

Seu dispositivo está sempre escutando uma palavra de ativação (ou frase). Por exemplo, "Ei, Cortana" é uma palavra de ativação para o assistente Cortana. Quando o usuário diz que a palavra de ativação, o dispositivo começa a enviar todo o áudio subsequente para a nuvem até que o usuário para de falar. Personalizar sua palavra de ativação é uma maneira eficiente de diferenciar o seu dispositivo e fortalecer sua identidade visual.

Neste artigo, você aprenderá a criar uma palavra de ativação personalizada para o seu dispositivo.

## <a name="choosing-an-effective-wake-word"></a>Escolhendo uma palavra de ativação eficaz

Considere as seguintes diretrizes ao escolher uma palavra de ativação.

* Sua palavra de ativação deve ser uma palavra ou uma frase em inglês. Não deve levar mais dois segundos para ser dita.

* Palavras de 4 a 7 sílabas funcionam melhor. Por exemplo, "Hey, Computer" é uma boa palavra de ativação, enquanto apenas "Hey" é uma palavra ruim.

* Palavras de ativação devem seguir as regras de pronúncia comuns do inglês.

* Uma palavra exclusiva ou até mesmo inventada que siga as regras comuns de pronúncia do inglês pode reduzir falsos positivos. Por exemplo, "computerama" pode ser uma boa palavra de ativação.

* Não escolha uma palavra comum. Por exemplo, "comer" e "ir" são palavras que as pessoas costumam usar em conversas. Elas podem ser gatilhos falsos para o seu dispositivo.

* Evite usar uma palavra de ativação que possa ter pronúncias alternativas. Os usuários precisariam saber a pronúncia "certa" para fazer o dispositivo responder. Por exemplo, "509" poderia ser pronunciado como "five zero nine", "five oh nine" ou "five hundred and nine." "R.E.I." pode ser pronunciado como "R E I" ou "Ray". "Live" poderia ser pronunciado como [līv] ou [liv].

* Não use caracteres especiais, símbolos nem dígitos. Por exemplo, "Go#" e "20 + cats" não seriam boas palavras de ativação. No entanto, "go sharp" ou "twenty plus cats" funcionariam. Você ainda pode usar os símbolos em sua identidade visual e usar marketing e documentação para reforçar a pronúncia adequada.

> [!NOTE]
> Se você escolher uma palavra de marca como sua palavra de ativação, você deverá ser o proprietário da marca ou ter permissão do proprietário para usá-la. A Microsoft não é responsável por nenhum problema legal que possa surgir da sua escolha de palavra de ativação.

## <a name="creating-your-wake-word"></a>Criando sua palavras de ativação

Antes de usar uma palavra de ativação personalizada com seu dispositivo, você deverá criá-la usando o serviço Geração de Palavra de Ativação Personalizada da Microsoft. Depois de você fornecer uma palavra de ativação, o serviço produz um arquivo que você pode implantar no seu kit de desenvolvimento para habilitar a palavra de ativação no dispositivo.

1. Vá para o [portal do Serviço de Fala Personalizado](https://cris.ai/).

2. Crie uma nova conta com o endereço de email em que você recebeu o convite para o Azure Active Directory. 

    ![criar nova conta](media/speech-devices-sdk/wake-word-1.png)
 
3.  Depois de conectado, preencha o formulário e clique em **Iniciar a jornada.**

    ![logon realizado com sucesso](media/speech-devices-sdk/wake-word-3.png)
 
4. A página **Palavra de Ativação Personalizada** não está disponível para o público, portanto, não há nenhum link que o leve para ela. Em vez disso, clique em ou cole este link: https://cris.ai/customkws.

    ![página oculta](media/speech-devices-sdk/wake-word-4.png)
 
6. Digite a palavra de ativação de sua escolha e, em seguida, escolha **Enviar**.

    ![insira sua palavra de ativação](media/speech-devices-sdk/wake-word-5.png)
 
7. Pode demorar alguns minutos para os arquivos serem gerados. Você verá um círculo girando na guia do navegador. Após um momento, uma barra de informações será exibida solicitando que você baixe um arquivo `.zip`.

    ![recebendo um arquivo .zip](media/speech-devices-sdk/wake-word-6.png)

8. Salve o arquivo `.zip` no computador. Você precisará desse arquivo para implantar a palavra de ativação personalizada para o kit de desenvolvimento, seguindo as instruções em [Introdução ao SDK de Dispositivos de Fala](speech-devices-sdk-qsg.md).

9. Agora você pode **Sair.**

## <a name="next-steps"></a>Próximas etapas

Para começar, obtenha uma [Conta gratuita do Azure](https://azure.microsoft.com/free/) e inscreva-se para o SDK de Dispositivos de Fala.

> [!div class="nextstepaction"]
> [Inscreva-se para o SDK de Dispositivos de Fala](get-speech-devices-sdk.md)

