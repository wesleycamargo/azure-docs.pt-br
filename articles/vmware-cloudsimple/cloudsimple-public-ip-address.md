---
title: Solução do VMware CloudSimple - endereço IP público do Azure
description: Saiba mais sobre endereços IP públicos e seus benefícios na solução do VMware CloudSimple
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: a366ec821e5e9663908f790ab3a88424fa956075
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/26/2019
ms.locfileid: "64576964"
---
# <a name="cloudsimple-public-ip-address-overview"></a>Visão geral CloudSimple público IP endereço

Um endereço IP público permite que os recursos da internet para se comunicar entrada aos recursos de nuvem privada em um endereço IP privado. O endereço IP privado é uma máquina virtual ou um balanceador de carga de software. É o endereço IP privado em sua nuvem privada vCenter. O endereço IP público permite que você exponha serviços em execução em sua nuvem privada à internet.

O endereço IP público dedicado para o endereço IP privado até que você não atribuí-lo. Um endereço IP público só pode ser atribuído a um endereço IP privado.

Um recurso associado com um endereço IP público sempre usa o endereço IP público para acessar a internet. Por padrão, apenas acesso de internet de saída é permitido em um endereço IP público.  Tráfego de entrada no endereço IP público é negado.  Para permitir o tráfego de entrada, crie uma regra de firewall para o endereço IP público para a porta específica.

## <a name="benefits"></a>Benefícios

Usando um endereço IP público para se comunicar fornece:

* Negação de prevenção de ataques de DDoS (serviço) distribuído. Essa proteção é habilitada automaticamente para o endereço IP público.
* Mitigação tráfego Always on de monitoramento e em tempo real de ataques comuns de nível de rede. Essas defesas são as mesmas defesas usadas pelo Microsoft online services.
* A escala inteira da rede global do Azure. A rede pode ser usada para distribuir e reduzir o tráfego de ataques entre regiões.  

## <a name="next-steps"></a>Próximas etapas

* Saiba como [alocar um endereço IP público](https://docs.azure.cloudsimple.com/publicips/)
