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
ms.date: 06/22/2018
ms.author: brenduns
ms.reviewer: justini
ms.openlocfilehash: 9fc58d971db18db63e4dc76123ff1311b77e0191
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/22/2018
ms.locfileid: "36316426"
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

- <!-- 15028744 - IS -->  **Suporte do Visual Studio para implantações de pilha do Azure desconectadas usando o AD FS**. No Visual Studio, você agora pode adicionar assinaturas e autenticar usando o AD FS federadas as credenciais do usuário. 
 
- <!-- 1779474, 1779458 - IS --> **Usar máquinas virtuais de série Av2 e F**. A pilha do Azure agora pode usar máquinas virtuais baseadas em tamanhos de máquina virtual de séries Av2 e F. Para obter mais informações, consulte [tamanhos de máquina Virtual com suporte no Azure pilha](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-vm-sizes). 

- <!-- 1759172 - IS, ASDK --> **Novas assinaturas administrativas**. Com 1804 há dois novos tipos de assinatura disponíveis no portal. Esses novos tipos de assinatura são além da assinatura do provedor padrão e visíveis em novas instalações de pilha do Azure começando com a versão 1804. *Não use esses novos tipos de assinatura com esta versão do Azure pilha*. Podemos anunciou a disponibilidade para usar esses tipos de assinatura com uma atualização futura. 

  Se você atualizar a pilha do Azure versão 1804, dois novos tipos de assinatura não são visíveis. No entanto, sistemas integrados de novas implantações de pilha do Azure e instalações da versão do Kit de desenvolvimento de pilha do Azure 1804 ou posterior têm acesso a todos os tipos de assinatura de três.  

  Esses novos tipos de assinatura são parte de uma alteração maior para proteger a assinatura do provedor padrão e para tornar mais fácil implantar recursos compartilhados, como servidores de hospedagem SQL. Como a pilha do Azure, adicionamos mais partes dessa alteração maior com atualizações futuras, recursos implantados sob esses novos tipos de assinatura podem ser perdidos. 

  Os tipos de três assinatura agora visíveis são:  
  - Padrão de assinatura do provedor: continuar a usar este tipo de assinatura. 
  - Assinatura de medição: *não use este tipo de assinatura.*
  - Assinatura de consumo: *não use este tipo de assinatura*

  



## <a name="fixed-issues"></a>Problemas corrigidos

- <!-- IS, ASDK -->  No portal de administração, você não precisa atualizar o bloco de atualização antes de exibir informações.
 
- <!-- 2050709  -->  Agora você pode usar o portal de administração para editar as métricas de armazenamento para o serviço Blob, o serviço de tabela e o serviço de fila.
 
- <!-- IS, ASDK --> Em **rede**, quando você clica em **Conexão** para configurar uma conexão VPN, **Site a site (IPsec)** agora é a única opção disponível.

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
 
