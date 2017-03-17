---
title: "Atualizar um cluster autônomo do Azure Service Fabric no Windows Server | Microsoft Docs"
description: "Atualize o código do Azure Service Fabric e/ou a configuração que executa um cluster do Service Fabric autônomo, incluindo a configuração de modo de atualização de cluster."
services: service-fabric
documentationcenter: .net
author: ChackDan
manager: timlt
editor: 
ms.assetid: 66296cc6-9524-4c6a-b0a6-57c253bdf67e
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/02/2017
ms.author: chackdan
translationtype: Human Translation
ms.sourcegitcommit: 094729399070a64abc1aa05a9f585a0782142cbf
ms.openlocfilehash: df15775f8e93c1dfad82c69ee4caa7838a39a545
ms.lasthandoff: 03/07/2017


---
# <a name="upgrade-your-standalone-azure-service-fabric-cluster-on-windows-server"></a>Atualizar seu cluster do Azure Service Fabric autônomo no Windows Server
> [!div class="op_single_selector"]
> * [Cluster do Azure](service-fabric-cluster-upgrade.md)
> * [Cluster Independente](service-fabric-cluster-upgrade-windows-server.md)
>
>

Para qualquer sistema moderno, oferecer a possibilidade de atualização é fundamental para o sucesso duradouro de seu produto. Um cluster do Azure Service Fabric é um recurso que pertence a você. Este artigo descreve como garantir que o cluster sempre execute versões com suporte do código e de configurações do Service Fabric.

## <a name="control-the-fabric-version-that-runs-on-your-cluster"></a>Controlar a versão do Fabric em execução no Cluster
Você pode configurar o cluster para baixar as atualizações do Service Fabric quando a Microsoft lança uma nova versão. A outra opção é selecionar uma versão do Fabric com suporte para seu cluster.

Para controlar a versão do Fabric, defina a configuração "fabricClusterAutoupgradeEnabled" do cluster como true ou false.

