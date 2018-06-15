---
title: Implantar um cluster do Service Fabric protegido na pilha do Azure | Microsoft Docs
description: Saiba como implantar um cluster do Service Fabric protegido na pilha do Azure
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/08/2018
ms.author: mattbriggs
ms.reviewer: shnatara
ms.openlocfilehash: a88d8dd2af94ac796a3b2e3c667fd40a308f02a1
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/08/2018
ms.locfileid: "33883018"
---
# <a name="deploy-a-service-fabric-cluster-in-azure-stack"></a>Implantar um cluster do Service Fabric na pilha do Azure

Use o **Cluster do Service Fabric** item do Marketplace do Azure para implantar um cluster do Service Fabric protegido na pilha do Azure. 

Para obter mais informações sobre como trabalhar com o Service Fabric, consulte [visão geral do Azure Service Frabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-overview) e [cenários de segurança de cluster do Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security), na documentação do Azure.

## <a name="prerequisites"></a>Pré-requisitos

A seguir é necessárias para implantar o cluster do Service Fabric:
1. **Certificado de cluster**  
   Esse é o certificado de servidor de x. 509 que você adicionar a KeyVault durante a implantação do Service Fabric. 
   - O **CN** sobre esse certificado deve corresponder o nome de domínio totalmente qualificado (FQDN) do cluster do Service Fabric você criar. 
   - O formato do certificado deve ser PFX, como as chaves públicas e privadas são necessárias. 
   Consulte [requisitos](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security) para criar esse certificado do servidor.

    > [!NOTE]  
    > Você pode usar um local do certificado autoassinado do certificado do servidor x. 509 para fins de teste. Os certificados autoassinados não precisa corresponder ao FQDN do cluster.

2.  **Certificado de cliente administrador** esse é o certificado que o cliente usará para autenticar para o cluster do Service Fabric, que pode ser autoassinado. Consulte [requisitos](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security) para criar esse certificado de cliente.

3.  **Os itens a seguir devem estar disponíveis no Azure Marketplace de pilha:**
     - **Windows Server 2016** – o modelo usa a imagem do Windows Server 2016 para criar o cluster.  
     - **Extensão do Script de cliente** -extensão da máquina Virtual da Microsoft.  
     - **Configuração do estágio desejado do PowerShell** -extensão da máquina Virtual da Microsoft.


## <a name="add-a-secret-to-key-vault"></a>Adicionar um segredo ao Key Vault
Para implantar um cluster do Service Fabric, você deve especificar o KeyVault correto *identificador de segredo* ou a URL para o cluster do Service Fabric. O modelo do Gerenciador de recursos do Azure usa um KeyVault como entrada e, em seguida, recupera o certificado de Cluster durante a instalação do cluster do Service Fabric. 

> [!IMPORTANT]  
> Você deve usar o PowerShell para adicionar um segredo para KeyVault para uso com o Service Fabric. Não use o portal.  

