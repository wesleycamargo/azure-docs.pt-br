---
title: Criar um cluster do Service Fabric no Azure | Microsoft Docs
description: Saiba como criar um cluster do Windows ou Linux do Service Fabric no Azure usando o PowerShell.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/03/2017
ms.author: cristyg
ms.custom: mvc
ms.openlocfilehash: 29a8d5cf9a59a08dc63df1dbd601aa1f509cad95
ms.sourcegitcommit: 9ae92168678610f97ed466206063ec658261b195
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/17/2017
---
# <a name="create-a-secure-cluster-on-azure-using-powershell"></a>Criar um cluster seguro no Azure usando o PowerShell
Essa é a primeira etapa em uma série de tutoriais que mostra como mover um aplicativo .NET para a nuvem usando clusters do Service Fabric, bem como contêineres. Nas etapas a seguir você aprenderá a criar um cluster do Service Fabric (do Windows ou Linux) em execução no Azure. Ao terminar, você terá um cluster seguro em execução na nuvem na qual você pode implantar aplicativos.

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar este tutorial:
- Se você não tem uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Instalar o [SDK do Service Fabric](service-fabric-get-started.md)
- Instale o [módulo do Azure Powershell versão 4.1 ou superior](https://docs.microsoft.com/powershell/azure/install-azurerm-ps) (se necessário, [Instale o Azure PowerShell](/powershell/azure/overview) ou [atualize para uma versão mais recente](https://docs.microsoft.com/en-us/powershell/azure/install-azurerm-ps?view=azurermps-4.4.0#update-azps))


# <a name="create-a-service-fabric-cluster"></a>Criar um cluster do Service Fabric

Este script cria um cluster do Service Fabric de visualização de nó único. O cluster é protegido por um certificado autoassinado que obtém criado juntamente com o cluster e, em seguida, é colocado em um cofre de chaves. Os clusters de nó único não podem ser dimensionados além de uma máquina virtual e clusters de versão prévia não podem ser atualizados para versões mais recentes.

Para calcular o custo gerado ao executar um cluster do Service Fabric no Azure, use a [Calculadora de preços do Azure](https://azure.microsoft.com/pricing/calculator/).
Para obter mais informações sobre a criação de clusters do Service Fabric, confira [Criar um cluster do Service Fabric usando o Azure Resource Manager](service-fabric-cluster-creation-via-arm.md).

## <a name="log-in-to-azure"></a>Fazer logon no Azure
Abra um console do PowerShell, faça logon no Azure e selecione a assinatura na qual você deseja implantar o cluster:

   ```powershell
   Login-AzureRmAccount
   Select-AzureRmSubscription -SubscriptionId <subscription-id>
   ```

## <a name="cluster-parameters"></a>Parâmetros do cluster

   Este script usa os seguintes parâmetros e conceitos. Personalize os parâmetros de acordo com suas necessidades.

   | Parâmetro       | Descrição | Valor sugerido |
   | --------------- | ----------- | --------------- |
   | Local | A região do Azure na qual o cluster será implantado. | *Por exemplo, westeurope, eastasia, eastus* |
   | Nome     | Nome do cluster que você deseja criar. O nome deve ter entre 4 e 23 caracteres e pode ter apenas letras minúsculas, números e hifens. | *Por exemplo, bobs-sfpreviewcluster* |
   | ResourceGroupName   | Nome do grupo de recursos no qual criar o cluster. | *por exemplo, myresourcegroup* |
   | VmSku  | SKU da máquina virtual a ser usado para os nós. | *Qualquer SKU válido da máquina virtual* |
   | SO  | SO da máquina virtual a ser usado para os nós. | *Qualquer SO válido da máquina virtual* |
   | KeyVaultName | Nome do KeyVault novo para associar ao cluster. | *por exemplo, mykeyvault* |
   | ClusterSize | O número de máquinas virtuais no cluster (pode ser 1 ou 3-99).| *Especifique apenas uma máquina virtual para um cluster de visualização* |
   | CertificateSubjectName | O nome da entidade do certificado a ser criado. | *Segue o formato <name>.<location>.cloudapp.azure.com* |

## <a name="default-parameter-values"></a>Valores do parâmetro padrão
As configurações da **Máquina Virtual** são opcionais. Se você não especificá-las, o nome de usuário de administrador padrão será "vmadmin" e o PowerShell solicitará uma senha de Máquina Virtual antes de criar o cluster.

As **Portas** padrão são as portas 80 e 8081. Você pode especificar portas adicionais, seguindo as diretrizes para [portas em clusters do Service Fabric](https://docs.microsoft.com/en-us/azure/service-fabric/create-load-balancer-rule).

**Diagnóstico** é habilitado por padrão.

**Serviço DNS** por padrão não está habilitado.

**Proxy reverso** também não é habilitado por padrão.

## <a name="create-the-cluster-with-your-parameters"></a>Crie o cluster com os seus parâmetros

Depois que você decidir sobre os parâmetros que correspondam aos seus requisitos, execute o seguinte comando para gerar um Cluster do Service Fabric seguro e seu certificado.

Você pode modificar este script para incluir parâmetros adicionais. Para obter mais informações sobre os parâmetros para a criação do cluster, confira o cmdlet [New-AzureRmServiceFabricCluster](/powershell/module/azurerm.servicefabric/new-azurermservicefabriccluster.md).

>[!NOTE]
>Antes de executar esse comando, você deve criar uma pasta para saída do certificado de cluster.

```powershell

    # Certificate variables. This will create and encrypt a password to be used by Service Fabric.
    $certpwd="Password#1234" | ConvertTo-SecureString -AsPlainText -Force

    # You must create the folder where you want to output the certificate on your machine before executing this step.
    $certfolder="c:\mycertificates\"

    # Variables for common values. Change the values to fit your needs.
    $clusterloc="WestUS"
    $clustername = "mysfcluster"
    $groupname="mysfclustergroup"       
    $vmsku = "Standard_D2_v2"
    $vaultname = "mykeyvault"
    $subname="$clustername.$clusterloc.cloudapp.azure.com"

    # Set the number of cluster nodes. Possible values: 1, 3-99
    $clustersize=1

    # Create the Service Fabric cluster and its self-signed certificate. The OS specified here allows you use this cluster with any applications that are also leveraging containers.
    New-AzureRmServiceFabricCluster -Name $clustername -ResourceGroupName $groupname -Location $clusterloc `
    -ClusterSize $clustersize -CertificateSubjectName $subname `
    -CertificatePassword $certpwd -CertificateOutputFolder $certfolder `
    -OS WindowsServer2016DatacenterwithContainers -VmSku $vmsku -KeyVaultName $vaultname
```

O processo de criação pode levar vários minutos. Depois que a configuração for concluído, ele produz informações sobre o cluster criado no Azure. Ele também copia o certificado de cluster para o diretório - CertificateOutputFolder no caminho especificado para esse parâmetro.

Anote a URL para o cluster de **ManagementEndpoint**, o que pode ser semelhante à seguinte URL: *https://mycluster.westeurope.cloudapp.azure.com:19080*

## <a name="import-the-certificate"></a>Importar o certificado

Quando o cluster for criado com sucesso, execute o seguinte comando para garantir que você possa usar o certificado autoassinado,

```powershell

    # Connect to the cluster using by installing the certificate into the Personal (My) store of the current user on your computer.
    Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My `
            -FilePath C:\mycertificates\mysfclustergroup20170531104310.pfx `
            -Password $certpwd
```

Esse comando instala o certificado no usuário atual do seu computador.  Você precisará deste certificado para acessar o Service Fabric Explorer e exibir a integridade do cluster.


## <a name="view-your-cluster-optional"></a>Exibir o cluster (Opcional)

Depois que você tiver o cluster e o certificado importado, pode se conectar ao cluster e exibir sua integridade. Há várias maneiras de conectar-se, por meio do Service Fabric Explorer ou do PowerShell.

### <a name="service-fabric-explorer"></a>Service Fabric Explorer
Você pode exibir a integridade do cluster abrindo o Service Fabric Explorer. Para fazer isso, navegue até a URL de **ManagementEndpoint** para o cluster usando um navegador da Web e, em seguida, selecione o certificado que foi salvo em seu computador.

>[!NOTE]
>Ao abrir o Service Fabric Explorer, você verá um erro de certificado, pois está usando um certificado autoassinado. No Edge, você precisa clicar em *Detalhes* e no link *Ir para a página da Web*. No Chrome, você precisa clicar em *Avançado* e no link *Continuar*.

### <a name="powershell"></a>PowerShell

O módulo do PowerShell do **Service Fabric** fornece vários cmdlets para gerenciar clusters, aplicativos e serviços do Service Fabric.  Use o cmdlet [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster) para se conectar ao cluster seguro. Os detalhes de ponto de extremidade de conexão e de impressão digital do certificado podem ser encontrados na saída da etapa anterior.

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint mysfcluster.southcentralus.cloudapp.azure.com:19000 `
          -KeepAliveIntervalInSec 10 `
          -X509Credential -ServerCertThumbprint C4C1E541AD512B8065280292A8BA6079C3F26F10 `
          -FindType FindByThumbprint -FindValue C4C1E541AD512B8065280292A8BA6079C3F26F10 `
          -StoreLocation CurrentUser -StoreName My
```

Você também pode verificar se está conectado e se o cluster está íntegro, usando o cmdlet [Get-ServiceFabricClusterHealth](/powershell/module/servicefabric/get-servicefabricclusterhealth).

```powershell
Get-ServiceFabricClusterHealth
```

## <a name="next-steps"></a>Próximas etapas
Neste tutorial, você aprendeu a criar um cluster do Service Fabric seguro no Azure usando o PowerShell

Em seguida, avance para o próximo tutorial para saber como implantar um aplicativo existente.
> [!div class="nextstepaction"]
> [Implantar um aplicativo existente do .NET com o Docker Compose](service-fabric-host-app-in-a-container.md)

---
