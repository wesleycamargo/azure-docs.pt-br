---
title: Atualização da pilha do Azure 1805 | Microsoft Docs
description: Saiba mais sobre as novidades na atualização 1805 para sistemas de pilha do Azure integradas, incluindo os problemas conhecidos e de onde baixar a atualização.
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
ms.date: 06/20/2018
ms.author: brenduns
ms.reviewer: justini
ms.openlocfilehash: 80ed0d2353fc6ea3a515c0d05475c713920abe46
ms.sourcegitcommit: 1438b7549c2d9bc2ace6a0a3e460ad4206bad423
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/20/2018
ms.locfileid: "36295740"
---
# <a name="azure-stack-1805-update"></a>Atualização de pilha 1805 do Azure

*Aplica-se a: sistemas integrados de pilha do Azure*

Este artigo descreve as melhorias e correções no pacote de atualização de 1805, problemas para esta versão e onde baixar a atualização. Problemas conhecidos são divididos em problemas relacionados diretamente para o processo de atualização e problemas com a compilação (após a instalação).

> [!IMPORTANT]        
> Este pacote de atualização é apenas para sistemas de pilha do Azure integradas. Não use este pacote de atualização para o Kit de desenvolvimento de pilha do Azure.

## <a name="build-reference"></a>Referência de build    
É o número de compilação de atualização do Azure pilha 1805 **1.1805.1.47**.  

> [!TIP]  
> Com base nos comentários dos clientes, há uma atualização para o esquema de versão em uso para a pilha do Microsoft Azure.  Começando com essa atualização, 1805, o novo esquema melhor representa a versão atual da nuvem.  
> 
> O esquema de versão é agora *Version.YearYearMonthMonth.MinorVersion.BuildNumber* onde os conjuntos de segundo e terceiro indicam a versão e a versão. Por exemplo, 1805.1 representa o *lançamento para fabricação* versão 1805 (RTM).  


### <a name="new-features"></a>Novos recursos
Esta atualização inclui os seguintes aprimoramentos para a pilha do Azure.
<!-- 2297790 - IS, ASDK --> 
- **A pilha do Azure agora inclui um *Syslog* cliente** como um *recurso de visualização*. Esse cliente permite que o encaminhamento de logs de auditoria e segurança relacionada à infraestrutura de pilha do Azure, um Syslog server ou segurança informações e eventos (SIEM) software de gerenciamento externo a pilha do Azure. No momento, o cliente de Syslog só dá suporte a conexões UDP não autenticadas através da porta padrão 514. A carga de cada mensagem Syslog é formatada em comum formato de evento (CEF). 

  Para configurar o cliente de Syslog, use o **SyslogServer conjunto** cmdlet exposta no ponto de extremidade com privilégios. 

  Com essa visualização, você poderá ver os seguintes três alertas. Quando for apresentada pela pilha do Azure, esses alertas incluem *descrições* e *correção* orientação. 
  - TÍTULO: Integridade do código desativado  
  - TÍTULO: Integridade do código no modo de auditoria 
  - TÍTULO: Conta de usuário criada

  Enquanto esse recurso estiver no modo de visualização, ele não deve ser usado em ambientes de produção.   




### <a name="fixed-issues"></a>Problemas corrigidos

