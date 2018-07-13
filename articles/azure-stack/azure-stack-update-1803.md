---
title: Atualização da pilha do Azure 1803 | Microsoft Docs
description: Saiba mais sobre as novidades na atualização para o Azure Stack 1803 integrada sistemas, os problemas conhecidos e onde baixar a atualização.
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
ms.openlocfilehash: 11430a0d194a722c0c0520c936db3c08b1a6b863
ms.sourcegitcommit: df50934d52b0b227d7d796e2522f1fd7c6393478
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/12/2018
ms.locfileid: "38989564"
---
# <a name="azure-stack-1803-update"></a>Atualização do 1803 de pilha do Azure

*Aplica-se a: sistemas integrados do Azure Stack*

Este artigo descreve os aperfeiçoamentos e correções no pacote de atualização de 1803, problemas conhecidos de nesta versão e onde baixar a atualização. Problemas conhecidos são divididos em questões relacionadas diretamente para o processo de atualização e os problemas com a compilação (após a instalação).

> [!IMPORTANT]        
> Este pacote de atualização é somente para os sistemas integrados do Azure Stack. Não aplique esse pacote de atualização para o Kit de desenvolvimento do Azure Stack.

## <a name="build-reference"></a>Referência de build    
É o número de compilação de atualização do Azure Stack 1803 **20180329.1**.


