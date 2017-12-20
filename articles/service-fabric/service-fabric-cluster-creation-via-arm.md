---
title: Atualizar um cluster do Azure Service Fabric de um modelo | Microsoft Docs
description: "Este artigo descreve como configurar um cluster seguro do Service Fabric no Azure usando o Azure Resource Manager, o Azure Key Vault e o Azure Active Directory (Azure AD) para autenticação do cliente."
services: service-fabric
documentationcenter: .net
author: chackdan
manager: timlt
editor: chackdan
ms.assetid: 15d0ab67-fc66-4108-8038-3584eeebabaa
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/07/2017
ms.author: chackdan
ms.openlocfilehash: 0065874c2f992ad9c18f68303878fb580ee8b391
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/11/2017
---
# <a name="create-a-service-fabric-cluster-by-using-azure-resource-manager"></a>Criar um cluster do Service Fabric usando o Azure Resource Manager 
> [!div class="op_single_selector"]
> * [Gerenciador de Recursos do Azure](service-fabric-cluster-creation-via-arm.md)
> * [Portal do Azure](service-fabric-cluster-creation-via-portal.md)
>
>

Este guia passo a passo orienta você pela configuração de um cluster do Azure Service Fabric seguro no Azure usando o Azure Resource Manager. Sabemos que o artigo é longo. No entanto, a menos que você já esteja totalmente familiarizado com o conteúdo, não deixe de seguir cada etapa com cuidado.

O guia aborda os seguintes procedimentos:

* Principais conceitos de que você precisa estar ciente antes de implantar um cluster do Service Fabric.
* Criar um cluster no Azure usando módulos de serviço do Resource Manager do Service Fabric.
* Configurar o Azure Active Directory (Azure AD) para autenticar os usuários que executam operações de gerenciamento no cluster.
* Criar um modelo personalizado do Azure Resource Manager para seu cluster e implantá-lo.

## <a name="key-concepts-to-be-aware-of"></a>Principais conceitos a serem considerados
No Azure, o Service Fabric exige que você use um certificado x509 para proteger o cluster e seus pontos de extremidade. Os certificados são usados no Service Fabric para fornecer autenticação e criptografia para proteger vários aspectos de um cluster e de seus aplicativos. Para operações de gerenciamento de desempenho/acesso do cliente no cluster, incluindo implantação, atualização e exclusão de aplicativos, serviços e os dados que eles contêm, você pode usar certificados ou as credenciais do Azure Active Directory. O uso do Azure Active Directory é altamente recomendado, já que é a única maneira de impedir o compartilhamento de certificados em seus clientes.  Para obter mais informações sobre como os certificados são usados no Service Fabric, consulte [Cenários de segurança do cluster do Service Fabric][service-fabric-cluster-security].

O Service Fabric usa certificados x.509 para proteger um cluster e fornecer recursos de segurança do aplicativo. Use o [Key Vault][key-vault-get-started] para gerenciar certificados de clusters do Service Fabric no Azure. 


### <a name="cluster-and-server-certificate-required"></a>Certificado de cluster e de servidor (necessário)
Esses certificados (um primário e, opcionalmente, um secundário) são necessárias para proteger um cluster e impedir o acesso não autorizado a ele. Ele fornece segurança de cluster de duas maneiras:

* **Autenticação do cluster:** autentica a comunicação de nó para nó para a federação de cluster. Somente os nós que podem provar sua identidade com esse certificado podem ingressar no cluster.
* **Autenticação de servidor**: autentica os pontos de extremidade de gerenciamento de cluster para um cliente de gerenciamento, para que o cliente de gerenciamento saiba que está se comunicando com o cluster real e não com um “homem no meio”. Esse certificado também fornece um SSL para a API de gerenciamento de HTTPS e para o Service Fabric Explorer sobre HTTPS.

Para servir a essas finalidades, o certificado deverá atender a estes requisitos:

* O certificado deve conter uma chave privada. Esses certificados geralmente têm extensões .pfx ou .pem  
* O certificado deve ser criado para troca de chaves, que deve ser exportável para um arquivo Troca de Informações Pessoais (.pfx).
* O **nome da entidade do certificado deve corresponder ao domínio usado para acessar o cluster do Service Fabric**. Essa correspondência é necessária para fornecer um SSL para os pontos de extremidade de gerenciamento de HTTPS e o Service Fabric Explorer do cluster. Você não pode obter um certificado SSL de uma AC (autoridade de certificação) para o domínio *.cloudapp.azure.com. Você deve obter um nome de domínio personalizado para seu cluster. Quando você solicitar um certificado de uma autoridade de certificação, o nome de assunto do certificado deve corresponder ao nome de domínio personalizado usado para seu cluster.

### <a name="set-up-azure-active-directory-for-client-authentication-optional-but-recommended"></a>Configurar o Azure Active Directory para autenticação de cliente (Opcional, mas recomendado)

O Azure AD permite que as organizações (conhecidas como locatários) gerenciem o acesso dos usuários aos aplicativos. Os aplicativos são divididos entre os que têm IU de entrada na Web e os que têm experiência de cliente nativa. Neste artigo, partimos do pressuposto que você já tenha criado um locatário. Se não for o caso, comece lendo [Como obter um locatário do Azure Active Directory][active-directory-howto-tenant].

