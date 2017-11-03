---
title: "Opções de depuração dos serviços de nuvem do Azure | Microsoft Docs"
description: "Depurando serviços de nuvem do Azure"
services: visual-studio-online
documentationcenter: n/a
author: mikejo
manager: ghogen
editor: 
ms.assetid: 80755da7-8350-4f5c-97ce-2962beabb36d
ms.service: visual-studio-online
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 03/18/2017
ms.author: mikejo
ms.openlocfilehash: 196a39a75f8a3151c277f0b2a7944e6886192171
ms.sourcegitcommit: b83781292640e82b5c172210c7190cf97fabb704
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/27/2017
---
# <a name="learn-the-various-ways-to-debug-an-azure-cloud-service"></a>Conheça as várias maneiras de depurar um serviço de nuvem do Azure
Este artigo fornece links para as várias maneiras de depurar um serviço de nuvem do Azure. 

## <a name="debugging-an-azure-cloud-service-in-visual-studio"></a>Depurando um serviço de nuvem do Azure no Visual Studio
Você pode economizar tempo e dinheiro usando o emulador de computação para depurar o serviço de nuvem em um computador local. Ao depurar um serviço localmente antes de implantá-lo, você pode aprimorar a confiabilidade e o desempenho sem pagar pelo tempo de computação. No entanto, alguns erros podem ocorrer somente quando um serviço de nuvem é executado no Azure. Os erros que ocorrem apenas quando um serviço de nuvem é executado no Azure podem ser depurados com a habilitação da depuração remota ao publicar o serviço e, em seguida, com a anexação do depurador a uma instância de função. Para saber mais, consulte [Depurar o serviço de nuvem no computador local](vs-azure-tools-debug-cloud-services-virtual-machines.md#debug-your-cloud-service-on-your-local-computer)

## <a name="using-azure-diagnostics"></a>Usando o Diagnóstico do Azure 
Você pode usar o Diagnóstico do Azure para registrar em log informações detalhadas do código em execução nas funções, estejam as funções em execução no ambiente de desenvolvimento ou no Azure. Para saber mais, confira [Habilitando o Diagnóstico do Azure nos Serviços de Nuvem do Azure](http://go.microsoft.com/fwlink/p/?LinkId=400450).

## <a name="using-intellitrace"></a>Usando o IntelliTrace 
Se você estiver usando o Visual Studio Enterprise para escrever funções destinadas ao .NET Framework 4.5, poderá habilitar o IntelliTrace durante a implantação de um serviço de nuvem do Azure por meio do Visual Studio. O IntelliTrace fornece um log que você pode usar com o Visual Studio para depurar seu aplicativo, como se ele estivesse em execução no Azure. Para saber mais, consulte [Depurando um serviço de nuvem publicado com o IntelliTrace e o Visual Studio](http://go.microsoft.com/fwlink/p/?LinkId=623016).

## <a name="remote-debugging"></a>Depuração remota 
Você pode habilitar a depuração remota nos serviços de nuvem no momento em que implanta o serviço de nuvem usando o Visual Studio. Se você optar por habilitar a depuração remota para uma implantação, os serviços de depuração remota serão instalados nas máquinas virtuais que executam cada instância de função. Esses serviços – como `msvsmon.exe` – não afetam o desempenho nem resultam em custos extras. Para saber mais, consulte [Depurar o serviço de nuvem no Azure](vs-azure-tools-debug-cloud-services-virtual-machines.md#debug-a-cloud-service-in-azure)

## <a name="next-steps"></a>Próximas etapas
- [Depurando um serviço de nuvem ou uma VM do Azure no Visual Studio](./vs-azure-tools-debug-cloud-services-virtual-machines.md) – conheça os detalhes de como depurar serviços de nuvem do Azure.