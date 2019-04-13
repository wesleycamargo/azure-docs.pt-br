---
title: Perguntas frequentes sobre a rede no Azure Functions
description: Respostas para algumas das perguntas mais comuns e cenários de rede com o Azure Functions.
services: functions
author: alexkarcher-msft
manager: jeconnoc
ms.service: azure-functions
ms.topic: troubleshooting
ms.date: 4/11/2019
ms.author: alkarche, glenga
ms.openlocfilehash: 126b9ccefedee1f5cefdac8a8666a58e7a4a1fef
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2019
ms.locfileid: "59548620"
---
# <a name="frequently-asked-questions-about-networking-in-azure-functions"></a>Perguntas frequentes sobre a rede no Azure Functions

Abaixo está uma lista de perguntas frequentes de rede. Para obter uma visão mais abrangente, leia o [funções de documento de opções de rede](functions-networking-options.md)

## <a name="how-do-i-set-a-static-ip-in-functions"></a>Como definir um endereço IP estático em funções?

Implantando uma função em um ambiente de serviço de aplicativo (ASE) atualmente é a única maneira de ter um IP estático de entrada e saído para sua função. Para obter detalhes sobre como usar um ASE, comece com este artigo: [Criando e usando um ASE ILB](../app-service/environment/create-ilb-ase.md).

## <a name="how-do-i-restrict-internet-access-to-my-function"></a>Como restringir o acesso à internet para minha função?

Você pode restringir o acesso à internet de diversas maneiras, listadas abaixo.

* [Restrições de IP](../app-service/app-service-ip-restrictions.md): restringir o tráfego de entrada para seu aplicativo de funções por intervalo de IP.
* Remova todos os gatilhos HTTP. Para alguns aplicativos, é suficiente simplesmente evitar gatilhos HTTP e usar qualquer outra fonte de evento para disparar sua função.

O aspecto mais importante ao fazer isso é ter em mente que o editor do portal do Azure requer acesso direto à sua função em execução a ser usada. Alterações de código por meio do portal do Azure exigirá que o dispositivo que você está usando para navegarem pelo portal para ter sua lista de permissões IP. Você ainda pode, no entanto, usar qualquer coisa na guia de recursos de plataforma com restrições de rede em vigor.

## <a name="how-do-i-restrict-my-function-app-to-a-vnet"></a>Como restringir o meu aplicativo de função a uma rede virtual?

A única maneira de restringir totalmente uma função, de modo que todo o tráfego flui através de uma rede virtual é usar um ambiente de serviço de aplicativo (ASE) (ILB) de balanceamento de carga de internamente. Essa opção implanta o seu site em uma infraestrutura dedicada em uma rede virtual e envia todos os disparadores e tráfego por meio da VNET. Para obter detalhes sobre como usar um ASE, comece com este artigo: [Criando e usando um ASE ILB](../app-service/environment/create-ilb-ase.md).

## <a name="how-can-i-access-resources-in-a-vnet-from-a-function-app"></a>Como acessar recursos em uma rede virtual de um aplicativo de função?

Você pode acessar recursos em uma rede virtual de uma função em execução usando a integração de rede virtual. Para obter mais informações, consulte [integração VNET](functions-networking-options.md#vnet-integration)

## <a name="how-do-i-access-resources-protected-by-service-endpoints"></a>Como fazer para acessar recursos protegidos por pontos de extremidade de serviço?

Usando a nova integração da rede virtual (atualmente em versão prévia), você pode acessar recursos de uma função em execução protegidos do ponto de extremidade do serviço. Para obter mais informações, consulte [visualizar a integração de rede virtual](functions-networking-options.md#preview-vnet-integration).

## <a name="how-can-i-trigger-a-function-from-a-resource-in-a-vnet"></a>Como é possível disparar uma função de um recurso em uma rede virtual?

Você só pode disparar uma função de um recurso em uma rede virtual implantando seu aplicativo de funções em um ambiente de serviço de aplicativo. Para obter detalhes sobre como usar um ASE, consulte [criando e usando um ASE ILB](../app-service/environment/create-ilb-ase.md).


## <a name="how-can-i-deploy-my-function-app-in-a-vnet"></a>Como posso implantar meu aplicativo de função em uma rede virtual?

Implantando em um ambiente de serviço de aplicativo é a única maneira de criar um aplicativo de função que está inteiramente dentro de uma rede virtual para obter detalhes sobre como usar um ASE ILB, comece com o artigo aqui: [Criando e usando um ASE ILB](https://docs.microsoft.com/azure/app-service/environment/create-ilb-ase).

Para cenários em que você só precisa unidirecional acesso aos recursos de rede virtual, ou menos isolamento de rede abrangente, consulte a [funções de visão geral da rede](functions-networking-options.md).

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a rede e as funções: 

* [Siga nosso tutorial de integração VNET Introdução](./functions-create-vnet.md)
* [Saiba mais sobre as opções de rede em funções aqui](./functions-networking-options.md)
* [Saiba mais sobre a integração de rede virtual com o Serviço de Aplicativo / Funções aqui](../app-service/web-sites-integrate-with-vnet.md)
* [Saiba mais sobre redes virtuais no Azure](../virtual-network/virtual-networks-overview.md)
* [Ativar mais recursos de rede e controle com ambientes do serviço de aplicativo](../app-service/environment/intro.md)
