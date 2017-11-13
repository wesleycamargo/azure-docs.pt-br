---
title: Configurar um cluster do Azure Service Fabric | Microsoft Docs
description: "Este guia de início rápido ajuda a criar um cluster do Service Fabric do Windows ou Linux no Azure."
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/13/2017
ms.author: ryanwi
ms.openlocfilehash: caf76bb739fa92982c511c8e3e6c6aaf2bf9d6c1
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/03/2017
---
# <a name="create-your-first-service-fabric-cluster-on-azure"></a>Criar seu primeiro cluster do Service Fabric no Azure
Um [cluster do Azure Service Fabric](service-fabric-deploy-anywhere.md) é um conjunto de máquinas físicas ou virtuais conectadas em rede, no qual os microsserviços são implantados e gerenciados. Este guia de início rápido ajuda a criar um cluster de cinco nós, em execução no Windows ou Linux, por meio do [Azure PowerShell](https://msdn.microsoft.com/library/dn135248) ou do [Portal do Azure](http://portal.azure.com) em poucos minutos. Também é possível usar a CLI do Azure nesta etapa.  

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.


## <a name="use-the-azure-portal"></a>Use o Portal do Azure

Entrar no Portal do Azure em [http://portal.azure.com](http://portal.azure.com).

### <a name="create-the-cluster"></a>Criar o cluster

1. No canto superior esquerdo do portal do Azure, selecione **Novo**.
2. Procure **Service Fabric** e selecione **Cluster do Service Fabric** nos resultados retornados. Em seguida, selecione **Criar**.
3. Preencha o formulário **Básico** do Service Fabric. Para o **Sistema operacional**, selecione a versão do Windows ou Linux com a qual você deseja que os nós do cluster sejam executados. O nome do usuário e a senha inseridos aqui são usados para fazer logon na máquina virtual. Para **Grupo de Recursos**, crie um novo. Um grupo de recursos é um contêiner lógico no qual os recursos do Azure são criados e gerenciados coletivamente. Quando terminar, selecione **OK**.

    ![Captura de tela de saída da configuração do cluster][cluster-setup-basics]

4. Preencha o formulário **Configuração do cluster**. Em **Contagem do tipo de nó**, digite **1**.

5. Selecione **Tipo de nó 1 (Primário)** e preencha o formulário **Configuração do tipo de nó**. Insira um nome do tipo de nó e defina a [Camada de durabilidade](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster) como **Bronze**. Em seguida, selecione um tamanho de VM.

    Os tipos de nó definem o tamanho da VM, número de VMs, pontos de extremidade personalizados e outras configurações para as VMs desse tipo. Cada tipo de nó definido é configurado como um conjunto de dimensionamento de máquinas virtuais, que é usado para implantar e gerenciar as máquinas virtuais como um conjunto. Cada tipo de nó pode ser aumentado ou reduzido de forma independente, possuir conjuntos diferentes de portas abertas e pode ter métricas de capacidade diferentes. O primeiro tipo de nó, ou primário, é onde os serviços do sistema do Service Fabric são hospedados. Esse tipo de nó deve ter cinco ou mais VMs.

    Para qualquer implantação de produção, o [planejamento da capacidade](service-fabric-cluster-capacity.md) é uma etapa importante. Para esse início rápido, no entanto, você não está executando aplicativos, portanto, escolha um tamanho de VM *DS1_v2 Padrão*. Selecione **Prata** para o [nível de confiabilidade](service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster) e especifique uma capacidade inicial do conjunto de dimensionamento de máquinas virtuais de 5.  

    Os pontos de extremidade personalizados abrem portas no Azure Load Balancer para que você possa conectar os aplicativos executados no cluster.  Insira **80, 8172** para abrir as portas 80 e 8172.

    Não marque a caixa **Definir configurações avançadas**, que é usada para personalizar os pontos de extremidade de gerenciamento do TCP/HTTP, intervalos de portas do aplicativo, [restrições de posicionamento](service-fabric-cluster-resource-manager-configure-services.md#placement-constraints), e [propriedades da capacidade](service-fabric-cluster-resource-manager-metrics.md).    
    
    ![Captura de tela da configuração do tipo de nó][node-type-config]

    Selecione **OK**.

6. No formulário **Configuração do cluster**, defina **Diagnóstico** para **Ativar**. Para este início rápido, você não precisa inserir nenhuma propriedade de [configuração do fabric](service-fabric-cluster-fabric-settings.md).  Na **Versão do fabric**, selecione o modo de atualização **Automático** para que a Microsoft atualize automaticamente a versão do código do fabric em execução no cluster.  Defina o modo para **Manual** se quiser [escolher uma versão com suporte](service-fabric-cluster-upgrade.md) para a qual atualizar.     

    Selecione **OK**.

7. Preencha o formulário **Segurança**. Para esse início rápido, selecione **Não seguro**. Observe que, em geral, você deve criar um cluster seguro para cargas de trabalho de produção. Qualquer pessoa pode se conectar ao cluster de forma anônima a um cluster não seguro e realizar operações de gerenciamento.  

   O Service Fabric usa os certificados para fornecer autenticação e criptografia para proteger vários aspectos de um cluster e de seus aplicativos. Para obter mais informações, consulte [Cenários de segurança do cluster do Service Fabric](service-fabric-cluster-security.md). Para habilitar a autenticação do usuário usando o Azure Active Directory ou configurar os certificados para a segurança do aplicativo, consulte [Criar um cluster a partir de um modelo do Resource Manager](service-fabric-cluster-creation-via-arm.md).

    Selecione **OK**.

8. Examine o resumo. Se você quiser baixar um modelo do Azure Resource Manager criado a partir das configurações inseridas, selecione **Baixar modelo e parâmetros**. Selecione **Criar** para criar o cluster.

    Você pode ver o progresso da criação nas notificações. (Selecione o ícone de "Sino" próximo à barra de status no canto superior direito da tela). Se você selecionou **Fixar no Quadro Inicial** durante a criação do cluster, você verá **Implantando o Cluster do Service Fabric** fixado na tela **Inicial**.

### <a name="connect-to-the-cluster-by-using-powershell"></a>Conectar o cluster usando o PowerShell
Verifique se o cluster está em execução conectando-se através do PowerShell. O módulo Service Fabric PowerShell é instalado com o [SDK do Service Fabric](service-fabric-get-started.md). O cmdlet [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) estabelece uma conexão com o cluster.   

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint quickstartcluster.westus2.cloudapp.azure.com:19000
```
Confira [Conectar-se a um cluster seguro](service-fabric-connect-to-secure-cluster.md) para obter outros exemplos de como se conectar a um cluster. Depois de se conectar ao cluster, use o cmdlet [Get-ServiceFabricNode](/powershell/module/servicefabric/get-servicefabricnode?view=azureservicefabricps) para exibir uma lista de nós de cluster e informações de status para cada nó. O **HealthState** deve ser *OK* para cada nó.

```powershell
PS C:\Users\sfuser> Get-ServiceFabricNode |Format-Table

NodeDeactivationInfo NodeName     IpAddressOrFQDN NodeType  CodeVersion  ConfigVersion NodeStatus NodeUpTime NodeDownTime HealthState
-------------------- --------     --------------- --------  -----------  ------------- ---------- ---------- ------------ -----------
                     _nodetype1_2 10.0.0.6        nodetype1 5.7.198.9494 1                     Up 03:00:38   00:00:00              Ok
                     _nodetype1_1 10.0.0.5        nodetype1 5.7.198.9494 1                     Up 03:00:38   00:00:00              Ok
                     _nodetype1_0 10.0.0.4        nodetype1 5.7.198.9494 1                     Up 03:00:38   00:00:00              Ok
                     _nodetype1_4 10.0.0.8        nodetype1 5.7.198.9494 1                     Up 03:00:38   00:00:00              Ok
                     _nodetype1_3 10.0.0.7        nodetype1 5.7.198.9494 1                     Up 03:00:38   00:00:00              Ok
```

### <a name="remove-the-cluster"></a>Remover o cluster
Um cluster do Service Fabric é composto por outros recursos do Azure, além do próprio recurso de cluster. Para excluir por completo um cluster do Service Fabric, também é necessário excluir todos os recursos que o compõem. A maneira mais simples de excluir o cluster e todos os recursos que ele consume é excluir o grupo de recursos. Para saber outras maneiras de excluir um cluster ou excluir alguns (mas não todos) recursos em um grupo de recursos, consulte [Excluir um cluster](service-fabric-cluster-delete.md).

Exclua um grupo de recursos no portal do Azure:
1. Navegue até o cluster do Service Fabric que você deseja excluir.
2. Na página de informações básicas do cluster, selecione o nome **Grupo de Recursos**.
3. Na página **Informações básicas do Grupo de Recursos**, selecione **Excluir grupo de recursos**. Siga as instruções nessa página para concluir a exclusão do grupo de recursos.
    ![Captura de tela da página de Informações Básicas do Grupo de Recursos, com Excluir grupo de recursos realçado][cluster-delete]


## <a name="use-azure-powershell"></a>Usar PowerShell do Azure
Outra maneira de criar o cluster é usar o PowerShell. Faça assim:

1. Baixe o [módulo Azure PowerShell, versão 4.0 ou mais recente](https://docs.microsoft.com/powershell/azure/install-azurerm-ps), em seu computador.

2. Execute o cmdlet [New-AzureRmServiceFabricCluster](/powershell/module/azurerm.servicefabric/new-azurermservicefabriccluster) para criar um cluster de cinco nós do Service Fabric protegido por um certificado X.509. O comando cria um certificado autoassinado e o carrega em um novo cofre de chaves. O certificado também é copiado para um diretório local. Defina o parâmetro *-OS* para escolher a versão do Windows ou Linux que é executada nos nós de cluster. Personalize os parâmetros conforme necessário. 

    ```powershell
    #Provide the subscription Id
    $subscriptionId = 'yourSubscriptionId'

    # Certificate variables.
    $certpwd="Password#1234" | ConvertTo-SecureString -AsPlainText -Force
    $certfolder="c:\mycertificates\"

    # Variables for VM admin.
    $adminuser="vmadmin"
    $adminpwd="Password#1234" | ConvertTo-SecureString -AsPlainText -Force 

    # Variables for common values
    $clusterloc="SouthCentralUS"
    $clustername = "mysfcluster"
    $groupname="mysfclustergroup"       
    $vmsku = "Standard_D2_v2"
    $vaultname = "mykeyvault"
    $subname="$clustername.$clusterloc.cloudapp.azure.com"

    # Set the number of cluster nodes. Possible values: 1, 3-99
    $clustersize=5 

    # Set the context to the subscription ID where the cluster will be created
    Login-AzureRmAccount
    Get-AzureRmSubscription
    Select-AzureRmSubscription -SubscriptionId $subscriptionId

    # Create the Service Fabric cluster.
    New-AzureRmServiceFabricCluster -Name $clustername -ResourceGroupName $groupname -Location $clusterloc `
    -ClusterSize $clustersize -VmUserName $adminuser -VmPassword $adminpwd -CertificateSubjectName $subname `
    -CertificatePassword $certpwd -CertificateOutputFolder $certfolder `
    -OS WindowsServer2016DatacenterwithContainers -VmSku $vmsku -KeyVaultName $vaultname
    ```

    O comando pode levar de 10 a 30 minutos para ser concluído. A saída tem informações sobre o certificado, o cofre de chaves onde ele foi carregado e a pasta local para a qual o certificado é copiado.     

3. Copie toda a saída e salve um arquivo de texto (você fará referência a ele mais tarde). Anote as informações a seguir da saída: 

    - CertificateSavedLocalPath
    - CertificateThumbprint
    - ManagementEndpoint
    - ClientConnectionEndpointPort

### <a name="install-the-certificate-on-your-local-machine"></a>Instalar o certificado no seu computador local
  
Para se conectar ao cluster, instale o certificado no repositório pessoal (Meu) do usuário atual. 

Execute o seguinte:

```powershell
$certpwd="Password#1234" | ConvertTo-SecureString -AsPlainText -Force
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My `
        -FilePath C:\mycertificates\<certificatename>.pfx `
        -Password $certpwd
```

Agora você está pronto para se conectar ao seu cluster seguro.

### <a name="connect-to-a-secure-cluster"></a>Conectar a um cluster seguro 

Execute o cmdlet [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster) para se conectar a um cluster seguro. Os detalhes do certificado devem corresponder a um certificado que foi usado para configurar o cluster. 

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <ManagementEndpoint>:19000 `
          -KeepAliveIntervalInSec 10 `
          -X509Credential -ServerCertThumbprint <CertificateThumbprint> `
          -FindType FindByThumbprint -FindValue <CertificateThumbprint> `
          -StoreLocation CurrentUser -StoreName My
```

O exemplo a seguir mostra os parâmetros de exemplo: 

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint mycluster.southcentralus.cloudapp.azure.com:19000 `
          -KeepAliveIntervalInSec 10 `
          -X509Credential -ServerCertThumbprint C4C1E541AD512B8065280292A8BA6079C3F26F10 `
          -FindType FindByThumbprint -FindValue C4C1E541AD512B8065280292A8BA6079C3F26F10 `
          -StoreLocation CurrentUser -StoreName My
```

Execute o seguinte comando para verificar se você está conectado e o cluster está íntegro.

```powershell

Get-ServiceFabricClusterHealth

```
### <a name="remove-the-cluster"></a>Remover o cluster
Um cluster é composto por outros recursos do Azure, além do próprio recurso do cluster. A maneira mais simples de excluir o cluster e todos os recursos que ele consume é excluir o grupo de recursos. 

```powershell
$groupname="mysfclustergroup"
Remove-AzureRmResourceGroup -Name $groupname -Force
```
## <a name="use-azure-cli"></a>Usar a CLI do Azure
Outra maneira de criar o cluster é usar a CLI. Faça assim:

1. Instalar a [CLI do Azure 2.0](/cli/azure/install-azure-cli?view=azure-cli-latest) em seu computador.
2. Faça logon no Azure e selecione a assinatura na qual você deseja criar o cluster.
   ```azurecli
   az login
   az account set --subscription <GUID>
   ```
3. Execute o comando [az sf cluster create](/cli/azure/sf/cluster?view=azure-cli-latest#az_sf_cluster_create) para criar um cluster de cinco nós do Service Fabric protegido por um certificado X.509. O comando cria um certificado autoassinado e o carrega em um novo cofre de chaves. O certificado também é copiado para um diretório local. Defina o parâmetro *-os* para escolher a versão do Windows ou Linux que é executada nos nós de cluster. Personalize os parâmetros conforme necessário.

    ```azurecli
    #!/bin/bash

    # Variables
    ResourceGroupName="aztestclustergroup" 
    ClusterName="aztestcluster" 
    Location="southcentralus" 
    Password="q6D7nN%6ck@6" 
    Subject="aztestcluster.southcentralus.cloudapp.azure.com" 
    VaultName="aztestkeyvault" 
    VaultGroupName="testvaultgroup"
    VmPassword="Mypa$$word!321"
    VmUserName="sfadminuser"

    # Create resource groups
    az group create --name $ResourceGroupName --location $Location 
    az group create --name $VaultGroupName --location $Location

    # Create secure five node Linux cluster. Creates a key vault in a resource group
    # and creates a certficate in the key vault. The certificate's subject name must match 
    # the domain that you use to access the Service Fabric cluster. The certificate is downloaded locally.
    az sf cluster create --resource-group $ResourceGroupName --location $Location --certificate-output-folder . \
        --certificate-password $Password --certificate-subject-name $Subject --cluster-name $ClusterName \
        --cluster-size 5 --os UbuntuServer1604 --vault-name $VaultName --vault-resource-group $VaultGroupName \
        --vm-password $VmPassword --vm-user-name $VmUserName
    ```
    
### <a name="connect-to-the-cluster"></a>Conectar-se ao cluster
Execute o seguinte comando da CLI para se conectar ao cluster usando o certificado.  Ao usar um certificado do cliente para autenticação, os certifique-se de que os detalhes do certificado correspondem a um certificado implantado para os nós de cluster. Use a opção `--no-verify` para um certificado autoassinado.

```azurecli
az sf cluster select --endpoint https://aztestcluster.southcentralus.cloudapp.azure.com:19080 --pem ./linuxcluster201709161647.pem --no-verify
```

Execute o seguinte comando para verificar se você está conectado e o cluster está íntegro.

```azurecli
az sf cluster health
```

### <a name="connect-to-the-nodes-directly"></a>Conectar-se diretamente aos nós 

Para conectar os nós em um cluster do Linux, você pode usar o comando secure shell (SSH). Especifique um número da porta de 3389 em diante. Por exemplo, para o cluster de cinco nós criado anteriormente, os comandos seriam os seguintes:
```bash
ssh sfadminuser@aztestcluster.southcentralus.cloudapp.azure.com -p 3389
ssh sfadminuser@aztestcluster.southcentralus.cloudapp.azure.com -p 3390
ssh sfadminuser@aztestcluster.southcentralus.cloudapp.azure.com -p 3391
ssh sfadminuser@aztestcluster.southcentralus.cloudapp.azure.com -p 3392
ssh sfadminuser@aztestcluster.southcentralus.cloudapp.azure.com -p 3393
```

### <a name="remove-the-cluster"></a>Remover o cluster
Um cluster é composto por outros recursos do Azure, além do próprio recurso do cluster. A maneira mais simples de excluir o cluster e todos os recursos que ele consume é excluir o grupo de recursos. 

```azurecli
ResourceGroupName = "aztestclustergroup"
az group delete --name $ResourceGroupName
```

## <a name="next-steps"></a>Próximas etapas
Agora que você configurou um cluster de desenvolvimento, tente o seguinte:
* [Visualizando o cluster com o Explorador do Service Fabric](service-fabric-visualizing-your-cluster.md)
* [Remover um cluster](service-fabric-cluster-delete.md) 
* [Implantar aplicativos usando o PowerShell](service-fabric-deploy-remove-applications.md)
* [Implantar aplicativos usando a CLI](service-fabric-application-lifecycle-sfctl.md)


[cluster-setup-basics]: ./media/service-fabric-get-started-azure-cluster/basics.png
[node-type-config]: ./media/service-fabric-get-started-azure-cluster/nodetypeconfig.png
[cluster-status]: ./media/service-fabric-get-started-azure-cluster/clusterstatus.png
[service-fabric-explorer]: ./media/service-fabric-get-started-azure-cluster/sfx.png
[cluster-delete]: ./media/service-fabric-get-started-azure-cluster/delete.png