Os clusters do Service Fabric oferecem vários pontos de entrada para a funcionalidade de gerenciamento, incluindo o [Service Fabric Explorer][service-fabric-visualizing-your-cluster] online o [Visual Studio][service-fabric-manage-application-in-visual-studio]. Como resultado, você criará dois aplicativos do Azure AD para controlar o acesso ao cluster, um aplicativo Web e um aplicativo nativo.

Mais informações sobre como configurá-lo posteriormente neste documento.

### <a name="application-certificates-optional"></a>Certificados de aplicativo (opcionais)
Qualquer número de certificados adicionais pode ser instalado em um cluster para fins de segurança do aplicativo. Antes de criar o cluster, considere os cenários de segurança de aplicativos que exigem um certificado a ser instalado em nós, como:

* Criptografia e descriptografia de valores de configuração de aplicativo.
* Criptografia de dados entre nós durante a replicação.

O conceito para criar clusters seguros é o mesmo, sejam clusters do Linux ou do Windows. 

### <a name="client-authentication-certificates-optional"></a>Certificados de autenticação de cliente (opcional)
Qualquer quantidade de certificados adicionais pode ser especificada para operações de cliente de usuário ou administrador. Por padrão, o certificado de cluster tem privilégios de cliente do administrador. Esses certificados de cliente adicionais não devem ser instalados no cluster, só precisam ser especificados como sendo permitidos na configuração do cluster, no entanto, eles precisam ser instalados nos computadores cliente para se conectarem ao cluster e executarem qualquer operação de gerenciamento.


## <a name="prerequisites"></a>Pré-requisitos 
O conceito para criar clusters seguros é o mesmo, sejam clusters do Linux ou do Windows. Este guia abrange o uso do Azure PowerShell ou da CLI do Azure para criar novos clusters. Os pré-requisitos são 

