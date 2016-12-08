---
title: Gerenciar pontos de extremidade de streaming com o portal do Azure | Microsoft Docs
description: "Este tópico mostra como gerenciar pontos de extremidade de streaming usando o portal do Azure."
services: media-services
documentationcenter: 
author: Juliako
writer: juliako
manager: erikre
editor: 
ms.assetid: 097ab5e5-24e1-4e8e-b112-be74172c2701
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2016
ms.author: juliako
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: e0351e286ae4f36c6c6fba25e5eaf4c135b21549


---
# <a name="manage-streaming-endpoints-with-the-azure-portal"></a>Gerenciar pontos de extremidade de streaming com o portal do Azure
## <a name="overview"></a>Visão geral
> [!NOTE]
> Para concluir este tutorial, você precisa de uma conta do Azure. Para obter detalhes, consulte [Avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/). 
> 
> 

Nos Serviços de Mídia do Microsoft Azure, um **Ponto de Extremidade de Streaming** representa um serviço de streaming que pode fornecer conteúdo diretamente a um aplicativo de player de cliente ou à CDN (Rede de Distribuição de Conteúdo) para a distribuição posterior. Os Serviços de Mídia também fornecem integração perfeita da CDN do Azure. O fluxo de saída do serviço StreamingEndpoint pode ser um fluxo ao vivo ou um ativo de vídeo por demanda na sua conta dos Serviços de Mídia.

Além disso, você pode controlar a capacidade do serviço de ponto de extremidade de streaming para lidar com necessidades crescentes de largura de banda ajustando as unidades de streaming. É recomendável alocar uma ou mais unidades de escala para aplicativos no ambiente de produção. As unidades de streaming fornecem a você capacidade de egresso dedicada que pode ser comprada em incrementos de 200 Mbps e funcionalidade adicional, que inclui: [empacotamento dinâmico](media-services-dynamic-packaging-overview.md), Integração da CDN e configuração avançada.

> [!NOTE]
> Você será cobrado apenas quando seu ponto de extremidade de streaming estiver em estado de execução.
> 
> 

Este tópico fornece uma visão geral das principais funcionalidades fornecidas pelos Pontos de Extremidade de Streaming. O tópico também mostra como usar o portal do Azure para gerenciar pontos de extremidade de streaming. Para obter informações sobre como dimensionar o ponto de extremidade de streaming, consulte [este](media-services-portal-scale-streaming-endpoints.md) tópico.

## <a name="start-managing-streaming-endpoints"></a>Começar a gerenciar pontos de extremidade de streaming
Para começar a gerenciar pontos de extremidade de streaming para sua conta, faça o indicado a seguir.

