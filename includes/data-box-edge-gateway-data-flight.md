---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: bea14544949f90290bf3878295b49f1e08be9eea
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/17/2019
ms.locfileid: "59684388"
---
Para os dados em trânsito:

- Para dados que passa entre o dispositivo e o Azure, o TLS 1.2 padrão é usado. Não há nenhum fallback para o TLS 1.1 e versões anteriores. A comunicação do agente será bloqueada se não há suporte para o TLS 1.2. O TLS 1.2 também é necessário para operações de gerenciamento de SDK e portal.
- Quando os clientes acessam seu dispositivo por meio da interface do usuário web local em um navegador, o TLS 1.2 padrão é usado como o protocolo de segurança padrão.

    - A prática recomendada é configurar seu navegador para usar o TLS 1.2.
    - Se o navegador não oferece suporte a TLS 1.2, você pode usar o TLS 1.1 ou TLS 1.0.
- Para proteger os dados ao copiar de seus servidores de dados, é recomendável que você use o SMB 3.0 com criptografia.
