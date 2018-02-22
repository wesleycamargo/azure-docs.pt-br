---
title: "Configurar zonas de pesquisa inversa para uma verificação de banner de SMTP no Azure | Microsoft Docs"
description: "Descreve como configurar zonas de pesquisa inversa para uma verificação de banner de SMTP no Azure"
services: virtual-network
documentationcenter: virtual-network
author: genlin
manager: WillChen
editor: 
tags: azure-resource-manager
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 02/06/2018
ms.author: genli
ms.custom: 
ms.openlocfilehash: 1e95b00ea08105238a860265e46275c24ed7bfbd
ms.sourcegitcommit: 4723859f545bccc38a515192cf86dcf7ba0c0a67
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/11/2018
---
#  <a name="configure-reverse-lookup-zones-for-an-smtp-banner-check"></a>Configurar zonas de pesquisa inversa para uma verificação de banner de SMTP

Este artigo descreve como usar uma zona inversa no DNS do Azure e criar um registro de DNS inverso (PTR) para verificação de banner de SMTP. 

## <a name="symptom"></a>Sintoma

Se você hospedar um servidor SMTP no Microsoft Azure, poderá receber a seguinte mensagem de erro ao enviar ou receber uma mensagem de servidores de email remotos:

**554: nenhum registro PTR** 

## <a name="solution"></a>Solução

Para um endereço IP virtual no Azure, os registros inversos são criados em zonas de domínio pertencentes à Microsoft, não em zonas de domínio personalizado.

Para configurar os registros PTR em zonas pertencentes à Microsoft, use a propriedade -ReverseFqdn no recurso PublicIpAddress. Para obter mais informações, veja [Configurar DNS reverso para serviços hospedados no Azure](../dns/dns-reverse-dns-for-azure-services.md). 

Quando você configurar os registros PTR, verifique se o endereço IP e o FQDN reverso pertencem à assinatura. Se você tentar definir um FQDN reverso que não pertença à assinatura, receberá a seguinte mensagem de erro:

    Set-AzureRmPublicIpAddress : ReverseFqdn mail.contoso.com that PublicIPAddress ip01 is trying to use does not belong to subscription <Subscription ID>. One of the following conditions need to be met to establish ownership: 
                        
    1) O ReverseFqdn realiza a correspondência do FQDN de qualquer recurso IP público na assinatura; 
    2) O ReverseFqdn resolve para o fqdn (por meio da cadeia de registros CName) de qualquer recurso IP público na assinatura; 
    3) Ele resolve para o endereço IP (por meio do CName e da cadeia de registros A) de qualquer recurso de IP público estático na assinatura. 

Se você alterar manualmente seu banner de SMTP para corresponder ao nosso FQDN padrão inverso, o servidor de email remoto ainda poderá falhar porque ele poderá esperar que o host do banner de SMTP corresponda ao registro MX para o domínio.