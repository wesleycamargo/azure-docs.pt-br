---
title: "Atualizar um cluster do Service Fabric autônomo no Windows Server | Microsoft Docs"
description: "Atualizar o código do Service Fabric e/ou a configuração que executada como um cluster do Service Fabric autônomo, incluindo a configuração de modo de atualização de cluster"
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
ms.date: 10/10/2016
ms.author: chackdan
translationtype: Human Translation
ms.sourcegitcommit: 3d2e6380e4987c559e9ed62c3eaacb1ce45c917d
ms.openlocfilehash: 79d9d65c93aae92170667c1bc98fbd6bb1ffc3ff


---
# <a name="upgrade-your-standalone-service-fabric-cluster-on-windows-server"></a>Atualizar seu cluster do Service Fabric autônomo no Windows Server
> [!div class="op_single_selector"]
> * [Cluster do Azure](service-fabric-cluster-upgrade.md)
> * [Cluster Independente](service-fabric-cluster-upgrade-windows-server.md)
> 
> 

Para qualquer sistema moderno, oferecer a possibilidade de atualização é fundamental para o sucesso duradouro de seu produto. Um cluster do Service Fabric é um recurso que pertence a você. Este artigo descreve como garantir que o cluster sempre execute versões com suporte do código e de configurações do Service Fabric.

## <a name="controlling-the-fabric-version-that-runs-on-your-cluster"></a>Controlando a versão do Fabric em execução no Cluster
Você pode definir o cluster para baixar atualizações Service do Fabric, quando a Microsoft lançar uma nova versão, ou escolher selecionar uma versão com suporte da malha na qual deseja que o cluster se encontre. 

Faça isso definindo a configuração do cluster "fabricClusterAutoupgradeEnabled" como true ou false.

