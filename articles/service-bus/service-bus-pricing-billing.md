<properties 
   pageTitle="Perguntas frequentes sobre preços e cobrança do Barramento de Serviço | Microsoft Azure"
   description="Visão geral da estrutura de preços de Barramento de Serviço."
   services="service-bus"
   documentationCenter="na"
   authors="sethmanheim"
   manager="timlt"
   editor="tysonn" />
<tags 
   ms.service="service-bus"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/10/2015"
   ms.author="sethm" />

# Barramento de Serviço, preços e cobrança

O Barramento de Serviço é oferecido nas camadas Basic, Standard e [Premium](service-bus-premium-messaging.md). Você pode escolher um nível de serviço para cada namespace do Barramento de Serviço criado por você e essa seleção de camada aplica-se a todas as filas, tópicos/assinaturas, retransmissões e Hubs de eventos criado dentro desse namespace.

>[AZURE.NOTE]Para saber mais sobre o preço do Barramento de Serviço, consulte [Perguntas Frequentes sobre Preços do Barramento de Serviço](service-bus-pricing-faq.md).

O Barramento de Serviço usa os dois medidores a seguir para filas e tópicos/assinaturas:

1. **Operações de mensagens**: definido como chamadas à API em pontos de extremidade de serviço de fila ou tópico/assinatura. Esse medidor substituirá as mensagens enviadas ou recebidas como a principal unidade de uso cobrável para filas e tópicos/assinaturas.

2. **Conexões orientadas**: definido como o número máximo de conexões persistentes abertas em filas, tópicos/assinaturas ou Hubs de eventos durante um período de amostragem de uma hora determinada. Esse medidor se aplicará somente na camada Standard, no qual você pode abrir conexões adicionais (anteriormente, as conexões eram limitadas a 100 por fila/tópico/assinatura) por uma taxa nominal por conexão.

A camada **Standard** apresenta preços graduados para operações executadas com filas e tópicos/assinaturas, resultando em descontos por volume de até 80% altos níveis de uso. Há também um encargo de base da camada Standard de US $10 por mês, que permite que você execute até 12,5 milhões de operações por mês sem custos adicionais.

A camada **Premium** fornece isolamento de recursos na camada de CPU e memória, de modo que a carga de trabalho do cliente seja executada isoladamente. Esse contêiner de recurso é chamado de *unidade do sistema de mensagens*. Cada namespace premium é alocado para pelo menos uma unidade do sistema de mensagens. Você pode adquirir 1, 2 ou 4 unidades do sistema de mensagens para cada namespace Premium do Barramento de serviço. Uma única carga de trabalho ou entidade pode abranger várias unidades do sistema de mensagens, e o número de unidades do sistema de mensagens pode ser alterado à vontade, embora a cobrança seja feita por taxas diárias ou de 24 horas. O resultado é um desempenho previsível e repetível para sua solução baseada no Barramento de Serviço. Esse desempenho não é apenas o mais previsível e disponível, mas também o mais rápido. O sistema de mensagens Premium do Barramento de Serviço do Azure se baseia no mecanismo de armazenamento introduzido nos Hubs de Eventos do Azure. Com o sistema de mensagens Premium, o desempenho de pico é muito mais rápido do que na camada Standard.

Observe que o custo base padrão é cobrado apenas uma vez por mês por assinatura do Azure. Isso significa que depois de criar um único namespace de Barramento de Serviço camada Standard ou Premium, você poderá criar tantos namespaces adicionais da camada Standard ou Premium quanto desejar nessa mesma assinatura do Azure, sem incorrer em encargos adicionais de base.

Todos os namespaces de Barramento de Serviço existentes criados antes de 1º de novembro de 2014 foram automaticamente colocados na camada Standard. Isso garante que você continue a ter acesso a todos os recursos disponíveis no momento com o Barramento de Serviço. Posteriormente, você pode usar o portal do Azure para fazer o downgrade para a camada Basic, se desejado.

A tabela a seguir resume as diferenças funcionais entre as camadas Basic e Standard/Premium.

