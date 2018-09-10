---
title: Atualização da pilha do Azure 1805 | Microsoft Docs
description: Saiba mais sobre quais são as novidades na atualização 1805 para sistemas integrados do Azure Stack, incluindo os problemas conhecidos e onde baixar a atualização.
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
ms.date: 08/27/2018
ms.author: brenduns
ms.reviewer: justini
ms.openlocfilehash: 4db0ce5e877f3054cc41e8940e8d9e672f7632c6
ms.sourcegitcommit: f6e2a03076679d53b550a24828141c4fb978dcf9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/27/2018
ms.locfileid: "43110341"
---
# <a name="azure-stack-1805-update"></a>Atualização da pilha 1805 do Azure

*Aplica-se a: sistemas integrados do Azure Stack*

Este artigo descreve os aperfeiçoamentos e correções no pacote de atualização de 1805, problemas conhecidos de nesta versão e onde baixar a atualização. Problemas conhecidos são divididos em questões relacionadas diretamente para o processo de atualização e os problemas com a compilação (após a instalação).

> [!IMPORTANT]        
> Este pacote de atualização é somente para os sistemas integrados do Azure Stack. Não aplique esse pacote de atualização para o Kit de desenvolvimento do Azure Stack.

## <a name="build-reference"></a>Referência de build    
É o número de compilação de atualização do Azure Stack 1805 **1.1805.1.47**.  

> [!TIP]  
> Com base nos comentários dos clientes, há uma atualização para o esquema de versão em uso para o Microsoft Azure Stack.  Começando com essa atualização, 1805, o novo esquema representa melhor a versão atual da nuvem.  
> 
> O esquema de versão agora é *Version.YearYearMonthMonth.MinorVersion.BuildNumber* onde os conjuntos de segundo e terceiro indicam a versão e a liberação. Por exemplo, 1805.1 representa o *lançamento para fabricação* versão (RTM) do 1805.  


### <a name="new-features"></a>Novos recursos
Esta atualização inclui os seguintes aprimoramentos para o Azure Stack.
<!-- 2297790 - IS, ASDK --> 
- **O Azure Stack agora inclui um *Syslog* cliente** como um *versão prévia do recurso*. Esse cliente permite o encaminhamento de logs de auditoria e segurança relacionados à infraestrutura do Azure Stack para um Syslog server ou segurança informações e eventos (SIEM) software de gerenciamento que é externo ao Azure Stack. Atualmente, o cliente de Syslog só dá suporte a conexões de UDP não autenticadas através da porta padrão 514. A carga de cada mensagem do Syslog é formatada em comum (CEF) do formato de evento. 

  Para configurar o cliente de Syslog, use o **SyslogServer conjunto** cmdlet exposta no ponto de extremidade com privilégios. 

  Com essa visualização, você poderá ver os três alertas a seguir. Quando apresentadas pela pilha do Azure, esses alertas incluem *descrições* e *correção* diretrizes. 
  - TÍTULO: Integridade de código desativado  
  - TÍTULO: Integridade do código no modo de auditoria 
  - TÍTULO: Conta de usuário criada

  Embora esse recurso está em visualização, ele não deve ser usado em ambientes de produção.   




### <a name="fixed-issues"></a>Problemas corrigidos

