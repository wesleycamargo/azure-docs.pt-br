
<properties
   pageTitle="Criar um cluster seguro do Service Fabric usando o Azure Resource Manager | Microsoft Azure"
   description="Este artigo descreve como configurar um cluster seguro do Service Fabric no Azure usando o Azure Resource Manager, o Cofre de Chaves do Azure e o Azure Active Directory (AAD) para autenticação do cliente."
   services="service-fabric"
   documentationCenter=".net"
   authors="chackdan"
   manager="timlt"
   editor="vturecek"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/25/2016"
   ms.author="vturecek"/>


# <a name="create-a-service-fabric-cluster-in-azure-using-azure-resource-manager"></a>Criar um cluster do Service Fabric no Azure usando o Azure Resource Manager

> [AZURE.SELECTOR]
- [Gerenciador de Recursos do Azure](service-fabric-cluster-creation-via-arm.md)
- [Portal do Azure](service-fabric-cluster-creation-via-portal.md)

Este é um guia passo a passo que orienta você pelas etapas de configuração de um cluster do Azure Service Fabric seguro no Azure usando o Azure Resource Manager. Este guia apresenta as seguintes etapas:

 - Configure o Cofre de Chaves para gerenciar chaves para segurança de cluster e de aplicativo.
 - Crie um cluster seguro no Azure com o Azure Resource Manager.
 - Autentique usuários com o AAD (Azure Active Directory) para gerenciamento de cluster.

Um cluster seguro é um cluster que impede o acesso não autorizado às operações de gerenciamento, que inclui implantar, atualizar e excluir aplicativos, serviços e os dados que eles contêm. Um cluster não seguro é um cluster ao qual qualquer pessoa pode se conectar a qualquer momento e realizar operações de gerenciamento. Embora seja possível criar um cluster não seguro, é **altamente recomendável criar um cluster seguro**. Um cluster não seguro **não pode ser protegido posteriormente** - um novo cluster deverá ser criado.

