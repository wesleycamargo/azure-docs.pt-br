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
ms.openlocfilehash: b34ab417ab1d9ef77c3141d5aa130c338fb89188
ms.sourcegitcommit: 235cd1c4f003a7f8459b9761a623f000dd9e50ef
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/11/2019
ms.locfileid: "57726321"
---
# <a name="azure-front-door-service---http-headers-protocol-support"></a>Azure Front Door Service – suporte para protocolo de cabeçalhos HTTP
Este documento descreve o protocolo ao qual o Azure Front Door Service dá suporte com várias partes do caminho da chamada, conforme ilustrado pela imagem abaixo. As seções a seguir fornecem mais insight sobre os cabeçalhos HTTP compatíveis com o Front Door.

![Protocolo de cabeçalhos HTTP do Azure Front Door Service][1]

>[!WARNING]
>O Azure Front Door Service não oferece garantias para os cabeçalhos HTTP que não estão documentados aqui.

## <a name="1-client-to-front-door"></a>1. Cliente para o Front Door
O Front Door aceita a maioria dos cabeçalhos de solicitação de entrada (sem modificá-los), no entanto, há alguns cabeçalhos reservados que serão removidos da solicitação de entrada se forem enviados. Isso inclui os cabeçalhos com os seguintes prefixos:
 - X-FD-*

## <a name="2-front-door-to-backend"></a>2. Front Door para o back-end

O Front Door incluirá os cabeçalhos da solicitação de entrada, a menos que eles tenham sido removidos devido às restrições mencionadas acima. O Front Door também adicionará os seguintes cabeçalhos:

| Cabeçalho  | Exemplo e descrição |
| ------------- | ------------- |
| Através de |  *Via: 1.1 azure* </br> Porta da frente adiciona a versão HTTP do cliente seguido por 'Azure' como o valor para por meio do cabeçalho. Isso é adicionado para indicar a versão HTTP do cliente e essa porta da frente do Azure foi um destinatário intermediário para a solicitação entre o cliente e o back-end.  |
| X-Azure-ClientIP | *X-Azure-ClientIP: 127.0.0.1* </br> Representa o endereço de protocolo de Internet de "cliente" associado à solicitação sendo processada. Por exemplo, uma solicitação proveniente de um proxy pode adicionar o cabeçalho X-Forwarded-For para indicar o endereço IP do chamador original. |
| X-Azure-SocketIP |  *X-Azure-SocketIP: 127.0.0.1* </br> Representa o endereço de protocolo de soquete associado com a conexão TCP, a solicitação atual foi originada. Endereço de IP do cliente da solicitação não pode ser igual ao seu endereço IP de soquete porque ele pode ser arbitrariamente substituído por um usuário final.|
| X-Azure-Ref |  *X-Azure-Ref: 0zxV+XAAAAABKMMOjBv2NT4TY6SQVjC0zV1NURURHRTA2MTkANDM3YzgyY2QtMzYwYS00YTU0LTk0YzMtNWZmNzA3NjQ3Nzgz* </br> Esse é uma cadeia de caracteres de referência exclusiva que identifica uma solicitação atendida pelo Front Door. Ele é essencial para solucionar problemas porque é usado para pesquisar os logs de acesso.|
| X-Azure-RequestChain |  *X-Azure-RequestChain: hops=1* </br> Esse é um cabeçalho que o Front Door usa para detectar loops de solicitação, e os usuários não devem usar uma dependência nele. |
| X-Forwarded-For | *X-Forwarded-For: 127.0.0.1* </br> O campo de cabeçalho HTTP X-Forwarded-For (XFF) é um método comum de identificar o endereço IP de origem de um cliente conectado a um servidor web por meio de um balanceador de carga ou proxy HTTP. Se houver um cabeçalho XFF existente, em seguida, porta da frente acrescenta o IP de soquete de cliente a ele mais adiciona o cabeçalho XFF com o IP de soquete de cliente. |
| X-Forwarded-Host | *X-Forwarded-Host: contoso.azurefd.net* </br> O campo de cabeçalho HTTP X-Forwarded-Host é um método comum de identificar o host original solicitado pelo cliente no cabeçalho da solicitação HTTP de Host, já que o nome do host de porta de entrada pode ser diferente para o servidor de back-end que está tratando a solicitação. |
| X-Forwarded-Proto | *X-Forwarded-Proto: http* </br> O campo de cabeçalho HTTP X-Forwarded-Proto é um método comum de identificar o protocolo de origem de uma solicitação HTTP, já que, dependendo da configuração de porta da frente podem se comunicar com o back-end usando HTTPS, mesmo se a solicitação para o proxy reverso é HTTP. |

## <a name="3-front-door-to-client"></a>3. Front Door para o cliente

A seguir estão os cabeçalhos que são enviados do Front Door para os clientes. Todos os cabeçalhos enviados para o Front Door do back-end também são passados para o cliente.

| Cabeçalho  | Exemplo |
| ------------- | ------------- |
| X-Azure-Ref |  *X-Azure-Ref: 0zxV+XAAAAABKMMOjBv2NT4TY6SQVjC0zV1NURURHRTA2MTkANDM3YzgyY2QtMzYwYS00YTU0LTk0YzMtNWZmNzA3NjQ3Nzgz* </br> Esse é uma cadeia de caracteres de referência exclusiva que identifica uma solicitação atendida pelo Front Door. Ele é essencial para solucionar problemas porque é usado para pesquisar os logs de acesso.|

## <a name="next-steps"></a>Próximas etapas

- Saiba como [criar um Front Door](quickstart-create-front-door.md).
- Saiba [como o Front Door funciona](front-door-routing-architecture.md).

<!--Image references-->
[1]: ./media/front-door-http-headers-protocol/front-door-protocol-summary.png