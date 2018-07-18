---
title: Implantar um aplicativo .NET em um contêiner do Azure Service Fabric | Microsoft Docs
description: Saiba como colocar um aplicativo existente do .NET em um contêiner usando o Visual Studio e depure os contêineres no Service Fabric localmente. O aplicativo colocado em um contêiner é enviado por push para um registro de contêiner do Azure e implantado em um cluster do Service Fabric. Quando implantado no Azure, o aplicativo usa o Banco de Dados SQL do Azure para manter os dados.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/18/2018
ms.author: ryanwi
ms.openlocfilehash: 6fe314125440096d21a1276defd082c4e1997b8e
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34642675"
---
# <a name="tutorial-deploy-a-net-application-in-a-windows-container-to-azure-service-fabric"></a>Tutorial: Implantar um aplicativo .NET em um contêiner do Windows no Azure Service Fabric

Este tutorial mostra como colocar um aplicativo ASP.NET existente em um contêiner e empacotá-lo como um aplicativo do Service Fabric.  Execute os contêineres localmente no cluster de desenvolvimento do Service Fabric e, em seguida, implante o aplicativo no Azure.  O aplicativo persiste os dados no [Banco de Dados SQL do Azure](/azure/sql-database/sql-database-technical-overview). 

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Colocar um aplicativo existente em um contêiner usando o Visual Studio
> * Criar um banco de dados SQL do Azure
> * Criar um registro de contêiner do Azure
> * Implantar um aplicativo do Service Fabric no Azure

## <a name="prerequisites"></a>pré-requisitos

1. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
2. Instalar o [Docker CE para Windows](https://store.docker.com/editions/community/docker-ce-desktop-windows?tab=description) de forma que você possa executar contêineres no Windows 10.
3. Instalar a [versão 6.2 ou posterior de tempo de execução do Service Fabric](service-fabric-get-started.md) e a [versão 3.1 do SDK do Service Fabric](service-fabric-get-started.md) ou posterior.
4. Instalar o [Visual Studio 2017 versão 15.7](https://www.visualstudio.com/) ou posterior com as cargas de trabalho de **desenvolvimento do Azure** e de **desenvolvimento para a Web e ASP.NET**.
5. Instalar o [Azure PowerShell][link-azure-powershell-install]
 

## <a name="download-and-run-fabrikam-fiber-callcenter"></a>Baixar e executar o Fabrikam Fiber CallCenter
Baixar o aplicativo de exemplo [Fabrikam Fiber CallCenter][link-fabrikam-github].  Clique no link **baixar arquivo**.  No diretório *sourceCode* no arquivo *fabrikam.zip*, extraia o arquivo *sourceCode.zip* e, em seguida, extraia o diretório *VS2015* no seu computador.

Verifique se o aplicativo Fabrikam fibra CallCenter é compilado e executado sem erros.  Inicie o Visual Studio como um **administrador** e abra o arquivo [FabrikamFiber.CallCenter.sln][link-fabrikam-github].  Pressione F5 para depurar e executar o aplicativo.

![Exemplo de Fabrikam Web][fabrikam-web-page]

## <a name="containerize-the-application"></a>Colocar o aplicativo em um contêiner
Clique com o botão direito do mouse no projeto **FabrikamFiber.Web** > **Adicionar** > **Suporte do Orquestrador do Contêiner**.  Selecione **Service Fabric** como o orchestrator do contêiner e clique em **OK**.

Clique em **Sim** para alternar o Docker para contêineres do Windows agora.

Um novo projeto de aplicativo do Service Fabric **FabrikamFiber.CallCenterApplication** é criado na solução.  Um Dockerfile é adicionado ao projeto **FabrikamFiber.Web** existente.  Um diretório **PackageRoot** também é adicionado ao projeto **FabrikamFiber.Web**, que contém o manifesto do serviço e as configurações para o novo serviço FabrikamFiber.Web. 

O contêiner agora está pronto para ser compilado e empacotado em um aplicativo do Service Fabric. Depois de compilar a imagem de contêiner no computador, você poderá enviá-la por push para qualquer registro de contêiner e efetuar pull dela em qualquer host para execução.

## <a name="create-an-azure-sql-db"></a>Criar um Banco de Dados SQL do Azure
Ao executar o aplicativo da Fabrikam Fiber CallCenter em produção, os dados precisam ser mantidos em um banco de dados. Atualmente, não existe uma maneira de garantir a persistência de dados em um contêiner. Portanto, não é possível armazenar dados de produção no SQL Server em um contêiner.

Recomendamos o [Banco de Dados SQL do Azure](/azure/sql-database/sql-database-get-started-powershell). Para configurar e executar um banco de dados gerenciado do SQL Server no Azure, execute o script a seguir.  Modificar as variáveis de script conforme necessário. *clientIP* é o endereço IP do seu computador de desenvolvimento.  Se você estiver atrás de um firewall corporativo, o endereço IP do seu computador de desenvolvimento não pode ser um endereço IP exposto à Internet.  Você também pode definir a regra de firewall do servidor para o banco de dados SQL por meio do [portal do Azure](https://portal.azure.com), que lista o endereço IP do seu computador.

```powershell
$subscriptionID="<subscription ID>"

# Sign in to your Azure account and select your subscription.
Login-AzureRmAccount -SubscriptionId $subscriptionID 

# The data center and resource name for your resources.
$dbresourcegroupname = "fabrikam-fiber-db-group"
$location = "southcentralus"

# The logical server name: Use a random value or replace with your own value (do not capitalize).
$servername = "fab-fiber-$(Get-Random)"

# Set an admin login and password for your database.
# The login information for the server.
$adminlogin = "ServerAdmin"
$password = "Password@123"

# The IP address of your development computer that accesses the SQL DB.
$clientIP = "24.18.117.76"

# The database name.
$databasename = "call-center-db"

# Create a new resource group for your deployment and give it a name and a location.
New-AzureRmResourceGroup -Name $dbresourcegroupname -Location $location

# Create the SQL server.
New-AzureRmSqlServer -ResourceGroupName $dbresourcegroupname `
    -ServerName $servername `
    -Location $location `
    -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))

# Create the firewall rule to allow your development computer to access the server.
New-AzureRmSqlServerFirewallRule -ResourceGroupName $dbresourcegroupname `
    -ServerName $servername `
    -FirewallRuleName "AllowClient" -StartIpAddress $clientIP -EndIpAddress $clientIP

# Creeate the database in the server.
New-AzureRmSqlDatabase  -ResourceGroupName $dbresourcegroupname `
    -ServerName $servername `
    -DatabaseName $databasename `
    -RequestedServiceObjectiveName "S0"

Write-Host "Server name is $servername"
```

## <a name="update-the-web-config"></a>Atualizar a configuração da Web
Novamente no projeto **FabrikamFiber.Web**, atualize a cadeia de conexão no arquivo **web.config** para que ela aponte para o SQL Server no contêiner.  Atualize a parte do *Server* da cadeia de conexão para o servidor criado pelo script anterior. 

```xml
<add name="FabrikamFiber-Express" connectionString="Server=tcp:fab-fiber-1300282665.database.windows.net,1433;Initial Catalog=call-center-db;Persist Security Info=False;User ID=ServerAdmin;Password=Password@123;MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;" providerName="System.Data.SqlClient" />
<add name="FabrikamFiber-DataWarehouse" connectionString="Server=tcp:fab-fiber-1300282665.database.windows.net,1433;Initial Catalog=call-center-db;Persist Security Info=False;User ID=ServerAdmin;Password=Password@123;MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;" providerName="System.Data.SqlClient" />
  
```
>[!NOTE]
>Você pode usar qualquer SQL Server que preferir para a depuração local, desde que ele seja acessível no host. No entanto, **localdb** não dá suporte à comunicação `container -> host`. Se você desejar usar outro banco de dados SQL ao criar um build de versão de seu aplicativo Web, adicione outra cadeia de conexão ao arquivo *web.release.config*.

## <a name="run-the-containerized-application-locally"></a>Executar o aplicativo em contêineres localmente
Pressione **F5** para executar e depurar o aplicativo em um contêiner no cluster de desenvolvimento do Service Fabric. Clique em **Sim** se for exibida uma caixa de mensagem pedindo para conceder permissões de leitura e execução para o grupo “ServiceFabricAllowedUsers” para o seu diretório de projeto do Visual Studio.

## <a name="create-a-container-registry"></a>Criar um registro de contêiner
Agora que o aplicativo é executado localmente, comece a preparar a implantação no Azure.  As imagens de contêiner precisam ser armazenadas em um registro de contêiner.  Criar um [registro de contêiner do Azure](/azure/container-registry/container-registry-intro) usando o script a seguir. O nome do registro de contêiner é visível por outras assinaturas do Azure, portanto ele deve ser exclusivo.
Antes de implantar o aplicativo no Azure, você pode enviar por push a imagem de contêiner para esse registro.  Quando o aplicativo for implantado no cluster no Azure, a imagem de contêiner é extraída desse registro.

```powershell
# Variables
$acrresourcegroupname = "fabrikam-acr-group"
$location = "southcentralus"
$registryname="fabrikamregistry$(Get-Random)"

New-AzureRmResourceGroup -Name $acrresourcegroupname -Location $location

$registry = New-AzureRMContainerRegistry -ResourceGroupName $acrresourcegroupname -Name $registryname -EnableAdminUser -Sku Basic
```

## <a name="create-a-service-fabric-cluster-on-azure"></a>Criar um cluster do Service Fabric no Azure
Aplicativos do Service Fabric são executados em um cluster, um conjunto conectado à rede de máquinas virtuais ou físicas.  Antes de implantar o aplicativo no Azure, primeiro crie um cluster do Service Fabric no Azure.

Você pode:
- Criar um cluster de teste no Visual Studio. Essa opção permite que você crie um cluster seguro diretamente no Visual Studio com suas configurações preferenciais. 
- [Criar um cluster seguro a partir de um modelo](service-fabric-tutorial-create-vnet-and-windows-cluster.md)

Este tutorial cria um cluster do Visual Studio, que é ideal para cenários de teste. Se você criar um cluster de alguma outra forma, ou usar um cluster existente, você pode copiar e colar o ponto de extremidade de conexão ou escolhê-lo em sua assinatura. 

Ao criar o cluster, escolha um SKU que ofereça suporte a contêineres em execução. O sistema operacional Windows Server nos seus nós de cluster devem ser compatíveis com o sistema operacional Windows Server do seu contêiner. Para obter mais informações, consulte [Compatibilidade do sistema operacional contêiner e do sistema operacional do host do Windows Server](service-fabric-get-started-containers.md#windows-server-container-os-and-host-os-compatibility). Por padrão, este tutorial cria uma imagem do Docker com base no Windows Server 2016 LTSC. Contêineres com base nesta imagem serão executados em clusters criados com o Windows Server 2016 Datacenter com contêineres. No entanto, se você cria um cluster ou usa um cluster existente com base no Windows Server Datacenter Core 1709 com contêineres, você deve alterar a imagem do sistema operacional do Windows Server na qual o contêiner se baseia. Abra o **Dockerfile** no projeto **FabrikamFiber.Web**, comente a instrução `FROM` existente (com base em `windowsservercore-ltsc`) e remova os comentários da instrução `FROM` com base em `windowsservercore-1709`. 

1. Clique com o botão direito do mouse no projeto de aplicativo **Fabrikam.CallCenterApplication** no Gerenciador de Soluções e escolha **Publicar**.

2. Entre usando sua conta do Azure para que você possa ter acesso às assinaturas. 

3. Selecione a lista suspensa para o **Ponto de Extremidade de Conexão** e selecione a opção **Criar Novo Cluster...**.    
        
4. Na caixa de diálogo **Criar cluster**, modifique as seguintes configurações:

    1. Especifique o nome do cluster no campo **Nome do Cluster**, bem como a assinatura e o local que você deseja usar.
    2. Opcional: você pode modificar o número de nós. Por padrão, existem três nós, o mínimo necessário para testar cenários do Service Fabric.
    3. Selecione a guia **Certificado**. Nessa guia, digite uma senha a ser usada para proteger o certificado do cluster. Esse certificado ajuda a proteger o cluster. Você também pode modificar o caminho de onde deseja salvar o certificado. O Visual Studio também pode importar o certificado para você, já que essa é uma etapa necessária para publicar o aplicativo no cluster.
    4. Selecione a guia **Detalhes da VM**. Especifique a senha que você deseja usar para as VMs (máquinas virtuais) que compõem o cluster. O nome de usuário e a senha podem ser usados para se conectar remotamente às VMs. Você também deve selecionar um tamanho de máquina VM e pode alterar a imagem da VM, se necessário.
    5. Na guia **Avançado**, liste a porta do aplicativo que deverá ser aberta no balanceador de carga quando o cluster for implantado. No Gerenciador de Soluções, abra FabrikamFiber.Web->PackageRoot->ServiceManifest.xml.  A porta para o front-end da Web é listada em **Ponto de extremidade**.  Você também pode adicionar uma chave existente do Application Insights a ser usada para direcionar os arquivos de log do aplicativo.
    6. Quando você terminar de modificar as configurações, selecione o botão **Criar**. 
5. A criação levará alguns minutos para ser concluída; a janela de saída indica quando o cluster foi totalmente criado.
    

## <a name="allow-your-application-running-in-azure-to-access-the-sql-db"></a>Permita que o aplicativo em execução no Azure acesse o Banco de Dados SQL
Anteriormente, você criou uma regra de firewall do SQL para conceder acesso ao seu aplicativo em execução localmente.  Em seguida, você precisa habilitar o aplicativo em execução no Azure para acessar o Banco de Dados SQL.  Crie um [ponto de extremidade de serviço de rede virtual](/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview) para o cluster do Service Fabric e, em seguida, crie uma regra para permitir que esse ponto de extremidade acesse o Banco de Dados SQL.

```powershell
# Create a virtual network service endpoint
$clusterresourcegroup = "fabrikamfiber.callcenterapplication_RG"
$resource = Get-AzureRmResource -ResourceGroupName $clusterresourcegroup -ResourceType Microsoft.Network/virtualNetworks | Select-Object -first 1
$vnetName = $resource.Name

Write-Host 'Virtual network name: ' $vnetName 

# Get the virtual network by name.
$vnet = Get-AzureRmVirtualNetwork `
  -ResourceGroupName $clusterresourcegroup `
  -Name              $vnetName

Write-Host "Get the subnet in the virtual network:"

# Get the subnet, assume the first subnet contains the Service Fabric cluster.
$subnet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet | Select-Object -first 1

$subnetName = $subnet.Name
$subnetID = $subnet.Id
$addressPrefix = $subnet.AddressPrefix

Write-Host "Subnet name: " $subnetName " Address prefix: " $addressPrefix " ID: " $subnetID

# Assign a Virtual Service endpoint 'Microsoft.Sql' to the subnet.
$vnet = Set-AzureRmVirtualNetworkSubnetConfig `
  -Name            $subnetName `
  -AddressPrefix   $addressPrefix `
  -VirtualNetwork  $vnet `
  -ServiceEndpoint Microsoft.Sql | Set-AzureRmVirtualNetwork

$vnet.Subnets[0].ServiceEndpoints;  # Display the first endpoint.

# Add a SQL DB firewall rule for the virtual network service endpoint
$subnet = Get-AzureRmVirtualNetworkSubnetConfig `
  -Name           $subnetName `
  -VirtualNetwork $vnet;

$VNetRuleName="ServiceFabricClusterVNetRule"
$vnetRuleObject1 = New-AzureRmSqlServerVirtualNetworkRule `
  -ResourceGroupName      $dbresourcegroupname `
  -ServerName             $servername `
  -VirtualNetworkRuleName $VNetRuleName `
  -VirtualNetworkSubnetId $subnetID;
```
## <a name="deploy-the-application-to-azure"></a>Implantar o aplicativo no Azure
Agora que o aplicativo está pronto, você poderá implantá-lo no cluster no Azure diretamente por meio do Visual Studio.  No Gerenciador de Soluções, clique com o botão direito do mouse no projeto de aplicativo **Fabrikam.CallCenterApplication** e selecione **Publicar**.  Em **Ponto de extremidade de Conexão**, selecione o ponto de extremidade do cluster que você criou anteriormente.  Em **Registro de Contêiner do Azure**, selecione o registro de contêiner que você criou anteriormente.  Clique em **Publicar** para implantar o aplicativo no cluster no Azure.

![aplicativo Publicar][publish-app]

Acompanhe o progresso da implantação na janela de saída.  Quando o aplicativo for implantado, abra um navegador e digite o endereço do cluster e a porta do aplicativo. Por exemplo, http://http://fabrikamfibercallcenter.southcentralus.cloudapp.azure.com:8659/.

![Exemplo de Fabrikam Web][fabrikam-web-page-deployed]

## <a name="clean-up-resources"></a>Limpar recursos
Se você tiver terminado, certifique-se de remover todos os recursos que você criou.  A maneira mais simples é remover os grupos de recursos que contêm o cluster do Service Fabric, o Banco de Dados SQL do Azure e o Registro de Contêiner do Azure.

```powershell
$dbresourcegroupname = "fabrikam-fiber-db-group"
$acrresourcegroupname = "fabrikam-acr-group"
$clusterresourcegroupname="fabrikamcallcentergroup"

# Remove the Azure SQL DB
Remove-AzureRmResourceGroup -Name $dbresourcegroupname

# Remove the container registry
Remove-AzureRmResourceGroup -Name $acrresourcegroupname

# Remove the Service Fabric cluster
Remove-AzureRmResourceGroup -Name $clusterresourcegroupname
```

## <a name="next-steps"></a>Próximas etapas
Neste tutorial, você aprendeu como:

> [!div class="checklist"]
> * Colocar um aplicativo existente em um contêiner usando o Visual Studio
> * Criar um banco de dados SQL do Azure
> * Criar um registro de contêiner do Azure
> * Implantar um aplicativo do Service Fabric no Azure

Na próxima parte do tutorial, saiba como configurar o [monitoramento do seu contêiner](service-fabric-tutorial-monitoring-wincontainers.md).


[link-fabrikam-github]: https://aka.ms/fabrikamcontainer
[link-azure-powershell-install]: /powershell/azure/install-azurerm-ps
[link-servicefabric-create-secure-clusters]: service-fabric-cluster-creation-via-arm.md
[link-visualstudio-cd-extension]: https://aka.ms/cd4vs
[link-servicefabric-containers]: service-fabric-get-started-containers.md
[link-servicefabric-createapp]: service-fabric-create-your-first-application-in-visual-studio.md
[link-azure-portal]: https://portal.azure.com
[link-sf-clustertemplate]: https://aka.ms/securepreviewonelineclustertemplate
[link-azure-pricing-calculator]: https://azure.microsoft.com/pricing/calculator/
[link-azure-subscription]: https://azure.microsoft.com/free/
[link-vsts-account]: https://www.visualstudio.com/team-services/pricing/
[link-azure-sql]: /azure/sql-database/

[fabrikam-web-page]: media/service-fabric-host-app-in-a-container/fabrikam-web-page.png
[fabrikam-web-page-deployed]: media/service-fabric-host-app-in-a-container/fabrikam-web-page-deployed.png
[publish-app]: media/service-fabric-host-app-in-a-container/publish-app.png