-  [O Azure PowerShell 4.1 e posterior][azure-powershell] ou [CLI do Azure 2.0 e posterior][azure-CLI].
-  Você pode encontrar detalhes sobre os módulos do Service Fabric aqui – [AzureRM.ServiceFabric](https://docs.microsoft.com/powershell/module/azurerm.servicefabric) e [módulo de CLI az SF](https://docs.microsoft.com/cli/azure/sf?view=azure-cli-latest)


## <a name="use-service-fabric-rm-module-to-deploy-a-cluster"></a>Use o módulo RM do Service Fabric para implantar um cluster

Neste documento, utilizamos o PowerShell do RM do Service Fabric e o módulo de CLI para implantar um cluster; o PowerShell ou o módulo de CLI permite vários cenários. Vamos passar por cada um deles. Escolha o cenário que você acha que atenda mais bem às suas necessidades. 

- Criar um novo cluster – usando um certificado autoassinado gerado pelo sistema
    - Usar um modelo de cluster padrão
    - usar um modelo que você já tenha
- Criar um novo cluster – usando um certificado que você já tem
    - Usar um modelo de cluster padrão
    - usar um modelo que você já tenha

### <a name="create-new-cluster----using-a-system-generated-self-signed-certificate"></a>Criar novo cluster – usando um certificado autoassinado gerado pelo sistema

Use o seguinte comando para criar o cluster, se desejar que o sistema gere um certificado autoassinado e usá-lo para proteger o cluster. Este comando configura um certificado de cluster primário que é usado para segurança do cluster e para configurar o acesso de administrador para executar operações de gerenciamento usando esse certificado.

### <a name="login-in-to-azure"></a>Faça logon no Azure.

```Powershell

Login-AzureRmAccount
Set-AzureRmContext -SubscriptionId <guid>

```

```CLI

azure login
az account set --subscription $subscriptionId

```
#### <a name="use-the-default-5-node-1-nodetype-template-that-ships-in-the-module-to-set-up-the-cluster"></a>Use o modelo de nodetype nó 1 padrão 5 que acompanha o módulo para configurar o cluster

Use o seguinte comando para criar um cluster rapidamente, especificando os parâmetros mínimos

O modelo usado está disponível nos [Exemplos de modelos do Azure Service Fabric: modelo do Windows](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure-NSG) e [Modelo Ubuntu](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Ubuntu-1-NodeTypes-Secure)

Os comandos abaixo funcionam para criação de clusters do Windows e Linux, basta especificar o sistema operacional adequadamente. Os comandos de CLI/PowerShell também geram o certificado no theCertificateOutputFolder especificado. O comando aceita outros parâmetros como VM SKU.

```Powershell

$resourceGroupLocation="westus"
$resourceGroupName="mycluster"
$vaultName="myvault"
$vaultResourceGroupName="myvaultrg"
$CertSubjectName="mycluster.westus.cloudapp.azure.com"
$certPassword="Password!1" | ConvertTo-SecureString -AsPlainText -Force 
$vmpassword="Password!1" | ConvertTo-SecureString -AsPlainText -Force
$vmuser="myadmin"
$os="WindowsServer2016DatacenterwithContainers"
$certOutputFolder="c:\certificates"

New-AzureRmServiceFabricCluster -ResourceGroupName $resourceGroupName -CertificateOutputFolder $certOutputFolder -CertificatePassword $certpassword -CertificateSubjectName $CertSubjectName -OS $os -VmPassword $vmpassword -VmUserName $vmuser 

```

```CLI

declare resourceGroupLocation="westus"
declare resourceGroupName="mylinux"
declare vaultResourceGroupName="myvaultrg"
declare vaultName="myvault"
declare CertSubjectName="mylinux.westus.cloudapp.azure.com"
declare vmpassword="Password!1"
declare certpassword="Password!1"
declare vmuser="myadmin"
declare vmOs="UbuntuServer1604"
declare certOutputFolder="c:\certificates"



az sf cluster create --resource-group $resourceGroupName --location $resourceGroupLocation  \
    --certificate-output-folder $certOutputFolder --certificate-password $certpassword  \
    --vault-name $vaultName --vault-resource-group $resourceGroupName  \
    --template-file $templateFilePath --parameter-file $parametersFilePath --vm-os $vmOs  \
    --vm-password $vmpassword --vm-user-name $vmuser

```

#### <a name="use-the-custom-template-that-you-already-have"></a>Usar um modelo personalizado que você já tem 

Caso precise criar um modelo personalizado para atender às suas necessidades, é altamente recomendável que você inicie com um dos modelos que estão disponíveis no [Exemplos de modelo do Azure Service Fabric](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master). Siga as orientações e explicações para [personalizar o modelo de cluster][customize-your-cluster-template] na seção abaixo.

Se você já tem um modelo personalizado, certifique-se de verificar novamente se todos os três parâmetros relacionados ao certificado no modelo e no arquivo de parâmetro são nomeados da seguinte maneira e se os valores são nulos da maneira a seguir.

```Json
   "certificateThumbprint": {
      "value": ""
    },
    "sourceVaultValue": {
      "value": ""
    },
    "certificateUrlValue": {
      "value": ""
    },
```


```Powershell


$resourceGroupLocation="westus"
$resourceGroupName="mycluster"
$CertSubjectName="mycluster.westus.cloudapp.azure.com"
$certPassword="Password!1" | ConvertTo-SecureString -AsPlainText -Force 
$certOutputFolder="c:\certificates"

$parameterFilePath="c:\mytemplates\mytemplateparm.json"
$templateFilePath="c:\mytemplates\mytemplate.json"


New-AzureRmServiceFabricCluster -ResourceGroupName $resourceGroupName -CertificateOutputFolder $certOutputFolder -CertificatePassword $certpassword -CertificateSubjectName $CertSubjectName -TemplateFile $templateFilePath -ParameterFile $parameterFilePath 

```

Este é o comando de CLI equivalente para fazer o mesmo. Altere os valores nas instruções declaradas para os valores apropriados. A CLI é compatível com todos os outros parâmetros compatíveis com o comando do Powershell acima.

```CLI

declare certPassword=""
declare resourceGroupLocation="westus"
declare resourceGroupName="mylinux"
declare certSubjectName="mylinuxsecure.westus.cloudapp.azure.com"
declare parameterFilePath="c:\mytemplates\linuxtemplateparm.json"
declare templateFilePath="c:\mytemplates\linuxtemplate.json"
declare certOutputFolder="c:\certificates"


az sf cluster create --resource-group $resourceGroupName --location $resourceGroupLocation  \
    --certificate-output-folder $certOutputFolder --certificate-password $certPassword  \
    --certificate-subject-name $certSubjectName \
    --template-file $templateFilePath --parameter-file $parametersFilePath

```


### <a name="create-new-cluster---using-the-certificate-you-bought-from-a-ca-or-you-already-have"></a>Criar novo cluster – usar o certificado que você adquiriu de uma autoridade de certificação ou que já tiver.

Use o seguinte comando para criar o cluster, se você tiver um certificado que deseja usar para proteger o cluster.

Caso esse seja um certificado assinado pela autoridade de certificação que você acabará usando para outras finalidades também, é recomendável que você forneça um grupo de recursos distintos especificamente para seu cofre de chaves. Recomendamos que você coloque o cofre de chaves em seu próprio grupo de recursos. Essa ação permite que você remova os grupos de recursos de computação e armazenamento, incluindo o grupo de recursos que contém o cluster do Service Fabric sem perder suas chaves e seus segredos. **O grupo de recursos que contém o cofre de chaves _deve estar na mesma região_ que o cluster que está sendo usado.**


#### <a name="use-the-default-5-node-1-nodetype-template-that-ships-in-the-module"></a>Use o modelo de nodetype nó 1 padrão 5 que acompanha o módulo
O modelo usado está disponível nos [exemplos do azure: modelo do Windows](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure-NSG) e [modelo Ubuntu](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Ubuntu-1-NodeTypes-Secure)

```Powershell
$resourceGroupLocation="westus"
$resourceGroupName="mylinux"
$vaultName="myvault"
$vaultResourceGroupName="myvaultrg"
$certPassword="Password!1" | ConvertTo-SecureString -AsPlainText -Force 
$vmpassword=("Password!1" | ConvertTo-SecureString -AsPlainText -Force) 
$vmuser="myadmin"
$os="WindowsServer2016DatacenterwithContainers"

New-AzureRmServiceFabricCluster -ResourceGroupName $resourceGroupName -KeyVaultResouceGroupName $vaultResourceGroupName -KeyVaultName $vaultName -CertificateFile C:\MyCertificates\chackocertificate3.pfx -CertificatePassword $certPassword -OS $os -VmPassword $vmpassword -VmUserName $vmuser 

```

```CLI

declare vmPassword="Password!1"
declare certPassword="Password!1"
declare vmUser="myadmin"
declare resourceGroupLocation="westus"
declare resourceGroupName="mylinux"
declare vaultResourceGroupName="myvaultrg"
declare vaultName="myvault"
declare certificate-file="c:\certificates\mycert.pem"
declare vmOs="UbuntuServer1604"


az sf cluster create --resource-group $resourceGroupName --location $resourceGroupLocation  \
    --certificate-file $certificate-file --certificate-password $certPassword  \
    --vault-name $vaultName --vault-resource-group $vaultResourceGroupName  \
    --vm-os vmOs \
    --vm-password $vmPassword --vm-user-name $vmUser

```

#### <a name="use-the-custom-template-that-you-have"></a>Usar o modelo personalizado que você tem 
Caso precise criar um modelo personalizado para atender às suas necessidades, é altamente recomendável que você inicie com um dos modelos que estão disponíveis no [Exemplos de modelo do Azure Service Fabric](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master). Siga as orientações e explicações para [personalizar o modelo de cluster][customize-your-cluster-template] na seção abaixo.

Se você já tem um modelo personalizado, certifique-se de verificar novamente se todos os três parâmetros relacionados ao certificado no modelo e no arquivo de parâmetro são nomeados da seguinte maneira e se os valores são nulos da maneira a seguir.

```Json
   "certificateThumbprint": {
      "value": ""
    },
    "sourceVaultValue": {
      "value": ""
    },
    "certificateUrlValue": {
      "value": ""
    },
```


```Powershell

$resourceGroupLocation="westus"
$resourceGroupName="mylinux"
$vaultName="myvault"
$vaultResourceGroupName="myvaultrg"
$certPassword="Password!1" | ConvertTo-SecureString -AsPlainText -Force 
$os="WindowsServer2016DatacenterwithContainers"
$parameterFilePath="c:\mytemplates\mytemplateparm.json"
$templateFilePath="c:\mytemplates\mytemplate.json"
$certificateFile="C:\MyCertificates\chackonewcertificate3.pem"


New-AzureRmServiceFabricCluster -ResourceGroupName $resourceGroupName -TemplateFile $templateFilePath -ParameterFile $parameterFilePath -KeyVaultResouceGroupName $vaultResourceGroupName -KeyVaultName $vaultName -CertificateFile $certificateFile -CertificatePassword #certPassword

```

Este é o comando de CLI equivalente para fazer o mesmo. Altere os valores nas instruções declaradas para os valores apropriados.

```CLI

declare certPassword="Password!1"
declare resourceGroupLocation="westus"
declare resourceGroupName="mylinux"
declare vaultResourceGroupName="myvaultrg"
declare vaultName="myvault"
declare parameterFilePath="c:\mytemplates\linuxtemplateparm.json"
declare templateFilePath="c:\mytemplates\linuxtemplate.json"

az sf cluster create --resource-group $resourceGroupName --location $resourceGroupLocation  \
    --certificate-file $certificate-file --certificate-password $password  \
    --vault-name $vaultName --vault-resource-group $vaultResourceGroupName  \
    --template-file $templateFilePath --parameter-file $parametersFilePath 
```

#### <a name="use-a-pointer-to-the-secret-you-already-have-uploaded-into-the-keyvault"></a>Usar um ponteiro para o segredo que você já carregou no cofre de chaves

Para usar um cofre de chaves existente, você _tem habilitá-lo para implantação_ a fim de permitir que o provedor de recursos de computação obtenha certificados e os instale em nós de cluster:

```powershell

Set-AzureRmKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -EnabledForDeployment


$parameterFilePath="c:\mytemplates\mytemplate.json"
$templateFilePath="c:\mytemplates\mytemplateparm.json"
$secertId="https://test1.vault.azure.net:443/secrets/testcertificate4/55ec7c4dc61a462bbc645ffc9b4b225f"


New-AzureRmServiceFabricCluster -ResourceGroupName $resourceGroup -SecretIdentifier $secretID -TemplateFile $templateFile -ParameterFile $templateParmfile 

```
Este é o comando de CLI equivalente para fazer o mesmo. Altere os valores nas instruções declaradas para os valores apropriados.

```cli

declare $parameterFilePath="c:\mytemplates\mytemplate.json"
declare $templateFilePath="c:\mytemplates\mytemplateparm.json"
declare $secertId="https://test1.vault.azure.net:443/secrets/testcertificate4/55ec7c4dc61a462bbc645ffc9b4b225f"


az sf cluster create --resource-group $resourceGroupName --location $resourceGroupLocation  \
    --secret-identifieraz $secretID  \
    --template-file $templateFilePath --parameter-file $parametersFilePath 

```

<a id="add-AAD-for-client"></a>

## <a name="set-up-azure-active-directory-for-client-authentication"></a>Configurar o Azure Active Directory para autenticação de cliente

O Azure AD permite que as organizações (conhecidas como locatários) gerenciem o acesso dos usuários aos aplicativos. Os aplicativos são divididos entre os que têm IU de entrada na Web e os que têm experiência de cliente nativa. Neste artigo, partimos do pressuposto que você já tenha criado um locatário. Se não for o caso, comece lendo [Como obter um locatário do Azure Active Directory][active-directory-howto-tenant].

Os clusters do Service Fabric oferecem vários pontos de entrada para a funcionalidade de gerenciamento, incluindo o [Service Fabric Explorer][service-fabric-visualizing-your-cluster] online o [Visual Studio][service-fabric-manage-application-in-visual-studio]. Como resultado, você criará dois aplicativos do Azure AD para controlar o acesso ao cluster, um aplicativo Web e um aplicativo nativo.

Para simplificar algumas das etapas envolvidas na configuração do Azure AD com um cluster do Service Fabric, criamos um conjunto de scripts do Windows PowerShell.

> [!NOTE]
> Você deve concluir as etapas a seguir para poder criar o cluster. Como os scripts esperam pontos de extremidade e nomes de cluster, os valores devem ser planejados, não os valores que você já tinha criado.

1. [Baixe os scripts][sf-aad-ps-script-download] em seu computador.
2. Clique com o botão direito do mouse no arquivo zip, selecione **Propriedades**, marque a caixa de seleção **Desbloquear** e clique em **Aplicar**.
3. Extraia o arquivo zip.
4. Execute `SetupApplications.ps1` e forneça TenantId, ClusterName e WebApplicationReplyUrl como parâmetros. Por exemplo:

```powershell
    .\SetupApplications.ps1 -TenantId '690ec069-8200-4068-9d01-5aaf188e557a' -ClusterName 'mycluster' -WebApplicationReplyUrl 'https://mycluster.westus.cloudapp.azure.com:19080/Explorer/index.html'

```

Você pode encontrar o TenantId executando o comando `Get-AzureSubscription` do PowerShell. A execução desse comando exibe a TenantId para cada assinatura.

O ClusterName é usado para prefixar os aplicativos do Azure AD criados pelo script. Ele não precisa corresponder exatamente ao nome real do cluster. Serve apenas para tornar mais fácil mapear artefatos do Azure AD para o cluster do Service Fabric com que estão sendo usados.

WebApplicationReplyUrl é o ponto de extremidade padrão que o Azure AD retorna para seus usuários depois que eles concluem o processo de entrada. Defina esse ponto de extremidade como o ponto de extremidade do Service Fabric Explorer para o seu cluster, que por padrão é:

https://&lt;cluster_domain&gt;:19080/Explorer

Você precisará entrar em uma conta que tenha privilégios administrativos para o locatário do Azure AD. Depois de entrar, o script criará aplicativos Web e nativos para representar seu cluster do Service Fabric. Se você observar os aplicativos do locatário no [Portal Clássico do Azure][azure-classic-portal], você verá duas novas entradas:

   * *ClusterName*\_Cluster
   * *ClusterName*\_Cliente

O script imprimirá o JSON necessário para o modelo do Azure Resource Manager ao criar o cluster na próxima seção. Portanto, convém manter a janela do PowerShell aberta.

```json
"azureActiveDirectory": {
  "tenantId":"<guid>",
  "clusterApplication":"<guid>",
  "clientApplication":"<guid>"
},
```

<a id="customize-arm-template" ></a>

## <a name="create-a-service-fabric-cluster-resource-manager-template"></a>Criar um modelo do Resource Manager do cluster do Service Fabric
Esta seção é para usuários que desejam criar um modelo do Resource Manager de cluster do Service Fabric personalizado. assim que você tiver um modelo, é possível voltar e usar os modelos de CLI e do PowerShell para implantá-lo. 

Os exemplos de modelo do Resource Manager estão disponíveis nos [Exemplos do Azure no GitHub](https://github.com/Azure-Samples/service-fabric-cluster-templates). Esses modelos podem ser usados como ponto de partida para o modelo de cluster.

### <a name="create-the-resource-manager-template"></a>Criar o modelo do Gerenciador de Recursos
Este guia usa o modelo de exemplo e parâmetros de modelo do [cluster seguro de cinco nós][service-fabric-secure-cluster-5-node-1-nodetype]. Baixe `azuredeploy.json` e `azuredeploy.parameters.json` em seu computador e abra ambos os arquivos em seu editor de texto favorito.

### <a name="add-certificates"></a>Adicionar certificados
Os certificados são adicionados a um modelo do Resource Manager de cluster quando você faz a referência ao Key Vault que contém as chaves de certificado. Adicione esses parâmetros de cofre de chaves e valores em um arquivo de parâmetros de modelo do Resource Manager (azuredeploy.parameters.json). 

#### <a name="add-all-certificates-to-the-virtual-machine-scale-set-osprofile"></a>Adicionar todos os certificados ao conjunto de dimensionamento de máquinas virtuais osProfile
Todos os certificados instalados no cluster devem ser configurados na seção osProfile do recurso de conjunto de dimensionamento (Microsoft.Compute/virtualMachineScaleSets). Essa ação instrui o provedor de recursos para instalar o certificado nas VMs. Essa instalação inclui o certificado do cluster e os certificados de segurança de aplicativo que você planeja usar para seus aplicativos:

```json
{
  "apiVersion": "[variables('vmssApiVersion')]",
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  ...
  "properties": {
    ...
    "osProfile": {
      ...
      "secrets": [
        {
          "sourceVault": {
            "id": "[parameters('sourceVaultValue')]"
          },
          "vaultCertificates": [
            {
              "certificateStore": "[parameters('clusterCertificateStorevalue')]",
              "certificateUrl": "[parameters('clusterCertificateUrlValue')]"
            },
            {
              "certificateStore": "[parameters('applicationCertificateStorevalue')",
              "certificateUrl": "[parameters('applicationCertificateUrlValue')]"
            },
            ...
          ]
        }
      ]
    }
  }
}
```

#### <a name="configure-the-service-fabric-cluster-certificate"></a>Configurar o certificado do cluster do Service Fabric
O certificado de autenticação do cluster tem que ser configurado tanto no recurso de cluster do Service Fabric (Microsoft.ServiceFabric/clusters) quanto na extensão do Service Fabric para conjuntos de dimensionamento de máquinas virtuais no recurso de conjunto de dimensionamento de máquinas virtuais. Essa disposição permite que o provedor de recursos do Service Fabric o configure para autenticação do cluster e autenticação de servidor para pontos de extremidade de gerenciamento.

##### <a name="add-the-certificate-information-the-virtual-machine-scale-set-resource"></a>Adicione as informações de certificado ao conjunto de dimensionamento de máquinas virtuais:
```json
{
  "apiVersion": "[variables('vmssApiVersion')]",
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  ...
  "properties": {
    ...
    "virtualMachineProfile": {
      "extensionProfile": {
        "extensions": [
          {
            "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType0Name')]",
            "properties": {
              ...
              "settings": {
                ...
                "certificate": {
                  "thumbprint": "[parameters('clusterCertificateThumbprint')]",
                  "x509StoreName": "[parameters('clusterCertificateStoreValue')]"
                },
                ...
              }
            }
          }
        ]
      }
    }
  }
}
```

##### <a name="add-the-certificate-information-to-the-service-fabric-cluster-resource"></a>Adicione as informações de certificado ao recurso de cluster do Service Fabric:
```json
{
  "apiVersion": "[variables('sfrpApiVersion')]",
  "type": "Microsoft.ServiceFabric/clusters",
  "name": "[parameters('clusterName')]",
  "location": "[parameters('clusterLocation')]",
  "dependsOn": [
    "[concat('Microsoft.Storage/storageAccounts/', variables('supportLogStorageAccountName'))]"
  ],
  "properties": {
    "certificate": {
      "thumbprint": "[parameters('clusterCertificateThumbprint')]",
      "x509StoreName": "[parameters('clusterCertificateStoreValue')]"
    },
    ...
  }
}
```

### <a name="add-azure-ad-configuration-to-use-azure-ad-for-client-access"></a>Adicionar a configuração do Azure AD para usar o Azure AD para acesso de cliente

Adicione a as configurações do Azure AD a um modelo do Resource Manager de cluster fazendo referência ao cofre de chaves que contém as chaves de certificado. Adicione esses valores e parâmetros do Azure AD em um arquivo de parâmetros de modelo do Resource Manager (azuredeploy.parameters.json).

```json
{
  "apiVersion": "[variables('sfrpApiVersion')]",
  "type": "Microsoft.ServiceFabric/clusters",
  "name": "[parameters('clusterName')]",
  ...
  "properties": {
    "certificate": {
      "thumbprint": "[parameters('clusterCertificateThumbprint')]",
      "x509StoreName": "[parameters('clusterCertificateStorevalue')]"
    },
    ...
    "azureActiveDirectory": {
      "tenantId": "[parameters('aadTenantId')]",
      "clusterApplication": "[parameters('aadClusterApplicationId')]",
      "clientApplication": "[parameters('aadClientApplicationId')]"
    },
    ...
  }
}
```

### <a name="populate-the-parameter-file-with-the-values"></a>Preencha o arquivo de parâmetro com os valores.
Por fim, use os valores de saída dos comandos do cofre de chaves e do PowerShell do Azure AD para preencher o arquivo de parâmetros:

Se você planeja usar os módulos do PowerShell RM do Azure Service Fabric, não precisa preencher as informações de certificado de cluster; se deseja que o sistema gere o certificado autoassinado para segurança de cluster, basta apenas mantê-los como nulos. 

> [!NOTE]
> Para que os módulos do RM peguem e preencham esses valores de parâmetros vazia, os nomes de parâmetros devem corresponder aos nomes abaixo
>

```json
        "clusterCertificateThumbprint": {
            "value": ""
        },
        "clusterCertificateUrlValue": {
            "value": ""
        },
        "sourceVaultvalue": {
            "value": ""
        },
```

Se está usando certificados de aplicativo ou um cluster existente carregado no cofre de chaves, é necessário obter essas informações e preenchê-las 

Os módulos de RM não têm a capacidade de gerar a configuração do Azure AD para você. Portanto, se você planeja usar o Azure AD para acesso de cliente, precisa preenchê-lo.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        ...
        "clusterCertificateStoreValue": {
            "value": "My"
        },
        "clusterCertificateThumbprint": {
            "value": "<thumbprint>"
        },
        "clusterCertificateUrlValue": {
            "value": "https://myvault.vault.azure.net:443/secrets/myclustercert/4d087088df974e869f1c0978cb100e47"
        },
        "applicationCertificateStorevalue": {
            "value": "My"
        },
        "applicationCertificateUrlValue": {
            "value": "https://myvault.vault.azure.net:443/secrets/myapplicationcert/2e035058ae274f869c4d0348ca100f08"
        },
        "sourceVaultvalue": {
            "value": "/subscriptions/<guid>/resourceGroups/mycluster-keyvault/providers/Microsoft.KeyVault/vaults/myvault"
        },
        "aadTenantId": {
            "value": "<guid>"
        },
        "aadClusterApplicationId": {
            "value": "<guid>"
        },
        "aadClientApplicationId": {
            "value": "<guid>"
        },
        ...
    }
}
```

### <a name="test-your-template"></a>Testar o modelo  
Use o seguinte comando do PowerShell para testar o modelo do Resource Manager com um arquivo de parâmetros:

```powershell
Test-AzureRmResourceGroupDeployment -ResourceGroupName "myresourcegroup" -TemplateFile .\azuredeploy.json -TemplateParameterFile .\azuredeploy.parameters.json
```

Caso você se depare com problemas e receba mensagens criptografadas, use "-Debug" como uma opção.

```powershell
Test-AzureRmResourceGroupDeployment -ResourceGroupName "myresourcegroup" -TemplateFile .\azuredeploy.json -TemplateParameterFile .\azuredeploy.parameters.json -Debug
```

O diagrama a seguir ilustra onde a configuração do cofre de chaves e do Azure AD se encaixa em seu modelo do Resource Manager.

![Mapa de dependências do Resource Manager][cluster-security-arm-dependency-map]

## <a name="create-the-cluster-using-azure-resource-template"></a>Criar o cluster usando o modelo de recurso do Azure 

Agora você pode implantar um cluster usando as etapas descritas anteriormente neste documento ou, se você tem os valores no arquivo de parâmetros preenchidos, está pronto para criar o cluster usando a [implantação de modelo de recurso do Azure][resource-group-template-deploy] diretamente.

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName "myresourcegroup" -TemplateFile .\azuredeploy.json -TemplateParameterFile .\azuredeploy.parameters.json
```

