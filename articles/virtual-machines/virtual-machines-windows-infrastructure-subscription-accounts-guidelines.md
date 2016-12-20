---
title: Diretrizes para assinatura e contas | Microsoft Docs
description: "Saiba mais sobre as principais diretrizes de design e de implementação para assinaturas e contas no Azure."
documentationcenter: 
services: virtual-machines-windows
author: iainfoulds
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 761fa847-78b0-4078-a33a-d95d198d1029
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 09/08/2016
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: b410436de11550aab2160421abfb27107f7874b0


---
# <a name="subscription-and-accounts-guidelines"></a>Diretrizes para contas e assinaturas
[!INCLUDE [virtual-machines-windows-infrastructure-guidelines-intro](../../includes/virtual-machines-windows-infrastructure-guidelines-intro.md)]

Este artigo se concentra em explicar como abordar o gerenciamento de assinaturas e de contas à medida que seu ambiente e sua base de usuários aumentam.

## <a name="implementation-guidelines-for-subscriptions-and-accounts"></a>Diretrizes de implementação para contas e assinaturas
Decisões:

* De que conjunto de assinaturas e contas você precisa para hospedar a infraestrutura ou carga de trabalho de TI?
* Como dividir a hierarquia de acordo com sua organização?

Tarefas:

* Defina a hierarquia da organização lógica como você gostaria de gerenciá-la em um nível de assinatura.
* Para corresponder a essa hierarquia lógica, defina as contas e as assinaturas necessárias em cada conta.
* Crie o conjunto de assinaturas e contas usando sua convenção de nomenclatura.

## <a name="subscriptions-and-accounts"></a>Assinaturas e contas
Para trabalhar com o Azure, você precisa de uma ou mais assinaturas do Azure. Recursos como VMs (máquinas virtuais) ou redes virtuais existem em todas essas assinaturas.

* Os clientes corporativos normalmente têm um Registro Enterprise, que é o recurso mais alto na hierarquia e está associado a uma ou mais contas.
* Para consumidores e clientes sem um Registro Enterprise, o recurso mais alto é a Conta.
* As assinaturas estão associadas a contas, e pode haver uma ou mais assinaturas por conta. O Azure registra as informações de cobrança no nível de assinatura.

Devido ao limite de dois níveis de hierarquia na relação de Conta/Assinatura, é importante alinhar a convenção de nomenclatura das contas e assinaturas às necessidades de cobrança. Por exemplo, se uma empresa global usar o Azure, poderá optar por ter uma conta por região e gerenciar as assinaturas no nível de região:

![](./media/virtual-machines-common-infrastructure-service-guidelines/sub01.png)

Por exemplo, você pode usar a seguinte estrutura:

![](./media/virtual-machines-common-infrastructure-service-guidelines/sub02.png)

Se uma região decide ter mais de uma assinatura associada a um determinado grupo, a convenção de nomenclatura deve incorporar uma forma de codificar os dados adicionais no nome da conta ou da assinatura. Essa organização permite manipular os dados de faturamento para gerar os novos níveis de hierarquia durante os relatórios de cobrança:

![](./media/virtual-machines-common-infrastructure-service-guidelines/sub03.png)

A organização pode ser semelhante a:

![](./media/virtual-machines-common-infrastructure-service-guidelines/sub04.png)

Fornecemos cobrança detalhada por meio de um arquivo para download, para uma única conta ou para todas as contas em um contrato empresarial.

## <a name="next-steps"></a>Próximas etapas
[!INCLUDE [virtual-machines-windows-infrastructure-guidelines-next-steps](../../includes/virtual-machines-windows-infrastructure-guidelines-next-steps.md)]




<!--HONumber=Nov16_HO3-->