> [!NOTE]
> Mantenha o cluster sempre executando uma versão com suporte do Service Fabric. Quando anunciamos o lançamento de uma nova versão do Service Fabric, a versão anterior é programada para encerrar sua vida útil após um mínimo de 60 dias a partir da data desse anúncio. As novas versões são anunciadas [no blog da equipe do Service Fabric](https://blogs.msdn.microsoft.com/azureservicefabric/). Então, a nova versão está disponível para escolha. 
> 
> 

Será possível atualizar o cluster para a nova versão apenas se você estiver usando uma configuração de nó de estilo de produção, em que cada nó do Service Fabric é alocado em uma máquina virtual ou física separada. Se você tiver um cluster de desenvolvimento, no qual há mais de um nós da malha de serviço em uma única máquina virtual ou física, destrua o cluster e recrie-o com a nova versão.

Há dois fluxos de trabalho distintos para atualizar o cluster para a versão mais recente ou uma versão do Service Fabric com suporte. Um para clusters com conectividade para baixar a versão mais recente automaticamente e o outro para clusters sem conectividade para baixar a versão mais recente do Service Fabric.

### <a name="upgrade-the-clusters-with-connectivity-to-download-the-latest-code-and-configuration"></a>Atualizar os clusters com conectividade para baixar o código e a configuração mais recentes
Use estas etapas para atualizar seu cluster para uma versão com suporte se os nós de cluster tiverem conectividade com a Internet [http://download.microsoft.com](http://download.microsoft.com) 

Para clusters que tenham conectividade com [http://download.microsoft.com](http://download.microsoft.com), verificamos periodicamente a disponibilidade de novas versões do Service Fabric.

Quando uma nova versão do Service Fabric estiver disponível, o pacote será baixado localmente para o cluster e provisionado para atualização. Além de informar o cliente dessa nova versão, o sistema colocará um aviso de integridade do cluster explícito semelhante ao seguinte:

“O suporte para a atual versão de cluster [nº de versão] termina em [Data]". Quando o cluster estiver executando a versão mais recente, o aviso desaparecerá.

#### <a name="cluster-upgrade-workflow"></a>Fluxo de trabalho de atualização do cluster.
Ao ver o aviso de integridade do cluster, faça o seguinte:

1. Conecte-se ao cluster de qualquer computador que tenha acesso de administrador a todos os computadores listados como nós no cluster. O computador no qual este script é executado não precisa fazer parte do cluster
   
    ```powershell
   
    ###### connect to the secure cluster using certs
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
2. Obter a lista de versões do Service Fabric para as quais você pode atualizar
   
    ```powershell
   
    ###### Get the list of available service fabric versions 
    Get-ServiceFabricRegisteredClusterCodeVersion
    ```
   
    você deverá receber uma saída semelhante a esta:
   
    ![obter versões de malha][getfabversions]
3. Disparar uma atualização de cluster para uma das versões disponíveis usando o [cmd do PowerShell Start-ServiceFabricClusterUpgrade](https://msdn.microsoft.com/library/mt125872.aspx)
   
    ```Powershell
   
    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion <codeversion#> -Monitored -FailureAction Rollback
   
    ###### Here is a filled out example
   
    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion 5.3.301.9590 -Monitored -FailureAction Rollback
   
    ```
   Você pode monitorar o progresso da atualização no Service Fabric Explorer ou executando o seguinte comando do PowerShell
   
    ```powershell
   
    Get-ServiceFabricClusterUpgrade
    ```
   
    Se as políticas de integridade do cluster não forem atendidas, a atualização será revertida. Você pode especificar as políticas de integridade personalizadas para o comando start-serviceFabricClusterUpgrade referir-se a [este documento](https://msdn.microsoft.com/library/mt125872.aspx) para obter detalhes. 

Depois de corrigir os problemas que resultaram na reversão, você precisará iniciar a atualização novamente, seguindo as mesmas etapas de antes.

### <a name="upgrade-the-clusters-with-uno-connectivityu-to-download-the-latest-code-and-configuration"></a>Atualizar os clusters <U>sem conectividade</u> para baixar o código e a configuração mais recentes
Siga estas etapas para atualizar seu cluster para uma versão com suporte se os nós de cluster [não tiverem conectividade](http://download.microsoft.com) com a Internet para **http://download.microsoft.com** 

> [!NOTE]
> Se estiver executando um cluster que não esteja conectado à Internet, você precisará monitorar o blog da equipe do Service Fabric para ser notificado de uma nova versão. O sistema **não** coloca nenhum aviso de integridade do cluster para alertá-lo disso.  
> 
> 

1. Modificar a configuração do cluster para definir a seguinte propriedade como false
   
        "fabricClusterAutoupgradeEnabled": false,

e iniciar uma atualização de configuração. Consulte [cmd do PS Start-ServiceFabricClusterConfigurationUpgrade](https://msdn.microsoft.com/en-us/library/mt788302.aspx) para obter detalhes de uso. Certifique-se de atualizar “clusterConfigurationVersion” no JSON antes de iniciar a atualização de configuração.

```powershell

    Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File> 

```

#### <a name="cluster-upgrade-workflow"></a>Fluxo de trabalho de atualização do cluster.
1. Baixe a versão mais recente do pacote do documento [Criar cluster do Service Fabric para o Windows Server](service-fabric-cluster-creation-for-windows-server.md) 
2. Conecte-se ao cluster de qualquer computador que tenha acesso de administrador a todos os computadores listados como nós no cluster. O computador no qual este script é executado não precisa fazer parte do cluster 
   
    ```powershell
   
    ###### connect to the cluster
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
   
   ###### Get the list of available service fabric versions
    Copy-ServiceFabricClusterPackage -Code -CodePackagePath <name of the .cab file including the path to it> -ImageStoreConnectionString "fabric:ImageStore"
   
   ###### Here is a filled out example
    Copy-ServiceFabricClusterPackage -Code -CodePackagePath .\MicrosoftAzureServiceFabric.5.3.301.9590.cab -ImageStoreConnectionString "fabric:ImageStore"

    ```

1. Registrar o pacote copiado 
   
    ```powershell
   
    ###### Get the list of available service fabric versions 
    Register-ServiceFabricClusterPackage -Code -CodePackagePath <name of the .cab file> 
   
    ###### Here is a filled out example
    Register-ServiceFabricClusterPackage -Code -CodePackagePath MicrosoftAzureServiceFabric.5.3.301.9590.cab
   
     ```
2. Iniciar uma atualização de cluster para uma das versões disponíveis. 
   
    ```Powershell
   
    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion <codeversion#> -Monitored -FailureAction Rollback
   
    ###### Here is a filled out example
    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion 5.3.301.9590 -Monitored -FailureAction Rollback
   
    ```
   Você pode monitorar o progresso da atualização no Service Fabric Explorer ou executando o seguinte comando do PowerShell
   
    ```powershell
   
    Get-ServiceFabricClusterUpgrade
    ```
   
    Se as políticas de integridade do cluster não forem atendidas, a atualização será revertida. Você pode especificar as políticas de integridade personalizadas para o comando start-serviceFabricClusterUpgrade referir-se a [este documento](https://msdn.microsoft.com/library/mt125872.aspx) para obter detalhes. 

Depois de corrigir os problemas que resultaram na reversão, você precisará iniciar a atualização novamente, seguindo as mesmas etapas de antes.

## <a name="next-steps"></a>Próximas etapas
* Saiba como personalizar algumas das [configurações de malha do cluster do Service Fabric](service-fabric-cluster-fabric-settings.md)
* Saiba como [reduzir e escalar horizontalmente seu cluster](service-fabric-cluster-scale-up-down.md)
* Saiba mais sobre [atualizações de aplicativo](service-fabric-application-upgrade.md)

<!--Image references-->
[getfabversions]: ./media/service-fabric-cluster-upgrade-windows-server/getfabversions.PNG



<!--HONumber=Nov16_HO3-->