Caso você se depare com problemas e receba mensagens criptografadas, use "-Debug" como uma opção.


<a name="assign-roles"></a>

## <a name="assign-users-to-roles"></a>Atribuir usuários a funções
Depois de criar os aplicativos para representar seu cluster, atribua os usuários às funções com suporte no Service Fabric: somente leitura e administrador. Você pode atribuir as funções usando o [portal clássico do Azure][azure-classic-portal].

1. No portal do Azure, vá para seu locatário e selecione **Aplicativos**.
2. Selecione o aplicativo Web, que tem um nome semelhante a `myTestCluster_Cluster`.
3. Clique na guia **Usuários** .
4. Selecione um usuário para atribuir e clique no botão **Atribuir** na parte inferior da tela.

    ![Botão Atribuir usuários a funções][assign-users-to-roles-button]
5. Selecione a função para atribuir ao usuário.

    ![Caixa de diálogo "Atribuir Usuários"][assign-users-to-roles-dialog]

> [!NOTE]
> Para saber mais sobre as funções no Service Fabric, veja [Controle de acesso baseado em função para clientes do Service Fabric](service-fabric-cluster-security-roles.md).
>
>


## <a name="troubleshooting-help-in-setting-up-azure-active-directory"></a>Ajuda da solução de problemas na configuração do Azure Active Directory
Configurar e usar o Azure AD, pode ser um desafio, portanto, aqui estão algumas dicas sobre o que você pode fazer para depurar o problema.

