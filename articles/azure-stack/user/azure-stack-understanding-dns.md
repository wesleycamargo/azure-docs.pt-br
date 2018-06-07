---
title: Noções básicas sobre iDNS na pilha do Azure | Microsoft Docs
description: Noções básicas sobre iDNS recursos e capacidades da pilha do Azure
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/21/2018
ms.author: mabrigg
ms.reviewer: scottnap
ms.openlocfilehash: 9123160f42adea57c28dff265bd5b5dbbcbb7918
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/04/2018
ms.locfileid: "34724251"
---
# <a name="introducing-idns-for-azure-stack"></a>Introdução ao iDNS pilha do Azure

*Aplica-se a: Azure pilha integrado sistemas e o Kit de desenvolvimento de pilha do Azure*

iDNS é um recurso de rede de pilha do Azure que permite resolver nomes DNS externos (por exemplo, http://www.bing.com.) também permite registrar nomes de rede virtual interna. Fazendo isso, você pode resolver as VMs na mesma rede virtual por nome em vez do endereço IP. Essa abordagem elimina a necessidade de fornecer entradas personalizadas de servidor DNS. Para obter mais informações sobre DNS, consulte o [visão geral do DNS do Azure](https://docs.microsoft.com/en-us/azure/dns/dns-overview).

## <a name="what-does-idns-do"></a>O que faz iDNS?

Com iDNS na pilha do Azure, você tem os seguintes recursos, sem precisar especificar entradas personalizadas de servidor DNS:

- Compartilhado serviços de resolução de nome DNS para cargas de trabalho de locatário.
- Serviço DNS autoritativo para a resolução de nome e o registro DNS na rede virtual do locatário.
- Serviço Recursive DNS para resolução de nomes de Internet de máquinas virtuais do locatário. Os locatários não precisam especificar entradas DNS para resolver nomes de Internet (por exemplo, www.bing.com.)

Você ainda pode trazer seu próprio DNS e usar servidores DNS personalizados. No entanto, usando iDNS, você pode resolver nomes DNS da Internet e conecte-se com outras VMs na mesma rede virtual, você não precisa criar entradas DNS personalizadas.

## <a name="what-doesnt-idns-do"></a>O que não iDNS?

Quais iDNS não permite que você faça, é criar um registro DNS para um nome que pode ser resolvido a partir de fora da rede virtual.

No Azure, você tem a opção de especificar um rótulo de nome DNS que está associado um endereço IP público. Você pode escolher o rótulo (prefixo), mas o Azure escolhe o sufixo, que é baseado na região em que você criar o endereço IP público.

![Exemplo de um rótulo de nome DNS](media/azure-stack-understanding-dns-in-tp2/image3.png)

Como mostra a imagem anterior, Azure criará um registro "A" no DNS para o rótulo de nome DNS especificado na zona **westus.cloudapp.azure.com**. O prefixo e o sufixo são combinadas para compor uma [nome de domínio totalmente qualificado](https://en.wikipedia.org/wiki/Fully_qualified_domain_name) (FQDN) que pode ser resolvido de qualquer lugar na Internet pública.

A pilha do Azure somente dá suporte a iDNS para o registro de nome interno para que ele não pode fazer o seguinte:

- Criar um registro DNS em uma zona DNS hospedado existente (por exemplo, local.azurestack.external.)
- Criar uma zona DNS (por exemplo, Contoso.com).
- Crie um registro em sua própria zona DNS personalizado.
- Suporte para a compra de nomes de domínio.

## <a name="next-steps"></a>Próximas etapas

[Usando o DNS na pilha do Azure](azure-stack-dns.md)
