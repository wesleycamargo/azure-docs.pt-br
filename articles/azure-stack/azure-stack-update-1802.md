---
title: Atualização da pilha do Azure 1802 | Microsoft Docs
description: Saiba mais sobre o que é a atualização 1802 pilha do Azure integradas de sistemas, os problemas conhecidos e onde baixar a atualização.
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
ms.date: 05/08/2018
ms.author: brenduns
ms.reviewer: justini
ms.openlocfilehash: 5cf61ccaadc40a5f250dcf477de5b446052aba9a
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/16/2018
---
# <a name="azure-stack-1802-update"></a>Atualização de pilha 1802 do Azure

*Aplica-se a: sistemas integrados de pilha do Azure*

Este artigo descreve as melhorias e correções no pacote de atualização de 1802, problemas para esta versão e onde baixar a atualização. Problemas conhecidos são divididos em problemas relacionados diretamente para o processo de atualização e problemas com a compilação (após a instalação).

> [!IMPORTANT]        
> Este pacote de atualização é apenas para sistemas de pilha do Azure integradas. Não use este pacote de atualização para o Kit de desenvolvimento de pilha do Azure.

## <a name="build-reference"></a>Referência de build    
É o número de compilação de atualização do Azure pilha 1802 **20180302.1**.  


## <a name="before-you-begin"></a>Antes de começar    
> [!IMPORTANT]    
> Não tente criar máquinas virtuais durante a instalação dessa atualização. Para obter mais informações sobre como gerenciar atualizações, consulte [gerenciar atualizações na visão geral do Azure pilha](/azure-stack-updates#plan-for-updates).


### <a name="prerequisites"></a>Pré-requisitos
- Instalar a pilha do Azure [1712 atualizar](azure-stack-update-1712.md) antes de aplicar a atualização 1802 de pilha do Azure.    

- Instalar **AzS Hotfix – 1.0.180312.1-Build 20180222.2** antes de aplicar a atualização 1802 de pilha do Azure. Esse hotfix atualiza o Windows Defender e está disponível quando você baixar atualizações para a pilha do Azure.

  Para instalar o hotfix, siga os procedimentos normais para [instalando atualizações para o Azure pilha](azure-stack-apply-updates.md). O nome da atualização é exibido como **AzS Hotfix – 1.0.180312.1**e inclui os seguintes arquivos: 
    - PUPackageHotFix_20180222.2-1.exe
    - PUPackageHotFix_20180222.2-1.bin
    - Metadata.xml

  Depois de carregar esses arquivos para uma conta de armazenamento e contêiner, execute a instalação do bloco de atualização no portal de administração. 
  
  Ao contrário das atualizações a pilha do Azure, instalar essa atualização não altera a versão da pilha do Azure.  Para confirmar se essa atualização é instalada, exiba a lista de **atualizações instaladas**.
 


### <a name="post-update-steps"></a>Etapas de pós-atualização
Após a instalação do 1802, instale os Hotfixes aplicáveis. Para mais informações, consulte os seguintes artigos da base de dados de Conhecimento, bem como nosso [política manutenção](azure-stack-servicing-policy.md). 
- Hotfix de pilha do Azure **1.0.180302.4**. [KB 4131152 - conjuntos de escala de máquinas virtuais existentes pode se tornar inutilizável]( https://support.microsoft.com/help/4131152) 

  Essa correção também resolve os problemas detalhados na [KB 4103348 - falha do serviço de API do controlador de rede ao tentar instalar uma atualização do Azure pilha](https://support.microsoft.com/help/4103348).


### <a name="new-features-and-fixes"></a>Novos recursos e correções
Esta atualização inclui os seguintes aprimoramentos e correções para a pilha do Azure.

- **Adicionado suporte para as seguintes versões de API do serviço de armazenamento do Azure**:
    - 2017-04-17 
    - 2016-05-31 
    - 2015-12-11 
    - 2015-07-08 
    
    Para obter mais informações, consulte [Azure pilha de armazenamento: diferenças e considerações](/azure/azure-stack/user/azure-stack-acs-differences).

- **Suporte para maior [Blobs de bloco](azure-stack-acs-differences.md)**:
    - O tamanho máximo permitido de bloco é aumentado de 4 MB para 100 MB.
    - O tamanho máximo do blob é aumentado de 195 GB 4,75 TB.  

- **Backup da infra-estrutura** agora aparece no bloco de provedores de recursos e alertas de backup estão habilitadas. Para obter mais informações sobre a infraestrutura de serviço de Backup, consulte [Backup e recuperação de dados para a pilha do Azure com o serviço de Backup de infraestrutura](azure-stack-backup-infrastructure-backup.md).

- **Atualizar para o *teste AzureStack* cmdlet** para melhorar o diagnóstico para armazenamento. Para obter mais informações sobre esse cmdlet, consulte [validação para o Azure pilha](azure-stack-diagnostic-test.md).

- **Melhorias de controle de acesso (RBAC) baseado em função** -agora você pode usar o RBAC para delegar permissões para os grupos universais do usuário quando a pilha do Azure é implantada com o AD FS. Para saber mais sobre o RBAC, consulte [RBAC gerenciar](azure-stack-manage-permissions.md).

- **Adicionado suporte para vários domínios de falha**.  Para obter mais informações, consulte [alta disponibilidade para o Azure pilha](azure-stack-key-features.md#high-availability-for-azure-stack).

- **Suporte para atualizações de memória física** -agora você pode expandir a capacidade de memória do sistema de pilha do Azure integradas após a implantação inicial. Para obter mais informações, consulte [gerenciar a capacidade de memória física para o Azure pilha](azure-stack-manage-storage-physical-memory-capacity.md).

- **Várias correções de** de desempenho, estabilidade, segurança e sistema operacional que é usado pela pilha do Azure.

<!--
#### New features
-->


<!--
#### Fixes
-->


### <a name="known-issues-with-the-update-process"></a>Problemas conhecidos com o processo de atualização    
*Não há nenhum problema conhecido para a instalação da atualização 1802.*


### <a name="known-issues-post-installation"></a>Problemas conhecidos (após a instalação)
A seguir são problemas conhecidos de pós-instalação para compilação **20180302.1**

#### <a name="portal"></a>Portal
- A capacidade de [para abrir uma nova solicitação de suporte na lista suspensa](azure-stack-manage-portals.md#quick-access-to-help-and-support) de dentro do administrador do portal não está disponível. Em vez disso, use o seguinte link:     
    - Para o Azure pilha sistemas integrados, use https://aka.ms/newsupportrequest.

- <!-- 2050709 --> In the admin portal, it is not possible to edit storage metrics for Blob service, Table service, or Queue service. When you go to Storage, and then select the blob, table, or queue service tile, a new blade opens that displays a metrics chart for that service. If you then select Edit from the top of the metrics chart tile, the Edit Chart blade opens but does not display options to edit metrics.

- Ele não poderá exibir os recursos de computação ou armazenamento no portal do administrador. A causa desse problema é um erro durante a instalação da atualização que faz com que a atualização a ser informada incorretamente como bem-sucedido. Se esse problema ocorrer, entre em contato com o Atendimento Microsoft para obter assistência.

- Você pode ver um painel em branco no portal. Para recuperar o painel de controle, selecione o ícone de engrenagem no canto superior direito do portal e, em seguida, selecione **restaurar as configurações padrão**.

- Excluir resultados de assinaturas do usuário em recursos órfãos. Como alternativa, primeiro exclua os recursos do usuário ou o grupo de recursos inteiro e exclua assinaturas de usuário.

- Você não pode exibir as permissões para sua assinatura usando os portais de pilha do Azure. Como alternativa, use o PowerShell para verificar as permissões.

- No painel do portal de administração, o bloco de atualização não pode exibir informações sobre atualizações. Para resolver esse problema, clique no bloco para atualizá-lo.

-   No portal de administração, você poderá ver um alerta crítico para o componente Microsoft.Update.Admin. O nome do alerta, descrição e correção toda a exibição como:  
    - *Erro - o modelo para o FaultType ResourceProviderTimeout está ausente.*

    Esse alerta pode ser ignorado. 

- <!-- 2253274 --> In the admin and user portals, the Settings blade for vNet Subnets fails to load. As a workaround, use PowerShell and the [Get-AzureRmVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/azurerm.network/get-azurermvirtualnetworksubnetconfig?view=azurermps-5.5.0) cmdlet to view and  manage this information.

- No portal de administração e no portal do usuário, a folha de visão geral não é carregado quando você seleciona a folha de visão geral de contas de armazenamento que foram criados com uma versão mais antiga de API (exemplo: 2015-06-15). Isso inclui contas de armazenamento do sistema como **updateadminaccount** que é usado durante o patch e atualização. 

  Como alternativa, use o PowerShell para executar o **ResourceSynchronization.ps1 início** script para restaurar o acesso para os detalhes da conta de armazenamento. [O script está disponível no GitHub]( https://github.com/Azure/AzureStack-Tools/tree/master/Support/scripts)e deve ser executada com as credenciais de administrador de serviço no ponto de extremidade com privilégios. 

- O **a integridade do serviço** folha Falha ao carregar. Quando você abre a folha de integridade do serviço no portal do administrador ou o usuário, Azure pilha exibirá um erro e não carregar as informações. Este comportamento é esperado. Embora você possa selecionar e abrir o serviço de integridade, esse recurso não está disponível, mas será implementado em uma versão futura da pilha do Azure.


#### <a name="health-and-monitoring"></a>Monitoramento e integridade
Não há nenhum problema conhecido depois de atualizar para 1802.

#### <a name="marketplace"></a>Marketplace
- Usuários podem procurar o marketplace completo sem uma assinatura e podem ver itens administrativos como planos e ofertas. Esses itens não estão funcionando para os usuários.

#### <a name="compute"></a>Computação
- As configurações de escala para conjuntos de escala de máquinas virtuais não estão disponíveis no portal. Como alternativa, você pode usar [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). Por causa das diferenças de versão do PowerShell, você deve usar o `-Name` parâmetro em vez de `-VMScaleSetName`.

- <!-- 2290877  --> You cannot scale up a virtual machine scale set (VMSS) that was created when using Azure Stack prior to version 1802. This is due to the change in support for using availability sets with virtual machine scale sets. This support was added with version 1802.  When you attempt to add additional instances to scale a VMSS that was created prior to this support being added, the action fails with the message *Provisioning state failed*. 

  Esse problema é resolvido na versão 1803. Para resolver esse problema para a versão 1802, instale o hotfix de pilha do Azure **1.0.180302.4**. Para obter mais informações, consulte [4131152 KB: conjuntos de escala de máquinas virtuais existentes podem se tornar inutilizáveis]( https://support.microsoft.com/help/4131152). 

- A pilha do Azure oferece suporte ao uso somente VHDs de tipo fixo. Algumas imagens oferecidas por meio do marketplace na pilha do Azure usam VHDs dinâmicos, mas aqueles foram removidos. Redimensionamento de uma máquina virtual (VM) com um disco dinâmico anexado a ele deixa a VM em um estado de falha.

  Para atenuar esse problema, exclua a máquina virtual sem excluir o disco da VM, um blob VHD em uma conta de armazenamento. Em seguida, converter o VHD de um disco dinâmico em um disco fixo e, em seguida, recrie a máquina virtual.

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

- O balanceamento de carga interno (ILB) incorretamente lida com endereços MAC para VMs de back-end, que faz com que o ILB interromper quando usar instâncias Linux na rede Back-End.  ILB funciona bem com instâncias do Windows na rede de Back-End.

-   O recurso de encaminhamento de IP é visível no portal, no entanto, habilitar o encaminhamento IP não tem nenhum efeito. Ainda não há suporte para esse recurso.

- A pilha do Azure oferece suporte a um único *gateway de rede local* por endereço IP. Isso é verdadeiro em todas as assinaturas de locatários. Após a criação da primeira conexão de gateway local de rede, subsequente tenta criar um recurso de gateway de rede local com o mesmo endereço IP são bloqueados.

- Em uma rede Virtual que foi criado com uma configuração de servidor DNS de *automáticas*, alteração de uma falha de servidor DNS personalizado. As configurações atualizadas não são propagadas para as VMs nessa rede virtual.

- Pilha do Azure não oferece suporte à adição de interfaces de rede adicionais para uma instância de VM depois que a VM for implantada. Se a VM requer mais de uma interface de rede, eles devem ser definidos no momento da implantação.

-   <!-- 2096388 --> You cannot use the admin portal to update rules for a network security group. 

    Solução alternativa para o serviço de aplicativo: se você precisar de área de trabalho remota para as instâncias de controlador, você modificar as regras de segurança dentro dos grupos de segurança de rede com o PowerShell.  A seguir estão exemplos de como *permitir*e, em seguida, restaurar a configuração para *negar*:  
    
    - *Permita:*
 
      ```powershell    
      Login-AzureRMAccount -EnvironmentName AzureStackAdmin
      
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
        
        Login-AzureRMAccount -EnvironmentName AzureStackAdmin
        
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

- <!-- IS, ASDK --> Special characters, including spaces and periods, are not supported in the **Family** name when you create a SKU for the SQL and MySQL resource providers.

> [!NOTE]  
> Depois de atualizar para o Azure 1802 de pilha, você pode continuar a usar os provedores de recursos SQL e MySQL implantado anteriormente.  Recomendamos que você atualize o SQL e MySQL quando uma nova versão estiver disponível. Como a pilha do Azure, aplica atualizações a provedores de recursos do SQL e MySQL sequencialmente.  Por exemplo, se você usar a versão 1710, primeiro aplique versão 1711 e, em seguida, 1712 e, em seguida, atualize para 1802.      
>   
> A instalação da atualização 1802 não afeta o uso atual de provedores de recursos do SQL ou MySQL pelos usuários.
> Independentemente da versão dos provedores de recurso que você usar, seus dados de usuários em seus bancos de dados não é tocados e permanecem acessíveis.    


#### <a name="app-service"></a>Serviço de Aplicativo
- Os usuários devem registrar o provedor de recursos de armazenamento antes de criar sua primeira função do Azure na assinatura.

- Para expandir a infraestrutura (os operadores, gerenciamento, funções de front-end), você deve usar o PowerShell conforme descrito nas notas de versão para a computação.

<!--
#### Identity
-->

#### <a name="downloading-azure-stack-tools-from-github"></a>Baixar ferramentas de pilha do Azure do GitHub
- Ao usar o *invoke-webrequest* das ferramentas de cmdlet do PowerShell para baixar a pilha do Azure do Github, você receberá um erro:     
    -  *Invoke-webrequest: A solicitação foi anulada: não foi possível criar o canal seguro de SSL/TLS.*     

  Esse erro ocorre devido a uma substituição de suporte do GitHub recente das Tlsv1 e Tlsv1.1 padrões de criptografia (o padrão para o PowerShell). Para obter mais informações, consulte [aviso de remoção de padrões de criptografia fraca](https://githubengineering.com/crypto-removal-notice/).

  Para resolver esse problema, adicione `[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12` para o início do script para forçar o console do PowerShell para usar TLSv1.2 durante o download de repositórios GitHub.


## <a name="download-the-update"></a>Baixe a atualização
Você pode baixar o pacote de atualização do Azure pilha 1802 [aqui](https://aka.ms/azurestackupdatedownload).


## <a name="more-information"></a>Mais informações
A Microsoft fornece uma maneira de monitorar e retomar atualizações usando o ponto de extremidade privilegiado (PEP) instalado com atualização 1710.

- Consulte o [monitorar atualizações na pilha do Azure usando a documentação do ponto de extremidade com privilégios](https://docs.microsoft.com/azure/azure-stack/azure-stack-monitor-update).

## <a name="see-also"></a>Consulte também

- Para obter uma visão geral do gerenciamento de atualizações na pilha do Azure, consulte [gerenciar atualizações na visão geral do Azure pilha](azure-stack-updates.md).
- Para obter mais informações sobre como aplicar atualizações com pilha do Azure, consulte [aplicar as atualizações na pilha do Azure](azure-stack-apply-updates.md).
