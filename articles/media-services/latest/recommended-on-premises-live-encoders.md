---
title: Saiba mais sobre codificadores locais de transmissão ao vivo recomendados pelos Serviços de Mídia -  Azure | Microsoft Docs
description: Saiba mais sobre codificadores locais de transmissão ao vivo recomendados pelos Serviços de Mídia
services: media-services
keywords: codificação; codificadores; mídia
author: johndeu
manager: johndeu
ms.author: johndeu
ms.date: 01/17/2019
ms.topic: article
ms.service: media-services
ms.openlocfilehash: c3e42ba9fe84ded8c60fc71f19de785945852116
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/02/2019
ms.locfileid: "55656661"
---
# <a name="recommended-live-streaming-encoders"></a>Codificadores de transmissão ao vivo recomendados

Nos Serviços de Mídia, um [evento ao vivo](https://docs.microsoft.com/rest/api/media/liveevents) (canal) representa um pipeline para processamento de conteúdo de transmissão ao vivo. O evento ao vivo recebe fluxos de entrada dinâmicos em uma de duas maneiras:

* Um codificador dinâmico local envia um RTMP com múltiplas taxas de bits ou fluxo Smooth Streaming (MP4 fragmentado) para o evento ao vivo que não está habilitado para executar a codificação ativa com os Serviços de Mídia. Os fluxos ingeridos passam por eventos ao vivo sem nenhum processamento adicional. Esse método é chamado **passagem**. Um codificador dinâmico pode enviar um fluxo de taxa de bits única para um canal de passagem, mas essa configuração não é recomendada porque não permite um streaming de taxa de bits adaptável para o cliente.

  > [!NOTE]
  > O uso de um método de passagem é a maneira mais econômica de realizar uma transmissão ao vivo.

* Um codificador dinâmico local envia um fluxo de taxa de bits única para o evento ao vivo que está habilitado para executar a codificação ativa com os Serviços de Mídia em um dos seguintes formatos: RTMP ou Smooth Streaming (MP4 fragmentado). O evento ao vivo, em seguida, executa a codificação ativa do fluxo de entrada de taxa de bits única para um fluxo de vídeo de múltiplas taxas de bits (adaptável).

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

Os Serviços de Mídia recomendam usar um dos seguintes codificadores dinâmicos que têm Smooth Streaming com múltiplas taxas de bits (MP4 fragmentado) como saída. Os esquemas de URL com suporte são `http://` ou `https://`.

- Ateme TITAN Live
- Cisco Digital Media Encoder 2200
- Elemental Live
- Envivio 4Caster C4 Gen III
- Imagine Communications Selenio MCP3
- Mídia Hero ao vivo e Hero 4K (UHD/HEVC) do Excel

## <a name="configuring-on-premises-live-encoder-settings"></a>Definir as configurações do codificador dinâmico local

Para obter informações sobre quais configurações são válidas para o seu tipo de evento ao vivo, consulte [Comparação de tipos de evento ao vivo](live-event-types-comparison.md).

### <a name="playback-requirements"></a>Requisitos de reprodução

Tanto um fluxo de áudio quanto um de vídeo devem estar presentes para que seja possível reproduzir conteúdo. Não há suporte para a reprodução de fluxo somente de vídeo.

### <a name="configuration-tips"></a>Dicas de configuração

- Sempre que possível, use uma conexão de Internet com fio.
- Uma boa regra geral ao determinar os requisitos de largura de banda é dobrar as taxas de bits de transmissão. Embora isso não seja um requisito obrigatório, isso ajuda a reduzir o impacto do congestionamento da rede.
- Ao usar codificadores baseados em software, feche todos os programas desnecessários.
- Não altere a configuração do codificador após ele começar a enviar por push. Isso tem efeitos negativos sobre o evento e pode torná-lo instável. 
- Reserve bastante tempo para configurar seu evento. Para eventos em alta escala, é recomendado iniciar a configuração uma hora antes do evento.

## <a name="how-to-become-an-on-premises-encoder-partner"></a>Como tornar-se um parceiro de codificador local

Como um parceiro de codificador local dos Serviços de Mídia do Azure, os Serviços de Mídia promovem seu produto, recomendando seu codificador aos clientes corporativos. Para tornar-se um parceiro de codificador local, é necessário verificar a compatibilidade do seu codificador local com os Serviços de Mídia. Para fazer isso, conclua as seguintes verificações:

### <a name="pass-through-live-event-verification"></a>Verificação de evento ao vivo de passagem

1. Na conta dos Serviços de Mídia, verifique se o **Ponto de Extremidade de Streaming** está em execução. 
2. Crie e inicie um evento ao vivo de **passagem**. <br/> Para saber mais, confira [Estados e cobrança do Evento ao vivo](live-event-states-billing.md).
3. Obtenha as URLs de ingestão e configure seu codificador local para usar a URL para enviar a transmissão ao vivo com taxas de bits múltiplas para os Serviços de Mídia.
4. Obtenha a URL de visualização e use-a para verificar se a entrada do codificador está sendo realmente recebida.
5. Crie um novo objeto de **ativo**.
6. Crie um **LiveOutput** e use o nome do ativo que você criou.
7. Crie um **Localizador de Streaming** com os tipos internos da **Política de Streaming**.
8. Liste os caminhos no **Localizador de Streaming** para retornar as URLs a serem usadas.
9. Obtenha o nome do host para o **ponto de extremidade de streaming** de onde você deseja transmitir.
10. Combine a URL da etapa 8 com o nome do host na etapa 9 para obter a URL completa.
11. Execute o codificador dinâmico por aproximadamente 10 minutos.
12. Interrompa o Evento ao Vivo. 
13. Use um player como o [Player de Mídia do Azure](https://ampdemo.azureedge.net/azuremediaplayer.html) para inspecionar o ativo arquivado e garantir que a reprodução não tenha falhas visíveis em todos os níveis de qualidade (Ou, como alternativa, inspecione e valide por meio da URL de Visualização durante a sessão ao vivo).
14. Registre a ID de Ativo, a URL de transmissão publicada para o arquivo ao vivo e as configurações e a versão usadas no codificador dinâmico.
15. Reinicie o estado do evento ao vivo após criar cada exemplo.
16. Repita as etapas de 5 a 15 para todas as configurações com suporte do codificador (com e sem sinalização de anúncios/legendas/diferentes velocidades de codificação).

### <a name="live-encoding-live-event-verification"></a>Verificação do evento ao vivo de codificação ativa

1. Na conta dos Serviços de Mídia, verifique se o **Ponto de Extremidade de Streaming** está em execução. 
2. Crie e inicie o evento ao vivo de **codificação ativa**. <br/> Para saber mais, confira [Estados e cobrança do Evento ao vivo](live-event-states-billing.md).
3. Obtenha as URLs de ingestão e configure seu codificador para enviar por push uma transmissão ao vivo de taxa de bits única para os Serviços de Mídia.
4. Obtenha a URL de visualização e use-a para verificar se a entrada do codificador está sendo realmente recebida.
5. Crie um novo objeto de **ativo**.
6. Crie um **LiveOutput** e use o nome do ativo que você criou.
7. Crie um **Localizador de Streaming** com os tipos internos da **Política de Streaming**.
8. Liste os caminhos no **Localizador de Streaming** para retornar as URLs a serem usadas.
9. Obtenha o nome do host para o **ponto de extremidade de streaming** de onde você deseja transmitir.
10. Combine a URL da etapa 8 com o nome do host na etapa 9 para obter a URL completa.
11. Execute o codificador dinâmico por aproximadamente 10 minutos.
12. Interrompa o Evento ao Vivo.
13. Use um player como o [Player de Mídia do Azure](https://ampdemo.azureedge.net/azuremediaplayer.html) para inspecionar o ativo arquivado e garantir que a reprodução não tenha falhas visíveis em todos os níveis de qualidade (Ou, como alternativa, inspecione e valide por meio da URL de Visualização durante a sessão ao vivo).
14. Registre a ID de Ativo, a URL de transmissão publicada para o arquivo ao vivo e as configurações e a versão usadas no codificador dinâmico.
15. Reinicie o estado do evento ao vivo após criar cada exemplo.
16. Repita as etapas de 5 a 15 para todas as configurações com suporte do codificador (com e sem sinalização de anúncios/legendas/diferentes velocidades de codificação).

### <a name="longevity-verification"></a>Verificação de longevidade

Mesmas etapas que as da [verificação de evento ao vivo de passagem](#pass-through-live-event-verification), exceto pela etapa 11. <br/>Em vez de dez minutos, execute o codificador dinâmico por uma semana ou mais. Use um player como o [Player de Mídia do Azure](https://ampdemo.azureedge.net/azuremediaplayer.html) para assistir à transmissão ao vivo de tempos em tempos (ou ativo arquivado) para garantir que a reprodução não tenha falhas visíveis.

### <a name="email-your-recorded-settings"></a>Envie suas configurações registradas por email

Por último, envie por email as configurações registradas e os parâmetros do arquivo ao vivo aos Serviços de Mídia do Azure em amsstreaming@microsoft.com como uma notificação de que todas as verificações de autoverificação foram aprovadas. Além disso, inclua suas informações de contato para quaisquer acompanhamentos. Contate a equipe dos Serviços de Mídia do Azure para tratar quaisquer questões relacionadas a esse processo.

## <a name="next-steps"></a>Próximas etapas

[Transmissão ao vivo com os Serviços de Mídia v3](live-streaming-overview.md)
