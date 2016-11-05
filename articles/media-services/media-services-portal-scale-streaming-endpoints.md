---
title: " Dimensionar pontos de extremidade de streaming com o portal do Azure | Microsoft Docs"
description: Este tutorial orienta você pelas etapas de dimensionar pontos de extremidade de streaming usando o portal do Azure.
services: media-services
documentationcenter: ''
author: Juliako
manager: erikre
editor: ''

ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/29/2016
ms.author: juliako

---
# Dimensionar pontos de extremidade de streaming com o portal do Azure
## Visão geral
> [!NOTE]
> Para concluir este tutorial, você precisa de uma conta do Azure. Para obter detalhes, consulte [Avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
> 
> 

Ao trabalhar com os Serviços de Mídia do Azure, um dos cenários mais comuns é fornecer o vídeo via streaming de taxa de bits adaptável para seus clientes. Os Serviços de Mídia dão suporte às seguintes tecnologias de streaming de taxa de bits adaptável: HTTP Live Streaming (HLS), Smooth Streaming, MPEG DASH e HDS (apenas para licenciados Adobe PrimeTime/Access).

Os Serviços de Mídia fornecem um empacotamento dinâmico que permite a você enviar o conteúdo codificado para MP4 da taxa de bits adaptável nos formatos de transmissão suportados pelos Serviços de Mídia (MPEG DASH, HLS, Smooth Streaming, HDS) just-in-time, sem ter que armazenar as versões recolocadas de cada um dos formatos de transmissão.

Para aproveitar os benefícios do empacotamento dinâmico, você precisa fazer o seguinte:

* Codifique seu arquivo mezanino (fonte) em um conjunto de arquivos MP4 da taxa de bits adaptável (as etapas de codificação serão demonstradas mais tarde neste tutorial).
* Crie pelo menos uma unidade de transmissão para o *ponto de extremidade de streaming* a partir da qual você planeja fornecer seu conteúdo. As etapas a seguir mostram como alterar o número de unidades da transmissão.

Com o empacotamento dinâmico, você só precisa armazenar e pagar pelos arquivos em um único formato de armazenamento, e os Serviços de Mídia criarão e fornecerão a resposta apropriada com base nas solicitações de um cliente.

Além disso, você pode controlar a capacidade do serviço de ponto de extremidade de streaming para lidar com necessidades crescentes de largura de banda ajustando as unidades de streaming. É recomendável alocar uma ou mais unidades de escala para aplicativos no ambiente de produção. As unidades de streaming fornecem capacidade de egresso dedicada que pode ser comprada em incrementos de 200 Mbps e funcionalidade adicional que inclui: [empacotamento dinâmico](media-services-dynamic-packaging-overview.md), integração da CDN e configuração avançada. Para obter mais informações, veja [Gerenciar pontos de extremidade de streaming com o portal do Azure](media-services-portal-manage-streaming-endpoints.md).

## Dimensionar pontos de extremidade de streaming
Para criar e alterar o número de unidades reservadas de transmissão, faça o seguinte:

1. Faça logon no [Portal do Azure](https://portal.azure.com/).
2. Na janela **Configurações**, selecione **Pontos de extremidade de streaming**.
3. Clique no ponto de extremidade de streaming que você deseja dimensionar.
4. Mover o controle deslizante para especificar o número de unidades de streaming

![Ponto de extremidade de streaming](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints3.png)

As seguintes considerações se aplicam:

* A alocação de quaisquer novas unidades de streaming pode levar cerca de 20 minutos para ser concluída.
* No momento, mudar de qualquer valor positivo de unidades de streaming de volta para nenhuma pode desabilitar o streaming sob demanda por até uma hora.
* O número mais alto de unidades especificadas para o período de 24 horas é usado para calcular o custo. Para saber mais sobre os detalhes de preços, consulte [Detalhes de preços dos Serviços de Mídia](http://go.microsoft.com/fwlink/?LinkId=275107).

## Próximas etapas
Examine os roteiros de aprendizagem dos Serviços de Mídia.

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## Fornecer comentários
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

<!---HONumber=AcomDC_0831_2016-->