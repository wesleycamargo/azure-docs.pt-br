---
title: Implantar Kubernetes no Azure Stack usando o Active Directory Federated Services (AD FS) | Microsoft Docs
description: Saiba como implantar o Kubernetes no Azure Stack usando o Active Directory Federated Services (AD FS).
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/05/2019
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 01/16/2019
ms.openlocfilehash: df84562c3ff95ac6fef65ea7c9911d5e12e558ef
ms.sourcegitcommit: 947b331c4d03f79adcb45f74d275ac160c4a2e83
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/05/2019
ms.locfileid: "55744956"
---
# <a name="deploy-kubernetes-to-azure-stack-using-active-directory-federated-services"></a>Implantar Kubernetes no Azure Stack usando o Active Directory Federated Services

*Aplica-se a: Integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

> [!Note]  
> Kubernetes no Azure Stack está em visualização. Cenário desconectado da pilha do Azure não é suportado pelo preview.

Você pode seguir as etapas neste artigo para implantar e configurar os recursos para o Kubernetes. Siga estas etapas ao Active Directory Federated Services (AD FS) é o serviço de gerenciamento de identidade.

## <a name="prerequisites"></a>Pré-requisitos 

Para começar, verifique se você tiver as permissões corretas e que o Azure Stack está pronto.

