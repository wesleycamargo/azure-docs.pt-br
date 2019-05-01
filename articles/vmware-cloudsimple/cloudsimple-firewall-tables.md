---
title: Tabelas de firewall do Azure - solução de VMware por CloudSimple-
description: Saiba mais sobre tabelas de firewall de nuvem privada CloudSimple e regras de firewall.
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 861c2e86d623c46c14366f19457d1f689386a316
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/26/2019
ms.locfileid: "64577339"
---
# <a name="firewall-tables-overview"></a>Visão geral de tabelas do firewall

Uma tabela de firewall lista as regras para filtrar o tráfego de rede para e de recursos de nuvem privada. Você pode aplicá-las a uma VLAN ou sub-rede. As regras, em seguida, controlam o tráfego de rede entre uma rede de origem ou o endereço IP e uma rede de destino ou o endereço IP.

## <a name="firewall-rules"></a>Regras de firewall

A tabela a seguir descreve os parâmetros em uma regra de firewall.

| Propriedade | Detalhes |
| ---------| --------|
| **Nome** | Um nome que identifica exclusivamente a regra de firewall e sua finalidade. |
| **Prioridade** | Um número entre 100 e 4096, com 100 a prioridade mais alta. As regras são processadas em ordem de prioridade. Quando o tráfego ocorrer em uma correspondência de regra, o processamento da regra para. Como resultado, as regras existentes com prioridades mais baixas que têm os mesmos atributos como regras com prioridades mais altas não são processadas.  Tome cuidado para evitar regras conflitantes. |
| **Acompanhamento de estado** | Rastreamento pode ser sem monitoração de estado (nuvem privada, Internet ou VPN) ou com monitoração de estado (IP público).  |
| **Protocolo** | As opções incluem qualquer, TCP ou UDP. Se você precisar de ICMP, use qualquer um. |
| **Direção** | Se a regra se aplica ao tráfego de entrada ou de saída. |
| **Ação** | Permitir ou negar para o tipo de tráfego definido na regra. |
| **Fonte** | Um endereço IP, o bloco de roteamento entre domínios (CIDR) (por exemplo, 10.0.0.0/24) ou qualquer um.  Especificar um intervalo, uma marca de serviço ou grupo de segurança de aplicativo permite que você crie menos regras de segurança. |
| **Porta de origem** | Porta de rede na qual se origina o tráfego.  Você pode especificar uma porta individual ou um intervalo de portas, como a 443 ou 8000-8080. A especificação de intervalos permite que você crie menos regras de segurança. |
| **Destino** | Um endereço IP, o bloco de roteamento entre domínios (CIDR) (por exemplo, 10.0.0.0/24) ou qualquer um.  Especificar um intervalo, uma marca de serviço ou grupo de segurança de aplicativo permite que você crie menos regras de segurança.  |
| **Porta de destino** | Porta à qual o tráfego de rede flui.  Você pode especificar uma porta individual ou um intervalo de portas, como a 443 ou 8000-8080. A especificação de intervalos permite que você crie menos regras de segurança.|

### <a name="stateless"></a>Sem estado

Uma regra sem monitoração de estado examina somente em pacotes individuais e filtra com base na regra.  
Regras adicionais podem ser necessárias para o fluxo de tráfego em direção inversa.  Use regras sem monitoração de estado para o tráfego entre os seguintes pontos:

* Subredes de nuvens privadas
* Sub-rede local e uma sub-rede de nuvem privada
* Tráfego de Internet de nuvens privadas

### <a name="stateful"></a>Com estado

 Uma regra com monitoração de estado está ciente das conexões que passam por ela. Um registro de fluxo é criado para as conexões existentes. A comunicação é permitida ou negada com base no estado de conexão do registro de fluxo.  Use esse tipo de regra para os endereços IP públicos para filtrar o tráfego da Internet.

### <a name="default-rules"></a>Regras padrão

Regras padrão a seguir são criadas em cada tabela de firewall.

|Prioridade|NOME|Acompanhamento de estado|Direção|Tipo de tráfego|Protocol|Fonte|Porta de origem|Destino|Porta de destino|Ação|
|--------|----|--------------|---------|------------|--------|------|-----------|-----------|----------------|------|
|65000|Permitir-all-para-a internet|Com estado|Saída|Tráfego de internet ou IP público|Todos|Qualquer|Qualquer|Qualquer|Qualquer|PERMITIR|
|65001|deny-all-from-internet|Com estado|Entrada|Tráfego de internet ou IP público|Todos|Qualquer|Qualquer|Qualquer|Qualquer|Negar|
|65002|Permitir-all-para-intranet|Sem estado|Saída|Nuvem privada interna ou tráfego VPN|Todos|Qualquer|Qualquer|Qualquer|Qualquer|PERMITIR|
|65003|allow-all-from-intranet|Sem estado|Entrada|Nuvem privada interna ou tráfego VPN|Todos|Qualquer|Qualquer|Qualquer|Qualquer|PERMITIR|

## <a name="next-steps"></a>Próximas etapas

* [Configurar regras e tabelas de firewall](https://docs.azure.cloudsimple.com/firewall/)