- <!-- TBD - IS --> Não tente criar máquinas virtuais durante a instalação dessa atualização. Para obter mais informações sobre como gerenciar atualizações, consulte [gerenciar atualizações na visão geral do Azure pilha](azure-stack-updates.md#plan-for-updates).


### <a name="post-update-steps"></a>Etapas de pós-atualização
Após a instalação do 1804, instale os Hotfixes aplicáveis. Para mais informações, consulte os seguintes artigos da base de dados de Conhecimento, bem como nosso [política manutenção](azure-stack-servicing-policy.md).  
 - [KB 4341392 - Hotfix da pilha do Azure 1.0.180523.11](https://support.microsoft.com/en-us/help/4341392).




### <a name="known-issues-post-installation"></a>Problemas conhecidos (após a instalação)
A seguir são problemas conhecidos de pós-instalação para compilação **20180513.1**.

#### <a name="portal"></a>Portal
- <!-- 1272111 - IS --> Depois de instalar ou atualizar para esta versão da pilha do Azure, você não poderá exibir as unidades de escala de pilha do Azure no portal de administração.  
  Solução alternativa: Use o PowerShell para exibir informações sobre unidades de escala. Para obter mais informações, consulte o [ajuda](https://docs.microsoft.com/powershell/azure/azure-stack/overview?view=azurestackps-1.3.0) conteúdo 1.3.0 do módulo de pilha do Azure. 

- <!-- 2332636 - IS -->  Quando você usar o AD FS para seu sistema de identidade de pilha do Azure e a atualização para esta versão da pilha do Azure, o proprietário padrão da assinatura do provedor padrão é redefinido para o interno **CloudAdmin** usuário.  
  Solução alternativa: Para resolver esse problema, depois de instalar esta atualização, use a etapa 3 do [na pilha do Azure de confiança do provedor de declarações de automação de gatilho para configurar](azure-stack-integrate-identity.md#trigger-automation-to-configure-claims-provider-trust-in-azure-stack-1) procedimento para redefinir o proprietário da assinatura do provedor padrão.   

- <!-- TBD - IS ASDK --> Alguns tipos de assinatura administrativas não estão disponíveis.  Quando você atualiza a pilha do Azure para esta versão, os tipos de duas assinatura foram [introduzida com a versão 1804](#new-features) não são visíveis no console. Isso é esperado. Os tipos de assinatura disponíveis são *medição assinatura*, e *assinatura consumo*. Esses tipos de assinatura são visíveis em novos ambientes de pilha do Azure começando com a versão 1804 mas ainda não estão prontos para uso. Você deve continuar a usar o *provedor padrão* tipo de assinatura.  


- <!-- TBD -  IS ASDK -->A capacidade de [para abrir uma nova solicitação de suporte na lista suspensa](azure-stack-manage-portals.md#quick-access-to-help-and-support) de dentro do administrador do portal não está disponível. Em vez disso, use o seguinte link:     
    - Para o Azure pilha sistemas integrados, use https://aka.ms/newsupportrequest.

- <!-- 2403291 - IS ASDK --> Você não pode ter o uso da barra de rolagem horizontal na parte inferior dos portais de administrador e usuário. Se você não pode acessar a barra de rolagem horizontal, use a trilha para navegar até um blade anterior no portal do selecionando o nome da folha você deseja exibir na lista de trilha de navegação na parte superior esquerda do portal.
  ![Navegação estrutural](media/azure-stack-update-1804/breadcrumb.png) 

- <!-- TBD - IS --> Ele não poderá exibir os recursos de computação ou armazenamento no portal do administrador. A causa desse problema é um erro durante a instalação da atualização que faz com que a atualização a ser informada incorretamente como bem-sucedido. Se esse problema ocorrer, entre em contato com o Atendimento Microsoft para obter assistência.

- <!-- TBD - IS --> Você pode ver um painel em branco no portal. Para recuperar o painel de controle, selecione o ícone de engrenagem no canto superior direito do portal e, em seguida, selecione **restaurar as configurações padrão**.

- <!-- TBD - IS ASDK --> Excluir resultados de assinaturas do usuário em recursos órfãos. Como alternativa, primeiro exclua os recursos do usuário ou o grupo de recursos inteiro e exclua assinaturas de usuário.

- <!-- TBD - IS ASDK --> Você não pode exibir as permissões para sua assinatura usando os portais de pilha do Azure. Como alternativa, use o PowerShell para verificar as permissões.

- <!-- TBD - IS ASDK --> No portal de administração, você poderá ver um alerta crítico para o *Microsoft.Update.Admin* componente. O nome do alerta, descrição e correção toda a exibição como:  
    - *Erro - o modelo para o FaultType ResourceProviderTimeout está ausente.*

  Esse alerta pode ser ignorado. 


#### <a name="health-and-monitoring"></a>Monitoramento e integridade
- <!-- 1264761 - IS ASDK -->  Você pode ver os alertas para o *controlador integridade* componente que tem os seguintes detalhes:  

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
- <!-- TBD - IS --> Ao selecionar um tamanho de máquina virtual para uma implantação de máquina virtual, alguns tamanhos de VM F-Series não são visíveis como parte do seletor de tamanho quando você cria uma máquina virtual. Os seguintes tamanhos VM não aparecem no seletor de: *F8s_v2*, *F16s_v2*, *F32s_v2*, e *F64s_v2*.  
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


- <!-- TBD - IS ASDK --> As configurações de escala para conjuntos de escala de máquinas virtuais não estão disponíveis no portal. Como alternativa, você pode usar [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). Por causa das diferenças de versão do PowerShell, você deve usar o `-Name` parâmetro em vez de `-VMScaleSetName`.

- <!-- TBD - IS --> Quando você cria uma conjunto de disponibilidade no portal do indo para **novo** > **de computação** > **conjunto de disponibilidade**, você pode criar apenas um conjunto de disponibilidade com um domínio de falha e o domínio de atualização de 1. Como alternativa, ao criar uma nova máquina virtual, crie a disponibilidade definida usando o PowerShell, CLI, ou de dentro do portal.

- <!-- TBD - IS ASDK --> Ao criar máquinas virtuais no portal do usuário de pilha do Azure, o portal exibe um número incorreto de discos de dados que pode se conectar a uma série D VM. Todas as séries de D VMs com suporte podem acomodar quantos discos de dados como a configuração do Azure.

- <!-- TBD - IS ASDK --> Quando uma imagem de VM Falha ao ser criado, um item com falha que você não pode excluir pode ser adicionado para a folha de computação de imagens VM.

  Como alternativa, crie uma nova imagem VM com um VHD fictício que pode ser criado pelo Hyper-V (New-VHD-caminho C:\dummy.vhd-fixa - SizeBytes 1 GB). Esse processo deve corrigir o problema que impede a exclusão do item com falha. Em seguida, 15 minutos depois de criar a imagem fictícia, você poderá com êxito excluí-la.

  Você pode tentar, em seguida, faça download novamente a imagem VM que falhou anteriormente.

- <!-- TBD - IS ASDK --> Se uma extensão em uma implantação de VM de provisionamento leva muito tempo, os usuários devem permitir que o tempo limite de provisionamento em vez de tentar parar o processo para desalocar ou exclua a máquina virtual.  

- <!-- 1662991 IS ASDK --> Não há suporte para o diagnóstico de VM do Linux na pilha do Azure. Quando você implanta uma VM do Linux com o diagnóstico VM habilitado, a implantação falhará. A implantação também falhará se você habilitar as métricas básicas de VM do Linux por meio de configurações de diagnóstico.  


#### <a name="networking"></a>Rede
- <!-- 1766332 - IS ASDK --> Em **rede**, se você clicar em **criar Gateway de VPN** para configurar uma conexão VPN, **com base na política** está listado como um tipo VPN. Não selecione essa opção. Somente o **rota com base em** opção tem suporte na pilha do Azure.

- <!-- 2388980 - IS ASDK --> Depois que uma máquina virtual é criada e associada a um endereço IP público, não é possível desassociar essa VM de endereço IP. Dissociação parece estar funcionando, mas o endereço IP público atribuído anteriormente permanecerá associado à VM original.

  No momento, você deve usar apenas novos endereços IP públicos para novas VMs que você criar.

  Esse comportamento ocorre mesmo se você reatribuir o endereço IP para uma nova VM (conhecido como um *permuta de VIP*). Todas as futuras tentativas de conexão por esse resultado do endereço IP em uma conexão à VM originalmente associado e não para o novo.

- <!-- 2292271 - IS ASDK --> Se você gerar um limite de cota para um recurso de rede que faz parte de uma oferta e o plano está associado uma assinatura de locatário, o novo limite não é aplicado a essa assinatura. No entanto, o novo limite se aplicam a novas assinaturas que são criadas depois que a cota é aumentada. 

  Para contornar esse problema, use um plano de complemento para aumentar a cota de rede quando o plano já está associado uma assinatura. Para obter mais informações, consulte como [disponibilizar um plano de complemento](azure-stack-subscribe-plan-provision-vm.md#to-make-an-add-on-plan-available).

- <!-- 2304134 IS ASDK --> Você não pode excluir uma assinatura que tem recursos de tabela de rotas associados a ele ou zona DNS. Para a assinatura foi excluída com êxito, exclua primeiro os recursos de zona DNS e a tabela de rotas da assinatura de locatário. 
  

- <!-- 1902460 - IS ASDK --> A pilha do Azure oferece suporte a um único *gateway de rede local* por endereço IP. Isso é verdadeiro em todas as assinaturas de locatários. Após a criação da primeira conexão de gateway local de rede, subsequente tenta criar um recurso de gateway de rede local com o mesmo endereço IP são bloqueados.

- <!-- 16309153 - IS ASDK --> Em uma rede Virtual que foi criado com uma configuração de servidor DNS de *automáticas*, alteração de uma falha de servidor DNS personalizado. As configurações atualizadas não são propagadas para as VMs nessa rede virtual.

- <!-- TBD - IS ASDK --> Pilha do Azure não oferece suporte à adição de interfaces de rede adicionais para uma instância de VM depois que a VM for implantada. Se a VM requer mais de uma interface de rede, eles devem ser definidos no momento da implantação.

- <!-- 2096388 IS --> Você não pode usar o portal de administração para atualizar as regras para um grupo de segurança de rede. 

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

- <!-- TBD - IS --> O provedor de recursos é suportado para criar itens em servidores que o host SQL ou MySQL. Itens criados em um servidor de host que não são criados pelo provedor de recursos podem resultar em um estado não correspondente.  

- <!-- IS, ASDK --> Caracteres especiais, incluindo espaços e pontos, não há suporte para o **família** ou **camada** nomes quando você cria uma SKU para os provedores de recursos do SQL e MySQL.


> [!NOTE]  
> <!-- TBD - IS --> Depois de atualizar para o Azure 1804 de pilha, você pode continuar a usar os provedores de recursos SQL e MySQL implantado anteriormente.  Recomendamos que você atualize o SQL e MySQL quando uma nova versão estiver disponível. Como a pilha do Azure, aplica atualizações a provedores de recursos do SQL e MySQL sequencialmente.  Por exemplo, se você usar a versão 1802, primeiro aplique versão 1803 e, em seguida, atualize para 1804.      
>   
> A instalação da atualização 1804 não afeta o uso atual de provedores de recursos do SQL ou MySQL pelos usuários.
> Independentemente da versão dos provedores de recurso que você usar, seus dados de usuários em seus bancos de dados não é tocados e permanecem acessíveis.    



#### <a name="app-service"></a>Serviço de Aplicativo
- <!-- 2352906 - IS ASDK --> Os usuários devem registrar o provedor de recursos de armazenamento antes de criar sua primeira função do Azure na assinatura.

- <!-- TBD - IS ASDK --> Para expandir a infraestrutura (os operadores, gerenciamento, funções de front-end), você deve usar o PowerShell conforme descrito nas notas de versão para a computação.

- <!-- TBD - IS ASDK --> Serviço de aplicativo só pode ser implantado no **assinatura do provedor padrão** neste momento.  Em uma atualização futura do serviço de aplicativo será implantado para a nova assinatura medição introduzido no Azure pilha 1804 e todas as implantações existentes serão migradas para essa nova assinatura também.

#### <a name="usage"></a>Uso  
- <!-- TBD - IS ASDK --> Dados de medição de uso do endereço de IP público uso mostram a mesma *EventDateTime* valor para cada registro em vez do *TimeDate* carimbo que mostra quando o registro foi criado. No momento, você não pode usar esses dados para realizar as estatísticas precisas de uso de endereço IP público.


<!-- #### Identity -->
<!-- #### Marketplace --> 


## <a name="download-the-update"></a>Baixe a atualização
Você pode baixar o pacote de atualização do Azure pilha 1804 [aqui](https://aka.ms/azurestackupdatedownload).


## <a name="see-also"></a>Consulte também
- Para usar o ponto de extremidade privilegiado (PEP) para monitorar e retomar as atualizações, consulte [monitorar atualizações na pilha do Azure usando o ponto de extremidade com privilégios](azure-stack-monitor-update.md).
- Para obter uma visão geral do gerenciamento de atualizações na pilha do Azure, consulte [gerenciar atualizações na visão geral do Azure pilha](azure-stack-updates.md).
- Para obter mais informações sobre como aplicar atualizações com pilha do Azure, consulte [aplicar as atualizações na pilha do Azure](azure-stack-apply-updates.md).
