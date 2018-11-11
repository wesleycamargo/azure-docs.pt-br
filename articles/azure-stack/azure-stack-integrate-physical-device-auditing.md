---
title: Dispositivo físico e auditoria do Azure Stack
description: Saiba como integrar a auditoria de acesso do dispositivo físico no Azure Stack
services: azure-stack
author: PatAltimore
manager: femila
ms.service: azure-stack
ms.topic: article
ms.date: 11/05/2018
ms.author: patricka
ms.reviewer: fiseraci
keywords: ''
ms.openlocfilehash: 83177363803c8f5c505c083614000dfa8f0df60d
ms.sourcegitcommit: f0c2758fb8ccfaba76ce0b17833ca019a8a09d46
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/06/2018
ms.locfileid: "51036896"
---
# <a name="azure-stack-datacenter-integration---physical-device-auditing"></a>Integração do datacenter do Azure Stack - auditoria do dispositivo físico

Todos os dispositivos físicos no Azure Stack, como os controladores de gerenciamento de placa-base (BMC) e comutadores de rede, emitem os logs de auditoria. Você pode integrar os logs de auditoria em sua solução geral de auditoria. Uma vez que os dispositivos variam entre os diferentes fornecedores de hardware de OEM de pilha do Azure, contate o fornecedor para obter a documentação sobre a integração de auditoria.
As seções a seguir fornecem algumas informações gerais para a auditoria do dispositivo físico no Azure Stack.  

## <a name="physical-device-access-auditing"></a>Auditoria de acesso do dispositivo físico

Todos os dispositivos físicos no Azure Stack suportam o uso de TACACS ou RADIUS. O suporte inclui acesso ao controlador de gerenciamento de placa-base (BMC) e comutadores de rede.

Soluções de pilha do Azure não são fornecidos com o RADIUS ou TACACS interno. No entanto, as soluções foram validadas para suporte ao uso do RADIUS ou TACACS soluções existentes disponíveis no mercado.

Para RADIUS, MSCHAPv2 foi validada. Isso representa a implementação mais segura usando RADIUS.
Consulte seu fornecedor de hardware de OEM para habilitar o TACAS ou RADIUS os dispositivos incluídos com sua solução do Azure Stack.

## <a name="syslog-forwarding-for-network-devices"></a>Encaminhamento de syslog para dispositivos de rede

Todos os dispositivos de rede físicos no Azure Stack dão suporte a mensagens do syslog. Soluções de pilha do Azure não são fornecidos com um servidor syslog. No entanto, os dispositivos foram validados para dar suporte a envio de mensagens a soluções existentes do syslog disponíveis no mercado.

O endereço de destino de syslog é um parâmetro opcional coletado para a implantação, mas ele também pode ser adicionado após a implantação. Consulte seu fornecedor de hardware de OEM para configurar o syslog de encaminhamento em seus dispositivos de rede.

## <a name="next-steps"></a>Próximas etapas

[Política de manutenção](azure-stack-servicing-policy.md)