### <a name="service-fabric-explorer-prompts-you-to-select-a-certificate"></a>O Service Fabric Explorer solicita que você selecione um certificado
#### <a name="problem"></a>Problema
Depois de entrar no Azure AD no Service Fabric Explorer, o navegador retornará para a home page, mas uma mensagem solicitará que você selecione um certificado.

![Caixa de diálogo do certificado SFX][sfx-select-certificate-dialog]

#### <a name="reason"></a>Motivo
O usuário não recebeu uma função no aplicativo de cluster do Azure AD. Assim, a autenticação do Azure AD falha no cluster do Service Fabric. O Service Fabric Explorer reverterá para autenticação de certificado.

#### <a name="solution"></a>Solução
Siga as instruções para configurar o Azure AD e atribuir funções de usuário. Além disso, recomendamos que você habilite a "Atribuição de usuário necessária para acessar o aplicativo" da mesma forma feita por `SetupApplications.ps1`.

### <a name="connection-with-powershell-fails-with-an-error-the-specified-credentials-are-invalid"></a>A conexão com o PowerShell falha e exibe o seguinte erro: “as credenciais especificadas são inválidas”
#### <a name="problem"></a>Problema
Quando você usa o PowerShell para se conectar ao cluster usando o modo de segurança "AzureActiveDirectory", ao entrar no AD do Azure, a conexão falhará com um erro: "as credenciais especificadas são inválidas."

