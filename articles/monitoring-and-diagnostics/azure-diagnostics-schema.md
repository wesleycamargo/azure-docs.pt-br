---
title: "Lista de versões de esquema de configuração do Diagnóstico do Azure | Microsoft Docs"
description: "Usado para configurar a coleta de contadores de desempenho em máquinas virtuais do Azure, conjuntos de dimensionamento de VM, o Service Fabric e serviços de nuvem."
services: multiple
documentationcenter: .net
author: rboucher
manager: carmonm
editor: 
ms.assetid: 
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 01/23/2017
ms.author: robb
translationtype: Human Translation
ms.sourcegitcommit: 8c9c9dea1248205aa6303e11e1166d5d38786c1b
ms.openlocfilehash: 2e1bc45b55cd79af8579a5ddaf43cf0db019c92f


---
# <a name="list-of-azure-diagnostics-versions"></a>Lista de versões de Diagnóstico do Azure
Esta página é um índice das versões dos Esquemas de Diagnóstico do Azure fornecida como parte do SDK do Microsoft Azure.  

> [!NOTE]
> O Diagnóstico do Azure é o componente usado para coletar contadores de desempenho e outras estatísticas de Máquinas Virtuais do Azure, Conjuntos de Dimensionamento de Máquina Virtual, Service Fabric e Serviços de Nuvem.  Esta página só é relevante se você estiver usando um desses serviços.
>

O Diagnóstico do Azure é usado em conjunto com outros produtos de diagnóstico da Microsoft, como Azure Monitor, Application Insights e Log Analytics.

## <a name="azure-sdk-and-diagnostics-versions-shipping-chart"></a>Gráfico de envio de versões do SDK e Diagnóstico do Azure  

|Versão do SDK do Azure | Versão do Diagnóstico do Azure | Modelo|  
|------------------|---------------------------|------|  
|1.x               |1.0                         | plug-in|  
|2.0 - 2.4         |1.0                         |"|  
|2.5               |1.2                         |extensão|  
|2.6               |1,3                         |"|  
|2.7               |1.4                         |"|  
|2.8               |1.5                         |"|  

 O Diagnóstico do Azure versão 1.0 foi enviado pela primeira vez em um modelo de plug-in, o que significa que quando você instalou o SDK do Azure, recebeu a versão do Diagnóstico do Azure fornecida com ele.  

 A partir do SDK 2.5 (versão de diagnóstico 1.2), o diagnóstico do Azure passou para um modelo de extensão. As ferramentas para utilizar os novos recursos só foram disponibilizadas em SDKs mais recentes do Azure, mas qualquer Serviço de Nuvem ou Máquina Virtual que usasse o diagnóstico escolheria a versão mais recente diretamente do Azure.  

 Por exemplo, alguém que ainda usasse o SDK 2.5 carregaria o Diagnóstico 1.5, mesmo que não estivessem usando os recursos mais recentes.  

## <a name="azure-diagnostics-schemas-index"></a>Índice de esquemas do Diagnóstico do Azure  
[Esquema de Configuração do Diagnóstico 1.0](azure-diagnostics-schema-1dot0.md)  

[Esquema de Configuração do Diagnóstico 1.2](azure-diagnostics-schema-1dot2.md)  

[Esquema de Configuração do Diagnóstico 1.3 a 1.5](azure-diagnostics-schema-1dot3-to-1dot5.md)  



<!--HONumber=Jan17_HO5-->


