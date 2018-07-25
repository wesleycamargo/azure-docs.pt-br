---
title: Atualização da pilha do Azure 1804 | Microsoft Docs
description: Saiba mais sobre as novidades na atualização para o Azure Stack 1804 integrada sistemas, os problemas conhecidos e onde baixar a atualização.
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
ms.date: 07/11/2018
ms.author: brenduns
ms.reviewer: justini
ms.openlocfilehash: 496aea1195885c582d3529d7ddb43210aad5fea1
ms.sourcegitcommit: df50934d52b0b227d7d796e2522f1fd7c6393478
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/12/2018
ms.locfileid: "38990125"
---
# <a name="azure-stack-1804-update"></a>Atualização do 1804 de pilha do Azure

*Aplica-se a: sistemas integrados do Azure Stack*

Este artigo descreve os aperfeiçoamentos e correções no pacote de atualização de 1804, problemas conhecidos de nesta versão e onde baixar a atualização. Problemas conhecidos são divididos em questões relacionadas diretamente para o processo de atualização e os problemas com a compilação (após a instalação).

> [!IMPORTANT]        
> Este pacote de atualização é somente para os sistemas integrados do Azure Stack. Não aplique esse pacote de atualização para o Kit de desenvolvimento do Azure Stack.

## <a name="build-reference"></a>Referência de build    
É o número de compilação de atualização do Azure Stack 1804 **20180513.1**.   

### <a name="new-features"></a>Novos recursos
Esta atualização inclui os seguintes aprimoramentos para o Azure Stack.

- <!-- 15028744 - IS -->  **Suporte do Visual Studio para implantações de pilha do Azure desconectados usando o AD FS**. Dentro do Visual Studio, você agora pode adicionar assinaturas e autenticar usando o AD FS federated credenciais do usuário. 
 
- <!-- 1779474, 1779458 - IS --> **Usar máquinas virtuais da série Av2 e F**. O Azure Stack agora pode usar máquinas virtuais baseadas em tamanhos de máquinas virtuais de série Av2 e da série F. Para obter mais informações, consulte [tamanhos de máquina Virtual com suporte no Azure Stack](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-vm-sizes). 

- <!-- 1759172 - IS, ASDK --> **Novas assinaturas administrativas**. Com 1804 há dois novos tipos de assinatura disponíveis no portal. Esses novos tipos de assinatura são além de assinatura do provedor padrão e visíveis em novas instalações do Azure Stack, começando com a versão 1804. *Não use esses novos tipos de assinatura com esta versão do Azure Stack*. Anunciaremos a disponibilidade para usar esses tipos de assinatura com uma atualização futura. 

  Se você atualizar o Azure Stack para a versão 1804, dois novos tipos de assinatura não são visíveis. No entanto, sistemas integrados de novas implantações do Azure Stack e instalações da versão do Kit de desenvolvimento do Azure Stack 1804 ou posterior têm acesso a todos os tipos de assinatura de três.  

  Esses novos tipos de assinatura são parte de uma alteração maior para proteger a assinatura do provedor padrão e para torná-lo mais fácil de implantar recursos compartilhados, como servidores de hospedagem SQL. Como podemos adicionar mais partes dessa alteração maior com futuras atualizações para o Azure Stack, recursos implantados sob esses novos tipos de assinatura podem ser perdidos. 

  Os tipos de assinatura de três agora visíveis são:  
  - Padrão de assinatura do provedor: continuar a usar este tipo de assinatura. 
  - Medição de assinatura: *não use este tipo de assinatura.*
  - Assinatura de consumo: *não use este tipo de assinatura*

  



## <a name="fixed-issues"></a>Problemas corrigidos

- <!-- IS, ASDK -->  No portal de administração, você não precisa atualizar o bloco de atualização antes de exibir informações.
 
- <!-- 2050709  -->  Agora você pode usar o portal de administração para editar as métricas de armazenamento para o serviço Blob, o serviço tabela e o serviço fila.
 
- <!-- IS, ASDK --> Sob **Networking**, quando você clica em **Conexão** para configurar uma conexão VPN, **Site a site (IPsec)** agora é a única opção disponível.

- **Várias correções de** para desempenho, estabilidade, segurança e o sistema operacional que é usado pelo Azure Stack.