## <a name="before-you-begin"></a>Antes de começar    
> [!IMPORTANT]    
> Não tente criar máquinas virtuais durante a instalação dessa atualização. Para obter mais informações sobre como gerenciar atualizações, consulte [gerenciar atualizações na visão geral do Azure Stack](azure-stack-updates.md#plan-for-updates).


### <a name="prerequisites"></a>Pré-requisitos
- Instalar o Azure Stack [1802 atualizar](azure-stack-update-1802.md) antes de aplicar a atualização do Azure Stack 1803.   

- Instale **AzS Hotfix – 1.0.180312.1-Build 20180222.2** antes de aplicar a atualização do Azure Stack 1803. Esse hotfix atualiza o Windows Defender e está disponível quando você baixar atualizações para o Azure Stack.

  Para instalar o hotfix, siga os procedimentos normais para [instalando atualizações para o Azure Stack](azure-stack-apply-updates.md). O nome da atualização aparece como **AzS Hotfix – 1.0.180312.1**e inclui os seguintes arquivos: 
    - PUPackageHotFix_20180222.2-1.exe
    - PUPackageHotFix_20180222.2-1.bin
    - Metadata.xml

  Depois de carregar esses arquivos para uma conta de armazenamento e um contêiner, execute a instalação do quadro de atualização no portal de administração. 
  
  Ao contrário das atualizações para o Azure Stack, instalar essa atualização não altera a versão do Azure Stack. Para confirmar se essa atualização é instalada, exiba a lista de **atualizações instaladas**.



### <a name="new-features"></a>Novos recursos 
Esta atualização inclui os seguintes aprimoramentos e correções para o Azure Stack.

- **Atualizar segredos do Azure Stack** - (contas e certificados). Para obter mais informações sobre o gerenciamento de segredos, consulte [girar segredos no Azure Stack](azure-stack-rotate-secrets.md). 

- <!-- 1914853 --> **Redirecionamento automático para HTTPS** quando você usa HTTP para acessar os portais de administrador e usuário. Essa melhoria foi feita com base na [UserVoice](https://feedback.azure.com/forums/344565-azure-stack/suggestions/32205385-it-would-be-great-if-there-was-a-automatic-redirec) comentários para o Azure Stack. 

- <!-- 2202621  --> **Acessar o Marketplace** – agora você pode abrir o Azure Stack Marketplace usando o [+ novo](https://ms.portal.azure.com/#create/hub) opção dentro os portais de administrador e usuário da mesma maneira que faria em portais do Azure.
 
- <!-- 2202621 --> **O Azure Monitor** -adiciona Azure Stack [do Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-azure-monitor) aos portais do administrador e usuário. Isso inclui novo explorers para métricas e logs de atividades. Para acessar este Azure Monitor de redes externas, a porta **13012** deve estar aberta em configurações de firewall. Para obter mais informações sobre as portas exigidas pelo Azure Stack, consulte [integração de datacenter do Azure Stack – publicar pontos de extremidade](azure-stack-integrate-endpoints.md).

   Também como parte dessa alteração, em **mais serviços**, *logs de auditoria* agora é exibido como *logs de atividade*. A funcionalidade agora está consistente com o portal do Azure. 

- <!-- 1664791 --> **Arquivos esparsos** - quando você adicionar uma nova imagem para o Azure Stack, ou adicionar uma imagem por meio de distribuição de mercado, a imagem é convertida em um arquivo esparso. Imagens que foram adicionadas antes de usar a versão 1803 do Azure Stack não podem ser convertidas. Em vez disso, você deve usar a sindicalização do marketplace para reenviar essas imagens para tirar proveito desse recurso. 
 
   Arquivos esparsos são um formato de arquivo eficiente usado para reduzir o uso do espaço de armazenamento e melhorar a e/s.  Para obter mais informações, consulte [Fsutil sparse](https://docs.microsoft.com/windows-server/administration/windows-commands/fsutil-sparse) para o Windows Server. 

### <a name="fixed-issues"></a>Problemas corrigidos

- <!-- 1739988 --> Balanceamento de carga interno (ILB) agora manipula corretamente a endereços MAC para VMs de back-end, que faz com que o ILB descartar pacotes para a rede de back-end ao usar instâncias do Linux na rede de back-end. ILB funciona bem com instâncias do Windows na rede de back-end. 

- <!-- 1805496 --> Um problema em que conexões VPN entre o Azure Stack seria forem desconectadas devido a pilha do Azure usando diferentes configurações para a política de IKE que o Azure. Os valores para SALifetime (hora) e SALiftetime (Bytes) não eram compatíveis com o Azure e foram alterados em 1803 para corresponder as configurações do Azure. O valor para SALifetime (segundos) antes do 1803 era 14.400 e agora as alterações 27.000 do 1803. O valor para SALifetime (Bytes) antes do 1803 era 819,200 e as alterações 33,553,408 do 1803.

- <!-- 2209262 --> O problema IP em que as conexões VPN foi anteriormente visíveis no portal; No entanto habilitar ou alternar o encaminhamento IP não tem efeito. O recurso é ativado por padrão e a capacidade de alterar isso ainda não tem suporte.  O controle foi removido do portal. 

- <!-- 1766332 --> O Azure Stack não dá suporte a Gateways de VPN com base em política, mesmo que a opção será exibida no Portal.  A opção foi removida do Portal. 

- <!-- 1868283 --> O Azure Stack agora evita que o redimensionamento de uma máquina virtual que é criada com discos dinâmicos. 

- <!-- 1756324 --> Dados de uso para máquinas virtuais agora estão separados em intervalos de horas. Isso é consistente com o Azure. 

- <!--  2253274 --> O problema em que a folha de configurações para as sub-redes de rede virtual nos portais do administrador e usuário, Falha ao carregar. Como alternativa, use o PowerShell e o [Get-AzureRmVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/azurerm.network/get-azurermvirtualnetworksubnetconfig?view=azurermps-5.5.0) cmdlet para exibir e gerenciar essas informações.

- Quando você cria uma máquina virtual, a mensagem *não é possível exibir preços* não aparece mais ao escolher um tamanho para o tamanho da VM.

- Várias correções de desempenho, estabilidade, segurança e o sistema operacional que é usado pelo Azure Stack.


### <a name="changes"></a>Alterações
- A maneira de alterar o estado de uma oferta recém-criada da *privados* para *pública* ou *encerrados* foi alterado. Para obter mais informações, consulte [criar uma oferta](azure-stack-create-offer.md).


### <a name="known-issues-with-the-update-process"></a>Problemas conhecidos com o processo de atualização    
<!-- 2328416 --> Durante a instalação da atualização 1803, pode haver tempo de inatividade do serviço de blob e os serviços internos que usam o serviço blob. Isso inclui algumas operações de máquina virtual. Esse tempo de inatividade pode causar falhas de locatário operações ou alertas de serviços que não é possível acessar os dados. Esse problema é resolvido em si quando a atualização é concluída a instalação. 



### <a name="post-update-steps"></a>Etapas de pós-atualização
- Após a instalação do 1803, instale os Hotfixes aplicáveis. Para obter mais informações, consulte os seguintes artigos de base de Conhecimento, bem como nossos [política de manutenção](azure-stack-servicing-policy.md).

  - [KB 4344115 - o Azure Stack Hotfix 1.0.180427.15](https://support.microsoft.com/help/4344115).

- Depois de instalar essa atualização, revise a configuração de firewall para garantir [as portas necessárias](azure-stack-integrate-endpoints.md) estão abertas. Por exemplo, essa atualização introduz *do Azure Monitor* que inclui uma alteração de logs de auditoria para os logs de atividade. Com essa alteração, a porta 13012 agora é usada e também deve estar aberta.  


### <a name="known-issues-post-installation"></a>Problemas conhecidos (após a instalação)
Estes são problemas conhecidos de pós-instalação para compilação **20180323.2**.

#### <a name="portal"></a>Portal
- <!-- 2332636 - IS -->  Quando você usa o AD FS para o seu sistema de identidade do Azure Stack e a atualização para esta versão do Azure Stack, o proprietário padrão de assinatura do provedor padrão é redefinido para o interno **CloudAdmin** usuário.  
  Solução alternativa: Para resolver esse problema depois de instalar essa atualização, use a etapa 3 das [disparar automação configurar declarações de confiança do provedor no Azure Stack](azure-stack-integrate-identity.md#trigger-automation-to-configure-claims-provider-trust-in-azure-stack-1) procedimento para redefinir o proprietário da assinatura do provedor padrão.   

- A capacidade [para abrir uma nova solicitação de suporte na lista suspensa](azure-stack-manage-portals.md#quick-access-to-help-and-support) de dentro do administrador do portal não está disponível. Em vez disso, use o seguinte link:     
    - Para o Azure Stack sistemas integrados, use https://aka.ms/newsupportrequest.

- <!-- 2050709 --> No portal de administração, não é possível editar métricas de armazenamento para o serviço Blob, serviço de tabela ou o serviço de fila. Quando você vá para o armazenamento e, em seguida, selecione o blob, tabela ou bloco de serviço de fila, uma nova folha será aberta que exibe um gráfico de métricas para o serviço. Se você selecionar Editar na parte superior do bloco de gráfico de métricas, a folha Editar gráfico abre, mas não exibe opções para editar as métricas.

- Pode não ser possível exibir os recursos de computação ou armazenamento no portal do administrador. A causa desse problema é um erro durante a instalação da atualização que faz com que a atualização a ser relatado incorretamente como bem-sucedido. Se esse problema ocorrer, entre em contato com o Atendimento Microsoft para obter assistência.

- Você pode ver um painel em branco no portal. Para recuperar o painel, selecione o ícone de engrenagem no canto superior direito do portal e, em seguida, selecione **restaurar as configurações padrão**.

- Excluindo resultados de assinaturas do usuário em recursos órfãos. Como alternativa, primeiro exclua os recursos de usuário ou grupo de recursos inteiro e, em seguida, exclua as assinaturas do usuário.

- Você não pode exibir as permissões para sua assinatura usando os portais do Azure Stack. Como alternativa, use o PowerShell para verificar permissões.

- No painel do portal de administração, o bloco de atualização não pode exibir informações sobre as atualizações. Para resolver esse problema, clique no bloco para atualizá-la.

- No portal de administração, você poderá ver um alerta crítico para o *Microsoft.Update.Admin* componente. O nome do alerta, descrição e correção todos exibem como:  
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


#### <a name="marketplace"></a>Marketplace
- Usuários podem procurar o marketplace completo sem uma assinatura e podem ver itens administrativos, como planos e ofertas. Esses itens não estão funcionando para os usuários.



#### <a name="compute"></a>Computação
- As configurações de escala para conjuntos de dimensionamento de máquinas virtuais não estão disponíveis no portal. Como alternativa, você pode usar [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). Devido às diferenças de versão do PowerShell, você deve usar o `-Name` parâmetro em vez de `-VMScaleSetName`.

- Quando você cria uma conjunto de disponibilidade no portal acessando **New** > **computação** > **conjunto de disponibilidade**, você só pode criar um conjunto de disponibilidade com um domínio de falha e domínio de atualização de 1. Como alternativa, ao criar uma nova máquina virtual, crie a disponibilidade definida por meio do PowerShell, CLI, ou de dentro do portal.

- Quando você cria máquinas virtuais no portal do usuário do Azure Stack, o portal exibe um número incorreto de discos de dados que podem anexar a uma VM da série D. Todas as séries D com suporte VMs podem acomodar a tantos discos de dados como a configuração do Azure.

- Quando uma imagem de VM não consegue ser criado, um item com falha que não é possível excluir pode ser adicionado até a folha de computação de imagens VM.

  Como alternativa, crie uma nova imagem VM com um VHD fictício que pode ser criado por meio do Hyper-V (New-VHD-caminho C:\dummy.vhd-fixa - SizeBytes 1 GB). Esse processo deve corrigir o problema que impede a exclusão de item com falha. Em seguida, 15 minutos após a criação da imagem fictícia, você pode excluí-la.

  Em seguida, você pode tentar redownload a imagem da VM que falharam anteriormente.

-  Se uma extensão em uma implantação de VM o provisionamento leva muito tempo, os usuários devem permitir que o tempo limite de provisionamento em vez de tentar interromper o processo para desaloque ou exclua a VM.  

- <!-- 1662991 --> Não há suporte para o diagnóstico de VM do Linux no Azure Stack. Quando você implanta uma VM do Linux com o diagnóstico VM habilitado, a implantação falhará. A implantação também falhará se você habilitar as métricas básicas da VM do Linux por meio das configurações de diagnóstico.  


#### <a name="networking"></a>Rede
- Depois que uma VM é criada e associada com um endereço IP público, você não pode desassociar dessa VM para esse endereço IP. Dissociação parece funcionar, mas o endereço IP público atribuído anteriormente permanece associado com a VM original.

  Atualmente, você deve usar somente novos endereços IP públicos para novas VMs que você cria.

  Esse comportamento ocorre mesmo se você reatribuir o endereço IP para uma nova VM (conhecido como um *permuta de VIP*). Todas as futuras tenta se conectar por meio desse resultado de endereço IP em uma conexão para a VM associada originalmente e não para um novo.



- O Azure Stack dá suporte a uma única *gateway de rede local* por endereço IP. Isso é verdadeiro em todas as assinaturas de locatário. Após a criação da primeira conexão de gateway local de rede, subsequente tenta criar um recurso de gateway de rede local com o mesmo endereço IP são bloqueados.

- Em uma rede Virtual que foi criado com uma configuração de servidor DNS de *automática*, alterar para uma falha de servidor DNS personalizado. As configurações atualizadas não são propagadas para as VMs nessa rede virtual.

- O Azure Stack não dá suporte para adicionar outros adaptadores de rede a uma instância VM depois que a VM é implantada. Se a máquina virtual exigir mais de uma interface de rede, deve ser definidos no momento da implantação.

- <!-- 2096388 --> Você não pode usar o portal de administração para atualizar as regras para um grupo de segurança de rede. 

    Solução alternativa para o serviço de aplicativo: se você precisar de área de trabalho remota para as instâncias de controlador, você modificar as regras de segurança dentro dos grupos de segurança de rede com o PowerShell.  Estes são exemplos de como *permitem*e, em seguida, restaurar a configuração para *negar*:  
    
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
- Antes de prosseguir, consulte a observação importante [antes de começar](#before-you-begin) perto do início essas notas de versão.

- Pode levar até uma hora antes dos usuários podem criar bancos de dados em uma nova implantação de SQL ou MySQL.

- O provedor de recursos é suportado para criar itens em servidores, esse host SQL ou MySQL. Criado em um servidor de host de itens que não são criados pelo provedor de recursos podem resultar em um estado incompatível.  

- <!-- IS, ASDK --> Caracteres especiais, incluindo espaços e pontos, não há suporte para o **família** nome quando você criar uma SKU para os provedores de recursos do SQL e MySQL.

> [!NOTE]  
> Depois de atualizar para o Azure Stack 1803, você pode continuar a usar os provedores de recursos SQL e MySQL implantado anteriormente.  É recomendável que você atualize o SQL e MySQL quando uma nova versão está disponível. Como o Azure Stack, aplica atualizações a provedores de recursos do SQL e MySQL em sequência.  Por exemplo, se você usar a versão 1711, primeiro aplique versão 1712 e, em seguida, 1802 e, em seguida, atualize para 1803.      
>   
> A instalação da atualização 1803 não afeta o uso atual de provedores de recursos do SQL ou MySQL pelos usuários.
> Independentemente da versão dos provedores de recursos que você usar, seus dados de usuários em seus bancos de dados não não tocados e permanecem acessíveis.    



#### <a name="app-service"></a>Serviço de Aplicativo
- Os usuários devem registrar o provedor de recursos de armazenamento antes de criar sua primeira função do Azure na assinatura.

- Para escalar horizontalmente a infraestrutura (funcionários, gerenciamento, as funções front-end), você deve usar o PowerShell conforme descrito nas notas de versão para a computação.


#### <a name="usage"></a>Uso  
- Dados de medidor uso do endereço de IP público de uso mostram a mesma *EventDateTime* valor para cada registro, em vez da *TimeDate* carimbo que mostra que o registro foi criado. Atualmente, é possível usar esses dados para realizar as estatísticas precisas de uso de endereço IP público.

<!--
#### Identity
-->



#### <a name="downloading-azure-stack-tools-from-github"></a>Baixar ferramentas do Azure Stack no GitHub
- Ao usar o *invoke-webrequest* das ferramentas de cmdlet do PowerShell para baixar o Azure Stack no Github, você receberá um erro:     
    -  *Invoke-webrequest: A solicitação foi anulada: não foi possível criar o canal seguro SSL/TLS.*     

  Esse erro ocorre devido a uma substituição de suporte do GitHub recente das Tlsv1 e Tlsv1.1 padrões de criptografia (o padrão para o PowerShell). Para obter mais informações, consulte [aviso de remoção de padrões de criptografia fraca](https://githubengineering.com/crypto-removal-notice/).

  Para resolver esse problema, adicione `[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12` na parte superior do script para forçar o console do PowerShell, use o TLSv1.2 ao baixar de repositórios do GitHub.


## <a name="download-the-update"></a>Baixe a atualização
Você pode baixar o pacote de atualização no Azure Stack 1803 [aqui](https://aka.ms/azurestackupdatedownload).


## <a name="see-also"></a>Consulte também
- Para usar o ponto de extremidade com privilégios (PEP) para monitorar e retomar as atualizações, consulte [monitorar as atualizações no Azure Stack usando o ponto de extremidade com privilégios](azure-stack-monitor-update.md).
- Para uma visão geral do gerenciamento de atualizações no Azure Stack, consulte [gerenciar atualizações na visão geral do Azure Stack](azure-stack-updates.md).
- Para obter mais informações sobre como aplicar atualizações com o Azure Stack, consulte [aplicar as atualizações no Azure Stack](azure-stack-apply-updates.md).