#### <a name="solution"></a>Solução
Essa solução é igual à anterior.

### <a name="service-fabric-explorer-returns-a-failure-when-you-sign-in-aadsts50011"></a>O Service Fabric Explorer retorna uma falha quando você entra: "AADSTS50011"
#### <a name="problem"></a>Problema
Ao tentar entrar no Azure AD no Service Fabric Explorer, a página retorna uma falha: “AADSTS50011: a &lt;URL&gt; do endereço de resposta não coincide com os endereços de resposta configurados para o aplicativo: &lt;GUID&gt;”.

![O endereço de resposta SFX não corresponde][sfx-reply-address-not-match]

#### <a name="reason"></a>Motivo
O aplicativo do cluster (Web) que representa o Service Fabric Explorer tenta se autenticar no Azure AD como parte da solicitação que ele fornece à URL de retorno de redirecionamento. Mas a URL não está listada na lista **URL DE RESPOSTA** do aplicativo Azure AD.

#### <a name="solution"></a>Solução
Na guia **Configurar** do aplicativo do cluster (Web), adicione a URL do Service Fabric Explorer à lista **URL DE RESPOSTA** ou substitua um dos itens na lista. Quando terminar, salve a alteração.

![URL de resposta do aplicativo Web][web-application-reply-url]

