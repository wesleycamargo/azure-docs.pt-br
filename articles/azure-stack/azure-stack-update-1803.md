---
title: Atualização da pilha do Azure 1803 | Microsoft Docs
description: Saiba mais sobre o que é a atualização 1803 pilha do Azure integradas de sistemas, os problemas conhecidos e onde baixar a atualização.
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
ms.date: 04/06/2018
ms.author: brenduns
ms.reviewer: justini
ms.openlocfilehash: 26c77b706f17f49eff782e6d0d73087050739874
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2018
---
# <a name="azure-stack-1803-update"></a>Atualização de pilha 1803 do Azure

*Aplica-se a: sistemas integrados de pilha do Azure*

Este artigo descreve as melhorias e correções no pacote de atualização de 1803, problemas para esta versão e onde baixar a atualização. Problemas conhecidos são divididos em problemas relacionados diretamente para o processo de atualização e problemas com a compilação (após a instalação).

> [!IMPORTANT]        
> Este pacote de atualização é apenas para sistemas de pilha do Azure integradas. Não use este pacote de atualização para o Kit de desenvolvimento de pilha do Azure.

## <a name="build-reference"></a>Referência de build    
É o número de compilação de atualização do Azure pilha 1803 **20180329.1**.


## <a name="before-you-begin"></a>Antes de começar    
> [!IMPORTANT]    
> Não tente criar máquinas virtuais durante a instalação dessa atualização. Para obter mais informações sobre como gerenciar atualizações, consulte [gerenciar atualizações na visão geral do Azure pilha](azure-stack-updates.md#plan-for-updates).


### <a name="prerequisites"></a>Pré-requisitos
- Instalar a pilha do Azure [1802 atualizar](azure-stack-update-1802.md) antes de aplicar a atualização 1803 de pilha do Azure.    


### <a name="post-update-steps"></a>Etapas de pós-atualização
- Após a instalação do 1803, instale os Hotfixes aplicáveis. Para mais informações, consulte os seguintes artigos da base de dados de Conhecimento, bem como nosso [política manutenção](azure-stack-servicing-policy.md).

  - [KB 4103348 - serviço de API do controlador de rede falha quando você tentar instalar uma atualização da pilha do Azure](https://support.microsoft.com/en-us/help/4103348)

- Depois de instalar esta atualização, examine a configuração de firewall para garantir [portas necessárias](azure-stack-integrate-endpoints.md) estão abertas. Por exemplo, esta atualização introduz o Monitor do Azure que inclui uma alteração de logs de auditoria para logs de atividade. Com essa alteração, a porta 13012 agora é usada e deve também ser aberta.  

### <a name="new-features"></a>Novos recursos 
Esta atualização inclui os seguintes aprimoramentos e correções para a pilha do Azure.

- **Atualizar os segredos da pilha do Azure** - (contas e certificados). Para obter mais informações sobre como gerenciar segredos, consulte [girar segredos na pilha do Azure](azure-stack-rotate-secrets.md). 

- <!-- 1914853 --> **Automatic redirect to HTTPS** when you use HTTP to access the administrator and user portals. This improvement was made based on [UserVoice](https://feedback.azure.com/forums/344565-azure-stack/suggestions/32205385-it-would-be-great-if-there-was-a-automatic-redirec) feedback for Azure Stack. 

- <!-- 2202621  --> **Access the Marketplace** – You can now open the Azure Stack Marketplace by using the [+New](https://ms.portal.azure.com/#create/hub) option from within the admin and user portals the same way you do in the Azure portals.
 
- <!-- 2202621 --> **Azure Monitor** - Azure Stack adds [Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-azure-monitor) to the admin and user portals. This includes new explorers for metrics and activity logs. To access this Azure Monitor from external networks, port **13012** must be open in firewall configurations. For more information about ports required by Azure Stack, see [Azure Stack datacenter integration - Publish endpoints](azure-stack-integrate-endpoints.md).

   Também como parte dessa alteração, em **mais serviços**, *logs de auditoria* agora aparece como *logs de atividade*. A funcionalidade é agora consistente com o portal do Azure. 

- <!-- 1664791 --> **Sparse files** -  When you add a New image to Azure Stack, or add an image through marketplace syndication, the image is converted to a sparse file. Images that were added prior to using Azure Stack version 1803 cannot be converted. Instead, you must use marketplace syndication to resubmit those images to take advantage of this feature. 
 
   Arquivos esparsos são um formato de arquivo eficiente usado para reduzir o uso de espaço de armazenamento e melhorar a e/s.  Para obter mais informações, consulte [Fsutil esparso](https://docs.microsoft.com/windows-server/administration/windows-commands/fsutil-sparse) para o Windows Server. 

### <a name="fixed-issues"></a>Problemas corrigidos

- <!-- 1739988 --> Internal Load Balancing (ILB) now properly handles MAC addresses for back-end VMs, which causes ILB to drop packets to the back-end network when using Linux instances on the back-end network. ILB works fine with Windows instances on the back-end network. 

- <!-- 1805496 --> An issue where VPN Connections between Azure Stack would become disconnected due to Azure Stack using different settings for the IKE policy than Azure.  The values now match the values in Azure. 

- <!-- 2209262 --> The IP issue where VPN Connections was previously visible in the portal; however enabling or toggling IP Forwarding has no effect. The feature is turned on by default and the ability to change this not yet supported.  The control has been removed from the portal. 

- <!-- 1766332 --> Azure Stack does not support Policy Based VPN Gateways, even though the option appears in the Portal.  The option has been removed from the Portal. 

- <!-- 1868283 --> Azure Stack now prevents resizing of a virtual machine that is created with dynamic disks. 

- <!-- 1756324 --> Usage data for virtual machines is now separated at hourly intervals. This is consistent with Azure. 

- <!--  2253274 --> The issue where in the admin and user portals, the Settings blade for vNet Subnets fails to load. As a workaround, use PowerShell and the [Get-AzureRmVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/azurerm.network/get-azurermvirtualnetworksubnetconfig?view=azurermps-5.5.0) cmdlet to view and manage this information.

- Quando você cria uma máquina virtual, a mensagem *não é possível exibir preço* deixará de aparecer ao escolher um tamanho para o tamanho da VM.

- Várias correções de desempenho, estabilidade, segurança e sistema operacional que é usado pela pilha do Azure.


### <a name="changes"></a>Alterações
- A maneira de alterar o estado de uma oferta recém-criado de *privada* para *pública* ou *encerrado* foi alterado. Para obter mais informações, consulte [criar uma oferta](azure-stack-create-offer.md).


### <a name="known-issues-with-the-update-process"></a>Problemas conhecidos com o processo de atualização    
<!-- 2328416 --> During installation of the 1803 update, there can be downtime of the blob service and internal services that use blob service. This includes some virtual machine operations. This down time can cause failures of tenant operations or alerts from services that can’t access data. This issue resolves itself when the update completes installation. 


### <a name="known-issues-post-installation"></a>Problemas conhecidos (após a instalação)
A seguir são problemas conhecidos de pós-instalação para compilação **20180323.2**.

#### <a name="portal"></a>Portal
- A capacidade de [para abrir uma nova solicitação de suporte na lista suspensa](azure-stack-manage-portals.md#quick-access-to-help-and-support) de dentro do administrador do portal não está disponível. Em vez disso, use o seguinte link:     
    - Para o Azure pilha sistemas integrados, use https://aka.ms/newsupportrequest.

- <!-- 2050709 --> In the admin portal, it is not possible to edit storage metrics for Blob service, Table service, or Queue service. When you go to Storage, and then select the blob, table, or queue service tile, a new blade opens that displays a metrics chart for that service. If you then select Edit from the top of the metrics chart tile, the Edit Chart blade opens but does not display options to edit metrics.

- Ele não poderá exibir os recursos de computação ou armazenamento no portal do administrador. A causa desse problema é um erro durante a instalação da atualização que faz com que a atualização a ser informada incorretamente como bem-sucedido. Se esse problema ocorrer, entre em contato com o Atendimento Microsoft para obter assistência.

- Você pode ver um painel em branco no portal. Para recuperar o painel de controle, selecione o ícone de engrenagem no canto superior direito do portal e, em seguida, selecione **restaurar as configurações padrão**.

- Quando você exibir as propriedades de um recurso ou grupo de recursos, o **mover** botão será desabilitado. Esse comportamento é esperado. Atualmente não há suporte para a movimentação ou grupos de recursos entre grupos de recursos ou assinaturas.

- Excluir resultados de assinaturas do usuário em recursos órfãos. Como alternativa, primeiro exclua os recursos do usuário ou o grupo de recursos inteiro e exclua assinaturas de usuário.

- Você não pode exibir as permissões para sua assinatura usando os portais de pilha do Azure. Como alternativa, use o PowerShell para verificar as permissões.

- No painel do portal de administração, o bloco de atualização não pode exibir informações sobre atualizações. Para resolver esse problema, clique no bloco para atualizá-lo.

- No portal de administração, você poderá ver um alerta crítico para o *Microsoft.Update.Admin* componente. O nome do alerta, descrição e correção toda a exibição como:  
    - *Erro - o modelo para o FaultType ResourceProviderTimeout está ausente.*

  Esse alerta pode ser ignorado. 


<!-- #### Health and monitoring --> 

#### <a name="marketplace"></a>Marketplace
- Usuários podem procurar o marketplace completo sem uma assinatura e podem ver itens administrativos como planos e ofertas. Esses itens não estão funcionando para os usuários.



#### <a name="compute"></a>Computação
- As configurações de escala para conjuntos de escala de máquinas virtuais não estão disponíveis no portal. Como alternativa, você pode usar [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). Por causa das diferenças de versão do PowerShell, você deve usar o `-Name` parâmetro em vez de `-VMScaleSetName`.

- Quando você cria uma conjunto de disponibilidade no portal do indo para **novo** > **de computação** > **conjunto de disponibilidade**, você pode criar apenas um conjunto de disponibilidade com um domínio de falha e o domínio de atualização de 1. Como alternativa, ao criar uma nova máquina virtual, crie a disponibilidade definida usando o PowerShell, CLI, ou de dentro do portal.

- Ao criar máquinas virtuais no portal do usuário de pilha do Azure, o portal exibe um número incorreto de discos de dados que podem ser anexados a uma VM da série DS. As VMs da série DS podem acomodar quantos discos de dados como a configuração do Azure.

- Quando uma imagem de VM Falha ao ser criado, um item com falha que você não pode excluir pode ser adicionado para a folha de computação de imagens VM.

  Como alternativa, crie uma nova imagem VM com um VHD fictício que pode ser criado pelo Hyper-V (New-VHD-caminho C:\dummy.vhd-fixa - SizeBytes 1 GB). Esse processo deve corrigir o problema que impede a exclusão do item com falha. Em seguida, 15 minutos depois de criar a imagem fictícia, você poderá com êxito excluí-la.

  Você pode tentar, em seguida, faça download novamente a imagem VM que falhou anteriormente.

-  Se uma extensão em uma implantação de VM de provisionamento leva muito tempo, os usuários devem permitir que o tempo limite de provisionamento em vez de tentar parar o processo para desalocar ou exclua a máquina virtual.  

- <!-- 1662991 --> Linux VM diagnostics is not supported in Azure Stack. When you deploy a Linux VM with VM diagnostics enabled, the deployment fails. The deployment also fails if you enable the Linux VM basic metrics through diagnostic settings.  


#### <a name="networking"></a>Rede
- Depois que uma máquina virtual é criada e associada a um endereço IP público, não é possível desassociar essa VM de endereço IP. Dissociação parece estar funcionando, mas o endereço IP público atribuído anteriormente permanecerá associado à VM original.

  No momento, você deve usar apenas novos endereços IP públicos para novas VMs que você criar.

  Esse comportamento ocorre mesmo se você reatribuir o endereço IP para uma nova VM (conhecido como um *permuta de VIP*). Todas as futuras tentativas de conexão por esse resultado do endereço IP em uma conexão à VM originalmente associado e não para o novo.



- A pilha do Azure oferece suporte a um único *gateway de rede local* por endereço IP. Isso é verdadeiro em todas as assinaturas de locatários. Após a criação da primeira conexão de gateway local de rede, subsequente tenta criar um recurso de gateway de rede local com o mesmo endereço IP são bloqueados.

- Em uma rede Virtual que foi criado com uma configuração de servidor DNS de *automáticas*, alteração de uma falha de servidor DNS personalizado. As configurações atualizadas não são propagadas para as VMs nessa rede virtual.

- Pilha do Azure não oferece suporte à adição de interfaces de rede adicionais para uma instância de VM depois que a VM for implantada. Se a VM requer mais de uma interface de rede, eles devem ser definidos no momento da implantação.

- <!-- 2096388 --> You cannot use the admin portal to update rules for a network security group. 

    Solução alternativa para o serviço de aplicativo: se você precisar de área de trabalho remota para as instâncias de controlador, você modificar as regras de segurança dentro dos grupos de segurança de rede com o PowerShell.  A seguir estão exemplos de como *permitir*e, em seguida, restaurar a configuração para *negar*:  
    
    - *Permita:*
 
      ```powershell    
      Add-AzureRmAccount -EnvironmentName AzureStackAdmin
      
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
        
        Add-AzureRmAccount -EnvironmentName AzureStackAdmin
        
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
- Antes de continuar, examine a observação importante [antes de começar](#before-you-begin) próximo do início essas notas de versão.

- Pode levar até uma hora antes dos usuários podem criar bancos de dados em uma nova implantação de SQL ou MySQL.

- O provedor de recursos é suportado para criar itens em servidores que o host SQL ou MySQL. Itens criados em um servidor de host que não são criados pelo provedor de recursos podem resultar em um estado não correspondente.  


> [!NOTE]  
> Depois de atualizar para o Azure 1803 de pilha, você pode continuar a usar os provedores de recursos SQL e MySQL implantado anteriormente.  Recomendamos que você atualize o SQL e MySQL quando uma nova versão estiver disponível. Como a pilha do Azure, aplica atualizações a provedores de recursos do SQL e MySQL sequencialmente.  Por exemplo, se você usar a versão 1711, primeiro aplique versão 1712 e, em seguida, 1802 e, em seguida, atualize para 1803.      
>   
> A instalação da atualização 1803 não afeta o uso atual de provedores de recursos do SQL ou MySQL pelos usuários.
> Independentemente da versão dos provedores de recurso que você usar, seus dados de usuários em seus bancos de dados não é tocados e permanecem acessíveis.    



#### <a name="app-service"></a>Serviço de Aplicativo
- Os usuários devem registrar o provedor de recursos de armazenamento antes de criar sua primeira função do Azure na assinatura.

- Para expandir a infraestrutura (os operadores, gerenciamento, funções de front-end), você deve usar o PowerShell conforme descrito nas notas de versão para a computação.


#### <a name="usage"></a>Uso  
- Dados de medição de uso do endereço de IP público uso mostram a mesma *EventDateTime* valor para cada registro em vez do *TimeDate* carimbo que mostra quando o registro foi criado. No momento, você não pode usar esses dados para realizar as estatísticas precisas de uso de endereço IP público.

<!--
#### Identity
-->

#### <a name="downloading-azure-stack-tools-from-github"></a>Baixar ferramentas de pilha do Azure do GitHub
- Ao usar o *invoke-webrequest* das ferramentas de cmdlet do PowerShell para baixar a pilha do Azure do Github, você receberá um erro:     
    -  *Invoke-webrequest: A solicitação foi anulada: não foi possível criar o canal seguro de SSL/TLS.*     

  Esse erro ocorre devido a uma substituição de suporte do GitHub recente das Tlsv1 e Tlsv1.1 padrões de criptografia (o padrão para o PowerShell). Para obter mais informações, consulte [aviso de remoção de padrões de criptografia fraca](https://githubengineering.com/crypto-removal-notice/).

  Para resolver esse problema, adicione `[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12` para o início do script para forçar o console do PowerShell para usar TLSv1.2 durante o download de repositórios GitHub.


## <a name="download-the-update"></a>Baixe a atualização
Você pode baixar o pacote de atualização do Azure pilha 1803 [aqui](https://aka.ms/azurestackupdatedownload).


## <a name="see-also"></a>Consulte também
- Para usar o ponto de extremidade privilegiado (PEP) para monitorar e retomar as atualizações, consulte [monitorar atualizações na pilha do Azure usando o ponto de extremidade com privilégios](azure-stack-monitor-update.md).
- Para obter uma visão geral do gerenciamento de atualizações na pilha do Azure, consulte [gerenciar atualizações na visão geral do Azure pilha](azure-stack-updates.md).
- Para obter mais informações sobre como aplicar atualizações com pilha do Azure, consulte [aplicar as atualizações na pilha do Azure](azure-stack-apply-updates.md).
