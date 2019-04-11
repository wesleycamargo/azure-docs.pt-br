---
title: Opções de rede do Azure Functions
description: Uma visão geral de todas as opções de rede disponíveis no Azure Functions
services: functions
author: alexkarcher-msft
manager: jehollan
ms.service: azure-functions
ms.topic: conceptual
ms.date: 1/14/2019
ms.author: alkarche
ms.openlocfilehash: 10d7daa6da45c56e20c622fcbca9ee288e737dab
ms.sourcegitcommit: 43b85f28abcacf30c59ae64725eecaa3b7eb561a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/09/2019
ms.locfileid: "59358166"
---
# <a name="azure-functions-networking-options"></a>Opções de rede do Azure Functions

Este documento descreve o conjunto de recursos de rede disponíveis entre o Azure Functions as opções de hospedagem. Todas as seguintes opções de rede fornecem alguma capacidade de acessar os recursos sem usar endereços roteáveis da internet ou restringir o acesso à internet para um aplicativo de funções. Os todos os modelos de hospedagem tem diferentes níveis de isolamento de rede disponível e escolher o correto permitirá que você atender aos requisitos de isolamento de rede.

Aplicativos de função podem ser hospedados de várias maneiras diferentes.

* Há um conjunto de opções de plano que são executados na infraestrutura de multilocatário, com vários níveis de conectividade de rede virtual e opções de dimensionamento.
    1. O plano de consumo, que dimensiona dinamicamente em resposta ao carregamento e oferece opções de isolamento de rede mínimas.
    1. O plano de Premium, que também dimensiona dinamicamente, além de oferecer isolamento de rede mais abrangente.
    1. O plano de serviço de aplicativo, que opera em uma escala fixa e oferece isolamento de rede semelhantes para o plano Premium.
* Funções também podem ser executadas em um aplicativo de serviço ASE (ambiente) que implanta sua função na sua rede virtual e oferece isolamento e controle de rede completo.

## <a name="networking-feature-matrix"></a>Matriz de recursos de rede

