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
ms.date: 7/27/2017
ms.author: subramar
ms.translationtype: Human Translation
ms.sourcegitcommit: c785ad8dbfa427d69501f5f142ef40a2d3530f9e
ms.openlocfilehash: e2d21b28c482427c60f708171336e6901b50e544
ms.contentlocale: pt-br
ms.lasthandoff: 05/26/2017

---
# <a name="differences-between-service-fabric-on-linux-preview-and-windows-generally-available"></a>Diferenças entre o Service Fabric no Linux (visualização) e no Windows (geralmente disponível)

Como o Service Fabric no Linux é uma visualização, há alguns recursos que têm suporte no Windows, mas ainda não têm no Linux. Eventualmente, os conjuntos de recursos serão iguais quando o Service Fabric ficar disponível no Linux. Nas futuras versões, esse intervalo de recursos será reduzido. Existem as seguintes diferenças entre as versões mais recentes disponíveis (ou seja, entre a versão 5.6 para Windows e a versão 5.5 no Linux): 

* Coleções Confiáveis (e Serviços Confiáveis com Estado) 
* ReverseProxy 
* Instalador autônomo 
* Validação do esquema XML para os arquivos de manifesto 
* Redirecionamento do console 
* Serviço de Análise de Falha (FAS)
* Docker Compose, volume e drivers de log para contêineres 
* Governança de recursos para contêineres e serviços 
* Serviço DNS
* Suporte ao Azure Active Directory
* Equivalentes de comando da CLI de certos comandos do Powershell 
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

