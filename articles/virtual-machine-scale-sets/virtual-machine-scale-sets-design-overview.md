---
title: "Como projetar conjuntos de dimensionamento de máquinas virtuais para dimensionamento no Azure| Microsoft Docs"
description: "Aprenda a projetar seus conjuntos de dimensionamento de máquina virtual para dimensionamento no Azure"
keywords: "máquina virtual linux, conjuntos de dimensionamento de máquina virtual"
services: virtual-machine-scale-sets
documentationcenter: 
author: gatneil
manager: madhana
editor: tysonn
tags: azure-resource-manager
ms.assetid: c27c6a59-a0ab-4117-a01b-42b049464ca1
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/13/2017
ms.author: negat
translationtype: Human Translation
ms.sourcegitcommit: e869b06935736fae72bd3b5407ebab7c3830098d
ms.openlocfilehash: de3687a1bf36bf49db400a5660ac631f20b629d0


---
# <a name="designing-vm-scale-sets-for-scale"></a>Projetar conjuntos de escala de VM para dimensionamento
Este tópico discute considerações de design para Conjuntos de Escala de Máquina Virtual. Para obter informações sobre o que são Conjuntos de Escala de Máquina Virtual, confira [Conjuntos de Dimensionamento de Máquina Virtual - Visão Geral](virtual-machine-scale-sets-overview.md).

## <a name="storage"></a>Armazenamento

### <a name="scale-sets-with-azure-managed-disks"></a>Conjuntos de dimensionamento com Azure Managed Disks
Agora, os conjuntos de dimensionamento podem ser criados com [Azure Managed Disks](../storage/storage-managed-disks-overview.md). Os Managed Disks oferecem os seguintes benefícios:
- Você não precisa criar previamente um conjunto de contas de armazenamento do Azure para VMs de conjunto de dimensionamento.
- Você pode definir [discos de dados anexados](virtual-machine-scale-sets-attached-disks.md) para as VMs no seu conjunto de dimensionamento.
- Conjuntos de dimensionamento podem ser configurados para [oferecer suporte a até 1.000 VMs em um conjunto](virtual-machine-scale-sets-placement-groups.md). 

Você pode criar conjuntos de dimensionamento comManaged Disks começando com a versão "2016-04-30-preview" da API de computação do Azure. Para obter informações sobre como converter um modelo de conjunto de dimensionamento em Managed Disks, consulte [Como converter um modelo de conjunto de dimensionamento em um modelo de conjunto de dimensionamento de disco gerenciado](virtual-machine-scale-sets-convert-template-to-md.md).

### <a name="user-managed-storage"></a>Armazenamento gerenciado pelo usuário
Um conjunto de dimensionamento que não é definido com os Azure Managed Disks se baseia em contas de armazenamento criadas por usuários para armazenar os discos do sistema operacional das VMs no conjunto. Recomendamos uma taxa de 20 VMs ou menor por conta de armazenamento para atingir o máximo de E/S e também tirar proveito do _provisionamento em excesso_ (confira abaixo). Também recomendamos distribuir os caracteres iniciais dos nomes de conta de armazenamento pelo alfabeto. Isso ajuda a distribuir a carga entre diferentes sistemas internos. 

>[!NOTE]
>A versão da API de conjuntos de dimensionamento de VMs `2016-04-30-preview` oferece suporte usando os Azure Managed Disks para o disco do sistema operacional e discos de dados extras. Para obter mais informações, confira [Visão geral do Managed Disks](../storage/storage-managed-disks-overview.md) e [Usar discos de dados anexados](virtual-machine-scale-sets-attached-disks.md). 

## <a name="overprovisioning"></a>Provisionamento em excesso
Desde a versão da API "2016-03-30", os conjuntos de dimensionamento de VMs realizam "provisionamento em excesso" de VMs por padrão. Com o provisionamento em excesso ativado, o conjunto de dimensionamento rotaciona mais VMs do que o solicitado e exclui as VMs extras assim que o número de VMs solicitado é provisionado com êxito. O provisionamento em excesso melhora as taxas de sucesso do provisionamento e reduz o tempo de implantação. Você não pagará por essas VMs extras e elas não contam nos seus limites de cota.

Embora o provisionamento em excesso melhore as taxas de sucesso do provisionamento, tal comportamento pode ser confuso para um aplicativo que não foi projetado para lidar com o surgimento e desaparecimento repentino de VMs extras. Para desativar o provisionamento em excesso, verifique se você tem a seguinte cadeia de caracteres no seu modelo: "overprovision": "false". Mais detalhes podem ser encontrados na [documentação da API REST do conjunto de escala da VM](https://msdn.microsoft.com/library/azure/mt589035.aspx).

Se o seu conjunto de dimensionamento usa armazenamento gerenciado pelo usuário, e você desativar o provisionamento em excesso, você poderá ter mais de 20 VMs por conta de armazenamento, mas não é recomendável ultrapassar 40 VMs para não prejudicar o desempenho do E/S. 

## <a name="limits"></a>limites
Um conjunto de dimensionamento criado em uma imagem do Marketplace (também conhecido como uma imagem de plataforma) e configurado para usar Azure Managed Disks oferece suporte a uma capacidade de até 1.000 VMs. Se você configurar seu conjunto de dimensionamento para suportar mais de 100 VMs, nem todos os cenários funcionam da mesma maneira, o balanceamento de carga é um exemplo. Para obter mais informações, confira [Como trabalhar com conjuntos de dimensionamento grandes de máquinas virtuais](virtual-machine-scale-sets-placement-groups.md). 

Um conjunto de dimensionamento configurado com contas de armazenamento gerenciadas pelo usuário é atualmente limitada a 100 VMs (e são recomendadas 5 contas de armazenamento para essa escala).

Um conjunto de dimensionamento criado em uma imagem personalizada (criado por você) pode ter uma capacidade de até 100 VMs quando configuradas com discos gerenciados do Azure. Se o conjunto de dimensionamento é configurado com contas de armazenamento gerenciadas pelo usuário, ele deve criar todos os VHDs de disco do sistema operacional em uma conta de armazenamento. Como resultado, o número máximo recomendado de VMs em um conjunto de dimensionamento criado em uma imagem personalizada e em um armazenamento gerenciado pelo usuário é 20. Se você desativar o provisionamento em excesso, será possível ir até 40.

Para ter mais VMs do que esses limites permitem, você precisará implantar vários conjuntos de dimensionamento, conforme mostrado [neste modelo](https://github.com/Azure/azure-quickstart-templates/tree/master/301-custom-images-at-scale).




<!--HONumber=Feb17_HO2-->


