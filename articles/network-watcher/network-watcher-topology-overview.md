---
title: "Introdução à topologia no Observador de Rede do Azure | Microsoft Docs"
description: "Esta página fornece uma visão geral dos recursos de tecnologia do Observador de Rede"
services: network-watcher
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
ms.assetid: e753a435-38e0-482b-846b-121cb547555c
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: fa79ab0ab80481c8969c6fa653e92b10985cab3b
ms.openlocfilehash: 42443f614b76b8180ac163b9889163021adbf048
ms.lasthandoff: 03/31/2017

---

# <a name="introduction-to-topology-in-azure-network-watcher"></a>Introdução à topologia no Observador de Rede do Azure

A topologia retorna um gráfico de recursos de rede em uma rede virtual. O gráfico mostra a interconexão entre os recursos para representar a conectividade de rede de ponta a ponta.

![visão geral da topologia][1]

No portal, a topologia retorna os objetos de recursos de acordo com a rede virtual. As relações são representadas por linhas entre os Recursos fora da região do Observador de Rede, mesmo se o grupo de recursos não for exibido. Os recursos retornados na exibição do portal são um subconjunto dos componentes de rede mostrados. Para ver a lista completa de recursos de rede, use [PowerShell](network-watcher-topology-powershell.md) ou [REST](network-watcher-topology-rest.md)

> [!NOTE]
> É necessário ter uma instância do Observador de Rede em cada região na qual você deseja executar a topologia.

Como os recursos retornam, a conexão entre eles é modelada em duas relações.

- **Confinamento** - Exemplo: uma rede virtual contém uma sub-rede que contém uma NIC
- **Associados** - Exemplo: uma NIC está associada a uma VM

### <a name="next-steps"></a>Próximas etapas

Aprenda a usar o PowerShell para recuperar o modo de exibição de Topologia visitando [Topologia do Observador de Rede com o PowerShell](network-watcher-topology-powershell.md)

<!--Image references-->

[1]: ./media/network-watcher-topology-overview/topology.png