<!-- # - applicability -->
- Corrigimos o problema que bloqueados [abrir uma nova solicitação de suporte na lista suspensa](azure-stack-manage-portals.md#quick-access-to-help-and-support) de dentro do portal de administração. Essa opção agora funciona conforme o esperado. 

- **Várias correções de** para desempenho, estabilidade, segurança e o sistema operacional que é usado pelo Azure Stack.


<!-- # Additional releases timed with this update    -->



## <a name="before-you-begin"></a>Antes de começar    

### <a name="prerequisites"></a>Pré-requisitos
- Instalar o Azure Stack [1804 atualizar](azure-stack-update-1804.md) antes de aplicar a atualização do Azure Stack 1805.  
- Instalar a versão mais recente disponível [atualização ou hotfix para a versão 1804](azure-stack-update-1804.md#post-update-steps).   
- Antes de você iniciar a instalação da atualização 1805, execute [AzureStack teste](azure-stack-diagnostic-test.md) para validar o status do Azure Stack e resolva os problemas operacionais encontrados. Também examinar os alertas ativos e resolva todos os que exigem ação. 

### <a name="known-issues-with-the-update-process"></a>Problemas conhecidos com o processo de atualização   
- Durante a instalação da atualização 1805, você pode ver os alertas com o título *erro – o modelo para FaultType UserAccounts.New está ausente.*  Você pode ignorar com segurança esses alertas. Esses alertas serão fechada automaticamente após a atualização para 1805.   

- <!-- 2489559 - IS --> Não tente criar máquinas virtuais durante a instalação dessa atualização. Para obter mais informações sobre como gerenciar atualizações, consulte [gerenciar atualizações na visão geral do Azure Stack](azure-stack-updates.md#plan-for-updates).


### <a name="post-update-steps"></a>Etapas de pós-atualização
Após a instalação do 1805, instale os Hotfixes aplicáveis. Para obter mais informações, consulte os seguintes artigos de base de Conhecimento, bem como nossos [política de manutenção](azure-stack-servicing-policy.md).  
 - [KB 4344102 - o Azure Stack Hotfix 1.1805.7.57](https://support.microsoft.com/help/4344102).


## <a name="known-issues-post-installation"></a>Problemas conhecidos (após a instalação)
A seguir estão os problemas conhecidos de pós-instalação para esta versão de compilação.

### <a name="portal"></a>Portal  
- <!-- 2931230 – IS  ASDK --> Os planos são adicionados a uma assinatura de usuário como um plano de complemento não podem ser excluídos, mesmo quando você remove o plano da assinatura do usuário. O plano permanecerá até que as assinaturas que o plano de complemento de referência também são excluídas. 

- <!-- TBD - IS ASDK --> É possível aplicar as atualizações de driver por meio de um pacote de extensão de OEM com esta versão do Azure Stack.  Não há nenhuma solução alternativa para esse problema.

- <!-- 2551834 - IS, ASDK --> Quando você seleciona **visão geral** nos portais de administrador ou usuário, as informações da conta de armazenamento a *Essentials* painel não será exibido.  O painel do Essentials exibe informações sobre a conta, como seu *grupo de recursos*, *local*, e *ID da assinatura*.  Outras opções de visão geral são acessíveis como *serviços* e *monitoramento*, bem como opções para *abrir no Gerenciador de* ou *excluir conta de armazenamento* . 

  Para exibir as informações disponíveis, use o [Get-azureRMstorageaccount](https://docs.microsoft.com/powershell/module/azurerm.storage/get-azurermstorageaccount?view=azurermps-6.2.0) cmdlet do PowerShell. 

- <!-- 2551834 - IS, ASDK --> Quando você seleciona **marcas** para uma conta de armazenamento nos portais de administrador ou usuário, as informações de falha ao carregar e não será exibido.  

  Para exibir as informações disponíveis, use o [Get-AzureRmTag](https://docs.microsoft.com/powershell/module/azurerm.tags/get-azurermtag?view=azurermps-6.2.0) cmdlet do PowerShell.


- <!-- 2332636 - IS -->  Quando você usa o AD FS para o seu sistema de identidade do Azure Stack e a atualização para esta versão do Azure Stack, o proprietário padrão de assinatura do provedor padrão é redefinido para o interno **CloudAdmin** usuário.  
  Solução alternativa: Para resolver esse problema depois de instalar essa atualização, use a etapa 3 das [disparar automação configurar declarações de confiança do provedor no Azure Stack](azure-stack-integrate-identity.md#trigger-automation-to-configure-claims-provider-trust-in-azure-stack-1) procedimento para redefinir o proprietário da assinatura do provedor padrão.   

- <!-- TBD - IS ASDK --> Alguns tipos de assinatura administrativas não estão disponíveis.  Quando você atualizar o Azure Stack para esta versão, os tipos de assinatura de dois foram [introduzido na versão 1804](azure-stack-update-1804.md#new-features) não são visíveis no console. Isso é esperado. São os tipos de assinatura não está disponível *assinatura de medição*, e *assinatura de consumo*. Esses tipos de assinatura são visíveis em novos ambientes Azure Stack, começando com a versão 1804, mas ainda não estão prontos para uso. Você deve continuar a usar o *provedor padrão* tipo de assinatura.  

- <!-- 2403291 - IS ASDK --> Você pode não ter o uso da barra de rolagem horizontal na parte inferior do portais do administrador e usuário. Se você não pode acessar a barra de rolagem horizontal, use a trilha para navegar até uma folha anterior no portal, selecionando o nome da folha você quer exibir na lista de navegação estrutural na parte superior esquerda do portal.
  ![Navegação estrutural](media/azure-stack-update-1804/breadcrumb.png)

- <!-- TBD - IS --> Pode não ser possível exibir os recursos de computação ou armazenamento no portal do administrador. A causa desse problema é um erro durante a instalação da atualização que faz com que a atualização a ser relatado incorretamente como bem-sucedido. Se esse problema ocorrer, entre em contato com o Atendimento Microsoft para obter assistência.

- <!-- TBD - IS --> Você pode ver um painel em branco no portal. Para recuperar o painel, selecione o ícone de engrenagem no canto superior direito do portal e, em seguida, selecione **restaurar as configurações padrão**.

- <!-- TBD - IS ASDK --> Excluindo resultados de assinaturas do usuário em recursos órfãos. Como alternativa, primeiro exclua os recursos de usuário ou grupo de recursos inteiro e, em seguida, exclua as assinaturas do usuário.

- <!-- TBD - IS ASDK --> Você não pode exibir as permissões para sua assinatura usando os portais do Azure Stack. Como alternativa, use o PowerShell para verificar permissões.


### <a name="health-and-monitoring"></a>Monitoramento e integridade
- <!-- 1264761 - IS ASDK -->  Você pode ver os alertas para o *controlador de integridade* componente que tem os seguintes detalhes:  
- 
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

  Ambos os alertas 1 # e #2 podem ser ignorados e será fechado automaticamente ao longo do tempo. 

  Você também poderá ver o seguinte alerta para *capacidade*. Para esse alerta, a porcentagem de memória disponível identificada na descrição pode variar:  

  #3 do alerta:
   - NOME: Capacidade de memória baixa
   - GRAVIDADE: crítico
   - COMPONENTE: capacidade
   - Descrição: A região consumiu mais de 80,00% da memória disponível. Criação de máquinas virtuais com grandes quantidades de memória pode falhar.  

  Nesta versão do Azure Stack, esse alerta pode ser acionado incorretamente. Se as máquinas virtuais de locatário continuar a implantar com êxito, você pode ignorar com segurança este alerta. 
  
  Não fechar automaticamente alerta #3. Se você fechar este alerta do Azure Stack criará o mesmo alerta dentro de 15 minutos.  

- <!-- 2368581 - IS. ASDK --> Como um operador do Azure Stack, se você receber um alerta de memória insuficiente e máquinas virtuais de locatário não conseguir implantar com um *erro de criação de VM do Fabric*, é possível que o carimbo de data / Azure Stack está fora de memória disponível. Use o [Planejador de capacidade do Azure Stack](https://gallery.technet.microsoft.com/Azure-Stack-Capacity-24ccd822) para entender melhor a capacidade disponível para suas cargas de trabalho. 


### <a name="compute"></a>Computação
- <!-- TBD - IS, ASDK --> Ao selecionar um tamanho de máquina virtual para uma implantação de máquina virtual, alguns tamanhos de VM da série F não são visíveis como parte do seletor de tamanho quando você cria uma VM. Os seguintes tamanhos VM não aparecem no seletor de: *F8s_v2*, *F16s_v2*, *F32s_v2*, e *F64s_v2*.  
  Como alternativa, use um dos seguintes métodos para implantar uma VM. Em cada método, você precisará especificar o tamanho da VM que deseja usar.

  - **Modelo do Gerenciador de recursos do Azure:** quando você usa um modelo, defina a *vmSize* no modelo para ser igual ao tamanho VM que você deseja usar. Por exemplo, a seguinte entrada é usada para implantar uma VM que usa o *F32s_v2* tamanho:  

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

- <!-- TBD - IS ASDK --> Quando você cria máquinas virtuais no portal do usuário do Azure Stack, o portal exibe um número incorreto de discos de dados que podem anexar a uma VM da série DS. As VMs da série DS podem acomodar a tantos discos de dados como a configuração do Azure.

- <!-- TBD - IS ASDK --> Quando uma imagem de VM não consegue ser criado, um item com falha que não é possível excluir pode ser adicionado até a folha de computação de imagens VM.

  Como alternativa, crie uma nova imagem VM com um VHD fictício que pode ser criado por meio do Hyper-V (New-VHD-caminho C:\dummy.vhd-fixa - SizeBytes 1 GB). Esse processo deve corrigir o problema que impede a exclusão de item com falha. Em seguida, 15 minutos após a criação da imagem fictícia, você pode excluí-la.

  Em seguida, você pode tentar redownload a imagem da VM que falharam anteriormente.

- <!-- TBD - IS ASDK --> Se uma extensão em uma implantação de VM o provisionamento leva muito tempo, os usuários devem permitir que o tempo limite de provisionamento em vez de tentar interromper o processo para desaloque ou exclua a VM.  

- <!-- 1662991 IS ASDK --> Não há suporte para o diagnóstico de VM do Linux no Azure Stack. Quando você implanta uma VM do Linux com o diagnóstico VM habilitado, a implantação falhará. A implantação também falhará se você habilitar as métricas básicas da VM do Linux por meio das configurações de diagnóstico.  


### <a name="networking"></a>Rede
- <!-- TBD - IS ASDK --> É possível criar rotas definidas pelo usuário no portal do administrador ou usuário. Como alternativa, use [Azure PowerShell](https://docs.microsoft.com/azure/virtual-network/tutorial-create-route-table-powershell).

- <!-- 1766332 - IS ASDK --> Sob **Networking**, se você clicar em **criar o Gateway de VPN** para configurar uma conexão VPN, **com base na política** está listado como um tipo de VPN. Não selecione essa opção. Somente o **com base em rota** opção tem suporte no Azure Stack.

- <!-- 2388980 - IS ASDK --> Depois que uma VM é criada e associada com um endereço IP público, você não pode desassociar dessa VM para esse endereço IP. Dissociação parece funcionar, mas o endereço IP público atribuído anteriormente permanece associado com a VM original.

  Atualmente, você deve usar somente novos endereços IP públicos para novas VMs que você cria.

  Esse comportamento ocorre mesmo se você reatribuir o endereço IP para uma nova VM (conhecido como um *permuta de VIP*). Todas as futuras tenta se conectar por meio desse resultado de endereço IP em uma conexão para a VM original e não para o novo.

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


### <a name="sql-and-mysql"></a>SQL e MySQL

- <!-- TBD - IS --> O provedor de recursos é suportado para criar itens em servidores, esse host SQL ou MySQL. Criado em um servidor de host de itens que não são criados pelo provedor de recursos podem resultar em um estado incompatível.  

- <!-- IS, ASDK --> Caracteres especiais, incluindo espaços e pontos, não há suporte para o **família** ou **camada** nomes quando você criar uma SKU para os provedores de recursos do SQL e MySQL.


> [!NOTE]  
> <!-- TBD - IS --> Depois de atualizar para o Azure Stack 1805, você pode continuar a usar os provedores de recursos SQL e MySQL implantado anteriormente.  É recomendável que você atualize o SQL e MySQL quando uma nova versão está disponível. Como o Azure Stack, aplica atualizações a provedores de recursos do SQL e MySQL em sequência. Por exemplo, se você usar a versão 1803, primeiro aplique versão 1804 e, em seguida, atualize para 1805.      
>   
> A instalação da atualização 1805 não afeta o uso atual de provedores de recursos do SQL ou MySQL pelos usuários.
> Independentemente da versão dos provedores de recursos que você usar, seus dados de usuários em seus bancos de dados não não tocados e permanecem acessíveis.    



### <a name="app-service"></a>Serviço de Aplicativo
- <!-- 2352906 - IS ASDK --> Os usuários devem registrar o provedor de recursos de armazenamento antes de criar sua primeira função do Azure na assinatura.

- <!-- 2489178 - IS ASDK --> Para escalar horizontalmente a infraestrutura (funcionários, gerenciamento, as funções front-end), você deve usar o PowerShell conforme descrito nas notas de versão para a computação.

- <!-- TBD - IS ASDK --> O serviço de aplicativo só pode ser implantado para o *assinatura do provedor padrão* neste momento. Em uma atualização futura, o serviço de aplicativo será implantado no novo *medição assinatura* que foi introduzido no Azure Stack 1804. Quando a medição é suporte para uso, todas as implantações existentes serão migradas para esse novo tipo de assinatura.


### <a name="usage"></a>Uso  
- <!-- TBD - IS ASDK --> Dados de medidor uso do endereço de IP público de uso mostram a mesma *EventDateTime* valor para cada registro, em vez da *TimeDate* carimbo que mostra que o registro foi criado. Atualmente, é possível usar esses dados para realizar as estatísticas precisas de uso de endereço IP público.


<!-- #### Identity -->
<!-- #### Marketplace -->


## <a name="download-the-update"></a>Baixe a atualização
Você pode baixar o pacote de atualização no Azure Stack 1805 [aqui](https://aka.ms/azurestackupdatedownload).


## <a name="see-also"></a>Consulte também
- Para usar o ponto de extremidade com privilégios (PEP) para monitorar e retomar as atualizações, consulte [monitorar as atualizações no Azure Stack usando o ponto de extremidade com privilégios](azure-stack-monitor-update.md).
- Para uma visão geral do gerenciamento de atualizações no Azure Stack, consulte [gerenciar atualizações na visão geral do Azure Stack](azure-stack-updates.md).
- Para obter mais informações sobre como aplicar atualizações com o Azure Stack, consulte [aplicar as atualizações no Azure Stack](azure-stack-apply-updates.md).
