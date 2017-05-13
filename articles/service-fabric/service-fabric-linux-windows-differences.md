---
title: "Diferenças no Azure Service Fabric entre Linux e Windows | Microsoft Docs"
description: "As diferenças entre a visualização do Azure Service Fabric no Linux e do Azure Service Fabric no Windows."
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: 
ms.assetid: d552c8cd-67d1-45e8-91dc-871853f44fc6
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/23/2017
ms.author: subramar
ms.translationtype: Human Translation
ms.sourcegitcommit: e155891ff8dc736e2f7de1b95f07ff7b2d5d4e1b
ms.openlocfilehash: 00c1f10f7df865344233143ce9cdf546e64acca0
ms.contentlocale: pt-br
ms.lasthandoff: 05/02/2017


---
# <a name="differences-between-service-fabric-on-linux-preview-and-windows-generally-available"></a>Diferenças entre o Service Fabric no Linux (visualização) e no Windows (geralmente disponível)

Como o Service Fabric no Linux é uma visualização, há alguns recursos que têm suporte no Windows, mas ainda não têm no Linux. Eventualmente, os conjuntos de recursos serão iguais quando o Service Fabric ficar disponível no Linux.

* Não há suporte para Coleções Confiáveis (e os Serviços Confiáveis com Estado) no Linux.
* O ReverseProxy não está disponível no Linux.
* O instalador autônomo não está disponível no Linux.
* A validação de esquema XML para arquivos de manifesto não é executada no Linux. 
* O redirecionamento de console não tem suporte no Linux. 
* O Serviço de Análise de Falha (FAS) não está disponível no Linux.
* O suporte do Azure Active Directory não está disponível no Linux.
* Alguns equivalentes de comando de CLI do Powershell não estão disponíveis.
* Somente um subconjunto de comandos do Powershell pode ser executado em um cluster do Linux (como explicado na próxima seção).

>[!NOTE]
>O redirecionamento de console não tem suporte em clusters de produção, mesmo no Windows.

As ferramentas de desenvolvimento também são diferente entre o Windows e o Linux. VisualStudio, Powershell, VSTS e ETW são usados no Windows, enquanto Yeoman, Eclipse, Jenkins e LTTng são usados no Linux.

## <a name="powershell-cmdlets-that-do-not-work-against-a-linux-service-fabric-cluster"></a>Cmdlets do PowerShell que não funcionam em um cluster Linux do Service Fabric

* Invoke-ServiceFabricChaosTestScenario
* Invoke-ServiceFabricFailoverTestScenario
* Invoke-ServiceFabricPartitionDataLoss
* Invoke-ServiceFabricPartitionQuorumLoss
* Restart-ServiceFabricPartition
* Start-ServiceFabricNode
* Stop-ServiceFabricNode
* Get-ServiceFabricImageStoreContent
* Get-ServiceFabricChaosReport
* Get-ServiceFabricNodeTransitionProgress
* Get-ServiceFabricPartitionDataLossProgress
* Get-ServiceFabricPartitionQuorumLossProgress
* Get-ServiceFabricPartitionRestartProgress
* Get-ServiceFabricTestCommandStatusList
* Remove-ServiceFabricTestState
* Start-ServiceFabricChaos
* Start-ServiceFabricNodeTransition
* Start-ServiceFabricPartitionDataLoss
* Start-ServiceFabricPartitionQuorumLoss
* Start-ServiceFabricPartitionRestart
* Stop-ServiceFabricChaos
* Stop-ServiceFabricTestCommand
* Cmd
* Get-ServiceFabricNodeConfiguration
* Get-ServiceFabricClusterConfiguration
* Get-ServiceFabricClusterConfigurationUpgradeStatus
* Get-ServiceFabricPackageDebugParameters
* New-ServiceFabricPackageDebugParameter
* New-ServiceFabricPackageSharingPolicy
* Add-ServiceFabricNode
* Copy-ServiceFabricClusterPackage
* Get-ServiceFabricRuntimeSupportedVersion
* Get-ServiceFabricRuntimeUpgradeVersion
* New-ServiceFabricCluster
* New-ServiceFabricNodeConfiguration
* Remove-ServiceFabricCluster
* Remove-ServiceFabricClusterPackage
* Remove-ServiceFabricNodeConfiguration
* Test-ServiceFabricClusterManifest
* Test-ServiceFabricConfiguration
* Update-ServiceFabricNodeConfiguration
* Approve-ServiceFabricRepairTask
* Complete-ServiceFabricRepairTask
* Get-ServiceFabricRepairTask
* Invoke-ServiceFabricDecryptText
* Invoke-ServiceFabricEncryptSecret
* Invoke-ServiceFabricEncryptText
* Invoke-ServiceFabricInfrastructureCommand
* Invoke-ServiceFabricInfrastructureQuery
* Remove-ServiceFabricRepairTask
* Start-ServiceFabricRepairTask
* Stop-ServiceFabricRepairTask
* Update-ServiceFabricRepairTaskHealthPolicy



## <a name="next-steps"></a>Próximas etapas
* [Preparar seu ambiente de desenvolvimento no Linux](service-fabric-get-started-linux.md)
* [Preparar seu ambiente de desenvolvimento no OSX](service-fabric-get-started-mac.md)
* [Criar e implantar seu primeiro aplicativo Java de do Service Fabric no Linux usando Yeoman](service-fabric-create-your-first-linux-application-with-java.md)
* [Criar e implantar seu primeiro aplicativo Java do Service Fabric no Linux usando o plug-in do Service Fabric para o Eclipse](service-fabric-get-started-eclipse.md)
* [Criar seu primeiro aplicativo do CSharp no Linux](service-fabric-create-your-first-linux-application-with-csharp.md)
* [Usar a CLI do Azure para gerenciar seus aplicativos do Service Fabric](service-fabric-azure-cli.md)

