---
title: Noções básicas sobre iDNS na pilha do Azure | Microsoft Docs
description: Noções básicas sobre iDNS recursos e capacidades da pilha do Azure
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 60f5ac85-be19-49ac-a7c1-f290d682b5de
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/15/2018
ms.author: mabrigg
ms.openlocfilehash: df9c22877eeac381d936f2fb86f5720c9cc9c930
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/16/2018
---
# <a name="introducing-idns-for-azure-stack"></a>Introdução ao iDNS pilha do Azure

*Aplica-se a: Azure pilha integrado sistemas e o Kit de desenvolvimento de pilha do Azure*

iDNS é um recurso de rede de pilha do Azure que permite resolver nomes DNS externos (por exemplo, http://www.bing.com.) também permite registrar nomes de rede virtual interna. Fazendo isso, você pode resolver as VMs na mesma rede virtual por nome em vez do endereço IP. Essa abordagem elimina a necessidade de fornecer entradas personalizadas de servidor DNS. Para obter mais informações sobre DNS, consulte o [visão geral do DNS do Azure](https://docs.microsoft.com/en-us/azure/dns/dns-overview).

## <a name="what-does-idns-do"></a>O que faz iDNS?

Com iDNS na pilha do Azure, você tem os seguintes recursos, sem precisar especificar entradas personalizadas de servidor DNS:

* Compartilhado serviços de resolução de nome DNS para cargas de trabalho de locatário.
* Serviço DNS autoritativo para a resolução de nome e o registro DNS na rede virtual do locatário.
* Serviço Recursive DNS para resolução de nomes de Internet de máquinas virtuais do locatário. Os locatários não precisam especificar entradas DNS para resolver nomes de Internet (por exemplo, www.bing.com.)

Você ainda pode trazer seu próprio DNS e usar servidores DNS personalizados. No entanto, usando iDNS, você pode resolver nomes DNS da Internet e conecte-se com outras VMs na mesma rede virtual, você não precisa criar entradas DNS personalizadas.

## <a name="what-doesnt-idns-do"></a>O que não iDNS?

Quais iDNS não permite que você faça, é criar um registro DNS para um nome que pode ser resolvido a partir de fora da rede virtual.

No Azure, você tem a opção de especificar um rótulo de nome DNS que está associado um endereço IP público. Você pode escolher o rótulo (prefixo), mas o Azure escolhe o sufixo, que é baseado na região em que você criar o endereço IP público.

![Exemplo de um rótulo de nome DNS](media/azure-stack-understanding-dns-in-tp2/image3.png)

Como mostra a imagem anterior, Azure criará um registro "A" no DNS para o rótulo de nome DNS especificado na zona **westus.cloudapp.azure.com**. O prefixo e o sufixo são combinadas para compor uma [nome de domínio totalmente qualificado](https://en.wikipedia.org/wiki/Fully_qualified_domain_name) (FQDN) que pode ser resolvido de qualquer lugar na Internet pública.

A pilha do Azure somente dá suporte a iDNS para o registro de nome interno para que ele não pode fazer o seguinte:

* Criar um registro DNS em uma zona DNS hospedado existente (por exemplo, local.azurestack.external.)
* Criar uma zona DNS (por exemplo, Contoso.com).
* Crie um registro em sua própria zona DNS personalizado.
* Suporte para a compra de nomes de domínio.

## <a name="next-steps"></a>Próximas etapas

[Usando o DNS na pilha do Azure](azure-stack-dns.md)