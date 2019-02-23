---
title: Adicionar endereços IP públicos no Azure Stack | Microsoft Docs
description: Saiba como adicionar mais endereços IP públicos para o Azure Stack.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/12/2019
ms.author: jeffgilb
ms.reviewer: scottnap
ms.lastreviewed: 09/17/2018
ms.openlocfilehash: cbfe4fcf30e9529f191f2a80de8ab83e7e0811f7
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/23/2019
ms.locfileid: "56735878"
---
# <a name="add-public-ip-addresses"></a>Adicionar endereços IP públicos
*Aplica-se a: Integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*  

Saiba como adicionar mais endereços IP públicos para o Azure Stack.  Neste artigo, nos referimos aos endereços externos, como endereços IP públicos, mas no Azure Stack isso se refere à adição de blocos de endereço IP à sua rede externa.  Se essa rede externa é roteável de Internet pública ou estiver em uma Intranet e usa o espaço de endereço privado realmente não importam para os fins deste artigo.  As etapas são os mesmos. 

## <a name="add-a-public-ip-address-pool"></a>Adicionar um Pool de endereços IP públicos
Você pode adicionar endereços IP públicos ao sistema do Azure Stack a qualquer momento após a implantação inicial do sistema do Azure Stack. Confira como [consumo de endereço IP público do modo de exibição](azure-stack-viewing-public-ip-address-consumption.md) para ver quais o uso atual e o endereço IP público disponibilidade está no Azure Stack.

Em um alto nível, o processo de adicionar um novo bloco de endereço IP público para o Azure Stack tem esta aparência:

 ![Adicionar fluxo de IP](media/azure-stack-add-ips/flow.PNG)

## <a name="obtain-the-address-block-from-your-provider"></a>Obter o bloco de endereço de seu provedor
A primeira coisa que você precisará fazer é obter o bloco de endereço que você deseja adicionar ao Azure Stack.  Dependendo de onde você obter seu bloco de endereços do, você precisará considerar o que é o prazo de entrega e gerenciar isso em relação a taxa em que está consumindo os endereços IP públicos no Azure Stack.  

> [!IMPORTANT]
> O Azure Stack aceitará qualquer bloco de endereço que você fornecer, desde que ele é um bloco de endereço válido e não se sobrepõe com um intervalo de endereços existente no Azure Stack.  Verifique se que você obter um bloco de endereço válido é roteável e não-sobreposição com a rede externa à qual o Azure Stack está conectado.  Depois de adicionar o intervalo para o Azure Stack, você não pode removê-lo.

## <a name="add-the-ip-address-range-to-azure-stack"></a>Adicionar intervalo de endereços IP para o Azure Stack

1. Em um navegador da Internet, navegue até seu painel do portal de administração.  Neste exemplo, vamos usar https://adminportal.local.azurestack.external.  
2.  Entre no portal de administração do Azure Stack como um operador de nuvem.
3.  No painel de controle padrão – encontrar a lista de gerenciamento de região e selecione a região que você deseja gerenciar, neste exemplo, local.
4.  Localize os provedores de recursos lado a lado e clique no provedor de recursos de rede.
5.  Clique no IP público de pools de bloco de uso.
6.  Clique no botão Adicionar IP pool.
7.  Forneça um nome para o Pool de IP.  O nome escolhido é apenas para que você possa identificar facilmente o pool de IP para que você pode chamá-lo que você quiser.  Ele é uma boa prática para tornar o nome que o mesmo que o intervalo de endereços, mas que não é necessária.
8.   Insira o bloco de endereço que você deseja adicionar na notação CIDR.  Por exemplo:  192.168.203.0/24
9.  Quando você fornece um intervalo CIDR válido no campo endereço do intervalo (bloco CIDR) o endereço IP inicial, endereço IP final e campos de endereços IP disponíveis serão preenchidos automaticamente.  Elas são somente leitura e geradas automaticamente para que você não pode alterá-las sem modificar o valor no campo de intervalo de endereço.
10. Depois de revisar as informações sobre a folha e confirmando que tudo parece corrigi, clique em Okey para confirmar a alteração e adicionar o intervalo de endereços para o Azure Stack.


## <a name="next-steps"></a>Próximas etapas 
[Ações de nó de unidade de escala de revisão](azure-stack-node-actions.md) 
