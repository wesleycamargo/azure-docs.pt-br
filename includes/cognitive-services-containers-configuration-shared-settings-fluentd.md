---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 01/02/2019
ms.openlocfilehash: ffd17f7a641e1481aa4c88f8b2eb12ec11fa7d8b
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/23/2019
ms.locfileid: "56741443"
---
O Fluentd é um coletor de dados de software livre para registro em log unificado. As configurações do `Fluentd` gerenciam a conexão do contêiner com um servidor [Fluentd](https://www.fluentd.org). O contêiner inclui um provedor de registro em log do Fluentd, que permite que seu contêiner grave logs e, opcionalmente, dados de métrica para um servidor do Fluentd.

A tabela a seguir descreve as definições de configuração com suporte sob o `Fluentd` seção.

| Nome | Tipo de Dados | Descrição |
|------|-----------|-------------|
| `Host` | Cadeia | O endereço IP ou o nome do host DNS do servidor Fluentd. |
| `Port` | Inteiro | A porta do servidor Fluentd.<br/> O valor padrão é 24224. |
| `HeartbeatMs` | Inteiro | O intervalo de pulsação, em milissegundos. Se nenhum tráfego de evento tiver sido enviado antes que esse intervalo expire, uma pulsação será enviada ao servidor Fluentd. O valor padrão é 60.000 milissegundos (1 minuto). |
| `SendBufferSize` | Inteiro | O espaço de buffer de rede, em bytes, alocado para operações de envio. O valor padrão é 32768 bytes (32 kilobytes). |
| `TlsConnectionEstablishmentTimeoutMs` | Inteiro | O tempo de limite, em milissegundos, para estabelecer uma conexão SSL/TLS com o servidor do Fluentd. O valor padrão é 10.000 milissegundos (10 segundos).<br/> Se `UseTLS` for definido como false, esse valor é ignorado. |
| `UseTLS` | Booliano | Indica se o contêiner deve usar SSL / TLS para se comunicar com o servidor Fluentd. O valor padrão é falso. |