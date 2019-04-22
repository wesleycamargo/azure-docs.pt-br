---
title: Fazer upgrade do tempo de execução do Service Fabric no Azure | Microsoft Docs
description: Neste tutorial, você aprende a usar o PowerShell para atualizar o tempo de execução de um cluster do Service Fabric hospedado pelo Azure.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/28/2017
ms.author: aljo
ms.custom: mvc
ms.openlocfilehash: 7e48684024d370d64f44b55cb4df0efb8f16cd3b
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/18/2019
ms.locfileid: "59046233"
---
# <a name="tutorial-upgrade-the-runtime-of-a-service-fabric-cluster-in-azure"></a>Tutorial: Atualizar o tempo de execução de um cluster do Service Fabric no Azure

Este tutorial é a parte quatro de uma série e mostra como fazer upgrade do tempo de execução do Service Fabric ou de um cluster do Azure Service Fabric. Esta parte do tutorial foi criada para clusters do Service Fabric em execução no Azure e não se aplica a clusters do Service Fabric autônomo.

> [!WARNING]
> Esta parte do tutorial requer o PowerShell. As ferramentas da CLI do Azure ainda não dão suporte ao upgrade do tempo de execução do cluster. Como alternativa, pode ser feito o upgrade de um cluster no portal. Para saber mais, veja [Upgrade de um cluster do Azure Service Fabric](service-fabric-cluster-upgrade.md).

Se o cluster já estiver executando o tempo de execução mais recente do Service Fabric, você não precisará executar esta etapa. No entanto, este artigo pode ser usado para instalar qualquer tempo de execução com suporte em um cluster do Azure Service Fabric.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Ler a versão do cluster
> * Definir a versão de cluster

Nesta série de tutoriais, você aprenderá a:
> [!div class="checklist"]
> * Criar um [cluster do Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md) seguro no Azure usando um modelo
> * [Monitorar um cluster](service-fabric-tutorial-monitor-cluster.md)
> * [Reduzir ou escalar um cluster horizontalmente](service-fabric-tutorial-scale-cluster.md)
> * Atualizar o tempo de execução de um cluster
> * [Excluir um cluster](service-fabric-tutorial-delete-cluster.md)


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar este tutorial:

