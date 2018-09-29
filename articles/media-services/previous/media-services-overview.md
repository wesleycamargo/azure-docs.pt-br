---
title: Visão geral dos Serviços de Mídia do Azure | Microsoft Docs
description: Este tópico oferece uma visão geral dos Serviços de Mídia do Azure
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.assetid: 7a5e9723-c379-446b-b4d6-d0e41bd7d31f
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 09/24/2018
ms.author: juliako;anilmur
ms.openlocfilehash: 65f38ca477cf1a861c1285e6195598be705005d7
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/25/2018
ms.locfileid: "47163742"
---
# <a name="azure-media-services-overview"></a>Visão geral dos Serviços de Mídia do Azure 

> [!div class="op_single_selector" title1="Select the version of Media Services that you are using:"]
> * [Versão 2 – GA](media-services-overview.md)
> * [Versão 3 – Versão prévia](../latest/media-services-overview.md)

O AMS (Serviços de Mídia do Microsoft Azure) trata-se de uma plataforma extensível baseada em nuvem que permite aos desenvolvedores compilar aplicativos de gerenciamento e entrega de mídia escalonável. Os serviços de mídia se baseiam em APIs REST que permitem que você carregue com segurança, armazene, codifique e empacote o conteúdo de áudio ou vídeo para entrega de streaming sob demanda e ao vivo para vários clientes (por exemplo, TV, PCs e dispositivos móveis).

Você pode compilar fluxos de trabalho de ponta a ponta usando totalmente os serviços de mídia. Você também pode optar por usar componentes de terceiros para algumas partes do seu fluxo de trabalho. Por exemplo, codifique usando um codificador de terceiros. Em seguida, carregue, proteja, empacote e entregue usando os serviços de mídia. Você pode optar por transmitir seu conteúdo ao vivo ou fornecer conteúdo sob demanda. 

## <a name="prerequisites"></a>Pré-requisitos

Para começar a usar o Azure Media Services, você deve possuir o seguinte:

* Uma conta do Azure. Se não tiver uma conta, você poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Avaliação gratuita do Azure](https://azure.microsoft.com).
* Uma conta de Serviços de Mídia do Azure. Para obter mais informações, veja [Criar conta](media-services-portal-create-account.md).
* (Opcional) Configure o ambiente de desenvolvimento. Escolha .NET ou API REST para seu ambiente de desenvolvimento. Para obter mais informações, veja [Configurar ambiente](media-services-dotnet-how-to-use.md).

    Além disso, saiba como [conectar-se programaticamente à API do AMS](media-services-use-aad-auth-to-access-ams-api.md).
* Um ponto de extremidade de streaming padrão ou premium em estado iniciado.  Para obter mais informações, consulte [Gerenciando pontos de extremidade de streaming](media-services-portal-manage-streaming-endpoints.md)

## <a name="sdks-and-tools"></a>SDKs e ferramentas

Para compilar soluções de serviços de mídia, você pode usar:

* [API REST dos Serviços de Mídia](https://docs.microsoft.com/rest/api/media/operations/azure-media-services-rest-api-reference)
* Um dos SDKs de cliente disponíveis:
    * [SDK dos Serviços de Mídia do Azure para .NET](https://github.com/Azure/azure-sdk-for-media-services),
    * [SDK do Azure para Java](https://github.com/Azure/azure-sdk-for-java),
    * [SDK do PHP do Azure](https://github.com/Azure/azure-sdk-for-php),
    * [Serviços de Mídia do Azure para Node.js](https://github.com/michelle-becker/node-ams-sdk/blob/master/lib/request.js) (esta é uma versão de um SDK do Node.js que não foi criada pela Microsoft. Ele é mantido por uma comunidade e atualmente não tem cobertura de 100% das APIs do AMS).
* Ferramentas existentes:
    * [Portal do Azure](https://portal.azure.com/)
    * [Azure-Media-Services-Explorer](https://github.com/Azure/Azure-Media-Services-Explorer) ([AMSE] Gerenciador de Serviços de Mídia do Azure é um aplicativo Winforms/C# para Windows)

> [!NOTE]
> Para obter a versão mais recente do SDK do Java e começar a desenvolver com Java, confira [Introdução ao SDK de cliente Java para Serviços de Mídia](https://docs.microsoft.com/azure/media-services/media-services-java-how-to-use). <br/>
> Para baixar o SDK mais recente do PHP para os Serviços de Mídia, procure a versão 0.5.7 do pacote do Microsoft/WindowAzure no [Repositório do Packagist](https://packagist.org/packages/microsoft/windowsazure#v0.5.7).  

## <a name="code-samples"></a>Exemplos de código

Localize vários exemplos de código na galeria **Exemplos de Código do Azure**: [exemplos de código dos Serviços de Mídia do Azure](https://azure.microsoft.com/resources/samples/?service=media-services&sort=0).

## <a name="concepts"></a>Conceitos

Para conferir os conceitos dos Serviços de Mídia do Azure, confira [Conceitos](media-services-concepts.md).

## <a name="supported-scenarios-and-availability-of-media-services-across-data-centers"></a>Cenários com suporte e disponibilidade dos Serviços de Mídia em data centers

Para obter informações detalhadas, veja [Cenários do AMS e disponibilidade de recursos e serviços nos data centers](scenarios-and-availability.md).

## <a name="service-level-agreement-sla"></a>Contrato de nível de serviço (SLA)

* Para a Codificação dos Serviços de mídia, garantimos a disponibilidade de 99,9% de transações de API REST.
* Para streaming, atenderemos com êxito a solicitações com uma garantia de disponibilidade de 99,9% para conteúdos de mídia existentes quando um ponto de extremidade de streaming padrão ou premium for adquirido.
* Para canais ao vivo, garantimos que os canais em execução terão conectividade externa em, no mínimo, 99,9% do tempo.
* Para proteção de conteúdo, garantimos que atenderemos com êxito a solicitações de chave em, no mínimo, 99,9% do tempo.
* Para o indexador, podemos atenderemos com êxito às solicitações de tarefa do indexador processadas com uma unidade reservada para codificação em 99,9% do tempo.

Para obter mais informações, veja [SLA do Microsoft Azure](https://azure.microsoft.com/support/legal/sla/).

Para obter informações sobre a disponibilidade nos datacenters, consulte a seção [Disponibilidade](scenarios-and-availability.md#availability).

## <a name="support"></a>Suporte

[Suporte do Azure](https://azure.microsoft.com/support/options/) fornece opções de suporte do Azure, incluindo os Serviços de Mídia.

## <a name="provide-feedback"></a>Fornecer comentários

[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