<!-- # - applicability -->
- Corrigimos o problema que bloqueados [abrir uma nova solicitação de suporte na lista suspensa](azure-stack-manage-portals.md#quick-access-to-help-and-support) de dentro do portal de administração. Essa opção agora funciona conforme o esperado. 

- **Várias correções de** de desempenho, estabilidade, segurança e sistema operacional que é usado pela pilha do Azure.


<!-- # Additional releases timed with this update    -->



## <a name="before-you-begin"></a>Antes de começar    

### <a name="prerequisites"></a>Pré-requisitos
- Instalar a pilha do Azure [1804 atualizar](azure-stack-update-1804.md) antes de aplicar a atualização 1805 de pilha do Azure.    
- Antes de iniciar a instalação da atualização 1805, execute [AzureStack teste](azure-stack-diagnostic-test.md) para validar o status da pilha do Azure e resolver os problemas operacionais encontrados. Também examinar os alertas ativos e resolver qualquer que exigem ação. 

### <a name="known-issues-with-the-update-process"></a>Problemas conhecidos com o processo de atualização   
- Durante a instalação da atualização 1805, você pode ver os alertas com o título *erro – o modelo para o FaultType UserAccounts.New está ausente.*  Você pode ignorar com segurança esses alertas. Esses alertas serão fechado automaticamente após a atualização 1805.   

- <!-- 2489559 - IS --> Não tente criar máquinas virtuais durante a instalação dessa atualização. Para obter mais informações sobre como gerenciar atualizações, seSe [gerenciar atualizações na visão geral do Azure pilha](azure-stack-updates.md#plan-for-updates).


### <a name="post-update-steps"></a>Etapas de pós-atualização
Após a instalação do 1805, instale os Hotfixes aplicáveis. Para mais informações, consulte os seguintes artigos da base de dados de Conhecimento, bem como nosso [política manutenção](azure-stack-servicing-policy.md).  
 - [KB 4340474 - Hotfix da pilha do Azure 1.1805.4.53](https://support.microsoft.com/en-us/help/4340474).


## <a name="known-issues-post-installation"></a>Problemas conhecidos (após a instalação)
A seguir são problemas conhecidos de pós-instalação para esta versão de compilação.

### <a name="portal"></a>Portal  
- <!-- 2551834 - IS, ASDK --> Quando você seleciona **visão geral** para portais de administrador ou o usuário, as informações de uma conta de armazenamento a *Essentials* painel não será exibido.  O painel do Essentials exibe informações sobre a conta como seu *grupo de recursos*, *local*, e *ID da assinatura*.  Outras opções de visão geral são acessadas, como *serviços* e *monitoramento*, bem como as opções para *abrir no Gerenciador de* ou *excluir conta de armazenamento* . 

  Para exibir as informações disponíveis, use o [azureRMstorageaccount Get](https://docs.microsoft.com/powershell/module/azurerm.storage/get-azurermstorageaccount?view=azurermps-6.2.0) cmdlet do PowerShell. 

- <!-- 2551834 - IS, ASDK --> Quando você seleciona **marcas** para uma conta de armazenamento, o administrador ou o usuário portais, as informações de falha ao carregar e não exibe.  

  Para exibir as informações disponíveis, use o [AzureRmTag Get](https://docs.microsoft.com/powershell/module/azurerm.tags/get-azurermtag?view=azurermps-6.2.0) cmdlet do PowerShell.


- <!-- 2332636 - IS -->  Quando você usar o AD FS para seu sistema de identidade de pilha do Azure e a atualização para esta versão da pilha do Azure, o proprietário padrão da assinatura do provedor padrão é redefinido para o interno **CloudAdmin** usuário.  
  Solução alternativa: Para resolver esse problema, depois de instalar esta atualização, use a etapa 3 do [na pilha do Azure de confiança do provedor de declarações de automação de gatilho para configurar](azure-stack-integrate-identity.md#trigger-automation-to-configure-claims-provider-trust-in-azure-stack-1) procedimento para redefinir o proprietário da assinatura do provedor padrão.   

- <!-- TBD - IS ASDK --> Alguns tipos de assinatura administrativas não estão disponíveis.  Quando você atualiza a pilha do Azure para esta versão, os tipos de duas assinatura foram [introduzida com a versão 1804](azure-stack-update-1804.md#new-features) não são visíveis no console. Isso é esperado. Os tipos de assinatura disponíveis são *medição assinatura*, e *assinatura consumo*. Esses tipos de assinatura são visíveis em novos ambientes de pilha do Azure começando com a versão 1804 mas ainda não estão prontos para uso. Você deve continuar a usar o *provedor padrão* tipo de assinatura.  

- <!-- 2403291 - IS ASDK --> Você não pode ter o uso da barra de rolagem horizontal na parte inferior dos portais de administrador e usuário. Se você não pode acessar a barra de rolagem horizontal, use a trilha para navegar até um blade anterior no portal do selecionando o nome da folha você deseja exibir na lista de trilha de navegação na parte superior esquerda do portal.
  ![Navegação estrutural](media/azure-stack-update-1804/breadcrumb.png)

- <!-- TBD - IS --> Ele não poderá exibir os recursos de computação ou armazenamento no portal do administrador. A causa desse problema é um erro durante a instalação da atualização que faz com que a atualização a ser informada incorretamente como bem-sucedido. Se esse problema ocorrer, entre em contato com o Atendimento Microsoft para obter assistência.

- <!-- TBD - IS --> Você pode ver um painel em branco no portal. Para recuperar o painel de controle, selecione o ícone de engrenagem no canto superior direito do portal e, em seguida, selecione **restaurar as configurações padrão**.

- <!-- TBD - IS ASDK --> Excluir resultados de assinaturas do usuário em recursos órfãos. Como alternativa, primeiro exclua os recursos do usuário ou o grupo de recursos inteiro e exclua assinaturas de usuário.

- <!-- TBD - IS ASDK --> Você não pode exibir as permissões para sua assinatura usando os portais de pilha do Azure. Como alternativa, use o PowerShell para verificar as permissões.


### <a name="health-and-monitoring"></a>Monitoramento e integridade
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

  Ambos os alertas podem ser ignorados com segurança e vai fechar automaticamente ao longo do tempo.  

- <!-- 2368581 - IS. ASDK --> Um operador de pilha do Azure, se você receber um alerta de memória insuficiente e máquinas virtuais de locatário Falha na implantação com um *erro de criação de malha VM*, é possível que o carimbo de pilha do Azure está fora da memória disponível. Use o [Planejador de capacidade de pilha do Azure](https://gallery.technet.microsoft.com/Azure-Stack-Capacity-24ccd822) para entender melhor a capacidade disponível para suas cargas de trabalho. 


### <a name="compute"></a>Computação
- <!-- TBD - IS, ASDK --> Ao selecionar um tamanho de máquina virtual para uma implantação de máquina virtual, alguns tamanhos de VM F-Series não são visíveis como parte do seletor de tamanho quando você cria uma máquina virtual. Os seguintes tamanhos VM não aparecem no seletor de: *F8s_v2*, *F16s_v2*, *F32s_v2*, e *F64s_v2*.  
  Como alternativa, use um dos métodos a seguir para implantar uma máquina virtual. Cada método, você precisa especificar o tamanho da VM que deseja usar.

  - **Modelo do Gerenciador de recursos do Azure:** quando você usa um modelo, defina o *vmSize* no modelo para igualar o tamanho da VM que deseja usar. Por exemplo, a seguinte entrada é usada para implantar uma VM que utilize o *F32s_v2* tamanho:  

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

- <!-- TBD - IS ASDK --> Ao criar máquinas virtuais no portal do usuário de pilha do Azure, o portal exibe um número incorreto de discos de dados que podem ser anexados a uma VM da série DS. As VMs da série DS podem acomodar quantos discos de dados como a configuração do Azure.

- <!-- TBD - IS ASDK --> Quando uma imagem de VM Falha ao ser criado, um item com falha que você não pode excluir pode ser adicionado para a folha de computação de imagens VM.

  Como alternativa, crie uma nova imagem VM com um VHD fictício que pode ser criado pelo Hyper-V (New-VHD-caminho C:\dummy.vhd-fixa - SizeBytes 1 GB). Esse processo deve corrigir o problema que impede a exclusão do item com falha. Em seguida, 15 minutos depois de criar a imagem fictícia, você poderá com êxito excluí-la.

  Você pode tentar, em seguida, faça download novamente a imagem VM que falhou anteriormente.

- <!-- TBD - IS ASDK --> Se uma extensão em uma implantação de VM de provisionamento leva muito tempo, os usuários devem permitir que o tempo limite de provisionamento em vez de tentar parar o processo para desalocar ou exclua a máquina virtual.  

- <!-- 1662991 IS ASDK --> Não há suporte para o diagnóstico de VM do Linux na pilha do Azure. Quando você implanta uma VM do Linux com o diagnóstico VM habilitado, a implantação falhará. A implantação também falhará se você habilitar as métricas básicas de VM do Linux por meio de configurações de diagnóstico.  


### <a name="networking"></a>Rede
- <!-- TBD - IS ASDK --> Você não pode criar rotas definidas pelo usuário no portal do administrador ou usuário. Como alternativa, use [Azure PowerShell](https://docs.microsoft.com/azure/virtual-network/tutorial-create-route-table-powershell).

- <!-- 1766332 - IS ASDK --> Em **rede**, se você clicar em **criar Gateway de VPN** para configurar uma conexão VPN, **com base na política** está listado como um tipo VPN. Não selecione essa opção. Somente o **rota com base em** opção tem suporte na pilha do Azure.

- <!-- 2388980 - IS ASDK --> Depois que uma máquina virtual é criada e associada a um endereço IP público, não é possível desassociar essa VM de endereço IP. Dissociação parece estar funcionando, mas o endereço IP público atribuído anteriormente permanecerá associado à VM original.

  No momento, você deve usar apenas novos endereços IP públicos para novas VMs que você criar.

  Esse comportamento ocorre mesmo se você reatribuir o endereço IP para uma nova VM (conhecido como um *permuta de VIP*). Todas as futuras tentativas de conexão por esse resultado do endereço IP em uma conexão para a máquina virtual original e não para o novo.

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


### <a name="sql-and-mysql"></a>SQL e MySQL

- <!-- TBD - IS --> O provedor de recursos é suportado para criar itens em servidores que o host SQL ou MySQL. Itens criados em um servidor de host que não são criados pelo provedor de recursos podem resultar em um estado não correspondente.  

- <!-- IS, ASDK --> Caracteres especiais, incluindo espaços e pontos, não há suporte para o **família** ou **camada** nomes quando você cria uma SKU para os provedores de recursos do SQL e MySQL.


> [!NOTE]  
> <!-- TBD - IS --> Depois de atualizar para o Azure 1805 de pilha, você pode continuar a usar os provedores de recursos SQL e MySQL implantado anteriormente.  Recomendamos que você atualize o SQL e MySQL quando uma nova versão estiver disponível. Como a pilha do Azure, aplica atualizações a provedores de recursos do SQL e MySQL sequencialmente. Por exemplo, se você usar a versão 1803, primeiro aplique versão 1804 e, em seguida, atualize para 1805.      
>   
> A instalação da atualização 1805 não afeta o uso atual de provedores de recursos do SQL ou MySQL pelos usuários.
> Independentemente da versão dos provedores de recurso que você usar, seus dados de usuários em seus bancos de dados não é tocados e permanecem acessíveis.    



### <a name="app-service"></a>Serviço de Aplicativo
- <!-- 2352906 - IS ASDK --> Os usuários devem registrar o provedor de recursos de armazenamento antes de criar sua primeira função do Azure na assinatura.

- <!-- 2489178 - IS ASDK --> Para expandir a infraestrutura (os operadores, gerenciamento, funções de front-end), você deve usar o PowerShell conforme descrito nas notas de versão para a computação.

- <!-- TBD - IS ASDK --> Serviço de aplicativo só pode ser implantado no *assinatura do provedor padrão* neste momento. Em uma atualização futura, o serviço de aplicativo será implantado para a nova *medição assinatura* que foi introduzido no Azure 1804 de pilha. Quando a medição é suportada para uso, todas as implantações existentes serão migradas para esse novo tipo de assinatura.


### <a name="usage"></a>Uso  
- <!-- TBD - IS ASDK --> Dados de medição de uso do endereço de IP público uso mostram a mesma *EventDateTime* valor para cada registro em vez do *TimeDate* carimbo que mostra quando o registro foi criado. No momento, você não pode usar esses dados para realizar as estatísticas precisas de uso de endereço IP público.


<!-- #### Identity -->
<!-- #### Marketplace -->


## <a name="download-the-update"></a>Baixe a atualização
Você pode baixar o pacote de atualização do Azure pilha 1805 [aqui](https://aka.ms/azurestackupdatedownload).


## <a name="see-also"></a>Consulte também
- Para usar o ponto de extremidade privilegiado (PEP) para monitorar e retomar as atualizações, consulte [monitorar atualizações na pilha do Azure usando o ponto de extremidade com privilégios](azure-stack-monitor-update.md).
- Para obter uma visão geral do gerenciamento de atualizações na pilha do Azure, consulte [gerenciar atualizações na visão geral do Azure pilha](azure-stack-updates.md).
- Para obter mais informações sobre como aplicar atualizações com pilha do Azure, consulte [aplicar as atualizações na pilha do Azure](azure-stack-apply-updates.md).