Use o script a seguir para criar o KeyVault e adicionar o *certificado de cluster* a ele. (Consulte o [pré-requisitos](#prerequisites).) Antes de executar o script, revise o script de exemplo e atualize os parâmetros indicados de acordo com seu ambiente. Esse script também mostrará os valores que você precisa fornecer para o modelo do Gerenciador de recursos do Azure. 

> [!TIP]  
> Antes do script pode ter êxito, deve haver uma oferta pública que inclui os serviços de computação, rede, armazenamento e KeyVault. 

  ```PowerShell
    function Get-ThumbprintFromPfx($PfxFilePath, $Password) 
        {
            return New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($PfxFilePath, $Password)
        }
    
    function Publish-SecretToKeyVault ($PfxFilePath, $Password, $KeyVaultName)
       {
            $keyVaultSecretName = "ClusterCertificate"
            $certContentInBytes = [io.file]::ReadAllBytes($PfxFilePath)
            $pfxAsBase64EncodedString = [System.Convert]::ToBase64String($certContentInBytes)
    
            $jsonObject = ConvertTo-Json -Depth 10 ([pscustomobject]@{
                data     = $pfxAsBase64EncodedString
                dataType = 'pfx'
                password = $Password
            })
    
            $jsonObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
            $jsonEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)
            $secret = ConvertTo-SecureString -String $jsonEncoded -AsPlainText -Force
            $keyVaultSecret = Set-AzureKeyVaultSecret -VaultName $KeyVaultName -Name $keyVaultSecretName -SecretValue $secret
            
            $pfxCertObject = Get-ThumbprintFromPfx -PfxFilePath $PfxFilePath -Password $Password
    
            Write-Host "KeyVault id: " -ForegroundColor Green
            (Get-AzureRmKeyVault -VaultName $KeyVaultName).ResourceId
            
            Write-Host "Secret Id: " -ForegroundColor Green
            (Get-AzureKeyVaultSecret -VaultName $KeyVaultName -Name $keyVaultSecretName).id
    
            Write-Host "Cluster Certificate Thumbprint: " -ForegroundColor Green
            $pfxCertObject.Thumbprint
       }
    
    #========================== CHANGE THESE VALUES ===============================
    $armEndpoint = "https://management.local.azurestack.external"
    $tenantId = "your_tenant_ID"
    $location = "local"
    $clusterCertPfxPath = "Your_path_to_ClusterCert.pfx"
    $clusterCertPfxPassword = "Your_password_for_ClusterCert.pfx"
    #==============================================================================
    
    Add-AzureRmEnvironment -Name AzureStack -ARMEndpoint $armEndpoint
    Login-AzureRmAccount -Environment AzureStack -TenantId $tenantId
    
    $rgName = "sfvaultrg"
    Write-Host "Creating Resource Group..." -ForegroundColor Yellow
    New-AzureRmResourceGroup -Name $rgName -Location $location
    
    Write-Host "Creating Key Vault..." -ForegroundColor Yellow
    $Vault = New-AzureRmKeyVault -VaultName sfvault -ResourceGroupName $rgName -Location $location -EnabledForTemplateDeployment -EnabledForDeployment -EnabledForDiskEncryption
    
    Write-Host "Publishing certificate to Vault..." -ForegroundColor Yellow
    Publish-SecretToKeyVault -PfxFilePath $clusterCertPfxPath -Password $clusterCertPfxPassword -KeyVaultName $vault.VaultName
   ``` 


Para obter mais informações, consulte [KeyVault gerenciar na pilha do Azure com o PowerShell](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-kv-manage-powershell).

## <a name="deploy-the-marketplace-item"></a>Implantar o item do Marketplace

1. No portal do usuário, vá para **novo** > **de computação** > **Cluster do Service Fabric**. 

   ![Selecione o Cluster do Service Fabric](./media/azure-stack-solution-template-service-fabric-cluster/image2.png)

2. Para cada página, como *Noções básicas de*, preencha o formulário de implantação. Use padrões se você não tiver certeza de que um valor. Selecione **Okey** para ir para a próxima página:

   ![Noções básicas](media/azure-stack-solution-template-service-fabric-cluster/image3.png)

3. Sobre o *as configurações de rede* página, você pode especificar portas específicas a serem abertas para seus aplicativos:

   ![Configurações de Rede](media/azure-stack-solution-template-service-fabric-cluster/image4.png)

4. No *segurança* página, adicione os valores que você obteve [criando o Azure KeyVault](#add-a-secret-to-key-vault) e carregando o segredo.

   Para o *impressão digital do certificado de cliente administrador*, insira a impressão digital do *certificado de cliente de administrador*. (Consulte o [pré-requisitos](#prerequisites).)
   
   - Cofre de chaves de origem: Especificar toda *keyVault id* cadeia de caracteres dos resultados do script. 
   - URL de certificado de cluster: Especificar a URL inteira no *segredo Id* dos resultados do script. 
   - Impressão digital do certificado de cluster: especifique o *impressão digital do certificado de Cluster* dos resultados do script.
   - Impressões digitais de certificado de cliente do administrador: Especifique o *impressão digital do certificado de cliente administrador* você criou nos pré-requisitos. 

   ![Saída do script](media/azure-stack-solution-template-service-fabric-cluster/image5.png)

   ![Segurança](media/azure-stack-solution-template-service-fabric-cluster/image6.png)

5. Conclua o assistente e, em seguida, selecione **criar** para implantar o Cluster do Service Fabric.



## <a name="access-the-service-fabric-cluster"></a>Acessar o Cluster do Service Fabric
Você pode acessar o cluster do Service Fabric usando o Service Fabric Explorer ou o PowerShell de malha do serviço.


### <a name="use-service-fabric-explorer"></a>Use o Gerenciador do Service Fabric
1.  Valide que o navegador da Web tem acesso ao seu certificado de cliente do administrador e pode se autenticar para o cluster do Service Fabric.  

    a. Abra o Internet Explorer e vá para **opções da Internet** > **conteúdo** > **certificados**.
  
    b. Em certificados, selecione **importação** para iniciar o *Assistente de importação de certificado*e, em seguida, clique em **próximo**. No *arquivo a ser importado* página clique **procurar**e selecione o **certificado do cliente administrador** fornecido para o modelo do Gerenciador de recursos do Azure.
        
       > [!NOTE]  
       > Este certificado não é o certificado de Cluster que já foi adicionado ao KeyVault.  

    c. Certifique-se de que você tenha "Troca de informações pessoais" selecionada no menu suspenso de extensão da janela do Explorador de arquivos.  

       ![Troca de informações pessoais](media/azure-stack-solution-template-service-fabric-cluster/image8.png)  

    d. Sobre o *repositório de certificados* página, selecione **pessoal**e, em seguida, conclua o assistente.  
       ![repositório de certificados](media/azure-stack-solution-template-service-fabric-cluster/image9.png)  
2. Para localizar o FQDN do cluster do Service Fabric:  

    a. Vá para o grupo de recursos que está associado com a malha do serviço de cluster e localize o *endereço IP público* recursos. Selecione o objeto associado ao endereço IP público para abrir o *endereço IP público* folha.  

      ![Endereço IP público](media/azure-stack-solution-template-service-fabric-cluster/image10.png)   

    b. Na folha de endereço IP público, o FQDN exibe como *nome DNS*.  

      ![Nome DNS](media/azure-stack-solution-template-service-fabric-cluster/image11.png)  

3. Para localizar a URL para o Service Fabric Explorer e o ponto de extremidade de conexão de cliente, examine os resultados da implantação do modelo.

4. Em seu navegador, vá para https://*FQDN*: 19080. Substituir *FQDN* com o FQDN do cluster do Service Fabric da etapa 2.   
   Se você tiver usado um certificado autoassinado, você receberá um aviso de que a conexão não é segura. Para prosseguir para o site da web, selecione **mais informações sobre**e, em seguida, **vá para a página da Web**. 

5. Para autenticar no site, você deve selecionar um certificado para uso. Selecione **mais opções**, selecione o certificado apropriado e, em seguida, clique em **Okey** para se conectar ao Gerenciador do Service Fabric. 

   ![Autenticar](media/azure-stack-solution-template-service-fabric-cluster/image14.png)



## <a name="use-service-fabric-powershell"></a>Usar o PowerShell do Service Fabric

1. Instalar o *SDK do Microsoft Azure Service Fabric* de [preparar o ambiente de desenvolvimento no Windows](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-get-started#install-the-sdk-and-tools) na documentação do Azure Service Fabric.  

2. Depois que a instalação for concluída, configure as variáveis de ambiente do sistema para garantir que os cmdlets do Service Fabric estejam acessíveis do PowerShell.  
    
    a. Vá para **painel de controle** > **sistema e segurança** > **sistema**e, em seguida, selecione **configurações avançadas do sistema**.  
    
      ![Painel de controle](media/azure-stack-solution-template-service-fabric-cluster/image15.png) 

    b. Sobre o **avançado** guia de *propriedades do sistema*, selecione **variáveis de ambiente**.  

    c. Para *variáveis de sistema*, editar **caminho** e certifique-se de que **c:\\arquivos de programa\\Service Fabric do Microsoft\\bin\\malha \\Fabric.Code** está no topo da lista de variáveis de ambiente.  

      ![Lista de variáveis de ambiente](media/azure-stack-solution-template-service-fabric-cluster/image16.png)

3. Depois de alterar a ordem das variáveis de ambiente, reiniciar o PowerShell e, em seguida, execute o seguinte script do PowerShell para acessar o cluster do Service Fabric:

   ````PowerShell  
    Connect-ServiceFabricCluster -ConnectionEndpoint "\[Service Fabric
    CLUSTER FQDN\]:19000" \`

    -X509Credential -ServerCertThumbprint
    761A0D17B030723A37AA2E08225CD7EA8BE9F86A \`

    -FindType FindByThumbprint -FindValue
    0272251171BA32CEC7938A65B8A6A553AA2D3283 \`

    -StoreLocation CurrentUser -StoreName My -Verbose
   ````
   
   > [!NOTE]  
   > Não há nenhum *https://* antes do nome do cluster no script. Porta 19000 é necessária.
 
