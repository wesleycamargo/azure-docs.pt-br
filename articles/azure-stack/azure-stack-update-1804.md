---
title: Atualização da pilha do Azure 1804 | Microsoft Docs
description: Saiba mais sobre o que é a atualização 1804 pilha do Azure integradas de sistemas, os problemas conhecidos e onde baixar a atualização.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/30/2018
ms.author: brenduns
ms.reviewer: justini
ms.openlocfilehash: 2c2813a7f2d909a23c8f5d4f5ac0280b3f932ba6
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/04/2018
ms.locfileid: "34700117"
---
# <a name="azure-stack-1804-update"></a>Atualização de pilha 1804 do Azure

*Aplica-se a: sistemas integrados de pilha do Azure*

Este artigo descreve as melhorias e correções no pacote de atualização de 1804, problemas para esta versão e onde baixar a atualização. Problemas conhecidos são divididos em problemas relacionados diretamente para o processo de atualização e problemas com a compilação (após a instalação).

> [!IMPORTANT]        
> Este pacote de atualização é apenas para sistemas de pilha do Azure integradas. Não use este pacote de atualização para o Kit de desenvolvimento de pilha do Azure.

## <a name="build-reference"></a>Referência de build    
É o número de compilação de atualização do Azure pilha 1804 **20180513.1**.   

### <a name="new-features"></a>Novos recursos
Esta atualização inclui os seguintes aprimoramentos para a pilha do Azure.

- <!-- 15028744 - IS -->  **Visual Studio support for disconnected Azure Stack deployments using AD FS**. Within Visual Studio you now can add subscriptions and authenticate using AD FS federated User credentials. 
 
- <!-- 1779474, 1779458 - IS --> **Use Av2 and F series virtual machines**. Azure Stack can now use virtual machines based on the Av2-series and F-series virtual machine sizes. For more information see [Virtual machine sizes supported in Azure Stack](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-vm-sizes). 

- <!-- 1759172 - IS, ASDK --> **New administrative subscriptions**. With 1804 there are two new subscription types available in the portal. These new subscription types are in addition to the Default Provider subscription and visible with new Azure Stack installations beginning with version 1804. *Do not use these new subscription types with this version of Azure Stack*. We will announce the availability to use these subscription types in with a future update. 

  Se você atualizar a pilha do Azure versão 1804, dois novos tipos de assinatura não são visíveis. No entanto, sistemas integrados de novas implantações de pilha do Azure e instalações da versão do Kit de desenvolvimento de pilha do Azure 1804 ou posterior têm acesso a todos os tipos de assinatura de três.  

  Esses novos tipos de assinatura são parte de uma alteração maior para proteger a assinatura do provedor padrão e para tornar mais fácil implantar recursos compartilhados, como servidores de hospedagem SQL. Como a pilha do Azure, adicionamos mais partes dessa alteração maior com atualizações futuras, recursos implantados sob esses novos tipos de assinatura podem ser perdidos. 

  Os tipos de três assinatura agora visíveis são:  
  - Padrão de assinatura do provedor: continuar a usar este tipo de assinatura. 
  - Assinatura de medição: *não use este tipo de assinatura.*
  - Assinatura de consumo: *não use este tipo de assinatura*

  



## <a name="fixed-issues"></a>Problemas corrigidos

- <!-- IS, ASDK -->  In the admin portal, you no longer have to refresh the Update tile before it displays information.
 
- <!-- 2050709  -->  You can now use the admin portal to edit storage metrics for Blob service, Table service, and Queue service.
 
- <!-- IS, ASDK --> Under **Networking**, when you click **Connection** to set up a VPN connection, **Site-to-site (IPsec)** is now the only available option.

- **Várias correções de** de desempenho, estabilidade, segurança e sistema operacional que é usado pela pilha do Azure.