1. Gere um público e privado par de chaves SSH para entrar na VM do Linux no Azure Stack. É necessário a chave pública ao criar o cluster.

    Para obter instruções sobre como gerar uma chave, consulte [geração de chave SSH](https://github.com/msazurestackworkloads/acs-engine/blob/master/docs/ssh.md#ssh-key-generation).

1. Verifique se você tiver uma assinatura válida no seu portal de locatário do Azure Stack e que você tem suficiente IP público endereços disponíveis para adicionar novos aplicativos.

    O cluster não pode ser implantado para Azure Stack **administrador** assinatura. Você deve usar um **usuário** assinatura. 

1. Se você não tiver um Kubernetes Cluster no seu marketplace, contate o administrador do Azure Stack.

## <a name="create-a-service-principal"></a>Criar uma entidade de serviço

Você precisará trabalhar com o administrador do Azure Stack para configurar a entidade de serviço ao usar o AD FS como sua solução de identidade. A entidade de serviço fornece o aplicativo tenha acesso aos recursos do Azure Stack.

1. O administrador do Azure Stack fornece um certificado e as informações para a entidade de serviço. Essa informação deve parecer com:

    ```Text  
        ApplicationIdentifier : S-1-5-21-1512385356-3796245103-1243299919-1356
        ClientId              : 3c87e710-9f91-420b-b009-31fa9e430145
        Thumbprint            : 30202C11BE6864437B64CE36C8D988442082A0F1
        ApplicationName       : Azurestack-MyApp-c30febe7-1311-4fd8-9077-3d869db28342
        PSComputerName        : azs-ercs01
        RunspaceId            : a78c76bb-8cae-4db4-a45a-c1420613e01b
    ```

2. Atribua uma função como um colaborador de sua nova entidade de serviço para sua assinatura. Para obter instruções, consulte [atribuir uma função](https://docs.microsoft.com/azure/azure-stack/azure-stack-create-service-principals#assign-role-to-service-principal#assign-role-to-service-principal).

3. Crie um cofre de chaves para armazenar o certificado para a implantação.

    - Você precisa que as seguintes partes de informações:

        | Valor | DESCRIÇÃO |
        | ---   | ---         |
        | Ponto de extremidade do Azure Resource Manager | O Gerenciador de recursos do Microsoft Azure é uma estrutura de gerenciamento que permite aos administradores implantar, gerenciar e monitorar recursos do Azure. O Azure Resource Manager pode lidar com essas tarefas, como um grupo, em vez de individualmente, em uma única operação.<br>O ponto de extremidade no Azure Stack desenvolvimento ASDK (Kit) é: `https://management.local.azurestack.external/`<br>É o ponto de extremidade em sistemas integrados: `https://management.<location>.ext-<machine-name>.masd.stbtest.microsoft.com/` |
        | Sua ID de assinatura | O [ID da assinatura](https://docs.microsoft.com/azure/azure-stack/azure-stack-plan-offer-quota-overview#subscriptions) é como você pode acessar ofertas no Azure Stack. |
        | Seu nome de usuário | Seu nome de usuário. |
        | O nome do grupo de recursos  | O nome de um novo grupo de recursos ou selecione um grupo de recursos. O nome do recurso precisa ser alfanuméricos e minúsculos. |
        | Nome do Keyvault | Nome do cofre.<br> Padrão de Regex: `^[a-zA-Z0-9-]{3,24}$` |
        | Localização do grupo de recursos | O local do grupo de recursos. Essa é a região que você escolhe para sua instalação do Azure Stack. |

    - Abra o PowerShell com um prompt com privilégios elevados. Execute o script a seguir com os parâmetros atualizados para seus valores:

    ```PowerShell  
        $armEndpoint="<Azure Resource Manager Endpoint>"
        $subscriptionId="<Your Subscription ID>"
        $username="<your user name >"
        $resource_group_name = "<the resource group name >"
        $key_vault_name = "<keyvault name>"
        $resource_group_location="<resource group location>"
        
        # Login Azure Stack Environment
        Add-AzureRmEnvironment -ARMEndpoint $armEndpoint -Name t
        $mycreds = Get-Credential
        Login-AzureRmAccount -Credential $mycreds -Environment t -Subscription $subscriptionId
        
        # Create new Resource group and key vault
        New-AzureRmResourceGroup -Name $resource_group_name -Location $resource_group_location -Force
        
        # Note, Do not omit -EnabledForTemplateDeployment flag
        New-AzureRmKeyVault -VaultName $key_vault_name -ResourceGroupName $resource_group_name -Location $resource_group_location -EnabledForTemplateDeployment
        
        # Obtain the security identifier(SID) of the active directory user
        $adUser = Get-ADUser -Filter "Name -eq '$username'" -Credential $mycreds
        $objectSID = $adUser.SID.Value
        # Set the key vault access policy
        Set-AzureRmKeyVaultAccessPolicy -VaultName $key_vault_name -ResourceGroupName $resource_group_name -ObjectId $objectSID -BypassObjectIdValidation -PermissionsToKeys all -PermissionsToSecrets all
    ```

4. Carregue seu certificado para o Cofre de chaves.

    - Você precisa que as seguintes partes de informações:

        | Valor | DESCRIÇÃO |
        | ---   | ---         |
        | Caminho do certificado | O caminho de arquivo ou FQDN para o certificado. |
        | Senha de certificado | A senha do certificado. |
        | Nome do segredo | O segredo é produzida na etapa anterior. |
        | Nome do Keyvault | O nome do Cofre de chaves criado na etapa anterior. |
        | Ponto de extremidade do Azure Resource Manager | O ponto de extremidade no Azure Stack desenvolvimento ASDK (Kit) é: `https://management.local.azurestack.external/`<br>É o ponto de extremidade em sistemas integrados: `https://management.<location>.ext-<machine-name>.masd.stbtest.microsoft.com/` |
        | Sua ID de assinatura | O [ID da assinatura](https://docs.microsoft.com/azure/azure-stack/azure-stack-plan-offer-quota-overview#subscriptions) é como você pode acessar ofertas no Azure Stack. |

    - Abra o PowerShell com um prompt com privilégios elevados. Execute o script a seguir com os parâmetros atualizados para seus valores:

    ```PowerShell  
        
    # upload the pfx to key vault
    $tempPFXFilePath = "<certificate path>"
    $password = "<certificate password>"
    $keyVaultSecretName = "<secret name>"
    $keyVaultName = "<keyvault name>"
    $armEndpoint="<Azure Resource Manager Endpoint>"
    $subscriptionId="<Your Subscription ID>"
    # Login Azure Stack Environment
    Add-AzureRmEnvironment -ARMEndpoint $armEndpoint -Name t
    $mycreds = Get-Credential
    Login-AzureRmAccount -Credential $mycreds -Environment t -Subscription $subscriptionId
    
    $certContentInBytes = [io.file]::ReadAllBytes($tempPFXFilePath)
    $pfxAsBase64EncodedString = [System.Convert]::ToBase64String($certContentInBytes)
    $jsonObject = @"
    {
    "data": "$pfxAsBase64EncodedString",
    "dataType" :"pfx",
    "password": "$password"
    }
    "@
    $jsonObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
    $jsonEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)
    $secret = ConvertTo-SecureString -String $jsonEncoded -AsPlainText -Force
    $keyVaultSecret = Set-AzureKeyVaultSecret -VaultName $keyVaultName -Name $keyVaultSecretName -SecretValue $secret 
    ```

## <a name="deploy-kubernetes"></a>Implantar Kubernetes

1. Abra o [portal do Azure Stack](https://portal.local.azurestack.external).

1. Selecione **+ criar um recurso** > **computação** > **Kubernetes Cluster**. Clique em **Criar**.

    ![Modelo de Solução de Implementação](media/azure-stack-solution-template-kubernetes-deploy/01_kub_market_item.png)

### <a name="1-basics"></a>1. Noções básicas

1. Selecione **Noções básicas de** em criar Cluster Kubernetes.

    ![Modelo de Solução de Implementação](media/azure-stack-solution-template-kubernetes-deploy/02_kub_config_basic.png)

1. Selecione suas **assinatura** ID.

1. Insira o nome de um novo grupo de recursos ou selecione um grupo de recursos. O nome do recurso precisa ser alfanuméricos e minúsculos.

1. Selecione o **local** do grupo de recursos. Essa é a região que você escolhe para sua instalação do Azure Stack.

### <a name="2-kubernetes-cluster-settings"></a>2. Configurações de Cluster do Kubernetes

1. Selecione **configurações de Cluster do Kubernetes** em criar Cluster Kubernetes.

    ![Modelo de Solução de Implementação](media/azure-stack-solution-template-kubernetes-deploy/03_kub_config_settings-adfs.png)

1. Insira o **o nome de usuário de administrador VM Linux**. Nome de usuário para as máquinas virtuais de Linux que fazem parte do cluster Kubernetes e DVM.

1. Insira o **chave pública SSH** usado para autorização em todas as máquinas Linux criado como parte do cluster Kubernetes e DVM.

1. Insira o **prefixo de DNS do mestre de perfil** que é exclusivo para a região. Isso deve ser um nome exclusivo de região, como `k8s-12345`. Tente escolhê-lo mesmo que o grupo de recursos nomeie como melhor prática.

    > [!Note]  
    > Para cada cluster, use um prefixo DNS do mestre de perfil novos e exclusivos.

1. Selecione o **contagem de perfil do Pool de mestre de Kubernetes**. A contagem de contém o número de nós no pool de mestre. Pode haver de 1 a 7. Esse valor deve ser um número ímpar.

1. Selecione **VMSize a das VMs de mestre de Kubernetes**.

1. Selecione o **contagem de perfil de Pool de nós de Kubernetes**. A contagem de contém o número de agentes no cluster. 

1. Selecione o **perfil de armazenamento**. Você pode escolher **Blob de disco** ou **Managed Disk**. Isso especifica que as VMs do nó de tamanho de VM do Kubernetes. 

1. Selecione **ADFS** para o **sistema de identidade do Azure Stack** para sua instalação do Azure Stack.

1. Insira o **entidade de serviço ClientId** isso é usado pelo provedor de nuvem do Azure do Kubernetes. A ID do cliente identificada como a ID do aplicativo quando o administrador do Azure Stack criado a entidade de serviço.

1. Insira o **grupo de recursos do Key Vault**. 

1. Insira o **nome do Key Vault**.

1. Insira o **segredo do Key Vault**.

1. Insira o **versão do provedor de nuvem Azure Kubernetes**. Essa é a versão para o provedor do Azure do Kubernetes. O Azure Stack libera uma criação personalizada de Kubernetes para cada versão do Azure Stack.

### <a name="3-summary"></a>3. Resumo

1. Selecione resumo. A folha exibe uma mensagem de validação para suas configurações de Cluster do Kubernetes.

    ![Modelo de Solução de Implementação](media/azure-stack-solution-template-kubernetes-deploy/04_preview.png)

2. Examine suas configurações.

3. Selecione **Okey** para implantar o cluster.

> [!TIP]  
>  Se você tiver dúvidas sobre sua implantação, você pode postar a pergunta ou ver se alguém já tem respondeu à pergunta na [Fórum do Azure Stack](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack). 

## <a name="next-steps"></a>Próximas etapas

[Conectar-se ao cluster](azure-stack-solution-template-kubernetes-deploy.md#connect-to-your-cluster)