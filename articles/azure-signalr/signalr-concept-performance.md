---
title: Guia de desempenho do serviço do Azure SignalR
description: Uma visão geral do desempenho do serviço do Azure SignalR.
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: zhshang
ms.openlocfilehash: 53139dd253c491ea6578fd0b9cbada4e7b331c7d
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/11/2019
ms.locfileid: "59502032"
---
# <a name="performance-guide-for-azure-signalr-service"></a>Guia de desempenho do serviço do Azure SignalR

Um dos principais benefícios para usar o serviço do Azure SignalR é a facilidade para dimensionar os aplicativos do SignalR. Em um cenário em larga escala, o desempenho se torna um fator importante. Este guia, apresentamos os fatores que têm impacto no desempenho do aplicativo SignalR e, em cenários de caso de uso diferentes, o que é o desempenho típico? No final, podemos também apresentará o ambiente e as ferramentas usadas para gerar o relatório de desempenho.

## <a name="terms-definition"></a>Definição de termos

*ASRS*: Serviço Azure SignalR

*Entrada*: a mensagem de entrada para o serviço do Azure SignalR

*Saída*: mensagem de saída do serviço do Azure SignalR

*Largura de banda*: tamanho total de todas as mensagens em 1 segundo

*Modo padrão*: ASRS espera que o servidor de aplicativo para estabelecer conexão com ele antes de aceitar todas as conexões de cliente. É o modo de trabalho padrão quando um ASRS foi criado.

*Modo sem servidor*: ASRS aceita apenas conexões de cliente. Nenhuma conexão de servidor é permitido.

## <a name="overview"></a>Visão geral

ASRS define sete camadas Standard para capacidades de desempenho diferentes, e este guia pretende responder às seguintes perguntas:

-   O que é o desempenho ASRS típico para cada camada?

-   ASRS atender aos meus requisitos de taxa de transferência de mensagem, por exemplo, enviar mensagens de 100.000 por segundo?

-   Para o meu cenário específico, qual camada é adequada para mim? Ou, como selecionar a camada correta?

-   Que tipo de servidor de aplicativos (tamanho da VM) é adequado para mim e quantas delas deverão implantar?

Para responder a essas perguntas, este guia de desempenho primeiro fornece uma explicação de alto nível sobre os fatores que têm impacto no desempenho, em seguida, ilustra as mensagens de entrada e saídas máximas para cada camada para casos de uso típico: **echo**, **difusão**, **envio ao grupo**, e **send-conexão** (ponto a ponto bate-papo).

É impossível para este documento cobrir todos os cenários (e diferentes casos de uso, o tamanho de mensagem diferente ou padrão de envio de mensagens etc.). No entanto, ele fornece alguns métodos de avaliação para ajudar os usuários a aproximadamente avaliar suas necessidades das mensagens de entrada ou saídas e, em seguida, encontrar os níveis apropriados, verificando a tabela de desempenho.

## <a name="performance-insight"></a>Análise de desempenho

Esta seção descreve as metodologias de avaliação de desempenho, em seguida, lista todos os fatores que têm impacto no desempenho. No final, ele fornece métodos para ajudar a avaliar os requisitos de desempenho.

### <a name="methodology"></a>Metodologia

**Taxa de transferência** e **latência** são dois aspectos típicos de verificação de desempenho. Para ASRS, a camada SKU diferente tem diferentes taxas de transferência a política de limitação. Este documento define **o máximo permitido de taxa de transferência (largura de banda de entrada e saída)** conforme o máximo de taxa de transferência obtida quando 99% das mensagens tem latência menor que 1 segundo.

A latência é o período de tempo do que o envio de mensagem para receber a mensagem de resposta de ASRS de conexão. Vamos **echo** como exemplo, cada conexão de cliente adiciona um carimbo de hora na mensagem. O hub do servidor de aplicativo envia a mensagem original para o cliente. Portanto, o atraso de propagação é calculado facilmente cada conexão de cliente. O carimbo de hora está anexado para cada mensagem no **difusão**, **grupo de envio**, e **send-conexão**.

Para simular milhares de conexões de clientes simultâneos, criação de várias VMs em uma rede virtual privada no Azure. Todas essas VMs se conectar à mesma instância ASRS.

No modo padrão ASRS, VMs do servidor de aplicativo também são implantados na mesma rede privada virtual como VMs do cliente.