> [!NOTE]
> Certifique-se de que o cluster sempre esteja executando uma versão do Service Fabric com suporte. Quando o lançamento uma nova versão do Service Fabric é anunciado, a versão anterior é programada para encerrar sua vida útil após um mínimo de 60 dias a partir da data desse anúncio. As novas versões são anunciadas [no blog da equipe do Service Fabric](https://blogs.msdn.microsoft.com/azureservicefabric/).
>
>

Será possível atualizar o cluster para a nova versão apenas se você estiver usando uma configuração de nó de estilo de produção, em que cada nó do Service Fabric será alocado em uma máquina virtual ou física separada. Se você tiver um cluster de desenvolvimento no qual exista mais de um nó do Service Fabric em uma única máquina virtual ou física, você deverá destruir o cluster e recriá-lo com a nova versão.

Os dois fluxos de trabalho a seguir estão disponíveis para atualizar seu cluster para a versão mais recente ou para uma versão do Service Fabric com suporte:


*   Clusters com conectividade para baixar a versão mais recente automaticamente
*   Clusters sem conectividade para baixar a versão mais recente do Service Fabric

### <a name="upgrade-the-clusters-with-connectivity-to-download-the-latest-code-and-configuration"></a>Atualizar os clusters com conectividade para baixar o código e a configuração mais recentes
Use estas etapas para atualizar seu cluster para uma versão com suporte se os nós de cluster tiverem conectividade com a Internet para acesso ao [Centro de Download da Microsoft](http://download.microsoft.com).

Para clusters que tenham conectividade com o [Centro de Download da Microsoft](http://download.microsoft.com), verificamos periodicamente a disponibilidade de novas versões do Service Fabric.

Quando uma nova versão do Service Fabric estiver disponível, o pacote será baixado localmente para o cluster e provisionado para atualização. Além de informar o cliente sobre essa nova versão, o sistema colocará um aviso explícito de integridade do cluster semelhante ao seguinte:

"O suporte à versão atual do cluster, [version#], termina em [Data]."

Depois que o cluster estiver executando a versão mais recente, o aviso será removido.

#### <a name="cluster-upgrade-workflow"></a>Fluxo de trabalho de atualização do cluster
Ao ver o aviso de integridade do cluster, você deve fazer o seguinte:

1. Conecte-se ao cluster de qualquer computador que tenha acesso de administrador a todos os computadores listados como nós no cluster. O computador no qual o script a seguir é executado não precisa fazer parte do cluster.

    ```powershell

    ###### Connect to the secure cluster using certs
    $ClusterName= "mysecurecluster.something.com:19000"
    $CertThumbprint= "70EF5E22ADB649799DA3C8B6A6BF7FG2D630F8F3"
    Connect-serviceFabricCluster -ConnectionEndpoint $ClusterName -KeepAliveIntervalInSec 10 `
        -X509Credential `
        -ServerCertThumbprint $CertThumbprint  `
        -FindType FindByThumbprint `
        -FindValue $CertThumbprint `
        -StoreLocation CurrentUser `
        -StoreName My
    ```

2. Obtenha a lista de versões do Service Fabric para as quais você pode atualizar.

    ```powershell

    ###### Get the list of available Service Fabric versions
    Get-ServiceFabricRegisteredClusterCodeVersion
    ```

    Você deverá receber uma saída semelhante a esta:

    ![Obter versões do Service Fabric][getfabversions]

3. Inicie uma atualização de cluster para uma das versões disponíveis usando o comando [Start-ServiceFabricClusterUpgrade](https://msdn.microsoft.com/library/mt125872.aspx) do Windows PowerShell.

    ```powershell

    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion <codeversion#> -Monitored -FailureAction Rollback

    ###### Cluster upgrade example

    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion 5.3.301.9590 -Monitored -FailureAction Rollback

    ```
   Você pode monitorar o progresso da atualização no Service Fabric Explorer ou executando o comando do Windows PowerShell a seguir.

    ```powershell

    Get-ServiceFabricClusterUpgrade
    ```

Se as políticas de integridade do cluster não forem atendidas, a atualização será revertida. Você pode especificar as políticas de integridade personalizadas para o comando Start-ServiceFabricClusterUpgrade. Para obter mais informações, consulte [Start-ServiceFabricClusterUpgrade](https://msdn.microsoft.com/library/mt125872.aspx).

Depois de corrigir os problemas que resultaram na reversão, você precisará iniciar a atualização novamente, seguindo as mesmas etapas de antes.

### <a name="upgrade-the-clusters-with-no-connectivity-to-download-the-latest-code-and-configuration"></a>Atualizar os clusters sem conectividade para baixar o código e a configuração mais recentes
Use os procedimentos nesta seção para atualizar seu cluster para uma versão com suporte se os nós de cluster não tiverem conectividade com a Internet para acesso ao [Centro de Download da Microsoft](http://download.microsoft.com).

> [!NOTE]
> Se estiver executando um cluster que não esteja conectado à Internet, você precisará monitorar o [blog da equipe do Service Fabric](https://blogs.msdn.microsoft.com/azureservicefabric/) para notificações sobre uma nova versão. O sistema *não* coloca um aviso de integridade do cluster para alertar você sobre a nova versão.  
>
>

1. Modificar a configuração do cluster para definir a seguinte propriedade como false

        "fabricClusterAutoupgradeEnabled": false,

2.      Inicie uma atualização de configuração. Para obter mais informações, consulte [Start-ServiceFabricClusterConfigurationUpgrade](https://msdn.microsoft.com/en-us/library/mt788302.aspx). Antes de iniciar a atualização de configuração, atualize o valor de clusterConfigurationVersion em seu arquivo JSON (JavaScript Object Notation).

```powershell

    Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File>

```

#### <a name="cluster-upgrade-workflow"></a>Fluxo de trabalho de atualização do cluster
1. Baixe a versão mais recente do pacote do documento [Criar cluster do Service Fabric para o Windows Server](service-fabric-cluster-creation-for-windows-server.md).
2. Conecte-se ao cluster de qualquer computador que tenha acesso de administrador a todos os computadores listados como nós no cluster. O computador no qual este script é executado não precisa fazer parte do cluster.

    ```powershell

    ###### Connect to the cluster
    $ClusterName= "mysecurecluster.something.com:19000"
    $CertThumbprint= "70EF5E22ADB649799DA3C8B6A6BF7FG2D630F8F3"
    Connect-serviceFabricCluster -ConnectionEndpoint $ClusterName -KeepAliveIntervalInSec 10 `
        -X509Credential `
        -ServerCertThumbprint $CertThumbprint  `
        -FindType FindByThumbprint `
        -FindValue $CertThumbprint `
        -StoreLocation CurrentUser `
        -StoreName My
    ```
3. Copie o pacote baixado para o repositório de imagens do cluster.

    ```powershell

   ###### Get the list of available Service Fabric versions
    Copy-ServiceFabricClusterPackage -Code -CodePackagePath <name of the .cab file including the path to it> -ImageStoreConnectionString "fabric:ImageStore"

   ###### Code example
    Copy-ServiceFabricClusterPackage -Code -CodePackagePath .\MicrosoftAzureServiceFabric.5.3.301.9590.cab -ImageStoreConnectionString "fabric:ImageStore"

    ```

4. Registre o pacote copiado.

    ```powershell

    ###### Get the list of available Service Fabric versions
    Register-ServiceFabricClusterPackage -Code -CodePackagePath <name of the .cab file>

    ###### Code example
    Register-ServiceFabricClusterPackage -Code -CodePackagePath MicrosoftAzureServiceFabric.5.3.301.9590.cab

     ```
5. Iniciar uma atualização de cluster para uma das versões disponíveis.

    ```Powershell

    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion <codeversion#> -Monitored -FailureAction Rollback

    ###### Code example
    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion 5.3.301.9590 -Monitored -FailureAction Rollback

    ```
   Você pode monitorar o progresso da atualização no Service Fabric Explorer ou executando o comando do Windows PowerShell a seguir.

    ```powershell

    Get-ServiceFabricClusterUpgrade
    ```

    Se as políticas de integridade do cluster não forem atendidas, a atualização será revertida. Você pode especificar as políticas de integridade personalizadas para o comando start-serviceFabricClusterUpgrade. Para obter mais informações, consulte [Start-ServiceFabricClusterUpgrade](https://msdn.microsoft.com/library/mt125872.aspx).

Depois de corrigir os problemas que resultaram na reversão, você precisará iniciar a atualização novamente, seguindo as mesmas etapas de antes.


## <a name="upgrade-the-cluster-configuration"></a>Atualizar a configuração do cluster
Para atualizar a configuração do cluster, execute o comando Start-ServiceFabricClusterConfigurationUpgrade. A atualização de configuração é processada por domínio de atualização.

```powershell

    Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File>

```


## <a name="next-steps"></a>Próximas etapas
* Saiba como personalizar algumas das [configurações de malha do cluster do Service Fabric](service-fabric-cluster-fabric-settings.md).
* Saiba como [reduzir e escalar horizontalmente seu cluster](service-fabric-cluster-scale-up-down.md).
* Saiba mais sobre [atualizações de aplicativo](service-fabric-application-upgrade.md).

<!--Image references-->
[getfabversions]: ./media/service-fabric-cluster-upgrade-windows-server/getfabversions.PNG

