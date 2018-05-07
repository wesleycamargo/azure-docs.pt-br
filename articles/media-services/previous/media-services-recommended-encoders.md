---
title: "Saiba mais sobre os codificadores recomendados pelos Serviços de Mídia do Azure | Microsoft Docs"
description: "Saiba mais sobre os codificadores recomendados pelos serviços de mídia"
services: media-services
keywords: "codificação; codificadores; mídia"
author: dbgeorge
manager: jasonsue
ms.author: dwgeo
ms.date: 11/10/2017
ms.topic: article
ms.service: media-services
ms.openlocfilehash: d0c5536d2339470eac058250cc14e1f250b86d90
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/15/2017
---
# <a name="recommended-on-premises-encoders"></a>Codificadores locais recomendados
Ao fazer uma transmissão ao vivo com os Serviços de Mídia do Azure, você pode especificar como deseja que o canal receba o fluxo de entrada. Se optar por usar um codificador local com um canal de codificação ativa, o codificador deverá enviar por push um fluxo de taxa de bits única de alta qualidade como saída. Se optar por usar um codificador local com um canal de passagem, o codificador deverá enviar por push um fluxo de múltiplas taxas de bits como saída com todas as qualidades de saída desejada. Para obter mais informações, consulte [Transmissão ao vivo com codificadores locais](media-services-live-streaming-with-onprem-encoders.md).

Os Serviços de Mídia do Azure recomendam o uso de um dos seguintes codificadores dinâmicos que têm RTMP como saída:
- Adobe Flash Media Live Encoder 3.2
- Haivision Makito X HEVC
- Telestream Wirecast 8.1
- Teradek Slice 756
- TriCaster 8000
- Tricaster Mini HD-4

Os Serviços de Mídia do Azure recomendam o uso de um dos seguintes codificadores dinâmicos que têm o Smooth Streaming de múltiplas taxas de bits como saída:
- Ateme TITAN Live
- Cisco Digital Media Encoder 2200
- Elemental Live
- Envivio 4Caster C4 Gen III
- Imagine Communications Selenio MCP3
- Media Excel Hero Live

> [!NOTE]
> Um codificador dinâmico pode enviar um fluxo de taxa de bits única para um canal de passagem, mas essa configuração não é recomendada porque não permite um streaming de taxa de bits adaptável para o cliente.

## <a name="how-to-become-an-on-prem-encoder-partner"></a>Como se tornar um parceiro de codificador local
Como um parceiro de codificador local dos Serviços de Mídia do Azure, os Serviços de Mídia promovem seu produto recomendando seu codificador para clientes corporativos. Para se tornar um parceiro de codificador local, você deve verificar a compatibilidade de seu codificador local com os Serviços de Mídia. Para fazer isso, conclua as seguintes verificações:

Verificação de canal de passagem
1. Criar ou acessar sua conta dos Serviços de Mídia do Azure
2. Criar e iniciar um canal de **passagem**
3. Configure seu codificador para enviar por push um fluxo ao vivo de múltiplas taxas de bits.
4. Criar um evento ao vivo publicado
5. Executar o codificador dinâmico por aproximadamente 10 minutos
6. Parar o evento ao vivo
7. Registrar a ID do Ativo, a URL de streaming publicada para o arquivo morto dinâmico e as configurações e a versão usada do codificador dinâmico
8. Redefinir o estado do canal depois de criar cada amostra
9. Repita as etapas 3 a 8 para todas as configurações com suporte no codificador (com e sem sinalização de anúncio/legendas/diferentes velocidades de codificação)

Verificação de canal de codificação ativa
1. Criar ou acessar sua conta dos Serviços de Mídia do Azure
2. Criar e iniciar um canal de **codificação ativa**
3. Configure seu codificador para enviar por push um fluxo ao vivo de taxa de bits única.
4. Criar um evento ao vivo publicado
5. Executar o codificador dinâmico por aproximadamente 10 minutos
6. Parar o evento ao vivo
7. Registrar a ID do Ativo, a URL de streaming publicada para o arquivo morto dinâmico e as configurações e a versão usada do codificador dinâmico
8. Redefinir o estado do canal depois de criar cada amostra
9. Repita as etapas 3 a 8 para todas as configurações com suporte no codificador (com e sem sinalização de anúncio/legendas/várias velocidades de codificação)

Verificação de longevidade
1. Criar ou acessar sua conta dos Serviços de Mídia do Azure
2. Criar e iniciar um canal de **passagem**
3. Configure seu codificador para enviar por push um fluxo ao vivo de múltiplas taxas de bits.
4. Criar um evento ao vivo publicado
5. Executar o codificador dinâmico por uma semana ou mais
6. Parar o evento ao vivo
7. Registrar a ID do Ativo, a URL de streaming publicada para o arquivo morto dinâmico e as configurações e a versão usada do codificador dinâmico

Por fim, envie as configurações e os parâmetros de arquivo morto dinâmico registrados para os Serviços de Mídia enviando um email para amsstreaming@microsoft.com. Após o recebimento, os Serviços de Mídia executam testes de verificação nas amostras no codificador dinâmico. Entre em contato com os Serviços de Mídia em caso de dúvidas sobre esse processo.