* Se você não tem uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Instale o [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps) ou a [CLI do Azure](/cli/azure/install-azure-cli).
* Criar um [cluster do Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md) seguro no Azure
* Configurar um ambiente de desenvolvimento do Windows. Instale as cargas de trabalho do [Visual Studio 2017](https://www.visualstudio.com) e o **Desenvolvimento do Azure**, de **desenvolvimento ASP.NET e Web** e de **desenvolvimento multiplataforma do .NET Core**.  Em seguida, configure um [ambiente de desenvolvimento .NET](service-fabric-get-started.md).

### <a name="sign-in-to-azure"></a>Entrar no Azure

Entre na sua conta do Azure e selecione sua assinatura antes de executar comandos do Azure.

```powershell
Connect-AzAccount
Get-AzSubscription
Set-AzContext -SubscriptionId <guid>
```

## <a name="get-the-runtime-version"></a>Obter a versão de tempo de execução

Depois que você tiver se conectado ao Azure e selecionado a assinatura que contém o cluster do Service Fabric, poderá obter a versão de tempo de execução do cluster.

```powershell
Get-AzServiceFabricCluster -ResourceGroupName SFCLUSTERTUTORIALGROUP -Name aztestcluster `
    | Select-Object ClusterCodeVersion
```

Ou simplesmente obtenha uma lista de todos os clusters na sua assinatura com o seguinte:

```powershell
Get-AzServiceFabricCluster | Select-Object Name, ClusterCodeVersion
```

Anote o valor de **ClusterCodeVersion**. Esse valor será usado na próxima seção.

## <a name="upgrade-the-runtime"></a>Fazer upgrade do tempo de execução

Use o valor de **ClusterCodeVersion** da seção anterior com o cmdlet `Get-ServiceFabricRuntimeUpgradeVersion` para descobrir para quais versões há upgrades disponíveis. Esse cmdlet só pode ser executado em um computador conectado à internet. Por exemplo, se quiser ver para quais versões de tempo de execução você pode fazer upgrade da versão `5.7.198.9494`, use o seguinte comando:

```powershell
Get-ServiceFabricRuntimeUpgradeVersion -BaseVersion "5.7.198.9494"
```

Com uma lista de versões, você poderá fazer upgrade do cluster do Azure Service Fabric para um tempo de execução mais recente. Por exemplo, se versão `6.0.219.9494` estiver disponível, use o seguinte comando para fazer upgrade do seu cluster.

```powershell
Set-AzServiceFabricUpgradeType -ResourceGroupName SFCLUSTERTUTORIALGROUP `
                                    -Name aztestcluster `
                                    -UpgradeMode Manual `
                                    -Version "6.0.219.9494"
```

> [!IMPORTANT]
> O upgrade do tempo de execução do cluster pode demorar muito para ser concluído. O PowerShell fica bloqueado durante a execução do upgrade. Você pode usar outra sessão do PowerShell para verificar o status do upgrade.

O status do upgrade pode ser monitorado com o PowerShell ou a CLI do Azure Service Fabric (sfctl).

Primeiro, conecte-se ao cluster com o certificado SSL criado na primeira parte do tutorial. Use o cmdlet `Connect-ServiceFabricCluster` ou `sfctl cluster upgrade-status`.

```powershell
$endpoint = "<mycluster>.southcentralus.cloudapp.azure.com:19000"
$thumbprint = "63EB5BA4BC2A3BADC42CA6F93D6F45E5AD98A1E4"

Connect-ServiceFabricCluster -ConnectionEndpoint $endpoint `
                             -KeepAliveIntervalInSec 10 `
                             -X509Credential -ServerCertThumbprint $thumbprint `
                             -FindType FindByThumbprint -FindValue $thumbprint `
                             -StoreLocation CurrentUser -StoreName My
```

```azurecli
sfctl cluster select --endpoint https://aztestcluster.southcentralus.cloudapp.azure.com:19080 \
--pem ./aztestcluster201709151446.pem --no-verify
```

Em seguida, use `Get-ServiceFabricClusterUpgrade` ou `sfctl cluster upgrade-status` para exibir o status. Algo semelhante ao resultado a seguir é mostrado.

```powershell
Get-ServiceFabricClusterUpgrade

TargetCodeVersion                          : 6.0.219.9494
TargetConfigVersion                        : 3
StartTimestampUtc                          : 11/28/2017 3:09:48 AM
UpgradeState                               : RollingForwardPending
UpgradeDuration                            : 00:09:00
CurrentUpgradeDomainDuration               : 00:09:00
NextUpgradeDomain                          : 1
UpgradeDomainsStatus                       : { "0" = "Completed";
                                             "1" = "Pending";
                                             "2" = "Pending";
                                             "3" = "Pending";
                                             "4" = "Pending" }
UpgradeKind                                : Rolling
RollingUpgradeMode                         : Monitored
FailureAction                              : Rollback
ForceRestart                               : False
UpgradeReplicaSetCheckTimeout              : 37201.09:59:01
HealthCheckWaitDuration                    : 00:05:00
HealthCheckStableDuration                  : 00:05:00
HealthCheckRetryTimeout                    : 00:45:00
UpgradeDomainTimeout                       : 02:00:00
UpgradeTimeout                             : 12:00:00
ConsiderWarningAsError                     : False
MaxPercentUnhealthyApplications            : 0
MaxPercentUnhealthyNodes                   : 100
ApplicationTypeHealthPolicyMap             : {}
EnableDeltaHealthEvaluation                : True
MaxPercentDeltaUnhealthyNodes              : 0
MaxPercentUpgradeDomainDeltaUnhealthyNodes : 0
ApplicationHealthPolicyMap                 : {}
```

```azurecli
sfctl cluster upgrade-status

{
  "codeVersion": "6.0.219.9494",
  "configVersion": "3",

... item cut to save space ...

  },
  "upgradeDomains": [
    {
      "name": "0",
      "state": "Completed"
    },
    {
      "name": "1",
      "state": "Pending"
    },
    {
      "name": "2",
      "state": "Pending"
    },
    {
      "name": "3",
      "state": "Pending"
    },
    {
      "name": "4",
      "state": "Pending"
    }
  ],
  "upgradeDurationInMilliseconds": "PT1H2M4.63889S",
  "upgradeState": "RollingForwardPending"
}
```

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu como:

> [!div class="checklist"]
> * Obter a versão do tempo de execução do cluster
> * Fazer upgrade do tempo de execução do cluster
> * Monitorar o upgrade

[!div class="checklist"]
> * Obter a versão do tempo de execução do cluster
> * Fazer upgrade do tempo de execução do cluster
> * Monitorar o upgrade

