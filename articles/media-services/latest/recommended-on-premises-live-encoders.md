---
title: Saiba mais sobre codificadores locais de transmissão ao vivo recomendados pelos Serviços de Mídia -  Azure | Microsoft Docs
description: Saiba mais sobre codificadores locais de transmissão ao vivo recomendados pelos Serviços de Mídia
services: media-services
keywords: codificação; codificadores; mídia
author: johndeu
manager: johndeu
ms.author: johndeu
ms.date: 12/14/2018
ms.topic: article
ms.service: media-services
ms.openlocfilehash: d1110669bd0ca8c0ba0caf34ef41861c500bdd33
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/27/2018
ms.locfileid: "53790021"
---
# <a name="recommended-live-streaming-encoders"></a>Codificadores de transmissão ao vivo recomendados

Nos Serviços de Mídia, um [LiveEvent](https://docs.microsoft.com/rest/api/media/liveevents) (canal) representa um pipeline para processamento de conteúdo de transmissão ao vivo. Um LiveEvent recebe fluxos de entrada dinâmicos de duas maneiras:

* Um codificador dinâmico local envia um RTMP com múltiplas taxas de bits ou fluxo Smooth Streaming (MP4 fragmentado) para o LiveEvent que não está habilitado para executar a codificação ativa com os Serviços de Mídia. Os fluxos ingeridos passam pelo LiveEvents sem qualquer processamento adicional. Esse método é chamado **passagem**. Um codificador dinâmico pode enviar um fluxo de taxa de bits única para um canal de passagem, mas essa configuração não é recomendada porque não permite um streaming de taxa de bits adaptável para o cliente.

  > [!NOTE]
  > O uso de um método de passagem é a maneira mais econômica de realizar uma transmissão ao vivo.

* Um codificador dinâmico local envia um fluxo de taxa de bits única para o LiveEvent que está habilitado para executar a codificação ativa com os Serviços de Mídia em um dos seguintes formatos: RTMP ou Smooth Streaming (MP4 fragmentado). O LiveEvent, em seguida, executa a codificação ativa do fluxo de entrada de taxa de bits única para um fluxo de vídeo de múltiplas taxas de bits (adaptável).

Para obter informações detalhadas sobre a codificação ativa com os Serviços de Mídia, consulte [Transmissão ao vivo com Serviços de Mídia v3](live-streaming-overview.md).

## <a name="live-encoders-that-output-rtmp"></a>Codificadores dinâmicos com RTMP de saída

Os Serviços de Mídias recomendam usar um dos seguintes codificadores dinâmicos que têm RTMP como saída. Os esquemas de URL com suporte são `rtmp://` ou `rtmps://`.

> [!NOTE]
 > Ao transmitir via RTMP, verifique as configurações de firewall e/ou proxy para confirmar se as portas TCP de saída 1935 e 1936 estão abertas.<br/>
 Ao transmitir via RTMPS, verifique as configurações de firewall e/ou proxy para confirmar se as portas TCP de saída 2935 e 2936 estão abertas.

- Adobe Flash Media Live Encoder 3.2
- Haivision KB
- Haivision Makito X HEVC
- OBS Studio
- Alternador Studio (iOS)
- Telestream Wirecast 8.1+
- Telestream Wirecast S
- Teradek Slice 756
- TriCaster 8000
- Tricaster Mini HD-4
- VMIX
- xStream

## <a name="live-encoders-that-output-fragmented-mp4"></a>Codificadores dinâmicos com saída MP4 fragmentado

Os Serviços de Mídia recomendam usar um dos seguintes codificadores dinâmicos que têm Smooth Streaming com múltiplas taxas de bits (MP4 fragmentado) como saída. Os esquemas de URL com suporte são `rtmp://` ou `rtmps://`.

- Ateme TITAN Live
- Cisco Digital Media Encoder 2200
- Elemental Live
- Envivio 4Caster C4 Gen III
- Imagine Communications Selenio MCP3
- Mídia Hero ao vivo e Hero 4K (UHD/HEVC) do Excel

## <a name="how-to-become-an-on-premises-encoder-partner"></a>Como tornar-se um parceiro de codificador local

Como um parceiro de codificador local dos Serviços de Mídia do Azure, os Serviços de Mídia promovem seu produto, recomendando seu codificador aos clientes corporativos. Para tornar-se um parceiro de codificador local, é necessário verificar a compatibilidade do seu codificador local com os Serviços de Mídia. Para fazer isso, conclua as seguintes verificações:

### <a name="pass-through-liveevent-verification"></a>Verificação de LiveEvent de passagem

1. Crie ou visite a conta dos Serviços de Mídia do Azure.
2. Crie e inicie um LiveEvent de **passagem**.
3. Configure seu codificador para enviar por push um fluxo ao vivo de múltiplas taxas de bits.
4. Crie um evento ao vivo publicado.
5. Execute o codificador dinâmico por aproximadamente 10 minutos.
6. Pare o evento ao vivo.
7. Crie, inicie um ponto de extremidade de streaming, use um player como o [Player de Mídia do Azure](https://ampdemo.azureedge.net/azuremediaplayer.html) para inspecionar o ativo arquivado e garantir que a reprodução não tenha falhas visíveis em todos os níveis de qualidade (Ou, como alternativa, inspecione e valide por meio da URL de Visualização durante a sessão ao vivo antes da etapa 6).
8. Registre a ID de Ativo, a URL de transmissão publicada para o arquivo ao vivo e as configurações e a versão usadas no codificador dinâmico.
9. Reinicie o estado LiveEvent após criar cada exemplo.
10. Repita as etapas de 3 a 9 para todas as configurações com suporte do codificador (com e sem sinalização de anúncios/legendas/diferentes velocidades de codificação).

### <a name="live-encoding-liveevent-verification"></a>Verificação do LiveEvent de codificação ativa

1. Crie ou visite a conta dos Serviços de Mídia do Azure.
2. Crie e inicie um LiveEvent de **codificação ativa**.
3. Configure seu codificador para enviar por push um fluxo ao vivo de taxa de bits única.
4. Crie um evento ao vivo publicado.
5. Execute o codificador dinâmico por aproximadamente 10 minutos.
6. Pare o evento ao vivo.
7. Crie, inicie um ponto de extremidade de streaming, use um player como o [Player de Mídia do Azure](https://ampdemo.azureedge.net/azuremediaplayer.html) para inspecionar o ativo arquivado e garantir que a reprodução não tenha falhas visíveis em todos os níveis de qualidade (Ou, como alternativa, inspecione e valide por meio da URL de Visualização durante a sessão ao vivo antes da etapa 6).
8. Registre a ID de Ativo, a URL de transmissão publicada para o arquivo ao vivo e as configurações e a versão usadas no codificador dinâmico.
9. Reinicie o estado LiveEvent após criar cada exemplo.
10. Repita as etapas de 3 a 9 para todas as configurações com suporte do codificador (com e sem sinalização de anúncios/legendas/várias velocidades de codificação).

### <a name="longevity-verification"></a>Verificação de longevidade

1. Crie ou visite a conta dos Serviços de Mídia do Azure.
2. Crie e inicie um canal de **passagem**.
3. Configure seu codificador para enviar por push um fluxo ao vivo de múltiplas taxas de bits.
4. Crie um evento ao vivo publicado.
5. Execute o codificador dinâmico por uma semana ou mais.
6. Use um player como o [Player de Mídia do Azure](https://ampdemo.azureedge.net/azuremediaplayer.html) para assistir à transmissão ao vivo de tempos em tempos (ou ativo arquivado) para garantir que a reprodução não tenha falhas visíveis.
7. Pare o evento ao vivo.
8. Registre a ID de Ativo, a URL de transmissão publicada para o arquivo ao vivo e as configurações e a versão usadas no codificador dinâmico.

Por último, envie por email as configurações registradas e os parâmetros do arquivo ao vivo aos Serviços de Mídia do Azure em amsstreaming@microsoft.com como uma notificação de que todas as verificações de autoverificação foram aprovadas. Além disso, inclua suas informações de contato para quaisquer acompanhamentos. Contate a equipe dos Serviços de Mídia do Azure para tratar quaisquer questões relacionadas a esse processo.

## <a name="next-steps"></a>Próximas etapas

[Transmissão ao vivo com os Serviços de Mídia v3](live-streaming-overview.md)
