---
title: "Recomendações de alta disponibilidade do Azure Advisor | Microsoft Docs"
description: "Use o Azure Advisor para melhorar a alta disponibilidade das implantações do Azure."
services: advisor
documentationcenter: NA
author: kumudd
manager: carmonm
editor: 
ms.assetid: 
ms.service: advisor
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/16/2016
ms.author: kumud
translationtype: Human Translation
ms.sourcegitcommit: e7fc6cb4ee4752bb7ed90a8f64edcc7c82ec3a88
ms.openlocfilehash: a6ee1d018bf33aa76d089d20f6e71318a3586aa8
ms.lasthandoff: 02/21/2017

---

# <a name="advisor-high-availability-recommendations"></a>Recomendações de alta disponibilidade do Advisor

O Advisor ajuda a garantir e melhorar a continuidade de aplicativos críticos aos negócios. Você pode obter recomendações de alta disponibilidade do Advisor na guia **Alta Disponibilidade** do painel Advisor.

![](./media/advisor-high-availability-recommendations/advisor-high-availability-tab.png)


## <a name="virtual-machines-without-an-availability-set"></a>Máquinas virtuais sem um conjunto de disponibilidade

O Advisor identifica máquinas virtuais que não fazem parte de um conjunto de disponibilidade e recomenda movê-las para um conjunto de disponibilidade. Para oferecer redundância para o seu aplicativo, recomendamos que agrupe uma ou mais máquinas virtuais em um conjunto de disponibilidade. Essa configuração garante que durante um evento de manutenção planejada ou não planejada, pelo menos, uma máquina virtual esteja disponível e cumpra o SLA de máquina virtual do Azure. Você pode optar por criar um conjunto de disponibilidade para a máquina virtual ou adicionar a máquina virtual a um conjunto de disponibilidade existente.

> [!NOTE]
> Se você optar por criar um conjunto de disponibilidade, será preciso adicionar pelo menos mais uma máquina virtual no conjunto após criá-lo. É recomendável agrupar duas ou mais máquinas virtuais em um conjunto para garantir que uma delas esteja disponível durante uma interrupção.

![](./media/advisor-high-availability-recommendations/advisor-high-availability-create-availability-set.png)

## <a name="availability-sets-with-a-single-virtual-machine"></a>Conjuntos de disponibilidade com uma única máquina virtual 

O Advisor identifica os conjuntos de disponibilidade que contêm uma única máquina virtual e recomenda a adição de uma ou mais máquinas virtuais. Para oferecer redundância para o seu aplicativo, recomendamos que agrupe uma ou mais máquinas virtuais em um conjunto de disponibilidade. Essa configuração garante que durante um evento de manutenção planejada ou não planejada, pelo menos, uma máquina virtual esteja disponível e cumpra o SLA de máquina virtual do Azure. Você pode optar por criar uma máquina virtual ou usar uma máquina virtual existente e adicioná-la ao conjunto de disponibilidade.  


![](./media/advisor-high-availability-recommendations/advisor-high-availability-add-vm-to-availability-set.png)

## <a name="virtual-machines-with-standard-disks"></a>Máquinas virtuais com Discos Standard

O Advisor identifica máquinas virtuais com Discos Standard e recomenda a atualização para Discos Premium.  
O Armazenamento Premium do Azure dá suporte de disco de alto desempenho e baixa latência para máquinas virtuais executando cargas de trabalho intensivas para entradas e saídas. Os discos da máquina virtual que usam Armazenamento Premium armazenam dados em SSDs (unidades de estado sólido). É recomendável migrar qualquer disco de máquina virtual que exija IOPS alta para o Armazenamento Premium do Azure para obter o melhor desempenho para o seu aplicativo. Se o disco não exigir IOPS alta, você poderá limitas os custos mantendo-o em Armazenamento Standard. O Armazenamento Standard armazena dados de disco da máquina virtual em HDDs (Unidades de Disco Rígido) em vez de SSDs. Você pode optar por migrar seus discos de máquina virtual para Discos Premium. Os Discos Premium são compatíveis com a maioria dos SKUs de máquina virtual. No entanto, em alguns casos, se você quiser usar Discos Premium, talvez também seja preciso fazer upgrade do SKU da máquina virtual.   

![](./media/advisor-high-availability-recommendations/advisor-high-availability-upgrade-to-premium-disks.png) 

## <a name="how-to-access-high-availability-recommendations-in-advisor"></a>Como acessar as recomendações de alta disponibilidade no Advisor

1. Entre no [Portal do Azure](https://portal.azure.com).
2. No painel de navegação esquerdo, clique em **Mais serviços**, no painel do menu de serviço, role para baixo até **Monitoramento e Gerenciamento** e clique em **Azure Advisor**. Isso inicia o painel do Advisor. 
3. No painel do Advisor, clique na guia **Alta Disponibilidade** e selecione a assinatura para a qual deseja receber recomendações.

> [!NOTE]
> Para acessar as recomendações do assistente, você deve primeiro **registrar** sua assinatura com o assistente. Uma assinatura é registrada quando um **proprietário da assinatura** inicia o painel Assistente e clica no botão **Obter recomendações**. Essa é uma **operação única**. Depois que uma assinatura for registrada, as recomendações do Assistente poderão ser acessadas por **proprietário**s, **colaborador**es ou **leitor**es de uma assinatura, grupo de recursos ou um recurso específico.

## <a name="next-steps"></a>Próximas etapas

Veja estes recursos para saber mais sobre as recomendações do Advisor:
-  [Introdução ao Azure Advisor](advisor-overview.md)
-  [Introdução ao Advisor](advisor-get-started.md)
-  [Recomendações de segurança do Advisor](advisor-security-recommendations.md)
-  [Recomendações de desempenho do Advisor](advisor-performance-recommendations.md)
-  [Recomendações de custo do Advisor](advisor-performance-recommendations.md)