### <a name="connect-the-cluster-by-using-azure-ad-authentication-via-powershell"></a>Conectar o cluster usando a autenticação do Azure AD por meio do PowerShell
Para se conectar ao cluster do Service Fabric, use o seguinte exemplo de comando do PowerShell:

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <endpoint> -KeepAliveIntervalInSec 10 -AzureActiveDirectory -ServerCertThumbprint <thumbprint>
```

Para saber mais sobre o cmdlet Connect-ServiceFabricCluster, confira [Connect-ServiceFabricCluster](https://msdn.microsoft.com/library/mt125938.aspx).

### <a name="can-i-reuse-the-same-azure-ad-tenant-in-multiple-clusters"></a>Posso reutilizar o mesmo locatário do Azure AD em vários clusters?
Sim. Mas lembre-se de adicionar a URL do Service Fabric Explorer ao aplicativo do cluster (Web). Caso contrário, o Service Fabric Explorer não funcionará.

### <a name="why-do-i-still-need-a-server-certificate-while-azure-ad-is-enabled"></a>Por que eu ainda preciso de um certificado de servidor quando o Azure AD está habilitado?
FabricClient e FabricGateway realizam autenticação mútua. Durante a autenticação do Azure AD, a integração dele fornece uma identidade de cliente para o servidor e o certificado do servidor é usado para verificar a identidade do servidor. Para saber mais sobre certificados do Service Fabric, confira [Certificados X.509 e Service Fabric][x509-certificates-and-service-fabric].

## <a name="next-steps"></a>Próximas etapas
Neste ponto, você tem um cluster seguro com o Azure Active Directory fornecendo autenticação de gerenciamento. Em seguida, [conecte-se ao cluster](service-fabric-connect-to-secure-cluster.md) e saiba como [gerenciar segredos do aplicativo](service-fabric-application-secret-management.md).


<!-- Links -->
[azure-powershell]:https://docs.microsoft.com/powershell/azure/install-azurerm-ps
[azure-CLI]:https://docs.microsoft.com/en-us/cli/azure/get-started-with-azure-cli?view=azure-cli-latest
[key-vault-get-started]:../key-vault/key-vault-get-started.md
[aad-graph-api-docs]:https://msdn.microsoft.com/library/azure/ad/graph/api/api-catalog
[azure-classic-portal]: https://portal.azure.com/
[service-fabric-cluster-security]: service-fabric-cluster-security.md
[active-directory-howto-tenant]: ../active-directory/active-directory-howto-tenant.md
[service-fabric-visualizing-your-cluster]: service-fabric-visualizing-your-cluster.md
[service-fabric-manage-application-in-visual-studio]: service-fabric-manage-application-in-visual-studio.md
[sf-aad-ps-script-download]:http://servicefabricsdkstorage.blob.core.windows.net/publicrelease/MicrosoftAzureServiceFabric-AADHelpers.zip
[azure-quickstart-templates]: https://github.com/Azure/azure-quickstart-templates
[service-fabric-secure-cluster-5-node-1-nodetype]: https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure
[resource-group-template-deploy]: https://azure.microsoft.com/documentation/articles/resource-group-template-deploy/
[x509-certificates-and-service-fabric]: service-fabric-cluster-security.md#x509-certificates-and-service-fabric
[customize-your-cluster-template]: service-fabric-cluster-creation-via-arm.md#Create-a-Service-Fabric-cluster- Resource-Manager-template

<!-- Images -->
[cluster-security-arm-dependency-map]: ./media/service-fabric-cluster-creation-via-arm/cluster-security-arm-dependency-map.png
[cluster-security-cert-installation]: ./media/service-fabric-cluster-creation-via-arm/cluster-security-cert-installation.png
[assign-users-to-roles-button]: ./media/service-fabric-cluster-creation-via-arm/assign-users-to-roles-button.png
[assign-users-to-roles-dialog]: ./media/service-fabric-cluster-creation-via-arm/assign-users-to-roles.png
[sfx-select-certificate-dialog]: ./media/service-fabric-cluster-creation-via-arm/sfx-select-certificate-dialog.png
[sfx-reply-address-not-match]: ./media/service-fabric-cluster-creation-via-arm/sfx-reply-address-not-match.png
[web-application-reply-url]: ./media/service-fabric-cluster-creation-via-arm/web-application-reply-url.png

