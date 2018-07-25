---
title: Regras e pontos de extremidade de serviço de Rede Virtual para Barramento de Serviço do Microsoft Azure | Microsoft Docs
description: Adicione um ponto de extremidade de serviço Microsoft.ServiceBus a uma rede virtual.
services: event-hubs
documentationcenter: ''
author: clemensv
manager: timlt
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.date: 07/05/2018
ms.author: clemensv
ms.openlocfilehash: f8874a1d9db754485e9624596465560981bd6425
ms.sourcegitcommit: 0b4da003fc0063c6232f795d6b67fa8101695b61
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/05/2018
ms.locfileid: "37858038"
---
# <a name="use-virtual-network-service-endpoints-with-azure-service-bus"></a>Usar pontos de extremidade de serviço de Rede Virtual com Barramento de Serviço

A integração de Barramento de Serviço com [Pontos de extremidade de serviço de VNet (rede virtual)][vnet-sep] permite acesso seguro a recursos de mensagens de cargas de trabalho, como máquinas virtuais associadas a redes virtuais, com o caminho de tráfego de rede sendo protegido em ambas as extremidades. 

Após ser configurado para associar-se a pelo menos um ponto de extremidade de serviço de sub-rede da rede virtual, o respectivo namespace de Barramento de Serviço não aceitará mais tráfego de nenhum lugar, exceto das redes virtuais autorizadas. Da perspectiva da rede virtual, a associação de um namespace de Barramento de Serviço a um ponto de extremidade de serviço configura um túnel de rede isolado da sub-rede da rede virtual para o serviço de sistema de mensagens.

O resultado é um relacionamento privado e isolado entre as cargas de trabalho associadas à sub-rede e o respectivo namespace de Barramento de Serviço, apesar do endereço de rede observável do ponto de extremidade de serviço de mensagens estar em um intervalo de IP público.

## <a name="enable-service-endpoints-with-service-bus"></a>Habilitar pontos de extremidade de serviço com o Barramento de Serviço

As redes virtuais têm suporte apenas em namespaces do Barramento de Serviço [camada Premium](service-bus-premium-messaging.md). 

Uma consideração importante ao usar pontos de extremidade de serviço de VNet com o Barramento de Serviço é que você não deve permitir esses pontos de extremidade em aplicativos que combinam os namespaces do Barramento de Serviço de camada Standard e Premium. Como a camada Standard não oferece suporte a VNets, o ponto de extremidade fica restrito apenas a namespaces da camada Premium. A VNet bloqueará o tráfego para o namespace Standard. 

## <a name="advanced-security-scenarios-enabled-by-vnet-integration"></a>Cenários avançados de segurança habilitados pela integração de VNet 

Soluções que exigem segurança compartimentada e restrita, e onde as sub-redes da rede virtual fornecem a segmentação entre os serviços compartimentados, geralmente ainda precisam de caminhos de comunicação entre os serviços que residem nesses compartimentos.

Qualquer rota IP imediata entre os compartimentos, incluindo aqueles que transportam HTTPS sobre TCP/IP, acarreta o risco de exploração de vulnerabilidades a partir da camada de rede. Os serviços de mensagens fornecem caminhos de comunicação completamente isolados, onde as mensagens são gravadas no disco na medida em que fazem a transição entre as partes. Cargas de trabalho em duas redes virtuais distintas que estão associadas à mesma instância de Barramento de Serviço podem comunicar-se de maneira eficiente e confiável por meio de mensagens, enquanto a integridade de limite de isolamento da respectiva rede é preservada.
 
Isso significa que as soluções na nuvem de segurança confidencial não apenas obtêm acesso aos recursos de mensagens assíncronas confiáveis e escalonáveis líderes do mercado do Azure, mas agora podem usar o sistema de mensagens para criar caminhos de comunicação entre compartimentos de solução protegidos que são inerentemente mais seguros que o modo de comunicação ponto a ponto, incluindo HTTPS e outros protocolos de socket protegidos por TLS.

## <a name="binding-service-bus-to-virtual-networks"></a>Associar Barramento de Serviço a Redes Virtuais

As *regras de rede virtual* são o recurso de segurança do firewall que controla se o servidor de Barramento de Serviço do Azure aceita conexões de uma sub-rede de rede virtual específica.

Associar um namespace de Barramento de Serviço a uma rede virtual é um processo de duas etapas. Primeiro é necessário criar um **ponto de extremidade de Rede Virtual** em uma sub-rede de Rede Virtual e habilitá-lo para "Microsoft.ServiceBus", conforme explicado na [visão geral do ponto de extremidade de serviço][vnet-sep]. Após adicionar o ponto de extremidade de serviço, você associa o namespace de Barramento de Serviço ao ponto de extremidade com uma *regra de rede virtual*.

A regra de rede virtual é uma associação nomeada do namespace de Barramento de Serviço com uma sub-rede de rede virtual. Embora a regra exista, todas as cargas de trabalho associadas à sub-rede recebem acesso ao namespace de Barramento de Serviço. Os próprios Barramentos de Serviço nunca estabelecem conexões de saída, não precisam obter acesso e, portanto, nunca recebem acesso à sub-rede habilitando essa regra.

### <a name="creating-a-virtual-network-rule-with-azure-resource-manager-templates"></a>Criar uma regra de rede virtual com modelos do Azure Resource Manager

O modelo do Resource Manager a seguir permite incluir uma regra de rede virtual em um namespace de Barramento de Serviço existente.

Parâmetros de modelo:

* **namespaceName**: namespace de Barramento de serviço.
* **vnetRuleName**: nome da regra de Rede Virtual a ser criada.
* **virtualNetworkingSubnetId**: caminho do Resource Manager totalmente qualificado para a sub-rede da rede virtual, por exemplo, `/subscriptions/{id}/resourceGroups/{rg}/providers/Microsoft.Network/virtualNetworks/{vnet}/subnets/default` para a sub-rede padrão de uma rede virtual.

Modelo:

```json
{  
   "$schema":"http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
   "contentVersion":"1.0.0.0",
   "parameters":{     
          "namespaceName":{  
             "type":"string",
             "metadata":{  
                "description":"Name of the namespace"
             }
          },
          "vnetRuleName":{  
             "type":"string",
             "metadata":{  
                "description":"Name of the Authorization rule"
             }
          },
          "virtualNetworkSubnetId":{  
             "type":"string",
             "metadata":{  
                "description":"subnet Azure Resource Manager ID"
             }
          }
      },
    "resources": [
        {
            "apiVersion": "2018-01-01-preview",
            "name": "[concat(parameters('namespaceName'), '/', parameters('vnetRuleName'))]",
            "type":"Microsoft.ServiceBus/namespaces/VirtualNetworkRules",           
            "properties": {             
                "virtualNetworkSubnetId": "[parameters('virtualNetworkSubnetId')]"  
            }
        } 
    ]
}
```

Para implantar o modelo, siga as instruções para o [Azure Resource Manager][lnk-deploy].

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre redes virtuais, consulte os links a seguir:

- [Pontos de extremidade de serviço de rede virtual do Azure][vnet-sep]
- [Filtragem IP do Barramento de Serviço do Azure][ip-filtering]

[vnet-sep]: ../virtual-network/virtual-network-service-endpoints-overview.md
[lnk-deploy]: ../azure-resource-manager/resource-group-template-deploy.md
[ip-filtering]: service-bus-ip-filtering.md