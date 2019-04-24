---
title: Lógica de processamento de regra do Firewall do Azure
description: Saiba mais sobre a lógica de processamento de regra do Firewall do Azure
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 9/27/2018
ms.author: victorh
ms.openlocfilehash: 12d86793c0d75413559aad77c558c4adb7ac91af
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60193638"
---
# <a name="azure-firewall-rule-processing-logic"></a>Lógica de processamento de regra do Firewall do Azure
O firewall do Azure tem regras NAT, regras de rede e regras de aplicativos. As regras são processadas de acordo com o tipo de regra.


## <a name="network-rules-and-applications-rules"></a>Regras de rede e regras de aplicativos 
As regras de rede são aplicadas primeiro e, seguida, são aplicadas as regras de aplicativo. As regras estão sendo encerradas. Portanto, se uma correspondência for encontrada nas regras de rede, as regras de aplicativo não serão processadas.  Se não houver qualquer correspondência da regra de rede e se o protocolo do pacote for HTTP/HTTPS, o pacote será avaliado pelas regras de aplicativo. Se ainda assim nenhuma correspondência for encontrada, o pacote será avaliado em relação à coleção de regras de infraestrutura. Se ainda não houver nenhuma correspondência, o pacote será negado por padrão.

## <a name="nat-rules"></a>Regras de NAT
Conectividade de entrada pode ser habilitada Configurando a tradução de endereço de rede de destino (DNAT) conforme descrito em [Tutorial: Filtrar o tráfego de entrada com DNAT de Firewall do Azure usando o portal do Azure](tutorial-firewall-dnat.md). As regras de DNAT são aplicadas primeiro. Se uma correspondência for encontrada, será adicionada uma regra de rede correspondente implícita para permitir o tráfego convertido. Você pode substituir esse comportamento adicionando explicitamente uma coleção de regras de rede com regras de negação que correspondem ao tráfego convertido. Não há regras de aplicativo aplicadas para essas conexões.


## <a name="next-steps"></a>Próximas etapas

- Aprenda a [Implantar e configurar um Firewall do Azure](tutorial-firewall-deploy-portal.md).