Todas as VMs de cliente e servidor de aplicativo que as VMs são implantadas na mesma rede da mesma região para evitar entre a latência de região.

### <a name="performance-factors"></a>Fatores de desempenho

Teoricamente, a capacidade ASRS é limitada pelos recursos de computação: CPU, memória e rede. Por exemplo, mais conexões a ASRS, mais memória ASRS consumidos. Para o tráfego de mensagem maior, por exemplo, cada mensagem é maior do que 2048 bytes, ele requer ASRS gastar mais ciclos de CPU para processar, bem. Enquanto isso, a largura de banda de rede do Azure também impõe um limite para o tráfego máximo.

O tipo de transporte [WebSocket](https://en.wikipedia.org/wiki/WebSocket), [eventos enviados servidor](https://en.wikipedia.org/wiki/Server-sent_events), ou [sondagem longa](https://en.wikipedia.org/wiki/Push_technology), é outro fator afeta o desempenho. WebSocket é um protocolo de comunicação bidirecional e full-duplex ao longo de uma única conexão de TCP. No entanto, o evento enviado servidor é o protocolo de unidirecional à mensagem de envio por push do servidor ao cliente. Sondagem longa requer que os clientes realizem chamada seletiva periodicamente informações de servidor por meio de solicitação HTTP. Para a mesma API sob a mesma condição, WebSocket tem o melhor desempenho, eventos enviados servidor é mais lento e sondagem longa é o mais lento. ASRS recomenda WebSocket por padrão.

Além disso, o custo de roteamento de mensagem também limita o desempenho. ASRS desempenha uma função como um roteador de mensagem, que roteia a mensagem de um conjunto de clientes ou servidores para outros clientes ou servidores. Cenário diferente ou a API requer a política de roteamento diferente. Para **echo**, o cliente envia uma mensagem para si mesmo e o destino de roteamento também é em si. Esse padrão é o custo mais baixo de roteamento. Mas para **difusão**, **grupo de envio**, **send para conexão**, ASRS precisa procurar as conexões de destino através da estrutura de dados distribuídos interno, que consome mais CPU, memória e até mesmo largura de banda de rede. Como resultado, o desempenho é mais lento do que **echo**.

No modo padrão, o servidor de aplicativo também pode se tornar um gargalo para determinados cenários, porque o SDK do Azure SignalR tem que invocar o Hub, enquanto isso, ele mantém a conexão ao vivo com cada cliente por meio de pulsação sinais.

No modo sem servidor, o cliente envia a mensagem por HTTP post, que não é tão eficiente quanto o WebSocket.

Outro fator é o protocolo: JSON e [MessagePack](https://msgpack.org/index.html). MessagePack é menor em tamanho e entregue mais rapidamente do que o JSON. Intuitivamente, MessagePack pode se beneficiar de desempenho, mas o desempenho de ASRS não é afetado com protocolos, pois ele não decodificar o conteúdo da mensagem durante o encaminhamento de mensagens de clientes para servidores ou vice-versa.

Em resumo, os seguintes fatores têm impacto sobre a capacidade de entrada e saída:

-   Camada de SKU (CPU/memória)

-   número de conexões

-   Tamanho da mensagem

-   taxa de envio de mensagem

-   tipo de transporte (WebSocket/servidor-enviado-evento/sondagem longa)

-   Use o cenário de caso (roteamento custo)

-   conexões de servidor e o serviço de aplicativo (no modo de servidor)


### <a name="find-a-proper-sku"></a>Encontrar um SKU adequado

Como avaliar a capacidade de entrada/saída ou como localizar qual camada é adequada para um caso de uso específico?

Vamos supor que o servidor de aplicativos é poderoso o suficiente e não é o gargalo de desempenho. Em seguida, podemos verificar a largura de banda máxima de entrada e saída para cada camada.

#### <a name="quick-evaluation"></a>Avaliação rápida

Vamos simplificar a avaliação pela primeira vez, supondo que algumas configurações padrão: WebSocket é usado, o tamanho de mensagem é 2048 bytes, enviar mensagem a cada segundo, e ele está no modo padrão.

Cada camada tem seu próprio máxima largura de banda de entrada e saída. Experiência do usuário uniforme não é garantida depois que a entrada ou saída excede o limite.

**Echo** proporciona a máxima largura de banda de entrada porque ela tem o custo mais baixo de roteamento. **Difusão** define a largura de banda máxima de mensagem de saída.

Fazer **não** exceder os valores realçados nas duas tabelas a seguir.

|       Echo                        | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|-----------------------------------|-------|-------|-------|--------|--------|--------|---------|
| conexões                       | 1.000 | 2.000 | 5.000 | 10.000 | 20.000 | 50.000 | 100.000 |
| **Largura de banda (bytes/s) de entrada** | **2 MIN**    | **4M**    | **10 MILHÕES**   | **20 MILHÕES**    | **40M**    | **100 MB**   | **200M**    |
| Largura de banda de saída (bytes/s) | 2 MIN    | 4M    | 10 MILHÕES   | 20 MILHÕES    | 40M    | 100 MB   | 200M    |


|     Difusão             | Unit1 | Unit2 | Unit5  | Unit10 | Unit20 | Unit50  | Unit100 |
|---------------------------|-------|-------|--------|--------|--------|---------|---------|
| conexões               | 1.000 | 2.000 | 5.000  | 10.000 | 20.000 | 50.000  | 100.000 |
| Largura de banda (bytes/s) de entrada  | 4K    | 4K    | 4K     | 4K     | 4K     | 4K      | 4K     |
| **Largura de banda de saída (bytes/s)** | **4M**    | **8 M**    | **20 MILHÕES**    | **40M**    | **80 MB**    | **200M**    | **400 MILHÕES**   |

A largura de banda de entrada e fórmulas de largura de banda de saída:
```
  inboundBandwidth = inboundConnections * messageSize / sendInterval
  outboundBandwidth = outboundConnections * messageSize / sendInterval
```

*inboundConnections*: o número de conexões de envio de mensagem

*outboundConnections*: o número de conexões de recebimento de mensagem

*messageSize*: o tamanho de uma única mensagem (valor médio). Para a mensagem pequena cujo tamanho é menor que 1024 bytes, ele tem o impacto no desempenho semelhante como 1024 bytes mensagem.

*sendInterval*: a hora de envio de uma mensagem, normalmente ele é de 1 segundo por mensagem, o que significa que enviar uma mensagem por segundo. SendInterval menor significa enviar mais mensagens em determinado período de tempo. Por exemplo, 0,5 segundo por mensagem significa enviar duas mensagens a cada segundo.

*Conexões* é o limite máximo de ASRS confirmado para cada camada. Se o número de conexão for aumentado ainda mais, ele sofrerá da limitação de conexão.

*Largura de banda de entrada* e *largura de banda de saída* têm o tamanho total de mensagens por segundo. Aqui estou ' significa megabyte para manter a simplicidade.

#### <a name="evaluation-for-complex-use-cases"></a>Avaliação para casos de uso complexos

##### <a name="bigger-message-size-or-different-sending-rate"></a>Maior tamanho de mensagem ou a taxa de envio diferente

O caso de uso real é mais complicado. Ele pode enviar mensagens maiores do que 2048 bytes ou taxa de mensagem de envio é não uma mensagem por segundo. Vamos difusão do unit100 como um exemplo para saber como avaliar seu desempenho.

A tabela a seguir mostra um caso real de **difusão**, mas o tamanho da mensagem, a contagem de conexão e a taxa de envio da mensagem são diferentes das, supomos na seção anterior. A pergunta é como podemos deduzir qualquer um desses itens (tamanho da mensagem, contagem de conexão ou taxa de envio de mensagem) se sabemos apenas 2 deles.

| Difusão  | Tamanho da mensagem (bytes) | (Mensagem/s) de entrada | conexões | Enviar intervalos (segundo) |
|---|---------------------|--------------------------|-------------|-------------------------|
| 1 | 20 K                 | 1                        | 100.000     | 5                       |
| 2 | 256 K                | 1                        | 8.000       | 5                       |

A fórmula a seguir é facilmente ser inferido com base na fórmula anterior existente:

```
outboundConnections = outboundBandwidth * sendInterval / messageSize
```

Para unit100, sabemos que a máxima largura de banda de saída é 400M na tabela anterior, então para tamanho de 20 mil mensagens, as conexões de saída máximas devem ser 400 milhões \* 5 / 20 mil = 100.000, que corresponde ao valor real.

##### <a name="mixed-use-cases"></a>Casos de uso misto

O caso de uso real normalmente combina quatro casos de uso básico juntos: **echo**, **difusão**, **enviar para o grupo**, ou **enviar para conexão**. A metodologia usada para avaliar a capacidade é dividir os casos de uso misto em quatro casos de uso básico **calcular a largura de banda máxima de mensagem de entrada e saída** usando as fórmulas acima separadamente e somá-las para obter o total largura de banda de entrada/saída máxima. Em seguida, pegue a camada correta das tabelas de entrada/saída de largura de banda máxima.

Enquanto isso, para enviar a mensagem para centenas ou milhares de pequenos grupos ou milhares de clientes que enviam mensagens entre si, o custo de roteamento se tornará dominante. Esse impacto deve ser levado em conta. Mais detalhes são abordados nas seções de "Case study" a seguir.

Para o caso de uso de envio de mensagem para os clientes, certifique-se do servidor de aplicativos **não** o gargalo. Seção de "Case study" oferece as diretrizes sobre quantos servidores de aplicativo que você precisa e quantas conexões de servidor devem ser configuradas.

## <a name="case-study"></a>Análise

As seções a seguir percorrem quatro casos de uso típico para o transporte de WebSocket: **echo**, **difusão**, **enviar ao grupo**, e **send-conexão** . Para cada cenário, ele lista o ASRS atual de entrada e a capacidade de saída, enquanto isso explica o que é os principais fatores no desempenho.

Em default o modo de servidor de aplicativo, por meio do SDK de serviço do Azure SignalR por padrão, cria cinco conexões de servidor com ASRS. No desempenho do resultado abaixo, as conexões são aumentadas 15 (ou mais para difusão e enviar mensagem ao grupo grande) do servidor de teste.

Diferentes casos de uso têm requisitos diferentes nos servidores do aplicativo. **Difusão** precisa pequeno número de servidores de aplicativo. **Echo** ou **send-conexão** precisa de muitos servidores de aplicativo.

Em todos os casos de uso, o tamanho da mensagem padrão é 2048 bytes e o intervalo de envio de mensagem é de 1 segundo.

## <a name="default-mode"></a>Modo padrão

Os clientes, servidores de aplicativo web e ASRS estão envolvidos neste modo. Todos os clientes significa uma única conexão.

### <a name="echo"></a>Echo

Em primeiro lugar, os aplicativos web se conectar ao ASRS. Em segundo lugar, muitos clientes se conectam ao aplicativo web, que redirecionar os clientes para ASRS com o token de acesso e o ponto de extremidade. Em seguida, os clientes estabelecem conexões WebSocket com ASRS.

Depois que todos os clientes estabelecem conexões, elas comecem a enviar a mensagem, que contém um carimbo de hora para o Hub específico a cada segundo. O Hub retorna a mensagem de volta ao cliente original. Todos os clientes calcula a latência quando ele recebe a mensagem de eco de volta.

As etapas 5\~8 (tráfego realçado vermelho) estão em um loop, que será executado por uma duração padrão (5 minutos) e obter a estatística de latência de todas as mensagens.
O guia de desempenho mostra o máximo número de conexão do cliente.

![Echo](./media/signalr-concept-performance/echo.png)

**Echo**do comportamento determina que a máxima largura de banda de entrada é igual a máxima largura de banda de saída. Consulte a tabela a seguir.

|       Echo                        | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|-----------------------------------|-------|-------|-------|--------|--------|--------|---------|
| conexões                       | 1.000 | 2.000 | 5.000 | 10.000 | 20.000 | 50.000 | 100.000 |
| Entrada/saída (mensagem/s) | 1.000 | 2.000 | 5.000 | 10.000 | 20.000 | 50.000 | 100.000 |
| Largura de banda de entrada/saída (bytes/s) | 2 MIN    | 4M    | 10 MILHÕES   | 20 MILHÕES    | 40M    | 100 MB   | 200M    |

Nesse caso, cada cliente invoca o hub definido no servidor de aplicativo. O hub apenas chama o método definido no lado do cliente original. Esse hub é o hub de ponderados mais claro para **echo**.

```
        public void Echo(IDictionary<string, object> data)
        {
            Clients.Client(Context.ConnectionId).SendAsync("RecordLatency", data);
        }
```

Mesmo para esse hub simple, a pressão de tráfego no servidor de aplicativo também é destacada como o **echo** aumentos de mensagem de entrada. Portanto, ele requer muitos servidores de aplicativo para as camadas SKU grandes. A tabela a seguir lista a contagem do servidor de aplicativo para cada camada.


|    Echo          | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| conexões      | 1.000 | 2.000 | 5.000 | 10.000 | 20.000 | 50.000 | 100.000 |
| Contagem de servidor de aplicativo | 2     | 2     | 2     | 3      | 3      | 10     | 20      |

> [!NOTE]
>
> O cliente conexão número, tamanho da mensagem, a taxa, a camada de SKU e a CPU/memória do servidor de aplicativo está enviando a mensagem ter impacto no desempenho geral do **echo**.

### <a name="broadcast"></a>Difusão

Para **difusão**, quando o aplicativo web recebe a mensagem, ele transmite a todos os clientes. Quanto mais clientes para transmitir o tráfego de mensagens mais a todos os clientes. Consulte o diagrama a seguir.

![Difusão](./media/signalr-concept-performance/broadcast.png)

A característica de difusão é que há um pequeno número de clientes de difusão, que significa que a largura de banda de mensagem de entrada for pequena, mas a largura de banda de saída é enorme. A largura de banda de saída de mensagem aumenta à medida que a conexão de cliente ou aumenta a taxa de difusão.

As conexões de cliente máximo, contagem de mensagens de entrada/saída e largura de banda são resumidas na tabela a seguir.

|     Difusão             | Unit1 | Unit2 | Unit5  | Unit10 | Unit20 | Unit50  | Unit100 |
|---------------------------|-------|-------|--------|--------|--------|---------|---------|
| conexões               | 1.000 | 2.000 | 5.000  | 10.000 | 20.000 | 50.000  | 100.000 |
| (Mensagem/s) de entrada  | 2     | 2     | 2      | 2      | 2      | 2       | 2       |
| Saída (mensagem/s) | 2.000 | 4.000 | 10.000 | 20.000 | 40.000 | 100.000 | 200.000 |
| Largura de banda (bytes/s) de entrada  | 4K    | 4K    | 4K     | 4K     | 4K     | 4K      | 4K      |
| Largura de banda de saída (bytes/s) | 4M    | 8 M    | 20 MILHÕES    | 40M    | 80 MB    | 200M    | 400 MILHÕES    |

Os clientes de difusão que postagem mensagens são não mais do que 4, portanto, requer menos servidores de aplicativo em comparação com **echo** , pois a quantidade de mensagem de entrada for pequena. Dois servidores de aplicativo são suficientes para consideração de SLA e o desempenho. Mas as conexões de servidor padrão devem ser aumentadas para evitar o problema desbalanceado especialmente para Unit50 e Unit100.

|   Difusão      | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| conexões      | 1.000 | 2.000 | 5.000 | 10.000 | 20.000 | 50.000 | 100.000 |
| Contagem de servidor de aplicativo | 2     | 2     | 2     | 2      | 2      | 2      | 2       |

> [!NOTE]
>
> Aumente as conexões de servidor padrão de 5 a 40 em cada servidor de aplicativo para evitar conexões de servidor desbalanceada possíveis para ASRS.
>
> O número de conexão do cliente, o tamanho de mensagem, taxa de envio de mensagem e camada de SKU tem impacto no desempenho geral para **difusão**

### <a name="send-to-group"></a>Enviar para o grupo

**Grupo de envio** tem padrão de tráfego semelhante, exceto que, após o estabelecimento de conexões de WebSocket com ASRS de clientes, eles devem ingressar em grupos antes de enviar mensagens a um grupo específico. O fluxo de tráfego é ilustrado pelo diagrama a seguir.

![Enviar ao grupo](./media/signalr-concept-performance/sendtogroup.png)

Membro do grupo e a contagem de grupo são dois fatores com impacto no desempenho. Para simplificar a análise, podemos definir dois tipos de grupos: grupo de pequeno e grande.

- `small group`: 10 conexões em cada grupo. O número de grupo é igual a (contagem de conexão máximo) / 10. Por exemplo, para 1 unidade, se houver contagens de conexão de 1000, em seguida, temos 1000 / 10 = 100 grupos.

- `Big group`: Número de grupo é sempre 10. A contagem de membros do grupo é igual a (contagem de conexão máximo) / 10. Por exemplo, para 1 unidade, se houver contagens de conexão de 1000, em seguida, cada grupo tem 1000 / 10 = 100 membros.

**Grupo de envio** traz o custo de roteamento para ASRS porque ele encontrou as conexões de destino por meio de uma estrutura de dados distribuídos. À medida que aumentam as conexões de envio, o custo aumenta também.

#### <a name="small-group"></a>Grupo pequeno

O custo de roteamento é significativo para enviar mensagens para muitos pequenos grupos. Atualmente, a implementação de ASRS atinge o limite de custo de roteamento em unit50. Adicionando mais CPU e memória não ajudar, assim, não é possível melhorar o unit100 ainda mais por design. Se você exigir entrada mais largura de banda, entre em contato com o atendimento ao cliente para personalização.

|   Enviar para um pequeno grupo     | Unit1 | Unit2 | Unit5  | Unit10 | Unit20 | Unit50 | Unit100 |
|---------------------------|-------|-------|--------|--------|--------|--------|---------|
| conexões               | 1.000 | 2.000 | 5.000  | 10.000 | 20.000 | 50.000 | 100.000
| Contagem de membros do grupo        | 10    | 10    | 10     | 10     | 10     | 10     | 10 
| Contagem de grupo               | 100   | 200   | 500    | 1.000  | 2.000  | 5.000  | 10.000 
| (Mensagem/s) de entrada  | 200   | 400   | 1.000  | 2.500  | 4.000  | 7.000  | 7.000   |
| Largura de banda (bytes/s) de entrada  | 400 K  | 800 K  | 2 MIN     | 5 M     | 8 M     | 14M    | 14M     |
| Saída (mensagem/s) | 2.000 | 4.000 | 10.000 | 25.000 | 40.000 | 70,000 | 70,000  |
| Largura de banda de saída (bytes/s) | 4M    | 8 M    | 20 MILHÕES    | 50 MILHÕES     | 80 MB    | M 140   | M 140    |

Há muitas conexões de cliente chamando o hub, portanto, o número de servidor de aplicativo também é fundamental para desempenho. A contagem de servidor de aplicativo sugerida é listada na tabela a seguir.

|  Enviar para um pequeno grupo   | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| conexões      | 1.000 | 2.000 | 5.000 | 10.000 | 20.000 | 50.000 | 100.000 |
| Contagem de servidor de aplicativo | 2     | 2     | 2     | 3      | 3      | 10     | 20      |

> [!NOTE]
>
> O número de conexão do cliente, o tamanho de mensagem, taxa de envio de mensagem, custo de roteamento, camada de SKU e CPU/memória do servidor de aplicativos tem impacto no desempenho geral do **envio pequeno grupo**.

#### <a name="big-group"></a>Grupo grande

Para **send-para-big-group**, a largura de banda de saída se torna o gargalo antes de atingir o roteamento de limite de custo. A tabela a seguir lista a saída largura de banda máxima, que é quase o mesmo como **difusão**.

|    Enviar para um grupo grande      | Unit1 | Unit2 | Unit5  | Unit10 | Unit20 | Unit50  | Unit100 |
|---------------------------|-------|-------|--------|--------|--------|---------|---------|
| conexões               | 1.000 | 2.000 | 5.000  | 10.000 | 20.000 | 50.000  | 100.000
| Contagem de membros do grupo        | 100   | 200   | 500    | 1.000  | 2.000  | 5.000   | 10.000 
| Contagem de grupo               | 10    | 10    | 10     | 10     | 10     | 10      | 10
| (Mensagem/s) de entrada  | 20    | 20    | 20     | 20     | 20     | 20      | 20      |
| Largura de banda (bytes/s) de entrada  | 80 K   | 40 K   | 40 K    | 20 K    | 40 K    | 40 K     | 40 K     |
| Saída (mensagem/s) | 2.000 | 4.000 | 10.000 | 20.000 | 40.000 | 100.000 | 200.000 |
| Largura de banda de saída (bytes/s) | 8 M    | 8 M    | 20 MILHÕES    | 40M    | 80 MB    | 200M    | 400 MILHÕES    |

A contagem de conexão de envio é não mais do que 40, a carga do servidor de aplicativos é pequena, assim, o número do aplicativo web sugerido também é pequeno.

|  Enviar para um grupo grande  | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| conexões      | 1.000 | 2.000 | 5.000 | 10.000 | 20.000 | 50.000 | 100.000 |
| Contagem de servidor de aplicativo | 2     | 2     | 2     | 2      | 2      | 2      | 2       |

> [!NOTE]
>
> Aumente as conexões de servidor padrão de 5 a 40 em cada servidor de aplicativo para evitar conexões de servidor desbalanceada possíveis para ASRS.
> 
> O número de conexão do cliente, tamanho da mensagem, taxa de envio de mensagem, custo de roteamento e camada de SKU tem impacto no desempenho geral do **send-para-big-group**.

### <a name="send-to-connection"></a>Enviar para a conexão

Nesse caso, quando os clientes estabelecem conexões com ASRS, cada cliente chama um hub especial para obter sua própria ID de conexão. O parâmetro de comparação de desempenho é responsável por coletar todas as IDs de conexão, em ordem aleatória-los e reatribuí-las a todos os clientes, como um destino de envio. Os clientes continuar enviando a mensagem para a conexão de destino até que o teste de desempenho seja concluído.

![Enviar ao cliente](./media/signalr-concept-performance/sendtoclient.png)

O roteamento de custo para **Send-conexão** é semelhante de **send pequeno grupo**.

À medida que aumenta a contagem de conexão, o desempenho geral é limitado pelo custo de roteamento. Unidade de 50 atingiu o limite. Como resultado, 100 de unidade não é possível aprimorar ainda mais.

A tabela a seguir é um resumo de estatística após a muitas rodadas de execução **send-conexão** benchmark

|   Enviar para a conexão   | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50          | Unit100         |
|------------------------------------|-------|-------|-------|--------|--------|-----------------|-----------------|
| conexões                        | 1.000 | 2.000 | 5.000 | 10.000 | 20.000 | 50.000          | 100.000         |
| Entrada / saída (mensagem/s) | 1.000 | 2.000 | 5.000 | 8.000  | 9.000  | 20.000 | 20.000 |
| Largura de banda de entrada / saída (bytes/s) | 2 MIN    | 4M    | 10 MILHÕES   | 16 M    | 18M    | 40M       | 40M       |

Esse caso de uso requer alta carga no lado do servidor de aplicativo. Ver o servidor de aplicativo sugerido contagem na tabela a seguir.

|  Enviar para a conexão  | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| conexões      | 1.000 | 2.000 | 5.000 | 10.000 | 20.000 | 50.000 | 100.000 |
| Contagem de servidor de aplicativo | 2     | 2     | 2     | 3      | 3      | 10     | 20      |

> [!NOTE]
>
> O número de conexão do cliente, o tamanho de mensagem, taxa de envio de mensagem, custo de roteamento, camada de SKU e CPU/memória do servidor de aplicativos tem impacto no desempenho geral do **send-conexão**.

### <a name="aspnet-signalr-echobroadcastsend-to-connection"></a>ASP.NET SignalR eco/difusão/send-para-conexão

ASRS fornece a mesma capacidade de desempenho para ASP.NET SignalR. Esta seção fornece a contagem de aplicativo web sugeridas para ASP.NET SignalR **echo**, **difusão**, e **send pequeno grupo**.

O teste de desempenho usa o aplicativo Web do Azure do [Standard S3 de plano de serviço](https://azure.microsoft.com/pricing/details/app-service/windows/) para ASP.NET SignalR.

- `echo`

|   Echo           | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| conexões      | 1.000 | 2.000 | 5.000 | 10.000 | 20.000 | 50.000 | 100.000 |
| Contagem de servidor de aplicativo | 2     | 2     | 4     | 4      | 8      | 32      | 40       |

- `broadcast`

|  Difusão       | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| conexões      | 1.000 | 2.000 | 5.000 | 10.000 | 20.000 | 50.000 | 100.000 |
| Contagem de servidor de aplicativo | 2     | 2     | 2     | 2      | 2      | 2      | 2       |

- `Send-to-small-group`

|  Enviar para um pequeno grupo     | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| conexões      | 1.000 | 2.000 | 5.000 | 10.000 | 20.000 | 50.000 | 100.000 |
| Contagem de servidor de aplicativo | 2     | 2     | 4     | 4      | 8      | 32      | 40       |

## <a name="serverless-mode"></a>Modo sem servidor

Os clientes e ASRS estão envolvidos neste modo. Todos os clientes significa uma única conexão. O cliente envia mensagens por meio da API REST para outro cliente ou difusão mensagens para todos.

Enviar mensagens de alta densidade por meio da API REST não é tão eficiente quanto o WebSocket, pois ele necessita para criar uma nova conexão de HTTP sempre - um adicional de custo no modo sem servidor.

### <a name="broadcast-through-rest-api"></a>Transmissão por meio da API REST
Todos os clientes estabeleçam conexões de WebSocket com ASRS. Em seguida, alguns clientes de inicio por meio da API REST. A mensagem é enviada (entrada) são tudo através de HTTP Post, que não é eficiente em comparação com WebSocket.

|   Transmissão por meio da API REST     | Unit1 | Unit2 | Unit5  | Unit10 | Unit20 | Unit50  | Unit100 |
|---------------------------|-------|-------|--------|--------|--------|---------|---------|
| conexões               | 1.000 | 2.000 | 5.000  | 10.000 | 20.000 | 50.000  | 100.000 |
| (Mensagem/s) de entrada  | 2     | 2     | 2      | 2      | 2      | 2       | 2       |
| Saída (mensagem/s) | 2.000 | 4.000 | 10.000 | 20.000 | 40.000 | 100.000 | 200.000 |
| Largura de banda (bytes/s) de entrada  | 4K    | 4K    | 4K     | 4K     | 4K     | 4K      | 4K      |
| Largura de banda de saída (bytes/s) | 4M    | 8 M    | 20 MILHÕES    | 40M    | 80 MB    | 200M    | 400 MILHÕES    |

### <a name="send-to-user-through-rest-api"></a>Enviar para o usuário por meio da API REST
O parâmetro de comparação atribui nomes de usuário para todos os clientes antes de começar a se conectar ao ASRS. Depois que os clientes estabeleceram conexões de WebSocket com ASRS, comecem a enviar mensagens para outras pessoas por meio de HTTP Post.

|   Enviar para o usuário por meio da API REST | Unit1 | Unit2 | Unit5  | Unit10 | Unit20 | Unit50  | Unit100 |
|---------------------------|-------|-------|--------|--------|--------|---------|---------|
| conexões               | 1.000 | 2.000 | 5.000  | 10.000 | 20.000 | 50.000  | 100.000 |
| (Mensagem/s) de entrada  | 300   | 600   | 900    | 1,300  | 2.000  | 10.000  | 18,000  |
| Saída (mensagem/s) | 300   | 600   | 900    | 1,300  | 2.000  | 10.000  | 18,000 |
| Largura de banda (bytes/s) de entrada  | 600 K  | 1.2 M  | 1.8 M   | 2.6 M   | 4M     | 10 MILHÕES     | 36M    |
| Largura de banda de saída (bytes/s) | 600 K  | 1.2 M  | 1.8 M   | 2.6 M   | 4M     | 10 MILHÕES     | 36M    |

## <a name="performance-test-environments"></a>Ambientes de teste de desempenho

O teste de desempenho para todos os casos de uso listadas acima foram conduzidos no ambiente do Azure. Na maioria das VMs de cliente 50 e o servidor de aplicativo 20 VMs são usadas.

Tamanho VM do cliente: StandardDS2V2 (2 vCPU, memória de 7G)

Tamanho da VM do servidor de aplicativos: StandardF4sV2 (4 vCPU, memória 8g)

Conexões de servidor SignalR SDK do Azure: 15

## <a name="performance-tools"></a>Ferramentas de desempenho

https://github.com/Azure/azure-signalr-bench/tree/master/SignalRServiceBenchmarkPlugin

## <a name="next-steps"></a>Próximos passos

Neste artigo, você deve obter uma visão geral do desempenho do SignalR Service em cenários de caso de uso típico.

Para obter mais detalhes sobre as operações internas do SignalR Service e o dimensionamento do SignalR Service, leia o guia a seguir.

* [Recursos internos do serviço Azure SignalR](signalr-concept-internals.md)
* [Serviço do Azure SignalR dimensionamento](signalr-howto-scale-multi-instances.md)