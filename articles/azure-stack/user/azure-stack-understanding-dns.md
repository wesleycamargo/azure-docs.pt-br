---
title: "Noções básicas sobre DNS na pilha do Azure | Microsoft Docs"
description: "Noções básicas sobre os recursos DNS e capacidades da pilha do Azure"
services: azure-stack
documentationcenter: 
author: ScottNapolitan
manager: darmour
editor: 
ms.assetid: 60f5ac85-be19-49ac-a7c1-f290d682b5de
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 9/25/2017
ms.author: scottnap
ms.openlocfilehash: 381947a5c936b2bbcae1cf61cdd36eb1653be30b
ms.sourcegitcommit: 5ac112c0950d406251551d5fd66806dc22a63b01
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/23/2018
---
# <a name="introducing-idns-for-azure-stack"></a>Introdução ao iDNS pilha do Azure

*Aplica-se a: Azure pilha integrado sistemas e o Kit de desenvolvimento de pilha do Azure*

iDNS é um recurso na pilha do Azure que permite resolver nomes DNS externos (como http://www.bing.com).
Ele também permite que você registre nomes de rede virtual interna. Fazendo isso, você pode resolver as VMs na mesma rede virtual por nome em vez do endereço IP, sem ter que fornecer entradas personalizadas de servidor DNS.

Isso é algo que sempre tenha sido existe no Azure, mas está disponível no Windows Server 2016 e do Azure pilha muito.

## <a name="what-does-idns-do"></a>O que faz iDNS?
Com iDNS na pilha do Azure, você tem os seguintes recursos, sem precisar especificar entradas personalizadas de servidor DNS:

* Compartilhado serviços de resolução de nome DNS para cargas de trabalho de locatário.
* Serviço DNS autoritativo para a resolução de nome e o registro DNS na rede virtual do locatário.
* Serviço Recursive DNS para resolução de nomes de Internet de máquinas virtuais do locatário. Locatários não precisam especificar entradas DNS para resolver nomes de Internet (por exemplo, www.bing.com).

Você pode ainda traga seu próprio DNS e usar servidores DNS personalizados. Mas, agora, se você apenas deseja ser capaz de resolver nomes de DNS da Internet e ser capaz de se conectar a outras máquinas virtuais na mesma rede virtual, você não precisa especificar nada e ele funcionará.

## <a name="what-does-idns-not-do"></a>O que não iDNS faz?
Quais iDNS não permite fazer é criar um registro DNS para um nome que pode ser resolvido a partir de fora da rede virtual.

No Azure, você tem a opção de especificar um rótulo de nome DNS que pode ser associado um endereço IP público. Você pode escolher o rótulo (prefixo), mas o Azure escolhe o sufixo, que é baseado na região em que você criar o endereço IP público.

![Rótulo de nome de captura de tela de DNS](media/azure-stack-understanding-dns-in-tp2/image3.png)

Na imagem acima, o Azure criará um registro "A" no DNS para o rótulo de nome DNS especificado na zona **westus.cloudapp.azure.com**. O prefixo e o sufixo junto compõem um totalmente qualificado domínio FQDN (nome) que pode ser resolvido a partir de qualquer lugar na Internet pública.

A pilha do Azure somente dá suporte a iDNS para o registro de nome interno para que ele não pode fazer o seguinte:

* Crie um registro DNS em uma zona DNS hospedado (por exemplo, local.azurestack.external) existente.
* Crie uma zona DNS (por exemplo, Contoso.com).
* Crie um registro em sua própria zona DNS personalizado.
* Suporte para a compra de nomes de domínio.

