---
title: Introdução ao Serviço de Fala Personalizado
titlesuffix: Azure Cognitive Services
description: Assinar o Serviço de Fala Personalizada e vincular as atividades de serviço a uma assinatura do Azure para treinar um modelo e fazer uma implantação.
services: cognitive-services
author: PanosPeriorellis
manager: cgronlun
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: conceptual
ms.date: 02/08/2017
ms.author: panosper
ROBOTS: NOINDEX
ms.openlocfilehash: ae72edd626bd91dea7cd2812a3ef821b905f59a4
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/26/2018
ms.locfileid: "47225235"
---
# <a name="get-started-with-custom-speech-service"></a>Introdução ao Serviço de Fala Personalizada

Explore os principais recursos do Serviço de Fala Personalizada e saiba como criar, implantar e usar modelos acústico e de linguagem para as necessidades do aplicativo. Documentação mais extensa e instruções passo a passo podem ser encontradas depois que você entrar no portal de Serviços de Fala Personalizada.

## <a name="samples"></a>Exemplos  
Há um bom exemplo que fornecemos para que você conhecer que pode ser encontrado [aqui](https://github.com/Microsoft/Cognitive-Custom-Speech-Service).

## <a name="prerequisites"></a>Pré-requisitos  

### <a name="subscribe-to-custom-speech-service-and-get-a-subscription-key"></a>Inscrever-se ao Serviço de Fala Personalizada e obter uma chave de assinatura
Antes de reproduzir com o exemplo acima, você deve inscrever-se ao Serviço de Fala Personalizada e obter uma assinatura chave, consulte [Assinaturas](https://portal.azure.com/#create/Microsoft.CognitiveServices/apitype/CustomSpeech) ou siga as explicações [aqui](CustomSpeech-How-to-Topics/cognitive-services-custom-speech-subscribe.md). Tanto a chave primária quanto a secundária podem ser usadas neste tutorial. Lembre-se de seguir as melhores práticas para manter o sigilo e a segurança de sua chave de API.

### <a name="get-the-client-library-and-example"></a>Obter a biblioteca de clientes e o exemplo
Você pode baixar uma biblioteca de cliente e exemplo por meio do [SDK](https://www.microsoft.com/cognitive-services/en-us/SDK-Sample?api=bing%20speech&category=sdk). O arquivo zip baixado precisa ser extraído para uma pasta de sua escolha. Muitos usuários escolhem a pasta do Visual Studio 2015.

## <a name="creating-a-custom-acoustic-model"></a>Criar um modelo acústico personalizado
Para personalizar um modelo acústico para um domínio específico, é necessário ter uma coleção de dados de fala. Essa coleção consiste em um conjunto de arquivos de áudio com dados de fala e um arquivo de texto com transcrições de cada arquivo de áudio. Os dados de áudio devem ser representativos do cenário no qual você gostaria de usar o reconhecedor.

Por exemplo: Se você deseja reconhecer melhor a fala em um ambiente de fábrica com ruídos, os arquivos de áudio devem conter pessoas que falem em uma fábrica com ruídos.
Se você estiver interessado em otimizar o desempenho de um único palestrante, por exemplo, será melhor transcrever os discursos de um palestrante e os arquivos de áudio devem consistir em muitos exemplos apenas dessa pessoa.

Você pode encontrar uma descrição detalhada sobre como criar um modelo acústico personalizado [aqui](CustomSpeech-How-to-Topics/cognitive-services-custom-speech-create-acoustic-model.md).

## <a name="creating-a-custom-language-model"></a>Criar um modelo de linguagem personalizado
O procedimento para criar um modelo de linguagem personalizado é semelhante à criação de um modelo acústico exceto que não há nenhum dado de áudio, somente texto. O texto deve consistir em muitos exemplos de consultas ou expressões que você espera que os usuários digam ou registrou os usuários falando (ou digitando) em seu aplicativo.

Você pode encontrar uma descrição detalhada sobre como criar um modelo de linguagem personalizado [aqui](CustomSpeech-How-to-Topics/cognitive-services-custom-speech-create-language-model.md).

## <a name="creating-a-custom-speech-to-text-endpoint"></a>Criar um ponto de extremidade personalizado de conversão de fala em texto
Ao criar modelos acústicos personalizados e/ou modelos de linguagem, eles podem ser implantados em um ponto de extremidade de conversão de fala em texto personalizado. Para criar um novo ponto de extremidade personalizado, clique em "Implantações" no menu "CRIS" no topo da página. Isso leva você até uma tabela chamada "Implantações" de pontos de extremidade personalizados atuais. Se você ainda não tiver criado nenhum ponto de extremidade, a tabela estará vazia. A localidade atual é refletida no título da tabela. Caso deseje criar uma implantação para uma linguagem diferente, clique em “Alterar Localidade”. Informações adicionais sobre linguagens com suporte podem ser encontradas na seção em Alterar Localidade.

Você pode encontrar uma descrição detalhada sobre como criar um ponto de extremidade de conversão de fala em texto personalizado [aqui](CustomSpeech-How-to-Topics/cognitive-services-custom-speech-create-endpoint.md).

## <a name="using-a-custom-speech-endpoint"></a>Usar um ponto de extremidade de fala personalizada
As solicitações podem ser enviadas para um ponto de extremidade de conversão de fala em texto CRIS de maneira muito semelhante ao ponto de extremidade de fala padrão dos Serviços Cognitivos do Azure. Observe que esses pontos de extremidade são funcionalmente idênticos aos pontos de extremidade padrão da API de Fala. Assim, a mesma funcionalidade disponível via a biblioteca de cliente ou a API REST para a API de Fala também está disponível para o ponto de extremidade personalizado.

Você pode encontrar uma descrição detalhada sobre como usar um ponto de extremidade de conversão de fala em texto personalizado [aqui](CustomSpeech-How-to-Topics/cognitive-services-custom-speech-use-endpoint.md).


Observe que os pontos de extremidade criados por meio de CRIS podem processar diferentes números de solicitações simultâneas dependendo da camada ao qual a assinatura está associada. Se houver mais reconhecimentos do que forem solicitados, elas retornarão o código de erro 429 (há muitas solicitações). Para obter mais informações, visite as [informações de preços](https://www.microsoft.com/cognitive-services/en-us/pricing). Além disso, há uma cota mensal de solicitações para a camada gratuita. Caso você acesse seu ponto de extremidade na camada gratuita acima da sua cota mensal o serviço retornará o código de erro 403 Proibido.

O serviço pressupõe que o áudio é transmitido em tempo real. Se for enviado mais rapidamente, a solicitação será considerada em execução até que sua duração em tempo real houver passado.

* [Visão geral](cognitive-services-custom-speech-home.md)
* [Perguntas frequentes](cognitive-services-custom-speech-faq.md)
* [Glossário](cognitive-services-custom-speech-glossary.md)