## <a name="additional-releases-timed-with-this-update"></a>Versões adicionais atingiu o tempo com essa atualização  
A seguir agora está disponíveis, mas não requerem a atualização da pilha do Azure 1804.
- **Atualizar para o pacote de monitoramento do Microsoft Azure pilha System Center Operations Manager**. Uma nova versão (1.0.3.0) do pacote Microsoft System Center Operations Manager monitoramento para a pilha do Azure está disponível para [baixar](https://www.microsoft.com/download/details.aspx?id=55184). Com esta versão, você pode usar entidades de serviço quando você adiciona uma implantação de pilha do Azure conectada. Esta versão também apresenta uma experiência de gerenciamento de atualização que permite que você realizar uma ação de correção diretamente de dentro do Operations Manager. Também há novos painéis que exibem provedores de recursos, unidade de escala e nós de unidade de escala.

- **Novo Azure pilha Admin PowerShell versão 1.3.0**.  O Azure PowerShell pilha 1.3.0 agora está disponível para instalação. Esta versão fornece comandos para todos os provedores de recursos de administração gerenciar a pilha do Azure.  Com esta versão, alguns tipos de conteúdo serão substituído do GitHub de ferramentas de pilha do Azure [repositório](https://github.com/Azure/AzureStack-Tools). 

   Para obter detalhes de instalação, execute o [instruções](azure-stack-powershell-install.md) ou [ajuda](https://docs.microsoft.com/powershell/azure/azure-stack/overview?view=azurestackps-1.3.0) conteúdo para o módulo do Azure pilha 1.3.0. 

- **Versão da referência de Rest de API do Azure pilha inicial**. O [referência de API para todos os provedores de recursos de administração do Azure pilha](https://docs.microsoft.com/rest/api/azure-stack/) agora está publicada. 


## <a name="before-you-begin"></a>Antes de começar    

### <a name="prerequisites"></a>Pré-requisitos
- Instalar a pilha do Azure [atualizar 1803](azure-stack-update-1803.md) antes de aplicar a atualização 1804 de pilha do Azure.    

### <a name="known-issues-with-the-update-process"></a>Problemas conhecidos com o processo de atualização   
- Durante a instalação da atualização 1804, você pode ver os alertas com o título *erro – o modelo para o FaultType UserAccounts.New está ausente.*  Você pode ignorar com segurança esses alertas. Esses alertas serão fechado automaticamente após a atualização 1804.   
 
- <!-- TBD - IS --> Do not attempt to create virtual machines during the installation of this update. For more information about managing updates, see [Manage updates in Azure Stack overview](azure-stack-updates.md#plan-for-updates).


### <a name="post-update-steps"></a>Etapas de pós-atualização
*Não há nenhuma etapa de pós-atualização para atualização 1804.*



### <a name="known-issues-post-installation"></a>Problemas conhecidos (após a instalação)
A seguir são problemas conhecidos de pós-instalação para compilação **20180513.1**.

#### <a name="portal"></a>Portal
- <!-- 1272111 - IS --> After you install or update to this version of Azure Stack, you might not be able to view Azure Stack scale units in the Admin portal.  
  Solução alternativa: Use o PowerShell para exibir informações sobre unidades de escala. Para obter mais informações, consulte o [ajuda](https://docs.microsoft.com/powershell/azure/azure-stack/overview?view=azurestackps-1.3.0) conteúdo 1.3.0 do módulo de pilha do Azure. 

- <!-- 2332636 - IS -->  When you use AD FS for your Azure Stack identity system and update to this version of Azure Stack, the default owner of the default provider subscription is reset to the built-in **CloudAdmin** user.  
  Solução alternativa: Para resolver esse problema, depois de instalar esta atualização, use a etapa 3 do [na pilha do Azure de confiança do provedor de declarações de automação de gatilho para configurar](azure-stack-integrate-identity.md#trigger-automation-to-configure-claims-provider-trust-in-azure-stack-1) procedimento para redefinir o proprietário da assinatura do provedor padrão.   

- <!-- TBD - IS ASDK --> Some administrative subscription types are not available.  When you upgrade Azure Stack to this version, the two subscription types that were [introduced with version 1804](#new-features) are not visible in the console. This is expected. The unavailable subscription types are *Metering subscription*, and *Consumption subscription*. These subscription types are visible in new Azure Stack environments beginning with version 1804 but are not yet ready for use. You should continue to use the *Default Provider* subscription type.  


- <!-- TBD -  IS ASDK -->The ability [to open a new support request from the dropdown](azure-stack-manage-portals.md#quick-access-to-help-and-support) from within the administrator portal isn’t available. Instead, use the following link:     
    - Para o Azure pilha sistemas integrados, use https://aka.ms/newsupportrequest.

- <!-- 2403291 - IS ASDK --> You might not have use of the horizontal scroll bar along the bottom of the admin and user portals. If you can’t access the horizontal scroll bar, use the breadcrumbs to navigate to a previous blade in the portal by selecting the name of the blade you want to view from the breadcrumb list found at the top left of the portal.
  ![Navegação estrutural](media/azure-stack-update-1804/breadcrumb.png) 

- <!-- TBD - IS --> It might not be possible to view compute or storage resources in the administrator portal. The cause of this issue is an error during the installation of the update that causes the update to be incorrectly reported as successful. If this issue occurs, contact Microsoft Customer Support Services for assistance.

- <!-- TBD - IS --> You might see a blank dashboard in the portal. To recover the dashboard, select the gear icon in the upper right corner of the portal, and then select **Restore default settings**.

- <!-- TBD - IS ASDK --> Deleting user subscriptions results in orphaned resources. As a workaround, first delete user resources or the entire resource group, and then delete user subscriptions.

- <!-- TBD - IS ASDK --> You cannot view permissions to your subscription using the Azure Stack portals. As a workaround, use PowerShell to verify permissions.

- <!-- TBD - IS ASDK --> In the admin portal, you might see a critical alert for the *Microsoft.Update.Admin* component. The Alert name, description, and remediation all display as:  
    - *Erro - o modelo para o FaultType ResourceProviderTimeout está ausente.*

  Esse alerta pode ser ignorado. 


#### <a name="health-and-monitoring"></a>Monitoramento e integridade
- <!-- 1264761 - IS ASDK -->  You might see alerts for the *Health controller* component that have the following details:  

   Alerta #1:
   - NOME: Função de infraestrutura não íntegro
   - SEVERIDADE: aviso
   - COMPONENTE: Controlador de integridade
   - Descrição: O Scanner de pulsação do controlador de integridade não está disponível. Isso pode afetar as métricas e relatórios de integridade.  

  Alerta #2:
   - NOME: Função de infraestrutura não íntegro
   - SEVERIDADE: aviso
   - COMPONENTE: Controlador de integridade
   - Descrição: O Scanner de falha do controlador de integridade não está disponível. Isso pode afetar as métricas e relatórios de integridade.

  Ambos os alertas podem ser ignorados. Ele será fechado automaticamente ao longo do tempo.  
 

#### <a name="compute"></a>Computação
- <!-- TBD - IS --> When selecting a virtual machine size for a virtual machine deployment, some F-Series VM sizes are not visible as part of the size selector when you create a VM. The following VM sizes do not appear in the selector: *F8s_v2*, *F16s_v2*, *F32s_v2*, and *F64s_v2*.  
  Como alternativa, use um dos métodos a seguir para implantar uma máquina virtual. Cada método, você precisa especificar o tamanho da VM que deseja usar.

  - **Modelo do Gerenciador de recursos do Azure:** quando você usa um modelo, defina o *vmSize* no modelo para igualar o tamanho desejado da VM. Por exemplo, o seguinte é usado para implantar uma VM que usa o *F32s_v2* tamanho:  

    ```
        "properties": {
        "hardwareProfile": {
                "vmSize": "Standard_F32s_v2"
        },
    ```  
  - **CLI do Azure:** você pode usar o [criar vm az](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-create) de comando e especifique o tamanho da VM como um parâmetro, semelhante ao `--size "Standard_F32s_v2"`.

  - **PowerShell:** com o PowerShell, você pode usar [New-AzureRMVMConfig](https://docs.microsoft.com/powershell/module/azurerm.compute/new-azurermvmconfig?view=azurermps-6.0.0) com o parâmetro que especifica o tamanho da VM, semelhante ao `-VMSize "Standard_F32s_v2"`.


- <!-- TBD - IS ASDK --> Scaling settings for virtual machine scale sets are not available in the portal. As a workaround, you can use [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). Because of PowerShell version differences, you must use the `-Name` parameter instead of `-VMScaleSetName`.

- <!-- TBD - IS --> When you create an availability set in the portal by going to **New** > **Compute** > **Availability set**, you can only create an availability set with a fault domain and update domain of 1. As a workaround, when creating a new virtual machine, create the availability set by using PowerShell, CLI, or from within the portal.

- <!-- TBD - IS ASDK --> When you create virtual machines on the Azure Stack user portal, the portal displays an incorrect number of data disks that can attach to a D series VM. All supported D series VMs can accommodate as many data disks as the Azure configuration.

- <!-- TBD - IS ASDK --> When a VM image fails to be created, a failed item that you cannot delete might be added to the VM images compute blade.

  Como alternativa, crie uma nova imagem VM com um VHD fictício que pode ser criado pelo Hyper-V (New-VHD-caminho C:\dummy.vhd-fixa - SizeBytes 1 GB). Esse processo deve corrigir o problema que impede a exclusão do item com falha. Em seguida, 15 minutos depois de criar a imagem fictícia, você poderá com êxito excluí-la.

  Você pode tentar, em seguida, faça download novamente a imagem VM que falhou anteriormente.

- <!-- TBD - IS ASDK --> If provisioning an extension on a VM deployment takes too long, users should let the provisioning time-out instead of trying to stop the process to deallocate or delete the VM.  

- <!-- 1662991 IS ASDK --> Linux VM diagnostics is not supported in Azure Stack. When you deploy a Linux VM with VM diagnostics enabled, the deployment fails. The deployment also fails if you enable the Linux VM basic metrics through diagnostic settings.  


#### <a name="networking"></a>Rede
- <!-- 1766332 - IS ASDK --> Under **Networking**, if you click **Create VPN Gateway** to set up a VPN connection, **Policy Based** is listed as a VPN type. Do not select this option. Only the **Route Based** option is supported in Azure Stack.

- <!-- 2388980 - IS ASDK --> After a VM is created and associated with a public IP address, you can't disassociate that VM from that IP address. Disassociation appears to work, but the previously assigned public IP address remains associated with the original VM.

  No momento, você deve usar apenas novos endereços IP públicos para novas VMs que você criar.

  Esse comportamento ocorre mesmo se você reatribuir o endereço IP para uma nova VM (conhecido como um *permuta de VIP*). Todas as futuras tentativas de conexão por esse resultado do endereço IP em uma conexão à VM originalmente associado e não para o novo.

- <!-- 2292271 - IS ASDK --> If you raise a Quota limit for a Network resource that is part of an Offer and Plan that is associated with a tenant subscription, the new limit is not applied to that subscription. However, the new limit does apply to new subscriptions that are created after the quota is increased. 

  Para contornar esse problema, use um plano de complemento para aumentar a cota de rede quando o plano já está associado uma assinatura. Para obter mais informações, consulte como [disponibilizar um plano de complemento](azure-stack-subscribe-plan-provision-vm.md#to-make-an-add-on-plan-available).

- <!-- 2304134 IS ASDK --> You cannot delete a subscription that has DNS Zone resources or Route Table resources associated with it. To successfully delete the subscription, you must first delete DNS Zone and Route Table resources from the tenant subscription. 
  

- <!-- 1902460 - IS ASDK --> Azure Stack supports a single *local network gateway* per IP address. This is true across all tenant subscriptions. After the creation of the first local network gateway connection, subsequent attempts to create a local network gateway resource with the same IP address are blocked.

- <!-- 16309153 - IS ASDK --> On a Virtual Network that was created with a DNS Server setting of *Automatic*, changing to a custom DNS Server fails. The updated settings are not pushed to VMs in that Vnet.

- <!-- TBD - IS ASDK --> Azure Stack does not support adding additional network interfaces to a VM instance after the VM is deployed. If the VM requires more than one network interface, they must be defined at deployment time.

- <!-- 2096388 IS --> You cannot use the admin portal to update rules for a network security group. 

    Solução alternativa para o serviço de aplicativo: se você precisar de área de trabalho remota para as instâncias de controlador, você modificar as regras de segurança dentro dos grupos de segurança de rede com o PowerShell.  A seguir estão exemplos de como *permitir*e, em seguida, restaurar a configuração para *negar*:  
    
    - *Permita:*
 
      ```powershell    
      Connect-AzureRmAccount -EnvironmentName AzureStackAdmin
      
      $nsg = Get-AzureRmNetworkSecurityGroup -Name "ControllersNsg" -ResourceGroupName "AppService.local"
      
      $RuleConfig_Inbound_Rdp_3389 =  $nsg | Get-AzureRmNetworkSecurityRuleConfig -Name "Inbound_Rdp_3389"
      
      ##This doesn’t work. Need to set properties again even in case of edit
      
      #Set-AzureRmNetworkSecurityRuleConfig -Name "Inbound_Rdp_3389" -NetworkSecurityGroup $nsg -Access Allow  
      
      Set-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg `
        -Name $RuleConfig_Inbound_Rdp_3389.Name `
        -Description "Inbound_Rdp_3389" `
        -Access Allow `
        -Protocol $RuleConfig_Inbound_Rdp_3389.Protocol `
        -Direction $RuleConfig_Inbound_Rdp_3389.Direction `
        -Priority $RuleConfig_Inbound_Rdp_3389.Priority `
        -SourceAddressPrefix $RuleConfig_Inbound_Rdp_3389.SourceAddressPrefix `
        -SourcePortRange $RuleConfig_Inbound_Rdp_3389.SourcePortRange `
        -DestinationAddressPrefix $RuleConfig_Inbound_Rdp_3389.DestinationAddressPrefix `
        -DestinationPortRange $RuleConfig_Inbound_Rdp_3389.DestinationPortRange
      
      # Commit the changes back to NSG
      Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg
      ```

    - *Nega:*

        ```powershell
        
        Connect-AzureRmAccount -EnvironmentName AzureStackAdmin
        
        $nsg = Get-AzureRmNetworkSecurityGroup -Name "ControllersNsg" -ResourceGroupName "AppService.local"
        
        $RuleConfig_Inbound_Rdp_3389 =  $nsg | Get-AzureRmNetworkSecurityRuleConfig -Name "Inbound_Rdp_3389"
        
        ##This doesn’t work. Need to set properties again even in case of edit
    
        #Set-AzureRmNetworkSecurityRuleConfig -Name "Inbound_Rdp_3389" -NetworkSecurityGroup $nsg -Access Allow  
    
        Set-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg `
          -Name $RuleConfig_Inbound_Rdp_3389.Name `
          -Description "Inbound_Rdp_3389" `
          -Access Deny `
          -Protocol $RuleConfig_Inbound_Rdp_3389.Protocol `
          -Direction $RuleConfig_Inbound_Rdp_3389.Direction `
          -Priority $RuleConfig_Inbound_Rdp_3389.Priority `
          -SourceAddressPrefix $RuleConfig_Inbound_Rdp_3389.SourceAddressPrefix `
          -SourcePortRange $RuleConfig_Inbound_Rdp_3389.SourcePortRange `
          -DestinationAddressPrefix $RuleConfig_Inbound_Rdp_3389.DestinationAddressPrefix `
          -DestinationPortRange $RuleConfig_Inbound_Rdp_3389.DestinationPortRange
          
        # Commit the changes back to NSG
        Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg 
        ```

#### <a name="sql-and-mysql"></a>SQL e MySQL

- <!-- TBD - IS --> Only the resource provider is supported to create items on servers that host SQL or MySQL. Items created on a host server that are not created by the resource provider might result in a mismatched state.  

- <!-- IS, ASDK --> Special characters, including spaces and periods, are not supported in the **Family** or **Tier** names when you create a SKU for the SQL and MySQL resource providers.


> [!NOTE]  
> <!-- TBD - IS --> After you update to Azure Stack 1804, you can continue to use the SQL and MySQL resource providers that you previously deployed.  We recommend you update SQL and MySQL when a new release becomes available. Like Azure Stack, apply updates to SQL and MySQL resource providers sequentially.  For example, if you use version 1802, first apply version 1803, and then update to 1804.      
>   
> A instalação da atualização 1804 não afeta o uso atual de provedores de recursos do SQL ou MySQL pelos usuários.
> Independentemente da versão dos provedores de recurso que você usar, seus dados de usuários em seus bancos de dados não é tocados e permanecem acessíveis.    



#### <a name="app-service"></a>Serviço de Aplicativo
- <!-- 2352906 - IS ASDK --> Users must register the storage resource provider before they create their first Azure Function in the subscription.

- <!-- TBD - IS ASDK --> In order to scale out infrastructure (workers, management, front-end roles), you must use PowerShell as described in the release notes for Compute.

- <!-- TBD - IS ASDK --> App Service can only be deployed into the **Default Provider Subscription** at this time.  In a future update App Service will deploy into the new Metering Subscription introduced in Azure Stack 1804 and all existing deployments will be migrated to this new subscription also.

#### <a name="usage"></a>Uso  
- <!-- TBD - IS ASDK --> Usage Public IP address usage meter data shows the same *EventDateTime* value for each record instead of the *TimeDate* stamp that shows when the record was created. Currently, you can’t use this data to perform accurate accounting of public IP address usage.


<!-- #### Identity -->
<!-- #### Marketplace --> 


## <a name="download-the-update"></a>Baixe a atualização
Você pode baixar o pacote de atualização do Azure pilha 1804 [aqui](https://aka.ms/azurestackupdatedownload).


## <a name="see-also"></a>Consulte também
- Para usar o ponto de extremidade privilegiado (PEP) para monitorar e retomar as atualizações, consulte [monitorar atualizações na pilha do Azure usando o ponto de extremidade com privilégios](azure-stack-monitor-update.md).
- Para obter uma visão geral do gerenciamento de atualizações na pilha do Azure, consulte [gerenciar atualizações na visão geral do Azure pilha](azure-stack-updates.md).
- Para obter mais informações sobre como aplicar atualizações com pilha do Azure, consulte [aplicar as atualizações na pilha do Azure](azure-stack-apply-updates.md).
