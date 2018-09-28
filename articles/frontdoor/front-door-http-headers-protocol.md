---
title: Azure Front Door Service – suporte para protocolo de cabeçalhos HTTP | Microsoft Docs
description: Este artigo ajuda você a entender os protocolos de cabeçalho HTTP compatíveis com o Front Door
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: sharadag
ms.openlocfilehash: efb3ffeb2e5fdde79fe3741377b8a06074671fea
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46951127"
---
# <a name="azure-front-door-service---http-headers-protocol-support"></a>Azure Front Door Service – suporte para protocolo de cabeçalhos HTTP
Este documento descreve o protocolo ao qual o Azure Front Door Service dá suporte com várias partes do caminho da chamada, conforme ilustrado pela imagem abaixo. As seções a seguir fornecem mais insight sobre os cabeçalhos HTTP compatíveis com o Front Door.

![Protocolo de cabeçalhos HTTP do Azure Front Door Service][1]

>[!WARNING]
>O Azure Front Door Service não oferece garantias para os cabeçalhos HTTP que não estão documentados aqui.

## <a name="1-client-to-front-door"></a>1. Cliente para o Front Door

Os cabeçalhos a seguir serão analisados pelo Front Door. Para outros cabeçalhos, o Front Door atuará como uma passagem e enviará os cabeçalhos para o back-end do aplicativo.

| Cabeçalho  | Exemplo e descrição |
| ------------- | ------------- |
| X-MSEdge-IG  | *X-MSEdge-IG: 2BC2B67F49ED47DFA47FBE2E962AC81F* </br> Um GUID que identifica uma ou mais solicitações HTTP feitas para renderizar uma única página da Web. O Front Door encaminhará o GUID ao back-end usando o cabeçalho X-FD-ImpressionGuid. </br> Se o cliente não enviar esse cabeçalho, o Front Door gerará automaticamente um novo GUID e o enviará ao back-end. |

## <a name="2-front-door-to-backend"></a>2. Front Door para o back-end

Os cabeçalhos a seguir serão enviados do Front Door para o back-end do aplicativo.

| Cabeçalho  | Exemplo e descrição |
| ------------- | ------------- |
| X-MS-Ref |  *X-FD-Ref: 0WrHgWgAAAACFupORp/8MS6vxhG/WUvawV1NURURHRTAzMjEARWRnZQ==* </br> A ID para identificar uma solicitação atendida pelo Front Door. O valor desse cabeçalho é a informação mais importante para a solução de problemas. Essa ID pode ser usada para pesquisar os logs de acesso. O Front Door enviará o mesmo cabeçalho tanto para o cliente quanto para o back-end. |
| X-FD-ClientHttpVersion | *X-FD-ClientHttpVersion: 1.1* </br>A versão do protocolo HTTP entre o cliente e o Front Door. Os valores possíveis são: 1.1, 2.0, etc. |
| X-FD-ClientIP | *X-FD-ClientIP: 131.107.192.35* </br>O endereço IP do "cliente" associado à solicitação que está sendo processada |
| X-FD-EdgeEnvironment | *X-FD-EdgeEnvironment: Edge-Prod-WSTr3* </br>O nó do Front Door específico que manipulou a solicitação. Esse cabeçalho pode ser registrado pelo back-end para auxiliar nos esforços de depuração. No entanto, não deve ser considerada nenhuma dependência de convenções de nomenclatura do Front Door, distribuição de tráfego entre os nós do Front Door ou disponibilidade de nós individuais específicos. |
| X-FD-EventID | *X-FD-EventId: FB805145C0124400915BE0E180F3A159* </br>Um GUID (identificador exclusivo) criado no início do processamento da solicitação. Todos os logs de acesso criados durante o processamento dessa solicitação serão associados ao mesmo GUID. |
| X-FD-ImpressionGuid | *X-FD-ImpressionGuid: 2BC2B67F49ED47DFA47FBE2E962AC81F* </br>Um GUID que identifica uma ou mais solicitações HTTP feitas para renderizar uma única página da Web. O lado do cliente pode especificar esse GUID: </br>– Enviando o cabeçalho de solicitação *X-MSEdge-IG*. Por exemplo: X-MSEdge-IG: 2BC2B67F49ED47DFA47FBE2E962AC81F </br>– Enviando a cadeia de consulta "ig". Por exemplo: *?ig=2BC2B67F49ED47DFA47FBE2E962AC81F*. Se o cliente não enviar um GUID, o Front Door o criará. |
| X-FD-OriginalURL | *X-FD-OriginalURL: http://www.contoso.com:80/* </br> A URL da solicitação original feita pelo cliente. |
| X-FD-Partner | *X-FD-Partner: ARM-contoso.azurefd.net_Default* </br>Identificador do perfil do Front Door para o qual a solicitação está sendo manipulada. Pode ser usado pelos back-ends que hospedam vários aplicativos de diferentes Front Doors como uma maneira de determinar para qual configuração de Front Door eles estão atendendo à solicitação atual. |
| X-FD-RequestHadClientId | Reservado |
| X-FD-ResponseHasClientId | Reservado |
| X-FD-SocketIP | *X-FD-SocketIP: 131.107.192.35* </br>O mesmo que o X-FD-ClientIP acima |

## <a name="3-front-door-to-client"></a>3. Front Door para o cliente

A seguir estão os cabeçalhos que são enviados do Front Door para os clientes. Todos os cabeçalhos enviados para o Front Door do back-end também são passados para o cliente.

| Cabeçalho  | Exemplo |
| ------------- | ------------- |
| X-MS-Ref  | *X-FD-Ref: 0WrHgWgAAAACFupORp/8MS6vxhG/WUvawV1NURURHRTAzMjEARWRnZQ==* </br>A ID para identificar uma solicitação atendida pelo Front Door. O valor desse cabeçalho é a informação mais importante para a solução de problemas. Essa ID pode ser usada para pesquisar os logs de acesso. O Front Door enviará o mesmo cabeçalho tanto para o cliente quanto para o back-end. |

## <a name="next-steps"></a>Próximas etapas

- Saiba como [criar um Front Door](quickstart-create-front-door.md).
- Saiba [como o Front Door funciona](front-door-routing-architecture.md).

<!--Image references-->
[1]: ./media/front-door-http-headers-protocol/front-door-protocol-summary.png