1. No [Portal do Azure](https://portal.azure.com/), selecione sua conta dos Serviços de Mídia do Azure.
2. Na janela **Configurações**, selecione **Pontos de extremidade de streaming**.
   
    ![ponto de extremidade de streaming](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints1.png)

## <a name="adddelete-a-streaming-endpoint"></a>Adicionar/excluir um ponto de extremidade de streaming
Para adicionar\excluir um ponto de extremidade de streaming usando o portal do Azure, faça o seguinte:

1. Para adicionar um ponto de extremidade de streaming, clique no **+ Ponto de extremidade** na parte superior da página. 
2. Para excluir um ponto de extremidade de streaming, pressione o botão **Excluir** . 
   
    O ponto de extremidade de streaming não pode ser excluído.
3. Clique no botão **Iniciar** para iniciar o ponto de extremidade de streaming.
   
    ![Ponto de Extremidade de Streaming](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints2.png)

Por padrão, você pode ter até dois pontos de extremidade de streaming. Se você precisar solicitar mais, consulte [Cotas e limitações](media-services-quotas-and-limitations.md).

## <a name="a-idconfigurestreamingendpointsaconfiguring-the-streaming-endpoint"></a><a id="configure_streaming_endpoints"></a>Configurando o ponto de extremidade de streaming
O Ponto de Extremidade de Streaming permite que você configure as seguintes propriedades quando tem pelo menos uma unidade de escala: 

* Controle de acesso
* Controle de cache
* Políticas de acesso entre sites

Para obter informações detalhadas sobre essas propriedades, consulte [StreamingEndpoint](https://msdn.microsoft.com/library/azure/dn783468.aspx).

Você pode configurar o ponto de extremidade de streaming fazendo o seguinte:

1. Selecione o ponto de extremidade de streaming que você deseja configurar.
2. Clique em **Configurações**.

A seguir há uma breve descrição dos campos.

![Ponto de Extremidade de Streaming](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints4.png)

1. Política de cache máximo: usada para configurar o tempo de vida de cache para ativos atendidos por esse ponto de extremidade de streaming. Se nenhum valor for definido, o padrão será usado. Os valores padrão também podem ser definidos diretamente no armazenamento do Azure. Se a CDN do Azure estiver habilitada para o ponto de extremidade de streaming, você não deverá definir o valor da política de cache para menos de 600 segundos.  
2. Endereços IP permitidos: usado para especificar endereços IP que terão permissão para se conectar ao ponto de extremidade de streaming publicado. Se nenhum endereço IP for especificado, qualquer endereço IP poderá se conectar. Os endereços IP podem ser especificados como um único endereço IP (por exemplo, '10.0.0.1'), um intervalo de IP usando um endereço IP e uma máscara de sub-rede CIDR (por exemplo, '10.0.0.1/22') ou um intervalo de IPs usando um endereço IP e uma máscara de sub-rede em notação decimal com ponto (por exemplo, '10.0.0.1(255.255.255.0)').
3. Configuração de autenticação de cabeçalho de assinatura da Akamai: usado para especificar como a solicitação de autenticação de cabeçalho de assinatura de servidores da Akamai está configurada. A data de validade é no formato UTC.

## <a name="a-idenablecdnaenable-azure-cdn-integration"></a><a id="enable_cdn"></a>Habilitar a integração da CDN do Azure
Você pode especificar a habilitação da integração da CDN do Azure para um Ponto de Extremidade de Streaming (isso é desabilitado por padrão).

Para configurar a integração da CDN do Azure como true:

* O ponto de extremidade de streaming deve ter pelo menos uma unidade de streaming. Se, posteriormente, você desejar definir unidades de escala como 0, primeiro deverá desabilitar a integração da CDN. Por padrão, quando você cria um novo ponto de extremidade de streaming, uma unidade de streaming é automaticamente definida.
* O ponto de extremidade de streaming deve estar no estado parado. Quando a CDN for habilitada, você poderá iniciar o ponto de extremidade de streaming. 

Pode levar até 90 minutos para que a integração da CDN do Azure seja habilitada.  Leva até duas horas para que as alterações estejam ativas em todos os POPS do CDN.

A integração com a CDN é habilitada em todos os data centers do Azure: Oeste dos EUA, Leste dos EUA, Europa Setentrional, Europa Ocidental, Oeste do Japão, Leste do Japão, Sudeste Asiático e Ásia Oriental.

Após ela ter sido habilitada, a configuração **Controle de Acesso** é desabilitada.

![Ponto de Extremidade de Streaming](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints5.png)

> [!IMPORTANT]
> A integração dos Serviços de Mídia do Azure com a CDN do Azure é implementada na **CDN do Azure da Verizon**.  Para usar a **CDN do Azure da Akamai** para Serviços de Mídia do Azure, é preciso [configurar o ponto de extremidade manualmente](../cdn/cdn-create-new-endpoint.md).  Para obter mais informações sobre os recursos da CDN do Azure, veja [Visão geral da CDN](../cdn/cdn-overview.md).
> 
> 

### <a name="additional-considerations"></a>Considerações adicionais
* Quando CDN está habilitado para um ponto de extremidade de streaming, os clientes não podem solicitar o conteúdo diretamente a partir da origem. Se você precisar da capacidade de testar seu conteúdo com ou sem CDN, poderá criar outro ponto de extremidade de streaming no qual a CDN não esteja habilitada.
* O nome de host do ponto de extremidade de streaming permanece o mesmo depois de habilitar o CDN. Você não precisa fazer nenhuma alteração ao seu fluxo de trabalho de serviços de mídia depois que o CDN for habilitado. Por exemplo, se o nome de host do ponto de extremidade de streaming for strasbourg.streaming.mediaservices.windows.net, depois de habilitar o CDN, o mesmo nome de host é usado.
* Para novos pontos de extremidade de streaming, é possível habilitar a CDN simplesmente criando um novo ponto de extremidade; para pontos de extremidade de streaming existentes, será necessário primeiro parar o ponto de extremidade e, em seguida, habilitar a CDN.

Para obter mais informações, consulte [Anunciando a integração dos Serviços de Mídia do Azure com o CDN do Azure (Rede de Distribuição de Conteúdo)](http://azure.microsoft.com/blog/2015/03/17/announcing-azure-media-services-integration-with-azure-cdn-content-delivery-network/).

## <a name="next-steps"></a>Próximas etapas
Examine os roteiros de aprendizagem dos Serviços de Mídia.

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornecer comentários
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]




<!--HONumber=Nov16_HO3-->