|                |[Plano de consumo](functions-scale.md#consumption-plan)|⚠ [Premium Plan](functions-scale.md##premium-plan-public-preview)|[Plano do Serviço de Aplicativo](functions-scale.md#app-service-plan)|[Ambiente do Serviço de Aplicativo](../app-service/environment/intro.md)|
|----------------|-----------|----------------|---------|-----------------------|  
|[**Restrições de IP de entrada**](#inbound-ip-restrictions)|✅Yes|✅Yes|✅Yes|✅Yes|
|[**Integração VNET**](#vnet-integration)|❌No|⚠ Sim|✅Yes|✅Yes|
|[**Visualização de integração de rede virtual (rota expressa e pontos de extremidade de serviço)**](#preview-vnet-integration)|❌No|⚠ Sim|⚠ Sim|✅Yes|
|[**Conexões Híbridas**](#hybrid-connections)|❌No|❌No|✅Yes|✅Yes|
|[**Acesso ao Site privado**](#private-site-access)|❌No| ❌No|❌No|✅Yes|

⚠ Recurso de visualização, não para uso em produção

## <a name="inbound-ip-restrictions"></a>Restrições de IP de entrada

As restrições de IP permitem definir uma lista de prioridade ordenada de permissão/negação dos endereços IP que são permitidos a acessar o seu aplicativo. A lista de permissões pode incluir endereços IPv4 e IPv6. Quando há uma ou mais entradas, há, em seguida, uma negação implícita de tudo o que existe no final da lista. O recurso de restrições de IP funciona com a função de todas as opções de hospedagem.

> [!NOTE]
> Para poder usar o editor do portal do Azure, o portal deve ser capaz de acessar diretamente o seu aplicativo de função em execução e o dispositivo que você está usando para acessar o portal deve ter sua lista de permissões IP. Com as restrições de rede em vigor, você ainda pode acessar qualquer recurso na **recursos da plataforma** guia.

[Saiba mais aqui](https://docs.microsoft.com/azure/app-service/app-service-ip-restrictions)

## <a name="vnet-integration"></a>Integração VNET

Integração de rede virtual permite que seu aplicativo de funções acessar recursos em uma rede virtual. Integração de rede virtual está disponível no plano Premium e no plano de serviço de aplicativo. Se seu aplicativo estiver em um ambiente de serviço de aplicativo, em seguida, ele já está em uma rede virtual e não requer o uso do recurso Integração VNet para acessar recursos na mesma rede virtual.

Integração VNet concede ao seu aplicativo de acesso de função a recursos em sua rede virtual, mas não concede [acesso de site privado](#private-site-access) ao seu aplicativo de função da rede virtual.

A Integração VNet geralmente é usada para habilitar o acesso de aplicativos a um banco de dados e a serviços Web em execução na VNet. Com a integração VNet, você não precisa expor um ponto de extremidade público para aplicativos em sua VM, mas pode usar, em vez disso, endereços roteáveis privados fora da Internet.

A versão disponível da integração VNET se baseia em um gateway de VPN para se conectar a aplicativos de funções a uma rede virtual. Ele está disponível nas funções hospedadas em um plano de serviço de aplicativo. Para saber como configurar esse recurso, consulte o [documento de serviço de aplicativo para o mesmo recurso](../app-service/web-sites-integrate-with-vnet.md#enabling-vnet-integration).

### <a name="preview-vnet-integration"></a>Integração de VNET de visualização

Há uma nova versão do recurso de Integração VNet que está em versão prévia. Ela não depende de VPN de ponto a site e também dá suporte ao acesso a recursos entre ExpressRoute ou Pontos de Extremidade de Serviço. Esse recurso está disponível no plano Premium e nos planos de serviço de aplicativo dimensionados para PremiumV2.

A nova versão da integração de rede virtual, que está atualmente em visualização, fornece os seguintes benefícios:

* Nenhum gateway é necessário para usar o novo recurso de Integração de VNet
* Você pode acessar recursos em conexões do ExpressRoute sem nenhuma configuração adicional além da integração com a VNet conectada ao ExpressRoute.
* Você pode consumir recursos de execução do Functions protegidos do ponto de extremidade do serviço. Para fazer isso, habilite os pontos de extremidade de serviço na sub-rede usada para a Integração VNet.
* Você não pode configurar gatilhos para usar o ponto de extremidade de serviço usando o novo recurso de integração de rede virtual de recursos protegidos. 
* O aplicativo de funções e a rede virtual devem estar na mesma região.
* O novo recurso requer uma sub-rede não usada na VNet do Resource Manager.
* Cargas de trabalho de produção não há suporte para a nova versão da integração VNet, enquanto ele estiver em visualização.
* Tabelas de rotas e emparelhamento global ainda não estão disponíveis com a nova Integração VNet.
* Um endereço é usado para cada instância de aplicativo de função potenciais. Porque o tamanho da sub-rede não pode ser alterado após a atribuição, use uma sub-rede que pode facilmente dar suporte a seu tamanho máximo de escala. Por exemplo, para dar suporte a um plano Premium que pode ser dimensionado para 80 instâncias, é recomendável um `/25` subrede que fornece endereços de host 126.

Para saber mais sobre como usar a integração de rede virtual de visualização, consulte [integrar um aplicativo de funções com uma rede Virtual do Azure](functions-create-vnet.md).

## <a name="hybrid-connections"></a>Conexões Híbridas

[Conexões híbridas](../service-bus-relay/relay-hybrid-connections-protocol.md) é um recurso de retransmissão do Azure que pode ser usado para acessar recursos do aplicativo em outras redes. Ele fornece acesso de seu aplicativo para um ponto de extremidade do aplicativo. Ele não pode ser usado para acessar o aplicativo. Conexões híbridas está disponível para as funções em execução em um [plano do serviço de aplicativo](functions-scale.md#app-service-plan) e uma [ambiente do serviço de aplicativo](../app-service/environment/intro.md).

Quando usados em funções, cada Conexão híbrida se correlaciona com uma única combinação de host e a porta TCP. Isso significa que o ponto de extremidade de Conexões Híbridas pode estar em qualquer sistema operacional e em qualquer aplicativo, desde que você esteja acessando uma porta de escuta TCP. O recurso Conexões Híbridas não sabe e nem se importa com o protocolo de aplicativo ou o que você está acessando. Ele simplesmente fornece acesso à rede.

Para obter mais informações, consulte o [documentação do serviço de aplicativo para as conexões híbridas](../app-service/app-service-hybrid-connections.md), que oferece suporte a aplicativos Web e funções.

## <a name="private-site-access"></a>Acesso ao Site privado

Acesso ao site privado significa tornar seu aplicativo acessível somente de uma rede privada, como de dentro de uma rede virtual do Azure. O acesso de site privado só está disponível com um ASE configurado com um ILB (Balanceador de carga interno). Para obter detalhes sobre como usar um ASE ILB, consulte [criando e usando um ASE ILB](../app-service/environment/create-ilb-ase.md).

Há várias maneiras para acessar recursos de rede virtual em outras opções de hospedagem, mas um ASE é a única maneira de permitir que gatilhos para uma função ocorrer em uma rede virtual.
