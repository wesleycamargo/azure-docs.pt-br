---
title: Noções básicas sobre iDNS no Azure Stack | Microsoft Docs
description: Noções básicas sobre iDNS recursos e funcionalidades no Azure Stack
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/14/2019
ms.author: mabrigg
ms.reviewer: scottnap
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: 32d00a1c0daf383d41d6bbc7c9fe105b3a2a6ab1
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/12/2019
ms.locfileid: "57774908"
---
# <a name="introducing-idns-for-azure-stack"></a>Apresentando os iDNS para o Azure Stack

*Aplica-se a: Integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

iDNS é um recurso de rede do Azure Stack que permite a resolução de nomes DNS externos (por exemplo, https://www.bing.com.) ele também permite que você registre os nomes de rede virtual interna. Ao fazer isso, você pode resolver as VMs na mesma rede virtual por nome em vez de um endereço IP. Essa abordagem elimina a necessidade para fornecer entradas de servidor DNS personalizadas. Para obter mais informações sobre o DNS, consulte o [visão geral do DNS do Azure](https://docs.microsoft.com/azure/dns/dns-overview).

## <a name="what-does-idns-do"></a>O que faz iDNS?

Com iDNS no Azure Stack, você obtém os seguintes recursos, sem precisar especificar entradas de servidor DNS personalizadas:

- Compartilhados dos serviços de resolução de nome DNS para cargas de trabalho de locatário.
- Serviço DNS autoritativo para resolução de nome e o registro DNS dentro da rede virtual do locatário.
- Serviço de Recursive DNS para resolução de nomes de Internet de VMs do locatário. Locatários não precisam mais especificar entradas personalizadas de DNS para resolver nomes de Internet (por exemplo, www.bing.com.)

Você ainda pode trazer seu próprio DNS e usar servidores DNS personalizados. No entanto, usando iDNS, você pode resolver nomes DNS de Internet e se conectar a outras VMs na mesma rede virtual, você não precisará criar entradas DNS personalizadas.

## <a name="what-doesnt-idns-do"></a>O que não iDNS?

Quais iDNS não permite que você faça, é criar um registro DNS para um nome que pode ser resolvido a partir de fora da rede virtual.

No Azure, você tem a opção de especificar um rótulo de nome DNS que está associado com um endereço IP público. Você pode escolher o rótulo (prefixo), mas o Azure escolhe o sufixo, o que é baseado na região em que você cria o endereço IP público.

![Exemplo de um rótulo de nome DNS](media/azure-stack-understanding-dns-in-tp2/image3.png)

Como mostra a imagem anterior, Azure criará um registro "A" no DNS para o rótulo de nome DNS especificado sob a zona **westus.cloudapp.azure.com**. O prefixo e o sufixo são combinados para compor uma [nome de domínio totalmente qualificado](https://en.wikipedia.org/wiki/Fully_qualified_domain_name) (FQDN) que pode ser resolvido em qualquer lugar na Internet pública.

O Azure Stack só dá suporte a iDNS para o registro de nome interno, portanto, ele não pode fazer o seguinte:

- Criar um registro DNS em uma zona DNS hospedado existente (por exemplo, local.azurestack.external.)
- Criar uma zona DNS (por exemplo, Contoso.com).
- Crie um registro em sua própria zona DNS personalizado.
- Suporte para a compra de nomes de domínio.

## <a name="next-steps"></a>Próximas etapas

[Usando o DNS no Azure Stack](azure-stack-dns.md)
