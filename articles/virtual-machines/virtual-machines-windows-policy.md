---
title: "Impor a segurança com políticas em VMs do Windows no Azure | Microsoft Docs"
description: "Como aplicar uma política a uma Máquina Virtual Windows do Azure Resource Manager"
services: virtual-machines-windows
documentationcenter: 
author: singhkays
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 0b71ba54-01db-43ad-9bca-8ab358ae141b
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 04/13/2016
ms.author: kasing
translationtype: Human Translation
ms.sourcegitcommit: 1cb57e5156dab976599ddfa9a58f26ca8ef1ee0e
ms.openlocfilehash: 54afb2f55ef9ff48448c63bc8ee8e745765f61e6
ms.lasthandoff: 02/22/2017


---
# <a name="apply-security-and-policies-to-windows-vms-with-azure-resource-manager"></a>Aplicar segurança e políticas às VMs do Windows com o Azure Resource Manager
Usando políticas, uma organização pode impor várias convenções e regras em toda a empresa. A imposição do comportamento desejado pode ajudar a reduzir o risco e contribui para o sucesso da organização. Neste artigo, descreveremos como você pode usar as políticas do Azure Resource Manager para definir o comportamento desejado para as Máquinas Virtuais da sua organização.

Veja abaixo a descrição das etapas para fazer isso

1. Política do Azure Resource Manager 101
2. Definir uma política para sua Máquina Virtual
3. Criar a política
4. Aplicar a política

## <a name="azure-resource-manager-policy-101"></a>Política do Azure Resource Manager 101
Para começar a trabalhar com as políticas do Azure Resource Manager, é recomendável ler o artigo a seguir para depois continuar com as etapas neste artigo. O artigo a seguir descreve a definição e a estrutura básicas de uma política, como políticas são avaliadas e fornece vários exemplos de definições de política.

* [Usar a política para gerenciar recursos e controlar o acesso](../azure-resource-manager/resource-manager-policy.md)

## <a name="define-a-policy-for-your-virtual-machine"></a>Definir uma política para sua Máquina Virtual
Um dos cenários comuns para uma empresa pode ser permitir que seus usuários apenas criem Máquinas Virtuais de sistemas operacionais específicos que foram testados para verificar sua compatibilidade com um aplicativo de LOB. Usando uma política do Azure Resource Manager, essa tarefa pode ser realizada em poucas etapas.
Neste exemplo de política, vamos permitir que apenas máquinas virtuais do Windows Server 2012 R2 Datacenter sejam criadas. A definição de política é semelhante àquela mostrada abaixo

```
"if": {
  "allOf": [
    {
      "field": "type",
      "equals": "Microsoft.Compute/virtualMachines"
    },
    {
      "not": {
        "allOf": [
          {
            "field": "Microsoft.Compute/virtualMachines/imagePublisher",
            "equals": "MicrosoftWindowsServer"
          },
          {
            "field": "Microsoft.Compute/virtualMachines/imageOffer",
            "equals": "WindowsServer"
          },
          {
            "field": "Microsoft.Compute/virtualMachines/imageSku",
            "equals": "2012-R2-Datacenter"
          }
        ]
      }
    }
  ]
},
"then": {
  "effect": "deny"
}
```

A política acima pode ser facilmente modificada para um cenário em que você desejaria permitir que qualquer imagem do Windows Server Datacenter possa ser usada para implantação de Máquina Virtual com a alteração abaixo

```
{
  "field": "Microsoft.Compute/virtualMachines/imageSku",
  "like": "*Datacenter"
}
```

#### <a name="virtual-machine-property-fields"></a>Campos de Propriedade da Máquina Virtual
A tabela abaixo descreve as propriedades da Máquina Virtual que podem ser usadas como campos na definição de política. Para obter mais informações sobre os campos de política, consulte o artigo a abaixo:

* [Campos e fontes](../azure-resource-manager/resource-manager-policy.md#conditions)

| Nome do campo | Descrição |
| --- | --- |
| imagePublisher |Especifica o editor da imagem |
| imageOffer |Especifica a oferta para o editor de imagem escolhido |
| imageSku |Especifica o SKU da oferta escolhida |
| imageVersion |Especifica a versão da imagem para o SKU escolhido |

## <a name="create-the-policy"></a>Criar a política
Uma política pode ser criada facilmente usando a API REST diretamente ou os cmdlets do PowerShell. Para criar a política, consulte o artigo abaixo:

* [Criando uma política](../azure-resource-manager/resource-manager-policy.md)

## <a name="apply-the-policy"></a>Aplicar a política
Depois de criar a política, você precisará aplicá-la a um escopo definido. O escopo pode ser uma assinatura, um grupo de recursos ou até mesmo um recurso. Para aplicar a política, consulte o artigo abaixo:

* [Criando uma política](../azure-resource-manager/resource-manager-policy.md)

