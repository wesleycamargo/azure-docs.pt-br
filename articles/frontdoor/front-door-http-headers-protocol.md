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
ms.openlocfilehash: 0dcb769627714be9da55faf2a8e82c8750789498
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "47038843"
---
# <a name="azure-front-door-service---http-headers-protocol-support"></a>Azure Front Door Service – suporte para protocolo de cabeçalhos HTTP
Este documento descreve o protocolo ao qual o Azure Front Door Service dá suporte com várias partes do caminho da chamada, conforme ilustrado pela imagem abaixo. As seções a seguir fornecem mais insight sobre os cabeçalhos HTTP compatíveis com o Front Door.

![Protocolo de cabeçalhos HTTP do Azure Front Door Service][1]

>[!WARNING]
>O Azure Front Door Service não oferece garantias para os cabeçalhos HTTP que não estão documentados aqui.

## <a name="1-client-to-front-door"></a>1. Cliente para o Front Door
O Front Door aceita a maioria dos cabeçalhos de solicitação de entrada (sem modificá-los), no entanto, há alguns cabeçalhos reservados que serão removidos da solicitação de entrada se forem enviados. Isso inclui os cabeçalhos com os seguintes prefixos:
 - X-FD*
 - X-MS*

## <a name="2-front-door-to-backend"></a>2. Front Door para o back-end

O Front Door incluirá os cabeçalhos da solicitação de entrada, a menos que eles tenham sido removidos devido às restrições mencionadas acima. O Front Door também adicionará os seguintes cabeçalhos:

| Cabeçalho  | Exemplo e descrição |
| ------------- | ------------- |
| X-MS-Ref |  *X-MS-Ref: 0WrHgWgAAAACFupORp/8MS6vxhG/WUvawV1NURURHRTAzMjEARWRnZQ==* </br> Esse é uma cadeia de caracteres de referência exclusiva que identifica uma solicitação atendida pelo Front Door. Ele é essencial para solucionar problemas porque é usado para pesquisar os logs de acesso.|
| X-MS-RequestChain |  *X-MS-RequestChain: hops=1* </br> Esse é um cabeçalho que o Front Door usa para detectar loops de solicitação, e os usuários não devem usar uma dependência nele. |
| X-MS-Via |  *X-MS-Via: Azure* </br> É adicionado pelo Front Door para indicar que o Azure/Front Door era um destinatário intermediário da solicitação entre o cliente e o back-end. |

## <a name="3-front-door-to-client"></a>3. Front Door para o cliente

A seguir estão os cabeçalhos que são enviados do Front Door para os clientes. Todos os cabeçalhos enviados para o Front Door do back-end também são passados para o cliente.

| Cabeçalho  | Exemplo |
| ------------- | ------------- |
| X-MS-Ref |  *X-MS-Ref: 0WrHgWgAAAACFupORp/8MS6vxhG/WUvawV1NURURHRTAzMjEARWRnZQ==* </br> Esse é uma cadeia de caracteres de referência exclusiva que identifica uma solicitação atendida pelo Front Door. Ele é essencial para solucionar problemas porque é usado para pesquisar os logs de acesso.|

## <a name="next-steps"></a>Próximas etapas

- Saiba como [criar um Front Door](quickstart-create-front-door.md).
- Saiba [como o Front Door funciona](front-door-routing-architecture.md).

<!--Image references-->
[1]: ./media/front-door-http-headers-protocol/front-door-protocol-summary.png