|Recurso|Basic|Standard/Premium|
|---|---|---|
|Hubs de Evento|SIM|SIM|
|Filas|SIM|SIM|
|Mensagens agendadas|SIM|SIM|
|Tópicos/assinaturas|NÃO|SIM|
|Retransmissão|NÃO|SIM|
|Transações|NÃO|SIM|
|Eliminação de duplicação|NÃO|SIM|
|Sessões|NÃO|SIM|
|Mensagens grandes|NÃO|SIM|
|ForwardTo|NÃO|SIM|
|SendVia|NÃO|SIM|
|Conexões orientadas (incluídas)|100 por namespace do Barramento de Serviço|1\.000 por assinatura do Azure|
|Conexões orientadas (excedente permitido)|NÃO|SIM (Faturável)|

## Operações de mensagens

Como parte do novo modelo de preço, a cobrança para filas e tópicos/assinaturas está mudando. Essas entidades estão fazendo a transição de cobrança por mensagem para cobrança por operação. Uma “operação” se refere a qualquer chamada à API feita em relação a um ponto de extremidade de serviço de fila ou tópico/assinatura. Isso inclui operações de gerenciamento, envio/recebimento e estado da sessão.

|Tipo de operação|Descrição|
|---|---|
|Gerenciamento|Criar, ler, atualizar, excluir (CRUD) em relação a filas ou tópicos/assinaturas.|
|Mensagens|Enviando e recebendo mensagens com filas ou tópicos/assinaturas.|
|Estado de sessão|Obter ou definir o estado de sessão em uma fila ou tópico/assinatura.|

Os seguintes preços entraram em vigor desde o dia 1° de novembro de 2014:

|Basic|Custo|
|---|---|
|Operações|US $0,05 por milhões de operações|

|Standard|Custo|
|---|---|
|Custo base|US $10/ mês|
|Primeiras 12,5 milhões de operações/mês|Incluso|
|12,5 a 100 milhões de operações/mês|US $0,80 por cada milhão de operações|
|100 milhões a 2,5 bilhões de operações/mês|US $0,50 por cada milhão de operações|
|Mais do que 2,5 bilhões de operações/mês|US $0,20 por cada milhão de operações|

>[AZURE.NOTE]A camada Premium está atualmente em fase prévia de apresentação e o preço a seguir reflete 50% de desconto sobre essa fase.

|Premium|Custo|
|---|---|
|Diário|Taxa fixa de US $11.13 por unidade de mensagem|

## Conexões orientadas

*Conexões orientadas* acomodam padrões de uso do cliente que envolvem um grande número de remetentes/destinatários "persistentemente conectados" em filas, tópicos/assinaturas ou Hubs de eventos. Remetentes/destinatários persistentemente conectados são aqueles que se conectam com AMQP ou HTTP diferente com tempo limite de recebimento diferente de zero (por exemplo, sondagem longa de HTTP). Remetentes e receptores HTTP com um tempo limite imediato não geram conexões orientadas.

Anteriormente, filas e tópicos/assinaturas tinham um limite de 100 conexões simultâneas por URL. O esquema de cobrança atual remove o limite por URL para filas e tópicos/assinaturas e implementa cotas e medição em conexões orientadas no namespace do Barramento de Serviço e níveis de assinatura do Azure.

A camada Basic inclui e é, estritamente limitada, a 100 conexões orientadas por namespace de Barramento de Serviço. Conexões acima desse número serão rejeitadas na camada Basic. A camada Standard remove o limite por namespace e conta o uso total de conexão orientada na assinatura do Azure. Na camada Standard, serão permitidas 1.000 conexões orientadas por assinatura do Azure sem custo (além do custo base) adicional. O uso de mais de um total de 1.000 conexões orientadas nos namespaces de Barramento de Serviço da camada Standard em uma assinatura do Azure será cobrado em um agendamento classificado, como mostrado na tabela a seguir.

|Conexões orientadas (camada Standard)|Custo|
|---|---|
|Primeiras 1.000/mês|Incluído no custo base|
|1\.000 a 100.000/mês|US $0,03 por conexão/mês|
|100\.000 a 500.000/mês|US $0,025 por conexão/mês|
|Mais de 500.000/mês|US $0,015 por conexão/mês|

>[AZURE.NOTE]A camada de mensagens Standard inclui 1.000 conexões orientadas (no custo base) que podem ser compartilhados entre todas as filas, tópicos/assinaturas e Hubs de eventos na assinatura do Azure associada.

>[AZURE.NOTE]A cobrança baseia-se no número máximo de conexões simultâneas e é rateada por hora com base em 744 horas por mês.

