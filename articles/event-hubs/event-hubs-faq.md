<properties 
    pageTitle="Perguntas frequentes sobre os Hubs de Eventos | Microsoft Azure"
    description="Perguntas frequentes sobre os Hubs de Eventos."
    services="event-hubs"
    documentationCenter="na"
    authors="sethmanheim"
    manager="timlt"
    editor="" />
<tags 
    ms.service="event-hubs"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="09/01/2016"
    ms.author="sethm" />


# <a name="event-hubs-faq"></a>Perguntas frequentes dos Hubs de Eventos

Hubs de eventos fornecem entrada, persistência e processamento de eventos de dados de fontes de dados de alta produtividade e/ou milhões de dispositivos. Quando combinados com tópicos e filas do Barramento de Serviço, os Hubs de Eventos habilitam implantações de comandos e controles persistentes para cenários da [Internet das Coisas (IoT)](https://azure.microsoft.com/services/iot-hub/) .

Este artigo discute informações sobre preços e responde algumas perguntas frequentes sobre os Hubs de Eventos:

## <a name="pricing-information"></a>Informações sobre preço

Para obter todas as informações sobre os preços dos Hubs de Eventos, consulte os [detalhes de preços dos Hubs de Eventos](https://azure.microsoft.com/pricing/details/event-hubs/).

## <a name="how-are-event-hubs-ingress-events-calculated?"></a>Como os eventos de entrada de Hubs de Eventos são calculados?

Cada evento enviado a um Hub de Eventos conta como uma mensagem faturável. Um *evento de entrada* é definido como uma unidade de dados menor ou igual a 64 KB. Qualquer evento menor ou igual a 64 KB de tamanho é considerado um evento faturável. Se o evento for maior que 64 KB, o número de eventos passíveis de cobrança será calculado de acordo com o tamanho do evento, em múltiplos de 64 KB. Por exemplo, um evento de 8 KB enviado para o Hub de Eventos é cobrado como um evento, mas uma mensagem de 96 KB enviada para o Hub de Eventos é cobrada como dois eventos.

Eventos consumidos a partir de um Hub de Eventos, bem como operações de gerenciamento e chamadas de controle, como pontos de verificação, não são contadas como eventos de entrada faturáveis, mas se acumulam no limite de unidade de produtividade.

## <a name="what-are-event-hubs-throughput-units?"></a>O que são unidades de produtividade dos Hubs de Eventos?

Você seleciona unidades de taxa de transferência de Hubs de Eventos de forma explícita, por meio do Portal do Azure ou de modelos do gerenciador de recursos de Hubs de Eventos. Unidades de produtividade se aplicam a todos os Hubs de Eventos em um namespace de Hubs de Eventos, e cada unidade de produtividade proporciona ao namespace os seguintes recursos:

- Até 1 MB por segundo de eventos de entrada (eventos enviados para um Hub de Eventos), mas não mais de 1.000 eventos de entrada, operações de gerenciamento ou chamadas de controle à API por segundo.

- Até 2 MB por segundo de eventos de saída (eventos consumidos a partir de um Hub de Eventos).

- Até 84 GB de armazenamento de eventos (suficiente para o período de retenção padrão de 24 horas).

As unidades de produtividade dos Hubs de Eventos são cobradas por hora, com base no número máximo de unidades selecionadas durante determinada hora.

## <a name="how-are-event-hubs-throughput-unit-limits-enforced?"></a>Como os limites de unidades de produtividade dos Hubs de Eventos são aplicados?

Se a produtividade de entrada total ou a taxa de eventos de entrada total em todos os Hubs de Eventos em um namespace exceder os limites de unidades de produtividade totais, os remetentes serão limitados e receberão erros indicando que a cota de entrada foi excedida.

Se a produtividade de saída total ou a taxa de eventos de saída total em todos os Hubs de Eventos em um namespace exceder os limites de unidades de produtividade totais, os receptores serão limitados e receberão erros indicando que a cota de saída foi excedida. As cotas de entrada e saída são aplicadas separadamente, para que nenhum remetente possa fazer com que o consumo de eventos se torne lento e para que um receptor não possa evitar que eventos sejam enviados para um Hub de Eventos.

A seleção de unidades de produtividade é independente do número de partições de Hubs de Eventos. Embora cada partição ofereça uma produtividade máxima de 1 MB por segundo de entrada (com um máximo de 1.000 eventos por segundo) e 2 MB por segundo de saída, há uma cobrança fixa pelas próprias partições. A cobrança é pelas unidades de produtividade agregadas em todos os Hubs de Eventos em um namespace de Hubs de Eventos. Com esse padrão, você pode criar partições suficientes para dar suporte à carga máxima prevista para seus sistemas, sem gerar cobranças por unidades de produtividade até que a carga de eventos no sistema realmente exija números mais altos de produtividade e sem a necessidade de alterar a estrutura e a arquitetura de seus sistemas conforme a carga no sistema aumenta.

## <a name="is-there-a-limit-on-the-number-of-throughput-units-that-can-be-selected?"></a>Há um limite para o número de unidades de produtividade que podem ser selecionadas?

Há uma cota padrão de 20 unidades de produtividade por namespace. Você pode solicitar uma cota maior de unidades de produtividade preenchendo um tíquete de suporte. Além do limite de 20 unidades de produtividade, há pacotes disponíveis de 20 e 100 unidades de produtividade. Observe que o uso de mais de 20 unidades de produtividade impossibilita a alteração do número de unidades de produtividade sem o preenchimento de um tíquete de suporte.

## <a name="is-there-a-charge-for-retaining-event-hubs-events-for-more-than-24-hours?"></a>Há um custo para a retenção de eventos de Hubs de Eventos por mais de 24 horas?

O nível Standard dos Hubs de Eventos permite a retenção de mensagens por períodos de mais de 24 horas, por um máximo de 30 dias. Se o número total de eventos armazenados exceder o limite de armazenamento para o número de unidades de produtividade selecionadas (84 GB por unidade de produtividade), o tamanho que exceder o limite será cobrado com base na taxa de armazenamento de Blobs do Azure publicada. O limite de armazenamento em cada unidade de produtividade cobre todos os custos de armazenamento para períodos de retenção de 24 horas (o padrão) mesmo que a unidade de produtividade seja usada até o limite máximo de entrada.

## <a name="what-is-the-maximum-retention-period?"></a>Qual é o período de retenção máximo?

O nível Standard dos Hubs de Eventos atualmente dão suporte a um período de retenção máximo de 7 dias. Os Hubs de Eventos não servem como um armazenamento de dados permanente. Períodos de retenção superiores a 24 horas destinam-se a cenários em que é conveniente repetir um fluxo de eventos nos mesmos sistemas; por exemplo, para treinar ou verificar um novo modelo de aprendizado de máquina nos dados existentes.

## <a name="how-is-the-event-hubs-storage-size-calculated-and-charged?"></a>Como o tamanho do armazenamento de Hubs de Eventos é calculado e cobrado?

O tamanho total de todos os eventos armazenados, incluindo eventuais sobrecargas internas para cabeçalhos de eventos ou estruturas de armazenamento em disco em todos os Hubs de Eventos, é medido durante todo o dia. No final do dia, o tamanho do armazenamento de pico é calculado. O limite de armazenamento diário é calculado com base no número mínimo de unidades de produtividade que foram selecionadas durante o dia (cada unidade de produtividade fornece um limite de 84 GB). Se o tamanho total exceder o limite de armazenamento diário calculado, o armazenamento em excesso será cobrado usando as taxas de armazenamento de Blob do Azure (com base na taxa de **armazenamento com redundância local** ).

## <a name="can-i-use-a-single-amqp-connection-to-send-and-receive-from-event-hubs-and-service-bus-queues/topics?"></a>Posso usar uma única conexão AMQP para enviar e receber de Hubs de Eventos e filas/tópicos do Barramento de Serviço?

Sim, contanto que todos os Hubs de Eventos, filas e tópicos estejam no mesmo namespace. Dessa forma, você pode implementar conectividade agenciada bidirecional com muitos dispositivos, com latências inferiores a um segundo, de forma econômica e altamente escalonável.

## <a name="do-brokered-connection-charges-apply-to-event-hubs?"></a>Cobranças por conexões agenciadas são aplicadas aos Hubs de Eventos?

Para remetentes, cobranças de conexão são aplicadas somente quando o protocolo AMQP é usado. Não há cobranças de conexão para enviar eventos usando HTTP, independentemente do número de sistemas ou dispositivos remetentes. Se você planeja usar o AMQP (por exemplo, para obter transmissões de eventos mais eficientes ou para habilitar a comunicação bidirecional em cenários de comando e controle da IoT), confira a página de [informações sobre o preço do Barramento de Serviço](https://azure.microsoft.com/pricing/details/service-bus/) para obter informações sobre o que constitui uma conexão agenciada e como elas são medidas.

## <a name="what-is-the-difference-between-event-hubs-basic-and-standard-tiers?"></a>Qual é a diferença entre os níveis Basic e Standard dos Hubs de Eventos?

O nível Standard dos Hubs de Eventos fornecem recursos adicionais aos disponíveis nos Hubs de Eventos Basic e em alguns sistemas concorrentes. Esses recursos incluem períodos de retenção de mais de 24 horas e a capacidade de usar uma única conexão AMQP para enviar comandos para um grande número de dispositivos com latências inferiores a um segundo, bem como para enviar telemetria desses dispositivos para os Hubs de Eventos. Para obter a lista de recursos, confira os [detalhes do preço dos Hubs de Eventos](https://azure.microsoft.com/pricing/details/event-hubs/).

## <a name="geographic-availability"></a>Disponibilidade geográfica

Os Hubs de Eventos estão disponíveis nas seguintes regiões:

|Localização geográfica|Regiões|
|---|---|
|Estados Unidos|Região Central dos EUA, Leste dos EUA, Leste dos EUA 2, Região Centro-Sul dos EUA, Oeste dos EUA|
|Europa|Norte da Europa, Europa Ocidental|
|Pacífico Asiático|Ásia Oriental, Sudeste Asiático|
|Japão|Leste do Japão, Oeste do Japão|
|Brasil|Sul do Brasil|
|Austrália|Leste da Austrália, Sudeste da Austrália|

## <a name="support-and-sla"></a>Suporte e SLA

Suporte técnico para os Hubs de Eventos está disponível por meio dos [fóruns da comunidade](https://social.msdn.microsoft.com/forums/azure/home). O suporte para gerenciamento de assinaturas e cobranças é fornecido sem custo adicional.

Para saber mais sobre nosso SLA, veja a página [Contratos de Nível de Serviço](https://azure.microsoft.com/support/legal/sla/) .

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre os Hubs de Eventos, consulte os artigos a seguir:

- [Visão geral dos Hubs de Eventos][].
- Um [aplicativo de exemplo completo que usa os Hubs de Evento][].

[Visão geral de Hubs de Evento]: event-hubs-overview.md
[aplicativo de exemplo completo que usa Hub de Eventos]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097



<!--HONumber=Oct16_HO2-->