Os conceitos são os mesmos para a criação de clusters seguros, se os clusters são do Linux ou do Windows. Para obter mais informações e scripts auxiliares para criar clusters do Linux seguras, consulte [Criando clusters seguros no Linux](#secure-linux-clusters)

## <a name="log-in-to-azure"></a>Fazer logon no Azure
Este guia usa [Azure PowerShell][azure-powershell]. Ao iniciar uma nova sessão do PowerShell, faça logon em sua conta do Azure e selecione sua assinatura antes de executar comandos do Azure.

Faça logon na sua conta do Azure:

```powershell
Login-AzureRmAccount
```

Selecione sua assinatura:

```powershell
Get-AzureRmSubscription
Set-AzureRmContext -SubscriptionId <guid>
```

## <a name="set-up-key-vault"></a>Configurar o Cofre de Chaves

Esta seção guia a criação de uma Cofre de Chaves para um cluster do Service Fabric no Azure e para aplicativos do Service Fabric. Para obter um guia completo do Cofre de Chaves, veja o [Guia de introdução ao Cofre de Chaves][key-vault-get-started].

O Service Fabric usa certificados x.509 para proteger um cluster e fornecer recursos de segurança do aplicativo. O Cofre de Chaves do Azure é usado para gerenciar certificados para clusters do Service Fabric no Azure. Quando um cluster é implantado no Azure, o provedor de recursos do Azure responsável pela criação de clusters do Service Fabric recebe certificados do Cofre de Chaves e os instala nas VMs do cluster.

O diagrama a seguir ilustra o relacionamento entre o Cofre de Chaves, um cluster do Service Fabric e o provedor de recursos do Azure que usa certificados armazenados no Cofre de Chaves quando ele cria um cluster:

![Instalação do certificado][cluster-security-cert-installation]

### <a name="create-a-resource-group"></a>Criar um grupo de recursos

A primeira etapa é criar um grupo de recursos especificamente para o Cofre de Chaves. É recomendável colocar o Cofre de Chaves em seu próprio grupo de recursos. Isso permite que você remova os grupos de recursos de computação e armazenamento, incluindo o grupo de recursos com o cluster do Service Fabric sem perder suas chaves e seus segredos. O grupo de recursos com o Cofre de Chaves deve estar na mesma região que o cluster que está sendo usado.

```powershell

    New-AzureRmResourceGroup -Name mycluster-keyvault -Location 'West US'
    WARNING: The output object type of this cmdlet is going to be modified in a future release.
    
    ResourceGroupName : mycluster-keyvault
    Location          : westus
    ProvisioningState : Succeeded
    Tags              :
    ResourceId        : /subscriptions/<guid>/resourceGroups/mycluster-keyvault

```

### <a name="create-key-vault"></a>Criar Cofre da Chave 

Crie um Cofre de Chaves no novo grupo de recursos. O Cofre de Chaves **deve estar habilitado para implantação** para permitir que o provedor de recursos do Service Fabric obtenha certificados ele e os instale em nós de cluster:

```powershell

    New-AzureRmKeyVault -VaultName 'myvault' -ResourceGroupName 'mycluster-keyvault' -Location 'West US' -EnabledForDeployment
    
    
    Vault Name                       : myvault
    Resource Group Name              : mycluster-keyvault
    Location                         : West US
    Resource ID                      : /subscriptions/<guid>/resourceGroups/mycluster-keyvault/providers/Microsoft.KeyVault/vaults/myvault
    Vault URI                        : https://myvault.vault.azure.net
    Tenant ID                        : <guid>
    SKU                              : Standard
    Enabled For Deployment?          : False
    Enabled For Template Deployment? : False
    Enabled For Disk Encryption?     : False
    Access Policies                  :
                                       Tenant ID                :    <guid>
                                       Object ID                :    <guid>
                                       Application ID           :
                                       Display Name             :    
                                       Permissions to Keys      :    get, create, delete, list, update, import, backup, restore
                                       Permissions to Secrets   :    all
    
    
    Tags                             :
```

Se você tiver um Cofre de Chaves existente, poderá habilitá-lo para implantação usando a CLI do Azure:

```cli
> azure login
> azure account set "your account"
> azure config mode arm 
> azure keyvault list
> azure keyvault set-policy --vault-name "your vault name" --enabled-for-deployment true
```

<a id="add-certificate-to-key-vault"></a>
## <a name="add-certificates-to-key-vault"></a>Adicionar certificados ao Cofre de Chaves

Os certificados são usados no Service Fabric para fornecer autenticação e criptografia para proteger vários aspectos de um cluster e de seus aplicativos. Para saber mais sobre como os certificados são usados no Service Fabric, veja [Cenários de segurança de cluster do Service Fabric][service-fabric-cluster-security].

### <a name="cluster-and-server-certificate-required"></a>Certificado de cluster e de servidor (necessário) 

Esse certificado é necessário para proteger um cluster e impedir o acesso não autorizado a ele. Ele fornece segurança de cluster de duas maneiras:
 
 - **Autenticação do cluster:** autentica a comunicação de nó para nó para a federação de cluster. Somente os nós que podem provar sua identidade com esse certificado podem ingressar no cluster.
 - **Autenticação de servidor:** autentica os pontos de extremidade de gerenciamento de cluster para um cliente de gerenciamento, para que o gerenciamento do cliente saiba que está se comunicando com o cluster real. Esse certificado também fornece SSL para a API de gerenciamento de HTTPS e para o Service Fabric Explorer sobre HTTPS.

Para servir a essas finalidades, o certificado deverá atender a estes requisitos:

 - O certificado deve conter uma chave privada.
 - O certificado deve ser criado para troca de chaves, exportável para um arquivo Troca de Informações Pessoais (.pfx).
 - O nome de assunto do certificado deve corresponder ao domínio usado para acessar o cluster do Service Fabric. Essa correspondência é necessária para fornecer SSL para pontos de extremidade de gerenciamento de HTTPS e Service Fabric Explorer do cluster. Você não pode obter um certificado SSL de uma autoridade de certificação (CA) para o domínio `.cloudapp.azure.com` . Você deve adquirir um nome de domínio personalizado para seu cluster. Quando você solicitar um certificado de uma AC, o nome da entidade do certificado deve corresponder ao nome de domínio personalizado usado para seu cluster.

### <a name="application-certificates-optional"></a>Certificados de aplicativo (opcionais)

Qualquer número de certificados adicionais pode ser instalado em um cluster para fins de segurança do aplicativo. Antes de criar o cluster, considere os cenários de segurança de aplicativos que exigem um certificado a ser instalado em nós, como:

 - Criptografia e descriptografia de valores de configuração de aplicativo
 - Criptografia de dados entre nós durante a replicação 

### <a name="formatting-certificates-for-azure-resource-provider-use"></a>Formatação de certificados para uso do provedor de recursos do Azure

Os arquivos de chave privada (.pfx) podem ser adicionados e usados diretamente por meio do Cofre de Chaves. No entanto, o provedor de recursos do Azure requer chaves para ser armazenado em um formato JSON especial que inclui o .pfx como uma cadeia de caracteres codificada em base 64 e a senha da chave privada. Para acomodar esses requisitos, as chaves deverão ser colocadas em uma cadeia de caracteres JSON e então armazenadas como *segredos* no Cofre de Chaves.

Para facilitar esse processo, um módulo do PowerShell está [disponível no GitHub][service-fabric-rp-helpers]. Siga estas etapas para usar o módulo:

  1. Baixe todo o conteúdo do repositório em um diretório local. 
  2. Importe o módulo na janela do PowerShell:

  ```powershell
  PS C:\Users\vturecek> Import-Module "C:\users\vturecek\Documents\ServiceFabricRPHelpers\ServiceFabricRPHelpers.psm1"
  ```
     
O comando `Invoke-AddCertToKeyVault` neste módulo do PowerShell formata automaticamente uma chave privada do certificado em uma cadeia de caracteres JSON e a carrega no Cofre de Chaves. Use-o para adicionar o certificado do cluster e todos os certificados adicionais de aplicativos para o Cofre de Chaves. Repita esta etapa para todos os certificados adicionais que deseja instalar em seu cluster.

```powershell
 Invoke-AddCertToKeyVault -SubscriptionId <guid> -ResourceGroupName mycluster-keyvault -Location "West US" -VaultName myvault -CertificateName mycert -Password "<password>" -UseExistingCertificate -ExistingPfxFilePath "C:\path\to\mycertkey.pfx"
    
    Switching context to SubscriptionId <guid>
    Ensuring ResourceGroup mycluster-keyvault in West US
    WARNING: The output object type of this cmdlet is going to be modified in a future release.
    Using existing valut myvault in West US
    Reading pfx file from C:\path\to\key.pfx
    Writing secret to myvault in vault myvault
    
    
Name  : CertificateThumbprint
Value : <value>

Name  : SourceVault
Value : /subscriptions/<guid>/resourceGroups/mycluster-keyvault/providers/Microsoft.KeyVault/vaults/myvault

Name  : CertificateURL
Value : https://myvault.vault.azure.net:443/secrets/mycert/4d087088df974e869f1c0978cb100e47

```

As cadeias de caracteres anteriores são todos os pré-requisitos do Cofre de Chaves para configurar um modelo do Resource Manager de cluster do Service Fabric que instala certificados para autenticação de nó, autenticação e segurança de ponto de extremidade de gerenciamento e recursos de segurança adicionais de aplicativos que usam certificados x.509. Neste ponto, agora você deve ter a seguinte configuração no Azure:

 - Grupo de recursos do Cofre de Chaves
   - Cofre da Chave
     - Certificado de autenticação de servidor de cluster
     - Certificados de aplicativo

## <a name="set-up-azure-active-directory-for-client-authentication"></a>Configurar o Azure Active Directory para autenticação de cliente

O AAD permite às organizações (conhecidas como locatários) gerenciarem o acesso de usuários a aplicativos, os quais são divididos em aplicativos com uma interface do usuário de logon baseada na Web e aplicativos com uma experiência de cliente nativo. Neste documento, partimos do pressuposto que você já tenha criado um locatário. Se não, comece lendo [Como obter um locatário do Azure Active Directory][active-directory-howto-tenant].

Os clusters do Service Fabric oferecem vários pontos de entrada para a funcionalidade de gerenciamento, incluindo o [Service Fabric Explorer][service-fabric-visualizing-your-cluster] e o [Visual Studio][service-fabric-manage-application-in-visual-studio] baseados na Web. Como resultado, você criará dois aplicativos do AAD para controlar o acesso ao cluster, um aplicativo Web e um aplicativo nativo.

Para simplificar algumas das etapas envolvidas na configuração do AAD com um cluster do Service Fabric, criamos um conjunto de scripts do Windows PowerShell.

>[AZURE.NOTE] Você deve executar essas etapas *antes* de criar o cluster, por isso em casos em que os scripts esperam nomes e pontos de extremidade de cluster, eles devem ser os valores planejados, não aqueles que você já criou.

1. [Baixar os scripts][IT-aad-ps-script-download] em seu computador.

2. Clique com o botão direito do mouse no arquivo zip, escolha **Properties**, marque a caixa de seleção **Unblock** e aplique.

3. Extraia o arquivo zip.

4. Execute `SetupApplications.ps1`, fornecendo TenantId, ClusterName e WebApplicationReplyUrl como parâmetros. Por exemplo:

    ```powershell
    .\SetupApplications.ps1 -TenantId '690ec069-8200-4068-9d01-5aaf188e557a' -ClusterName 'mycluster' -WebApplicationReplyUrl 'https://mycluster.westus.cloudapp.azure.com:19080/Explorer/index.html'
    ```

    Você pode encontrar a **TenantId** observando a URL do locatário no portal clássico do Azure. O GUID incorporado na URL é a TenantId. Por exemplo:

    https://<i></i>manage.windowsazure.com/microsoft.onmicrosoft.com#Workspaces/ActiveDirectoryExtension/Directory/**690ec069-8200-4068-9d01-5aaf188e557a**/users

    O **ClusterName** é usado para prefixar os aplicativos do AAD criados pelo script. Ele não precisa corresponder ao nome do cluster real exatamente, pois ele se destina apenas a facilitar o mapeamento dos artefatos do AAD para o cluster do Service Fabric com o qual eles estão sendo usados.

    A **WebApplicationReplyUrl** é o ponto de extremidade padrão que o AAD retorna aos seus usuários depois de concluir o processo de entrada. Você deve definir isso para o ponto de extremidade do Service Fabric Explorer para o seu cluster, que por padrão é:

    https://&lt;cluster_domain&gt;:19080/Explorer

    Você precisará entrar em uma conta que tenha privilégios administrativos para o locatário do AAD. Depois que você fizer isso, o script continuará a criar aplicativos Web e nativos para representar seu cluster do Service Fabric. Se você observar os aplicativos do locatário no [Portal Clássico do Azure][azure-classic-portal], você verá duas novas entradas:

    - *ClusterName*\_Cluster
    - *ClusterName*\_Cliente

    O script imprimirá o Json necessário para o modelo do Azure Resource Manager ao criar o cluster na próxima seção por isso mantenha a janela do PowerShell aberta.

```json
"azureActiveDirectory": {
  "tenantId":"<guid>",
  "clusterApplication":"<guid>",
  "clientApplication":"<guid>"
},
```

## <a name="create-a-service-fabric-cluster-resource-manager-template"></a>Criar um modelo do Resource Manager do cluster do Service Fabric

Nesta seção, a saída dos comandos anteriores do PowerShell será usada em um modelo do Resource Manager de um cluster do Service Fabric.

Os exemplos de modelo do Gerenciador de Recursos estão disponíveis na [Galeria de modelos de início rápido do Azure no GitHub][azure-quickstart-templates]. Esses modelos podem ser usados como ponto de partida para o modelo de cluster. 

### <a name="create-the-resource-manager-template"></a>Criar o modelo do Gerenciador de Recursos

Este guia usa o modelo de exemplo do [cluster seguro de 5 nós][service-fabric-secure-cluster-5-node-1-nodetype-wad] e seus parâmetros de modelo. Baixe `azuredeploy.json` e `azuredeploy.parameters.json` em seu computador e abra ambos os arquivos em seu editor de texto favorito.

### <a name="add-certificates"></a>Adicionar certificados

Os certificados são adicionados a um modelo do Resource Manager de cluster ao fazer referência ao Cofre de Chaves que contém as chaves de certificado. É recomendável que esses valores do Cofre de Chaves sejam colocados em um arquivo de parâmetros de modelo do Resource Manager para manter o arquivo de modelo do Resource Manager reutilizável e sem valores específicos de uma implantação.

#### <a name="add-all-certificates-to-the-vmss-osprofile"></a>Adicionar todos os certificados ao osProfile do VMSS

Todos os certificados que precisem ser instalados no cluster devem ser configurados na seção osProfile do recurso VMSS (Microsoft.Compute/virtualMachineScaleSets). Isso instrui o provedor de recursos para instalar o certificado nas máquinas virtuais. Isso inclui o certificado do cluster, bem como os certificados de segurança de aplicativo que você planeja usar para seus aplicativos:

```json
{
  "apiVersion": "2016-03-30",
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

#### <a name="configure-service-fabric-cluster-certificate"></a>Configurar o certificado do cluster do Service Fabric

O certificado de autenticação de cluster também deve ser configurado no recurso de cluster do Service Fabric (Microsoft.ServiceFabric/clusters) e na extensão do Service Fabric para VMSS no recurso VMSS. Isso permite que o provedor de recursos do Service Fabric o configure para autenticação do cluster e autenticação de servidor para pontos de extremidade de gerenciamento.

##### <a name="vmss-resource"></a>Recurso VMSS:

```json
{
  "apiVersion": "2016-03-30",
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

##### <a name="service-fabric-resource"></a>Recursos do Service Fabric:

```json
{
  "apiVersion": "2016-03-01",
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

### <a name="insert-aad-config"></a>Inserir configuração do AAD

A configuração do AAD criada anteriormente pode ser inserida diretamente em seu modelo do Resource Manager, mas é recomendável extrair os valores para parâmetros primeiro para um arquivo de parâmetros para manter o modelo do Resource Manager reutilizável e sem valores específicos de uma implantação.

```json
{
  "apiVersion": "2016-03-01",
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

### <a name="a-configurearm-aconfigure-resource-manager-template-parameters"></a><a "configure-arm" ></a>Configurar os parâmetros do modelo do Resource Manager

Por fim, use os valores de saída dos comandos do Cofre de Chaves e do PowerShell do AAD para preencher o arquivo de parâmetros:

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
Neste ponto, você agora deve ter o seguinte:

 - Grupo de recursos do Cofre de Chaves
    - Cofre da Chave
    - Certificado de autenticação de servidor de cluster
    - Certificado de codificação de dados
 - Locatário do Azure Active Directory 
    - Aplicativo AAD para gerenciamento baseado na Web e no Service Fabric Explorer
    - Aplicativo AAD para o gerenciamento de clientes nativos
    - Usuários com funções atribuídas 
 - Modelo do Resource Manager de cluster do Service Fabric
    - Certificados configurados por meio do Cofre de Chaves
    - Azure Active Directory configurado 

O diagrama a seguir ilustra onde a configuração do Cofre de Chaves e do AAD se encaixa ao seu modelo do Resource Manager.

![Mapa de dependências do Resource Manager][cluster-security-arm-dependency-map]

## <a name="create-the-cluster"></a>Criar o cluster

Agora você está pronto para criar o cluster usando a [implantação do ARM][resource-group-template-deploy].

#### <a name="test-it"></a>Testá-lo

Use o seguinte comando do PowerShell para testar o modelo do Resource Manager com um arquivo de parâmetros:

```powershell
Test-AzureRmResourceGroupDeployment -ResourceGroupName "myresourcegroup" -TemplateFile .\azuredeploy.json -TemplateParameterFile .\azuredeploy.parameters.json
```

#### <a name="deploy-it"></a>Implantá-lo

Se o modelo do Resource Manager passar no teste, use o comando do PowerShell a seguir para implantar seu modelo do Resource Manager com um arquivo de parâmetros:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName "myresourcegroup" -TemplateFile .\azuredeploy.json -TemplateParameterFile .\azuredeploy.parameters.json
```

<a name="assign-roles"></a>
## <a name="assign-users-to-roles"></a>Atribuir usuários a funções

Depois de criar os aplicativos para representar seu cluster, você precisará atribuir os usuários às funções com suporte no Service Fabric: somente leitura e administrador. Você pode fazer isso usando o [Portal Clássico do Azure][azure-classic-portal].

1. Navegue até seu locatário e escolha Aplicativos.
2. Escolha o aplicativo Web, que tem um nome como `myTestCluster_Cluster`.
3. Clique na guia Usuários.
4. Escolha um usuário para atribuir e clique no botão **Atribuir** na parte inferior da tela.

    ![Botão Atribuir usuários a funções][assign-users-to-roles-button]

5. Selecione a função para atribuir ao usuário.

    ![Atribuir usuários a funções][assign-users-to-roles-dialog]

>[AZURE.NOTE] Para saber mais sobre as funções no Service Fabric, veja [Controle de acesso baseado em função para clientes do Service Fabric](service-fabric-cluster-security-roles.md).

 <a name="secure-linux-cluster"></a> 
##  <a name="create-secure-clusters-on-linux"></a>Criar clusters seguros no Linux

Para facilitar o processo, foi fornecido um script auxiliar [aqui](http://github.com/ChackDan/Service-Fabric/tree/master/Scripts/CertUpload4Linux). Para usar esse script auxiliar, supõe-se que você já tenha a CLI do Azure instalada e ela está em seu caminho. Certifique-se de que o script tenha permissões para execução ao executar `chmod +x cert_helper.py` após fazer o download. A primeira etapa é fazer logon na sua conta do Azure usando a CLI com o comando `azure login` . Depois de fazer logon em sua conta do Azure, use o auxiliar com seu certificado de AC assinado, como mostra o seguinte comando:

```sh
./cert_helper.py [-h] CERT_TYPE [-ifile INPUT_CERT_FILE] [-sub SUBSCRIPTION_ID] [-rgname RESOURCE_GROUP_NAME] [-kv KEY_VAULT_NAME] [-sname CERTIFICATE_NAME] [-l LOCATION] [-p PASSWORD]

The -ifile parameter can take a .pfx or a .pem file as input, with the certificate type (pfx or pem, or ss if it is a self-signed cert).
The parameter -h prints out the help text.
```

Esse comando retorna as três cadeias de caracteres a seguir como a saída: 

1. Um SourceVaultID, que é a ID para o novo KeyVault ResourceGroup criado para você. 

2. Um CertificateUrl para acessar o certificado.

3. Um CertificateThumbprint, que é usado para autenticação.


O exemplo a seguir mostra como usar o comando:

```sh
./cert_helper.py pfx -sub "fffffff-ffff-ffff-ffff-ffffffffffff"  -rgname "mykvrg" -kv "mykevname" -ifile "/home/test/cert.pfx" -sname "mycert" -l "East US" -p "pfxtest"
```
A execução do comando anterior fornece três cadeias de caracteres da seguinte maneira:

```sh
SourceVault: /subscriptions/fffffff-ffff-ffff-ffff-ffffffffffff/resourceGroups/mykvrg/providers/Microsoft.KeyVault/vaults/mykvname
CertificateUrl: https://myvault.vault.azure.net/secrets/mycert/00000000000000000000000000000000
CertificateThumbprint: 0xfffffffffffffffffffffffffffffffffffffffff
```

 O nome de assunto do certificado deve corresponder ao domínio usado para acessar o cluster do Service Fabric. Isso é necessário para fornecer SSL para pontos de extremidade de gerenciamento de HTTPS e Service Fabric Explorer do cluster. Você não pode obter um certificado SSL de uma autoridade de certificação (CA) para o domínio `.cloudapp.azure.com` . Você deve adquirir um nome de domínio personalizado para seu cluster. Quando você solicitar um certificado de uma autoridade de certificação, o nome de assunto do certificado deve corresponder ao nome de domínio personalizado usado para seu cluster.

Essas são as entradas necessárias para criar um cluster do Service Fabric seguro (sem AAD), conforme descrito em [Configurar parâmetros de modelo do Resource Manager](#configure-arm). Você pode se conectar ao cluster seguro por meio de instruções em [autenticando o acesso de cliente a um cluster](service-fabric-connect-to-secure-cluster.md). Os clusters de preview do Linux não dão suporte à autenticação AAD. Você pode atribuir funções de administrador e cliente, conforme descrito na seção [Atribuir funções a usuários](#assign-roles). Ao especificar funções de administrador e cliente para um cluster de preview do Linux, você precisa fornecer as impressões digitais de certificado para autenticação (em vez do nome do assunto, desde que nenhuma validação de cadeia ou revogação esteja sendo executada nesta versão de preview).


Se você quiser usar um certificado autoassinado para testes, poderá usar o mesmo script para gerar um certificado autoassinado e carregá-lo para o KeyVault, fornecendo o sinalizador `ss` em vez de fornecer o caminho e o nome do certificado. Por exemplo, consulte o seguinte comando para criar e carregar um certificado autoassinado:

```sh
./cert_helper.py ss -rgname "mykvrg" -sub "fffffff-ffff-ffff-ffff-ffffffffffff" -kv "mykevname"   -sname "mycert" -l "East US" -p "selftest" -subj "mytest.eastus.cloudapp.net" 
```

Esse comando retorna as mesmas três cadeias de caracteres, SourceVault, CertificateUrl e CertificateThumbprint, usadas para criar um cluster do Linux seguro, juntamente com a localização onde o certificado autoassinado foi colocado. Você precisará do certificado autoassinado para se conectar ao cluster.  Você pode se conectar ao cluster seguro por meio de instruções em [autenticando o acesso de cliente a um cluster](service-fabric-connect-to-secure-cluster.md). O nome de assunto do certificado deve corresponder ao domínio usado para acessar o cluster do Service Fabric. Isso é necessário para fornecer SSL para pontos de extremidade de gerenciamento de HTTPS e Service Fabric Explorer do cluster. Você não pode obter um certificado SSL de uma autoridade de certificação (CA) para o domínio `.cloudapp.azure.com` . Você deve adquirir um nome de domínio personalizado para seu cluster. Quando você solicitar um certificado de uma autoridade de certificação, o nome de assunto do certificado deve corresponder ao nome de domínio personalizado usado para seu cluster.

Os parâmetros fornecidos pelo script auxiliar podem ser preenchidos no portal, conforme descrito na seção [Criar um cluster no portal do Azure](service-fabric-cluster-creation-via-portal.md#create-cluster-portal).

## <a name="next-steps"></a>Próximas etapas

Neste ponto, você tem um cluster seguro com o Azure Active Directory fornecendo autenticação de gerenciamento. Em seguida, [conecte-se ao cluster](service-fabric-connect-to-secure-cluster.md) e saiba como [gerenciar segredos do aplicativo](service-fabric-application-secret-management.md).

## <a name="troubleshoot-setting-up-azure-active-directory-for-client-authentication"></a>Solucionar problemas na configuração do Azure Active Directory para autenticação de cliente

Se você encontrar um problema durante a configuração do Azure Active Directory para autenticação de cliente, examine as sugestões a seguir para obter as soluções possíveis.

### <a name="service-fabric-explorer-prompts-for-selecting-certificate"></a>O Service Fabric Explorer solicita a seleção de certificado

#### <a name="problem"></a>Problema

Após fazer logon com êxito na página de logon do AAD no Service Fabric Explorer, o navegador retorna à home page, mas exibe uma caixa de diálogo solicitando a seleção de um certificado.

![Caixa de diálogo de seleção de certificado SFX][sfx-select-certificate-dialog]

#### <a name="reason"></a>Motivo

Nenhuma função foi atribuída ao usuário no aplicativo de cluster do AAD. Assim, autenticação do AAD falha no cluster do Service Fabric. O Service Fabric Explorer reverterá para autenticação de certificado.

#### <a name="solution"></a>Solução

Siga as instruções para configuração do AAD e atribuição de funções de usuário. Além disso, é recomendado que "ATRIBUIÇÃO DE USUÁRIO NECESSÁRIA PARA ACESSO do aplicativo" esteja ativado quando `SetupApplications.ps1` também estiver.

### <a name="connect-with-powershell-fails-with-error-the-specified-credentials-are-invalid"></a>Falha ao conectar-se ao PowerShell com o erro: as credenciais especificadas são inválidas

#### <a name="problem"></a>Problema

Ao usar o PowerShell para se conectar ao cluster usando o modo de segurança "AzureActiveDirectory", após fazer logon com êxito na página de logon do AAD, a conexão falhará mostrando o erro: as credenciais especificadas são inválidas.

#### <a name="solution"></a>Solução

Mesmo que acima.

### <a name="service-fabric-explorer-signing-in-return-failure-aadsts50011"></a>Entrar no Service Fabric Explorer retorna a falha: AADSTS50011

#### <a name="problem"></a>Problema

Após o logon na página de entrada do AAD no Service Fabric Explorer, a página retorna a falha de entrada – AADSTS50011: A &lt;URL&gt; do endereço de resposta não coincide com os endereços de resposta configurados para o aplicativo: &lt;GUID&gt;. 

![O endereço de resposta SFX não corresponde][sfx-reply-address-not-match]

#### <a name="reason"></a>Motivo

O aplicativo do cluster (Web) que representa o Service Fabric Explorer tenta se autenticar no AAD como parte da solicitação que ele fornece à URL de retorno de redirecionamento. No entanto, ele não está relacionado na lista de 'URL de RESPOSTA' do aplicativo do AAD.

#### <a name="solution"></a>Solução

Adicione a URL do Service Fabric Explorer para 'URL DE RESPOSTA' na guia Configurar do aplicativo do cluster (Web) ou substitua um dos itens na lista. Em seguida, salve.

![URL de resposta do aplicativo Web][web-application-reply-url]

### <a name="can-i-reuse-the-same-aad-tenant-for-multiple-clusters"></a>Posso reutilizar o mesmo locatário do AAD para vários clusters?

#### <a name="answer"></a>Resposta

Sim. Mas lembre-se de adicionar a URL do Service Fabric Explorer a seu aplicativo do cluster (Web), caso contrário o Service Fabric Explorer não funcionará.

### <a name="why-do-i-still-need-server-certificate-while-aad-enabled"></a>Por que eu ainda preciso do certificado do servidor enquanto o AAD está habilitado?

#### <a name="answer"></a>Resposta

FabricClient e FabricGateway realizam autenticação mútua. No caso da autenticação do AAD, a integração do AAD fornece a identidade do cliente ao servidor e o certificado do servidor é usado para verificar a identidade do servidor. Para obter mais informações sobre o funcionamento do certificado no Service Fabric, verifique [Certificados X.509 e Service Fabric][x509-certificates-and-service-fabric]

<!-- Links -->
[azure-powershell]:https://azure.microsoft.com/documentation/articles/powershell-install-configure/
[key-vault-get-started]:../key-vault/key-vault-get-started.md
[aad-graph-api-docs]:https://msdn.microsoft.com/library/azure/ad/graph/api/api-catalog
[azure-classic-portal]: https://manage.windowsazure.com
[service-fabric-rp-helpers]: https://github.com/ChackDan/Service-Fabric/tree/master/Scripts/ServiceFabricRPHelpers
[service-fabric-cluster-security]: service-fabric-cluster-security.md
[active-directory-howto-tenant]: ../active-directory/active-directory-howto-tenant.md
[service-fabric-visualizing-your-cluster]: service-fabric-visualizing-your-cluster.md
[service-fabric-manage-application-in-visual-studio]: service-fabric-manage-application-in-visual-studio.md
[sf-aad-ps-script-download]:http://servicefabricsdkstorage.blob.core.windows.net/publicrelease/MicrosoftAzureServiceFabric-AADHelpers.zip
[azure-quickstart-templates]: https://github.com/Azure/azure-quickstart-templates
[service-fabric-secure-cluster-5-node-1-nodetype-wad]: https://github.com/Azure/azure-quickstart-templates/blob/master/service-fabric-secure-cluster-5-node-1-nodetype-wad/
[resource-group-template-deploy]: https://azure.microsoft.com/documentation/articles/resource-group-template-deploy/
[x509-certificates-and-service-fabric]: service-fabric-cluster-security.md#x509-certificates-and-service-fabric

<!-- Images -->
[cluster-security-arm-dependency-map]: ./media/service-fabric-cluster-creation-via-arm/cluster-security-arm-dependency-map.png
[cluster-security-cert-installation]: ./media/service-fabric-cluster-creation-via-arm/cluster-security-cert-installation.png
[assign-users-to-roles-button]: ./media/service-fabric-cluster-creation-via-arm/assign-users-to-roles-button.png
[assign-users-to-roles-dialog]: ./media/service-fabric-cluster-creation-via-arm/assign-users-to-roles.png
[sfx-select-certificate-dialog]: ./media/service-fabric-cluster-creation-via-arm/sfx-select-certificate-dialog.png
[sfx-reply-address-not-match]: ./media/service-fabric-cluster-creation-via-arm/sfx-reply-address-not-match.png
[web-application-reply-url]: ./media/service-fabric-cluster-creation-via-arm/web-application-reply-url.png


<!--HONumber=Oct16_HO2-->