|Camada Premium
|---|
|As conexões orientadas não são cobradas na camada Premium.|

Para obter mais informações sobre conexões orientadas, consulte a seção de [Perguntas frequentes](#FAQ) mais adiante neste tópico.

## Retransmissão

Retransmissões estão disponíveis apenas nos namespaces de camada Standard. Caso contrário, as cotas de preços e conexão para retransmissões permanecem inalteradas. Isso significa que as retransmissões continuarão a ser cobradas sobre o número de mensagens (não operações) e horas de retransmissão.

|Preços de retransmissão|Custo|
|---|---|
|Horas de retransmissão|US $0,10 por cada 100 horas de retransmissão|
|Mensagens|US $0,01 para cada 10.000 mensagens|

## Perguntas frequentes

### Como é calculado o medidor de horas de retransmissão?

Veja [Neste tópico](service-bus-pricing-faq.md#How-is-the-Relay-Hours-meter-calculated?).

### O que são conexões orientadas e como serei cobrado para elas?

Uma conexão orientada é definida como uma das conexões a seguir:

1. Uma conexão AMQP de um cliente para um tópico/assinatura, fila ou Hub de eventos do Barramento de Serviço.

2. Uma chamada HTTP para receber uma mensagem de uma fila ou tópico do Barramento de Serviço que possua um valor de tempo limite de recebimento maior que zero.

Encargos de Barramento de Serviço para o número máximo de conexões orientadas simultâneas orientadas que excedem a quantidade incluída (1.000 na camada Standard). Os picos são medidos por hora, rateados dividindo por 744 horas por mês e adicionados ao longo do período de faturamento mensal. A quantidade incluída (1.000 conexões orientadas por mês) é aplicada ao final do período de cobrança em relação à soma dos picos de hora em hora rateados.

Por exemplo:

1. Cada um dos 10.000 dispositivos se conecta por meio de uma única conexão AMQP e recebe comandos de um tópico do Barramento de Serviço. Os dispositivos enviam eventos de telemetria para um Hub de eventos. Se todos os dispositivos ficam conectados 12 horas por dia, os encargos de conexão a seguir se aplicam (além de quaisquer outros encargos de tópico do Barramento de Serviço): 10.000 conexões * 12 horas * 31 dias / 744 = 5.000 conexões orientadas. Após o limite mensal de 1.000 conexões orientadas, você seria cobrado por 4.000 conexões orientadas, a uma taxa de US $0,03 por conexão orientada, para um total de US $120.

2. 10\.000 dispositivos recebem mensagens de uma fila do Barramento de Serviço por meio de HTTP, especificando um tempo limite diferente de zero. Se todos os dispositivos ficam conectados 12 horas por dia, os encargos de conexão a seguir se aplicam (além de quaisquer outros encargos de tópico do Barramento de Serviço): 10.000 conexões de recebimento de HTTP * 12 horas * 31 dias / 744 = 5.000 conexões orientadas.

### Os encargos de conexão gerenciada são aplicáveis a filas e tópicos/assinaturas?

Sim. Não há cobranças de conexão para enviar eventos usando HTTP, independentemente do número de sistemas ou dispositivos remetentes. O recebimento de eventos com HTTP usando um tempo limite maior que zero, às vezes chamado de "sondagem longa", gera encargos de conexão gerenciada. Conexões do AMQP geram encargos de conexão gerenciada independentemente se as conexões estão sendo usadas para enviar ou receber. Observe que são permitidas 100 conexões orientadas gratuitamente em um namespace da camada Basic. Esse também é o número máximo de conexões orientadas permitidas para a assinatura do Azure. As primeiras 1.000 conexões orientadas em todos os namespaces da camada Standard em uma assinatura do Azure são incluídas sem custo adicional (além do custo base). Como essas permissões são suficientes para cobrir muitos cenários de mensagens de serviço para serviço, os encargos de conexão gerenciada normalmente apenas se tornam relevantes se você planeja usar sondagem longa AMQP ou HTTP com um grande número de clientes; por exemplo, para obter um fluxo de eventos mais eficiente ou habilitar a comunicação bidirecional com muitos dispositivos ou instâncias do aplicativo.

## Consulte também

[Perguntas Frequentes sobre Preços do Barramento de Serviço](service-bus-pricing-faq.md)

<!---HONumber=Sept15_HO3-->