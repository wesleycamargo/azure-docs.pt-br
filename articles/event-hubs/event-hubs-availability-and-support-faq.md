<properties 
   pageTitle="Perguntas frequentes sobre disponibilidade e suporte dos Hubs de Eventos"
   description="Preços e perguntas frequentes dos Hubs de Eventos"
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
   ms.workload="tbd"
   ms.date="06/09/2015"
   ms.author="sethm" />

# Perguntas frequentes sobre disponibilidade e suporte dos Hubs de Eventos

Hubs de eventos fornecem entrada, persistência e processamento de eventos de dados de fontes de dados de alta produtividade e/ou milhões de dispositivos. Quando combinados com tópicos e filas do Barramento de Serviço, os Hubs de Eventos permitem implantações de comandos e controles persistentes para cenários de Internet das Coisas.

Esta seção traz informações de disponibilidade e respostas a algumas perguntas frequentes.

## Informações sobre preço

Para obter informações sobre os preços dos Hubs de Eventos, consulte os [detalhes de preços dos Hubs de Eventos](http://azure.microsoft.com/pricing/details/event-hubs/).

## Como os eventos de entrada de Hubs de Eventos são calculados?

Cada evento enviado a um Hub de Eventos conta como uma mensagem faturável. Um *evento de entrada* é definido como uma unidade de dados menor que ou igual a 64 KB. Qualquer evento menor que ou igual a 64 KB de tamanho é considerado um evento faturável. Se o evento for maior que 64 KB, o número de eventos passíveis de cobrança é calculado de acordo com o tamanho do evento, em múltiplos de 64 KB. Por exemplo, um evento de 8 KB enviado para o Hub de Eventos é cobrado como um evento, mas uma mensagem de 96 KB enviada para o Hub de Eventos é cobrada como dois eventos.

Eventos consumidos a partir de um Hub de Eventos, bem como operações de gerenciamento e chamadas de controle, como pontos de verificação, não são contadas como eventos de entrada faturáveis, mas se acumulam no limite de unidade de produtividade.

## O que são unidades de produtividade dos Hubs de Eventos?

Unidades de produtividade dos Hubs de Eventos são selecionadas explicitamente pelo usuário, por meio do Portal do Azure ou de APIs de gerenciamento dos Hubs de Eventos. Unidades de produtividade se aplicam a todos os Hubs de Eventos em um namespace do Barramento de Serviço, e cada unidade de produtividade proporciona ao namespace os seguintes recursos:

- Até 1 MB por segundo de eventos de entrada (eventos enviados para um Hub de Eventos), mas não mais de 1.000 eventos de entrada, operações de gerenciamento ou chamadas de controle à API por segundo.

- Até 2 MB por segundo de eventos de saída (eventos consumidos a partir de um Hub de Eventos).

- Até 84 GB de armazenamento de eventos (suficiente para o período de retenção padrão de 24 horas).

As unidades de produtividade dos Hubs de Eventos são cobradas por hora, com base no número máximo de unidades selecionadas durante determinada hora.

## Como os limites de unidades de produtividade dos Hubs de Eventos são aplicados?

Se a produtividade de entrada total ou a taxa de eventos de entrada total em todos os Hubs de Eventos em um namespace exceder os limites de unidades de produtividade totais, os remetentes serão limitados e receberão erros indicando que a cota de entrada foi excedida.

Se a produtividade de saída total ou a taxa de eventos de saída total em todos os Hubs de Eventos em um namespace exceder os limites de unidades de produtividade totais, os receptores serão limitados e receberão erros indicando que a cota de saída foi excedida. As cotas de entrada e saída são aplicadas separadamente, para que nenhum remetente possa fazer com que o consumo de eventos se torne lento e para que um receptor não possa evitar que eventos sejam enviados para um Hub de Eventos.

A seleção de unidades de produtividade é independente do número de partições de Hubs de Eventos. Embora cada partição ofereça uma produtividade máxima de 1 MB por segundo de entrada (com um máximo de 1.000 eventos por segundo) e 2 MB por segundo de saída, há uma cobrança fixa pelas próprias partições. A cobrança é pelas unidades de produtividade agregadas em todos os Hubs de Eventos em um namespace do Barramento de Serviço. Com esse padrão, você pode criar partições suficientes para dar suporte à carga máxima prevista para seus sistemas, sem gerar cobranças por unidades de produtividade até que a carga de eventos no sistema realmente exija números mais altos de produtividade e sem a necessidade de alterar a estrutura e a arquitetura de seus sistemas conforme a carga no sistema aumenta.

## Há um limite para o número de unidades de produtividade que podem ser selecionadas?

Há uma cota padrão de 20 unidades de produtividade por namespace. Você pode solicitar uma cota maior de unidades de produtividade preenchendo um tíquete de suporte. Além do limite de 20 unidades de produtividade, há pacotes disponíveis de 20 e 100 unidades de produtividade. Observe que o uso de mais de 20 unidades de produtividade impossibilita a alteração do número de unidades de produtividade sem o preenchimento de um tíquete de suporte.

## Há um custo para a retenção de eventos de Hubs de Eventos por mais de 24 horas?

O nível Standard dos Hubs de Eventos permite a retenção de mensagens por períodos de mais de 24 horas, por um máximo de 30 dias. Se a quantidade total de eventos armazenados exceder o limite de armazenamento para o número de unidades de produtividade selecionadas (84 GB por unidade de produtividade), o tamanho que exceder o limite será cobrado com base na taxa de armazenamento de Blog do Azure publicada. O limite de armazenamento em cada unidade de produtividade cobre todos os custos de armazenamento para períodos de retenção de 24 horas (o padrão) mesmo que a unidade de produtividade seja usada até o limite máximo de entrada.

## Qual é o período de retenção máximo?

O nível Standard dos Hubs de Eventos atualmente dão suporte a um período de retenção máximo de 7 dias. Os Hubs de Eventos não servem como um armazenamento de dados permanente. Períodos de retenção superiores a 24 horas destinam-se a cenários em que é conveniente repetir um fluxo de eventos nos mesmos sistemas; por exemplo, para treinar ou verificar um novo modelo de aprendizado de máquina nos dados existentes.

## Como o tamanho do armazenamento de Hubs de Eventos é calculado e cobrado?

O tamanho total de todos os eventos armazenados, incluindo eventuais sobrecargas internas para cabeçalhos de eventos ou estruturas de armazenamento em disco em todos os Hubs de Eventos, é medido durante todo o dia. No final do dia, o tamanho do armazenamento de pico é calculado. O limite de armazenamento diário é calculado com base no número mínimo de unidades de produtividade que foram selecionadas durante o dia (cada unidade de produtividade fornece um limite de 84 GB). Se o tamanho total exceder o limite de armazenamento diário calculado, o armazenamento em excesso será cobrado usando as taxas de armazenamento de Blob do Azure (com base na taxa de **armazenamento com redundância local**).

## Posso usar uma única conexão AMQP para enviar e receber de Hubs de Eventos e filas/tópicos do Barramento de Serviço?

Sim, contanto que os Hubs de Eventos, filas e tópicos estejam no mesmo namespace do Barramento de Serviço. Dessa forma, você pode implementar conectividade agenciada bidirecional com muitos dispositivos, com latências inferiores a um segundo, de forma econômica e altamente dimensionável.

## Cobranças por conexões agenciadas são aplicadas aos Hubs de Eventos?

Para remetentes, cobranças de conexão são aplicadas somente quando o protocolo AMQP é usado. Não há cobranças de conexão para enviar eventos usando HTTP, independentemente do número de sistemas ou dispositivos remetentes. Se você planeja usar AMQP (por exemplo, para obter fluxo de eventos mais eficiente ou para habilitar a comunicação bidirecional em cenários de comando e controle de Internet das Coisas), consulte a página de [informações sobre preços do Barramento de Serviço](http://azure.microsoft.com/pricing/details/service-bus/) para obter informações sobre o que constitui uma conexão agenciada e como elas são medidas.

## Qual é a diferença entre os níveis Basic e Standard dos Hubs de Eventos?

O nível Standard dos Hubs de Eventos fornecem recursos adicionais aos disponíveis nos Hubs de Eventos Basic, bem como em alguns sistemas concorrentes. Esses recursos incluem períodos de retenção de mais de 24 horas e a capacidade de usar uma única conexão AMQP para enviar comandos para um grande número de dispositivos com latências inferiores a um segundo, bem como para enviar telemetria desses dispositivos para os Hubs de Eventos. Consulte os [detalhes de preços] ([detalhes de preços dos Hubs de Eventos](http://azure.microsoft.com/pricing/details/event-hubs/)) para ver a lista de recursos.

## Disponibilidade geográfica

Os Hubs de Eventos estão disponíveis nas seguintes regiões:

|Localização geográfica|Regiões|
|---|---|
|Estados Unidos|Região Central dos EUA, Leste dos EUA, Leste dos EUA 2, Região Centro-Sul dos EUA, Oeste dos EUA|
|Europa|Norte da Europa, Europa Ocidental|
|Pacífico Asiático|Ásia Oriental, Sudeste Asiático|
|Japão|Leste do Japão, Oeste do Japão|
|Brasil|Sul do Brasil|
|Austrália|Leste da Austrália, Sudeste da Austrália|

## Suporte e SLA

Suporte técnico para os Hubs de Eventos está disponível por meio dos [fóruns da comunidade](https://social.msdn.microsoft.com/forums/azure/home). O suporte para gerenciamento de assinaturas e cobranças é fornecido sem custo adicional.

Para saber mais sobre nosso SLA, visite a página de [contratos de nível de serviço](http://azure.microsoft.com/support/legal/sla/).

## Próximas etapas

Para saber mais sobre os Hubs de Eventos, consulte os tópicos a seguir:

- [Visão Geral dos Hubs de Evento]
- Um [aplicativo de exemplo completo que usa os Hubs de Eventos].
- Uma [solução de mensagens enfileiradas] usando filas do Barramento de Serviço.

[Visão Geral dos Hubs de Evento]: event-hubs-overview.md
[aplicativo de exemplo completo que usa os Hubs de Eventos]: https://code.msdn.microsoft.com/windowsazure/Service-Bus-Event-Hub-286fd097
[solução de mensagens enfileiradas]: ../cloud-services-dotnet-multi-tier-app-using-service-bus-queues.md
 

<!---HONumber=July15_HO4-->