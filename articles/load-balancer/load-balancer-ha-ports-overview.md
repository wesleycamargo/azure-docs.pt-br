---
title: "Visão geral de portas de alta disponibilidade no Azure | Microsoft Docs"
description: Saiba mais sobre o balanceamento de carga de portas de alta disponibilidade em um balanceador de carga interno
services: load-balancer
documentationcenter: na
author: rdhillon
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 46b152c5-6a27-4bfc-bea3-05de9ce06a57
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/26/2017
ms.author: kumud
ms.openlocfilehash: e72fc0d4323f7a2d203fee66311c3fea10ad7a09
ms.sourcegitcommit: 3ab5ea589751d068d3e52db828742ce8ebed4761
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/27/2017
---
# <a name="high-availability-ports-overview-preview"></a>Visão geral de portas de alta disponibilidade (versão prévia)

O Azure Load Balancer Standard apresenta uma nova capacidade para balancear cargas de fluxos TCP e UDP em todas as portas simultaneamente usando um Load Balancer interno. 

>[!NOTE]
> O recurso de Portas de alta disponibilidade está disponível com o Load Balancer padrão e atualmente na versão prévia. Durante a versão prévia, o recurso pode não ter o mesmo nível de disponibilidade e confiabilidade que os recursos que estão na versão de disponibilidade geral. Para obter mais informações, consulte [Termos de Uso Complementares do Microsoft Azure para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). É necessário inscrever-se para a Versão prévia do Load Balancer Standard para usar portas de alta disponibilidade com recursos do Load Balancer Padrão. Siga as instruções para inscrever-se também no Load Balancer [Standard Preview](https://aka.ms/lbpreview#preview-sign-up).

Uma regra de portas de alta disponibilidade é uma variante de uma regra de balanceamento de carga configurada em um Load Balancer Standard interno.  Os cenários são simplificados, fornecendo uma única regra de LB para balancear a carga de todos os fluxos TCP e UDP que chegam em todas as portas de um front-end do Load Balancer Standard. A decisão de balanceamento de carga é feita por fluxo nas cinco tuplas de Endereço IP de origem, Porta de origem, Endereço IP de destino, Porta de destino e Protocolo.

Portas de alta disponibilidade permitem obter cenários críticos como alta disponibilidade e escala para NVA (Dispositivos de Rede Virtual) dentro de redes virtuais, bem como outros cenários em que um grande número de portas deve passar por balanceamento de carga. 

Portas de alta disponibilidade são configuradas definindo as portas de front-end e de back-end para **0** e o protocolo para **Todos**.  O recurso de Load Balancer interno agora balanceia todos os fluxos TCP e UDP, independentemente do número da porta.

## <a name="why-use-ha-ports"></a>Por que usar portas de alta disponibilidade

### <a name="nva"></a>Dispositivos Virtuais de Rede

É possível usar as NVA (soluções de virtualização de rede) para proteger as cargas de trabalho do Azure contra vários tipos de ameaças à segurança. Quando as NVA são usadas nesses cenários, elas devem ser confiáveis, altamente disponíveis e expandir sob demanda.

Para atingir essas metas em seu cenário, basta adicionar instâncias NVA ao pool de back-end do Azure Load Balancer interno e configurar uma regra do Load Balancer para portas de alta disponibilidade.

Portas de alta disponibilidade oferecem diversas vantagens para cenários de NVA de alta disponibilidade:
- failover rápido para instâncias íntegras com por investigações de integridade por instância
- alto desempenho com expansão para instâncias n-ativas
- cenários n-ativo e ativo-passivo
- eliminar a necessidade de soluções complexas como nós de Zookeeper para monitorar dispositivos

O exemplo a seguir apresenta uma implantação de rede virtual hub e spoke, com os spokes criando túneis forçados para o tráfego na rede virtual do hub e por meio de NVA, antes de deixar o espaço confiável. As NVAs estão por atrás de um Load Balancer Standard com a configuração de Portas de alta disponibilidade.  Todo o tráfego pode ser devidamente processado e encaminho. 

![exemplo de portas de HA](./media/load-balancer-ha-ports-overview/nvaha.png)

Figura 1 – rede virtual de hub e spoke com NVAs implantados no modo de HA

Se você usar Soluções de Virtualização de Rede, confirme com o respectivo provedor a melhor maneira de usar portas de alta disponibilidade e quais cenários são compatíveis.

### <a name="load-balancing-large-numbers-of-ports"></a>Balanceamento de carga de grandes quantidades de portas

Também é possível usar portas de alta disponibilidade para cenários de aplicação que exigem o balanceamento de carga de grandes quantidades de portas. Esses cenários podem ser simplificados usando o [Load Balancer Standard](https://aka.ms/lbpreview) interno com portas de alta disponibilidade, em que uma única regra de balanceamento de carga substitui várias regras de balanceamento de carga individuais, uma para cada porta.

## <a name="region-availability"></a>Disponibilidade de região

Portas de alta disponibilidade estão disponíveis [nas mesmas regiões que o Load Balancer Standard](https://aka.ms/lbpreview#region-availability).  

## <a name="preview-sign-up"></a>Inscrição na versão prévia

Para participar da versão prévia do recurso de portas de alta disponibilidade no Load Balancer Standard, registre sua assinatura para obter acesso usando a CLI do Azure 2.0 ou o PowerShell.  Execute estas três etapas:

>[!NOTE]
>Para usar esse recurso, você também deve se inscrever para a [Versão prévia padrão](https://aka.ms/lbpreview#preview-sign-up) do Load Balancer além das portas de alta disponibilidade. O registro das Portas de alta disponibilidade ou das versões prévias padrão do Load Balancer pode levar até uma hora.

### <a name="sign-up-using-azure-cli-20"></a>Inscrever-se usando a CLI do Azure 2.0

1. Registre o recurso junto ao provedor
    ```cli
    az feature register --name AllowILBAllPortsRule --namespace Microsoft.Network
    ```
    
2. A operação anterior pode demorar até 10 minutos para ser concluída.  É possível verificar o status da operação com o comando a seguir:

    ```cli
    az feature show --name AllowILBAllPortsRule --namespace Microsoft.Network
    ```
    
    Passe para a etapa 3 quando o estado de registro do recurso retornar “Registrado”, como mostrado abaixo:
   
    ```json
    {
       "id": "/subscriptions/foo/providers/Microsoft.Features/providers/Microsoft.Network/features/AllowLBPreview",
       "name": "Microsoft.Network/AllowILBAllPortsRule",
       "properties": {
          "state": "Registered"
       },
       "type": "Microsoft.Features/providers/features"
    }
    ```
    
3. Conclua a inscrição da versão prévia registrando novamente sua assinatura junto ao provedor de recursos:

    ```cli
    az provider register --namespace Microsoft.Network
    ```
    
### <a name="sign-up-using-powershell"></a>Inscrever-se usando o PowerShell

1. Registre o recurso junto ao provedor
    ```powershell
    Register-AzureRmProviderFeature -FeatureName AllowILBAllPortsRule -ProviderNamespace Microsoft.Network
    ```
    
2. A operação anterior pode demorar até 10 minutos para ser concluída.  É possível verificar o status da operação com o comando a seguir:

    ```powershell
    Get-AzureRmProviderFeature -FeatureName AllowILBAllPortsRule -ProviderNamespace Microsoft.Network
    ```
    Passe para a etapa 3 quando o estado de registro do recurso retornar “Registrado”, como mostrado abaixo:
   
    ```
    FeatureName          ProviderName      RegistrationState
    -----------          ------------      -----------------
    AllowILBAllPortsRule Microsoft.Network Registered
    ```
    
3. Conclua a inscrição da versão prévia registrando novamente sua assinatura junto ao provedor de recursos:

    ```powershell
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network
    ```


## <a name="limitations"></a>Limitações

A seguir estão as configurações com suporte ou exceções para portas de HA:

- Uma configuração de IP de front-end pode ter uma única regra de Load Balancer de DSR com portas de alta disponibilidade ou então ela pode ter uma única regra de Load Balancer não DSR com portas de alta disponibilidade. Ele não pode ter ambos.
- Uma única configuração de IP de adaptador de rede pode ter apenas uma regra de balanceador de carga não DSR com portas de alta disponibilidade. Nenhuma outra regra pode ser configurada para essa ipconfig.
- Uma mesma configuração de IP de adaptador de rede pode ter uma ou mais regras de balanceador de carga DSR com portas de alta disponibilidade, desde que todas as respectivas configurações de IP de front-end sejam exclusivas.
- Se todas as regras de balanceamento de carga forem de portas de alta disponibilidade (somente DSR) ou se todas as regras forem de portas que não são de alta disponibilidade (DSR e não DSR), duas (ou mais) regras de Load Balancer apontando para o mesmo pool de back-end poderão coexistir. Essas duas regras de balanceamento de carga não poderão coexistir se houver uma combinação de regras de portas de alta disponibilidade e portas que não são de alta disponibilidade.
- Portas de alta disponibilidade não estão disponíveis para IPv6.
- A simetria de fluxo para cenários NVA é compatível somente com uma única NIC. Veja a descrição e o diagrama para [Dispositivos de Rede Virtual](#nva). 



## <a name="next-steps"></a>Próximas etapas

- [Configurar portas de alta disponibilidade em um Load Balancer Standard interno](load-balancer-configure-ha-ports.md)
- [Saiba mais sobre a versão prévia do Load Balancer Standard](https://aka.ms/lbpreview)