## <a name="additional-releases-timed-with-this-update"></a>Versões adicionais atingiu o tempo com essa atualização  
O exemplo a seguir agora está disponíveis, mas não exige a atualização do Azure Stack 1804.
- **Atualizar para o pacote de monitoramento do Microsoft Azure Stack System Center Operations Manager**. Uma nova versão (1.0.3.0) do pacote Microsoft System Center Operations Manager de monitoramento para o Azure Stack está disponível para [baixar](https://www.microsoft.com/download/details.aspx?id=55184). Com esta versão, você pode usar as entidades de serviço quando você adiciona uma implantação do Azure Stack conectada. Esta versão também apresenta uma experiência de gerenciamento de atualizações que permite que você aproveite a ação de correção diretamente de dentro do Operations Manager. Também há novos painéis que exibem provedores de recursos, unidades de escala e dimensionar nós de unidade.

- **Novo Azure PowerShell versão 1.3.0 do administrador de pilha**.  O Azure Stack PowerShell 1.3.0 agora está disponível para instalação. Esta versão fornece comandos para todos os provedores de recursos de administração gerenciar o Azure Stack.  Com esta versão, algum conteúdo será preterido a partir do GitHub de ferramentas do Azure Stack [repositório](https://github.com/Azure/AzureStack-Tools). 

   Para obter detalhes de instalação, execute as [instruções](azure-stack-powershell-install.md) ou o [ajuda](https://docs.microsoft.com/powershell/azure/azure-stack/overview?view=azurestackps-1.3.0) conteúdo para o módulo do Azure Stack 1.3.0. 

- **Versão inicial do Azure Stack referência de Rest API**. O [referência de API para todos os provedores de recursos de administração do Azure Stack](https://docs.microsoft.com/rest/api/azure-stack/) agora está publicada. 


## <a name="before-you-begin"></a>Antes de começar    

### <a name="prerequisites"></a>Pré-requisitos
- Instalar o Azure Stack [1803 atualizar](azure-stack-update-1803.md) antes de aplicar a atualização do 1804 de pilha do Azure.    

### <a name="known-issues-with-the-update-process"></a>Problemas conhecidos com o processo de atualização   
- Durante a instalação da atualização 1804, você pode ver os alertas com o título *erro – o modelo para FaultType UserAccounts.New está ausente.*  Você pode ignorar com segurança esses alertas. Esses alertas serão fechada automaticamente após a atualização para 1804.   
 
- <!-- TBD - IS --> Não tente criar máquinas virtuais durante a instalação dessa atualização. Para obter mais informações sobre como gerenciar atualizações, consulte [gerenciar atualizações na visão geral do Azure Stack](azure-stack-updates.md#plan-for-updates).


### <a name="post-update-steps"></a>Etapas de pós-atualização
Após a instalação do 1804, instale os Hotfixes aplicáveis. Para obter mais informações, consulte os seguintes artigos de base de Conhecimento, bem como nossos [política de manutenção](azure-stack-servicing-policy.md).  
 - [KB 4344114 - o Azure Stack Hotfix 1.0.180527.15](https://support.microsoft.com/help/4344114).




### <a name="known-issues-post-installation"></a>Problemas conhecidos (após a instalação)
Estes são problemas conhecidos de pós-instalação para compilação **20180513.1**.

#### <a name="portal"></a>Portal
- <!-- 1272111 - IS --> Depois de instalar ou atualizar para esta versão do Azure Stack, você não poderá exibir as unidades de escala do Azure Stack no portal de administração.  
  Solução alternativa: Use o PowerShell para exibir informações sobre unidades de escala. Para obter mais informações, consulte o [ajudar](https://docs.microsoft.com/powershell/azure/azure-stack/overview?view=azurestackps-1.3.0) conteúdo para o módulo do Azure Stack 1.3.0. 

- <!-- 2332636 - IS -->  Quando você usa o AD FS para o seu sistema de identidade do Azure Stack e a atualização para esta versão do Azure Stack, o proprietário padrão de assinatura do provedor padrão é redefinido para o interno **CloudAdmin** usuário.  
  Solução alternativa: Para resolver esse problema depois de instalar essa atualização, use a etapa 3 das [disparar automação configurar declarações de confiança do provedor no Azure Stack](azure-stack-integrate-identity.md#trigger-automation-to-configure-claims-provider-trust-in-azure-stack-1) procedimento para redefinir o proprietário da assinatura do provedor padrão.   

- <!-- TBD - IS ASDK --> Alguns tipos de assinatura administrativas não estão disponíveis.  Quando você atualizar o Azure Stack para esta versão, os tipos de assinatura de dois foram [introduzido na versão 1804](#new-features) não são visíveis no console. Isso é esperado. São os tipos de assinatura não está disponível *assinatura de medição*, e *assinatura de consumo*. Esses tipos de assinatura são visíveis em novos ambientes Azure Stack, começando com a versão 1804, mas ainda não estão prontos para uso. Você deve continuar a usar o *provedor padrão* tipo de assinatura.  


- <!-- TBD -  IS ASDK -->A capacidade [para abrir uma nova solicitação de suporte na lista suspensa](azure-stack-manage-portals.md#quick-access-to-help-and-support) de dentro do administrador do portal não está disponível. Em vez disso, use o seguinte link:     
    - Para o Azure Stack sistemas integrados, use https://aka.ms/newsupportrequest.

- <!-- 2403291 - IS ASDK --> Você pode não ter o uso da barra de rolagem horizontal na parte inferior do portais do administrador e usuário. Se você não pode acessar a barra de rolagem horizontal, use a trilha para navegar até uma folha anterior no portal, selecionando o nome da folha você quer exibir na lista de navegação estrutural na parte superior esquerda do portal.
  ![Navegação estrutural](media/azure-stack-update-1804/breadcrumb.png) 

- <!-- TBD - IS --> Pode não ser possível exibir os recursos de computação ou armazenamento no portal do administrador. A causa desse problema é um erro durante a instalação da atualização que faz com que a atualização a ser relatado incorretamente como bem-sucedido. Se esse problema ocorrer, entre em contato com o Atendimento Microsoft para obter assistência.

- <!-- TBD - IS --> Você pode ver um painel em branco no portal. Para recuperar o painel, selecione o ícone de engrenagem no canto superior direito do portal e, em seguida, selecione **restaurar as configurações padrão**.

- <!-- TBD - IS ASDK --> Excluindo resultados de assinaturas do usuário em recursos órfãos. Como alternativa, primeiro exclua os recursos de usuário ou grupo de recursos inteiro e, em seguida, exclua as assinaturas do usuário.

- <!-- TBD - IS ASDK --> Você não pode exibir as permissões para sua assinatura usando os portais do Azure Stack. Como alternativa, use o PowerShell para verificar permissões.

- <!-- TBD - IS ASDK --> No portal de administração, você poderá ver um alerta crítico para o *Microsoft.Update.Admin* componente. O nome do alerta, descrição e correção todos exibem como:  
    - *Erro - o modelo para FaultType ResourceProviderTimeout está ausente.*

  Esse alerta pode ser ignorado. 


#### <a name="health-and-monitoring"></a>Monitoramento e integridade
- <!-- 1264761 - IS ASDK -->  Você pode ver os alertas para o *controlador de integridade* componente que tem os seguintes detalhes:  

   #1 do alerta:
   - NOME: Função de infraestrutura não íntegro
   - GRAVIDADE: aviso
   - COMPONENTE: Controlador de integridade
   - Descrição: O Scanner de pulsação do controlador de integridade não está disponível. Isso pode afetar as métricas e relatórios de integridade.  

  #2 do alerta:
   - NOME: Função de infraestrutura não íntegro
   - GRAVIDADE: aviso
   - COMPONENTE: Controlador de integridade
   - Descrição: O Scanner de falha do controlador de integridade não está disponível. Isso pode afetar as métricas e relatórios de integridade.

  Ambos os alertas podem ser ignorados com segurança. Será fechada automaticamente ao longo do tempo.  
 

#### <a name="compute"></a>Computação
- <!-- TBD - IS --> Ao selecionar um tamanho de máquina virtual para uma implantação de máquina virtual, alguns tamanhos de VM da série F não são visíveis como parte do seletor de tamanho quando você cria uma VM. Os seguintes tamanhos VM não aparecem no seletor de: *F8s_v2*, *F16s_v2*, *F32s_v2*, e *F64s_v2*.  
  Como alternativa, use um dos seguintes métodos para implantar uma VM. Em cada método, você precisará especificar o tamanho da VM que deseja usar.

  - **Modelo do Gerenciador de recursos do Azure:** quando você usa um modelo, defina a *vmSize* no modelo para ser igual ao tamanho VM desejado. Por exemplo, o seguinte é usado para implantar uma VM que usa o *F32s_v2* tamanho:  

    ```
        "properties": {
        "hardwareProfile": {
                "vmSize": "Standard_F32s_v2"
        },
    ```  
  - **CLI do Azure:** você pode usar o [criar vm az](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-create) comando e especifique o tamanho da VM como um parâmetro, semelhante ao `--size "Standard_F32s_v2"`.

  - **PowerShell:** com o PowerShell, você pode usar [New-AzureRMVMConfig](https://docs.microsoft.com/powershell/module/azurerm.compute/new-azurermvmconfig?view=azurermps-6.0.0) com o parâmetro que especifica o tamanho da VM, semelhante ao `-VMSize "Standard_F32s_v2"`.


- <!-- TBD - IS ASDK --> As configurações de escala para conjuntos de dimensionamento de máquinas virtuais não estão disponíveis no portal. Como alternativa, você pode usar [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). Devido às diferenças de versão do PowerShell, você deve usar o `-Name` parâmetro em vez de `-VMScaleSetName`.

- <!-- TBD - IS --> Quando você cria uma conjunto de disponibilidade no portal acessando **New** > **computação** > **conjunto de disponibilidade**, você só pode criar um conjunto de disponibilidade com um domínio de falha e domínio de atualização de 1. Como alternativa, ao criar uma nova máquina virtual, crie a disponibilidade definida por meio do PowerShell, CLI, ou de dentro do portal.

- <!-- TBD - IS ASDK --> Quando você cria máquinas virtuais no portal do usuário do Azure Stack, o portal exibe um número incorreto de discos de dados que podem anexar a uma VM da série D. Todas as séries D com suporte VMs podem acomodar a tantos discos de dados como a configuração do Azure.

- <!-- TBD - IS ASDK --> Quando uma imagem de VM não consegue ser criado, um item com falha que não é possível excluir pode ser adicionado até a folha de computação de imagens VM.

  Como alternativa, crie uma nova imagem VM com um VHD fictício que pode ser criado por meio do Hyper-V (New-VHD-caminho C:\dummy.vhd-fixa - SizeBytes 1 GB). Esse processo deve corrigir o problema que impede a exclusão de item com falha. Em seguida, 15 minutos após a criação da imagem fictícia, você pode excluí-la.

  Em seguida, você pode tentar redownload a imagem da VM que falharam anteriormente.

- <!-- TBD - IS ASDK --> Se uma extensão em uma implantação de VM o provisionamento leva muito tempo, os usuários devem permitir que o tempo limite de provisionamento em vez de tentar interromper o processo para desaloque ou exclua a VM.  

- <!-- 1662991 IS ASDK --> Não há suporte para o diagnóstico de VM do Linux no Azure Stack. Quando você implanta uma VM do Linux com o diagnóstico VM habilitado, a implantação falhará. A implantação também falhará se você habilitar as métricas básicas da VM do Linux por meio das configurações de diagnóstico.  


#### <a name="networking"></a>Rede
- <!-- 1766332 - IS ASDK --> Sob **Networking**, se você clicar em **criar o Gateway de VPN** para configurar uma conexão VPN, **com base na política** está listado como um tipo de VPN. Não selecione essa opção. Somente o **com base em rota** opção tem suporte no Azure Stack.

- <!-- 2388980 - IS ASDK --> Depois que uma VM é criada e associada com um endereço IP público, você não pode desassociar dessa VM para esse endereço IP. Dissociação parece funcionar, mas o endereço IP público atribuído anteriormente permanece associado com a VM original.

  Atualmente, você deve usar somente novos endereços IP públicos para novas VMs que você cria.

  Esse comportamento ocorre mesmo se você reatribuir o endereço IP para uma nova VM (conhecido como um *permuta de VIP*). Todas as futuras tenta se conectar por meio desse resultado de endereço IP em uma conexão para a VM associada originalmente e não para um novo.

- <!-- 2292271 - IS ASDK --> Se você aumentar um limite de cota para um recurso de rede que faz parte de uma oferta e o plano que está associado uma assinatura de locatário, o novo limite não é aplicado a essa assinatura. No entanto, o novo limite se aplica às novas assinaturas que são criadas depois que a cota for aumentada. 

  Para contornar esse problema, use um plano de complemento para aumentar a cota de rede quando o plano já está associado uma assinatura. Para obter mais informações, consulte como [disponibilizar um plano de complemento](azure-stack-subscribe-plan-provision-vm.md#to-make-an-add-on-plan-available).

- <!-- 2304134 IS ASDK --> Você não pode excluir uma assinatura que tem recursos de zona DNS ou recursos de tabela de rotas associados a ele. Para excluir com êxito a assinatura, primeiro você deve excluir os recursos de zona DNS e a tabela de rotas da assinatura do locatário. 
  

- <!-- 1902460 - IS ASDK --> O Azure Stack dá suporte a uma única *gateway de rede local* por endereço IP. Isso é verdadeiro em todas as assinaturas de locatário. Após a criação da primeira conexão de gateway local de rede, subsequente tenta criar um recurso de gateway de rede local com o mesmo endereço IP são bloqueados.

- <!-- 16309153 - IS ASDK --> Em uma rede Virtual que foi criado com uma configuração de servidor DNS de *automática*, alterar para uma falha de servidor DNS personalizado. As configurações atualizadas não são propagadas para as VMs nessa rede virtual.

- <!-- TBD - IS ASDK --> O Azure Stack não dá suporte para adicionar outros adaptadores de rede a uma instância VM depois que a VM é implantada. Se a máquina virtual exigir mais de uma interface de rede, deve ser definidos no momento da implantação.

- <!-- 2096388 IS --> Você não pode usar o portal de administração para atualizar as regras para um grupo de segurança de rede. 

    Solução alternativa para o serviço de aplicativo: se você precisar de área de trabalho remota para as instâncias de controlador, você modificar as regras de segurança dentro dos grupos de segurança de rede com o PowerShell.  Estes são exemplos de como *permitem*e, em seguida, restaurar a configuração para *negar*:  
    
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

- <!-- TBD - IS --> O provedor de recursos é suportado para criar itens em servidores, esse host SQL ou MySQL. Criado em um servidor de host de itens que não são criados pelo provedor de recursos podem resultar em um estado incompatível.  

- <!-- IS, ASDK --> Caracteres especiais, incluindo espaços e pontos, não há suporte para o **família** ou **camada** nomes quando você criar uma SKU para os provedores de recursos do SQL e MySQL.


> [!NOTE]  
> <!-- TBD - IS --> Depois de atualizar para o Azure Stack 1804, você pode continuar a usar os provedores de recursos SQL e MySQL implantado anteriormente.  É recomendável que você atualize o SQL e MySQL quando uma nova versão está disponível. Como o Azure Stack, aplica atualizações a provedores de recursos do SQL e MySQL em sequência.  Por exemplo, se você usar a versão 1802, primeiro aplique versão 1803 e, em seguida, atualize para 1804.      
>   
> A instalação da atualização 1804 não afeta o uso atual de provedores de recursos do SQL ou MySQL pelos usuários.
> Independentemente da versão dos provedores de recursos que você usar, seus dados de usuários em seus bancos de dados não não tocados e permanecem acessíveis.    



#### <a name="app-service"></a>Serviço de Aplicativo
- <!-- 2352906 - IS ASDK --> Os usuários devem registrar o provedor de recursos de armazenamento antes de criar sua primeira função do Azure na assinatura.

- <!-- TBD - IS ASDK --> Para escalar horizontalmente a infraestrutura (funcionários, gerenciamento, as funções front-end), você deve usar o PowerShell conforme descrito nas notas de versão para a computação.

- <!-- TBD - IS ASDK --> O serviço de aplicativo só pode ser implantado na **assinatura do provedor padrão** neste momento.  Em uma atualização futura do serviço de aplicativo será implantado para a nova assinatura de medição introduzido no Azure Stack 1804 e todas as implantações existentes serão migradas para essa nova assinatura também.

#### <a name="usage"></a>Uso  
- <!-- TBD - IS ASDK --> Dados de medidor uso do endereço de IP público de uso mostram a mesma *EventDateTime* valor para cada registro, em vez da *TimeDate* carimbo que mostra que o registro foi criado. Atualmente, é possível usar esses dados para realizar as estatísticas precisas de uso de endereço IP público.


<!-- #### Identity -->
<!-- #### Marketplace --> 


## <a name="download-the-update"></a>Baixe a atualização
Você pode baixar o pacote de atualização no Azure Stack 1804 [aqui](https://aka.ms/azurestackupdatedownload).


## <a name="see-also"></a>Consulte também
- Para usar o ponto de extremidade com privilégios (PEP) para monitorar e retomar as atualizações, consulte [monitorar as atualizações no Azure Stack usando o ponto de extremidade com privilégios](azure-stack-monitor-update.md).
- Para uma visão geral do gerenciamento de atualizações no Azure Stack, consulte [gerenciar atualizações na visão geral do Azure Stack](azure-stack-updates.md).
- Para obter mais informações sobre como aplicar atualizações com o Azure Stack, consulte [aplicar as atualizações no Azure Stack](azure-stack-apply-updates.md).
