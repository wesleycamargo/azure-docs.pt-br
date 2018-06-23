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
ms.date: 06/22/2018
ms.author: brenduns
ms.reviewer: justini
ms.openlocfilehash: a74e77f84aa70519015a589cbc6e7478c0c41592
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/22/2018
ms.locfileid: "36318802"
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

- Instalar **AzS Hotfix – 1.0.180312.1-Build 20180222.2** antes de aplicar a atualização 1803 de pilha do Azure. Esse hotfix atualiza o Windows Defender e está disponível quando você baixar atualizações para a pilha do Azure.

  Para instalar o hotfix, siga os procedimentos normais para [instalando atualizações para o Azure pilha](azure-stack-apply-updates.md). O nome da atualização é exibido como **AzS Hotfix – 1.0.180312.1**e inclui os seguintes arquivos: 
    - PUPackageHotFix_20180222.2-1.exe
    - PUPackageHotFix_20180222.2-1.bin
    - Metadata.xml

  Depois de carregar esses arquivos para uma conta de armazenamento e contêiner, execute a instalação do bloco de atualização no portal de administração. 
  
  Ao contrário das atualizações a pilha do Azure, instalar essa atualização não altera a versão da pilha do Azure. Para confirmar se essa atualização é instalada, exiba a lista de **atualizações instaladas**.



### <a name="new-features"></a>Novos recursos 
Esta atualização inclui os seguintes aprimoramentos e correções para a pilha do Azure.

- **Atualizar os segredos da pilha do Azure** - (contas e certificados). Para obter mais informações sobre como gerenciar segredos, consulte [girar segredos na pilha do Azure](azure-stack-rotate-secrets.md). 

- <!-- 1914853 --> **Redirecionamento automático para HTTPS** quando você usar HTTP para acessar os portais de administrador e usuário. Essa melhoria foi feita com base em [UserVoice](https://feedback.azure.com/forums/344565-azure-stack/suggestions/32205385-it-would-be-great-if-there-was-a-automatic-redirec) comentários para a pilha do Azure. 

- <!-- 2202621  --> **Acessar o Marketplace** – agora você pode abrir a pilha do Azure Marketplace usando o [+ novo](https://ms.portal.azure.com/#create/hub) opção dentro de portais de administração e o usuário da mesma maneira que faria em portais do Azure.
 
- <!-- 2202621 --> **Monitor do Azure** -Azure pilha adiciona [Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-azure-monitor) para os portais de administrador e usuário. Isso inclui gerenciadores de novo para métricas e registros de atividades. Para acessar esse Monitor do Azure de redes externas, porta **13012** deve estar aberta em configurações de firewall. Para obter mais informações sobre as portas necessárias pela pilha do Azure, consulte [integração do datacenter do Azure pilha - publicar pontos de extremidade](azure-stack-integrate-endpoints.md).

   Também como parte dessa alteração, em **mais serviços**, *logs de auditoria* agora aparece como *logs de atividade*. A funcionalidade é agora consistente com o portal do Azure. 

- <!-- 1664791 --> **Arquivos esparsos** - quando você adiciona uma nova imagem para a pilha do Azure, ou adiciona uma imagem por meio de distribuição do marketplace, a imagem é convertida em um arquivo esparso. Imagens que foram adicionadas antes de usar a versão da pilha do Azure 1803 não podem ser convertidas. Em vez disso, você deve usar a distribuição do marketplace reenviar essas imagens para tirar proveito desse recurso. 
 
   Arquivos esparsos são um formato de arquivo eficiente usado para reduzir o uso de espaço de armazenamento e melhorar a e/s.  Para obter mais informações, consulte [Fsutil esparso](https://docs.microsoft.com/windows-server/administration/windows-commands/fsutil-sparse) para o Windows Server. 

### <a name="fixed-issues"></a>Problemas corrigidos

- <!-- 1739988 --> O balanceamento de carga interno (ILB) agora manipula adequadamente endereços MAC para VMs de back-end, que faz com que o ILB descartar pacotes para a rede de back-end ao usar instâncias Linux na rede de back-end. ILB funciona bem com instâncias do Windows na rede de back-end. 

- <!-- 1805496 --> Um problema em que conexões VPN entre a pilha do Azure seria forem desconectadas devido a pilha do Azure usando diferentes configurações para a política de IKE que o Azure. Os valores de SALifetime (hora) e SALiftetime (Bytes) não são compatíveis com o Azure e foram alterados em 1803 para corresponder às configurações do Azure. O valor de SALifetime (segundos) antes de 1803 estava 14.400 e agora alterações 27.000 em 1803. O valor de SALifetime (Bytes) antes de 1803 foi 819,200 e 33,553,408 em 1803 as alterações.

- <!-- 2209262 --> O problema IP em que foi anteriormente visível no portal de; conexões VPN No entanto habilitando ou alternância de encaminhamento IP não tem efeito. O recurso é ativado por padrão e a capacidade de alterar isso ainda não tem suporte.  O controle foi removido do portal. 

- <!-- 1766332 --> Pilha do Azure não oferece suporte a Gateways de VPN com base em política, mesmo que a opção é exibida no Portal.  A opção foi removida do Portal. 

- <!-- 1868283 --> Pilha do Azure agora evita o redimensionamento de uma máquina virtual que é criada com discos dinâmicos. 

- <!-- 1756324 --> Dados de uso de máquinas virtuais agora são separados de hora em hora. Isso é consistente com o Azure. 

- <!--  2253274 --> O problema em que a folha de configurações para a rede virtual sub-redes em portais de administrador e usuário, Falha ao carregar. Como alternativa, use o PowerShell e o [AzureRmVirtualNetworkSubnetConfig Get](https://docs.microsoft.com/powershell/module/azurerm.network/get-azurermvirtualnetworksubnetconfig?view=azurermps-5.5.0) cmdlet para exibir e gerenciar essas informações.

- Quando você cria uma máquina virtual, a mensagem *não é possível exibir preço* deixará de aparecer ao escolher um tamanho para o tamanho da VM.

- Várias correções de desempenho, estabilidade, segurança e sistema operacional que é usado pela pilha do Azure.


### <a name="changes"></a>Alterações
- A maneira de alterar o estado de uma oferta recém-criado de *privada* para *pública* ou *encerrado* foi alterado. Para obter mais informações, consulte [criar uma oferta](azure-stack-create-offer.md).


### <a name="known-issues-with-the-update-process"></a>Problemas conhecidos com o processo de atualização    
<!-- 2328416 --> Durante a instalação da atualização 1803, pode haver tempo de inatividade do serviço de blob e serviços internos que usam o serviço blob. Isso inclui algumas operações de máquina virtual. Esse tempo de inatividade pode causar falhas de locatário operações ou alertas de serviços que não é possível acessar os dados. Esse problema é resolvido em si quando a atualização for concluída a instalação. 



### <a name="post-update-steps"></a>Etapas de pós-atualização
- Após a instalação do 1803, instale os Hotfixes aplicáveis. Para mais informações, consulte os seguintes artigos da base de dados de Conhecimento, bem como nosso [política manutenção](azure-stack-servicing-policy.md).

  - [KB 4341390 - Hotfix da pilha do Azure 1.0.180424.12](https://support.microsoft.com/en-us/help/4341390).

- Depois de instalar esta atualização, examine a configuração de firewall para garantir [portas necessárias](azure-stack-integrate-endpoints.md) estão abertas. Por exemplo, esta atualização introduz *Azure Monitor* que inclui uma alteração de logs de auditoria para logs de atividade. Com essa alteração, a porta 13012 agora é usada e deve também ser aberta.  


### <a name="known-issues-post-installation"></a>Problemas conhecidos (após a instalação)
A seguir são problemas conhecidos de pós-instalação para compilação **20180323.2**.

#### <a name="portal"></a>Portal
- <!-- 2332636 - IS -->  Quando você usar o AD FS para seu sistema de identidade de pilha do Azure e a atualização para esta versão da pilha do Azure, o proprietário padrão da assinatura do provedor padrão é redefinido para o interno **CloudAdmin** usuário.  
  Solução alternativa: Para resolver esse problema, depois de instalar esta atualização, use a etapa 3 do [na pilha do Azure de confiança do provedor de declarações de automação de gatilho para configurar](azure-stack-integrate-identity.md#trigger-automation-to-configure-claims-provider-trust-in-azure-stack-1) procedimento para redefinir o proprietário da assinatura do provedor padrão.   

- A capacidade de [para abrir uma nova solicitação de suporte na lista suspensa](azure-stack-manage-portals.md#quick-access-to-help-and-support) de dentro do administrador do portal não está disponível. Em vez disso, use o seguinte link:     
    - Para o Azure pilha sistemas integrados, use https://aka.ms/newsupportrequest.

- <!-- 2050709 --> No portal de administração, não é possível editar as métricas de armazenamento para o serviço Blob, o serviço de tabela ou o serviço de fila. Quando você vá para o armazenamento e, em seguida, selecione o blob, tabela ou bloco de serviço de fila, uma nova folha que exibe um gráfico de métricas para esse serviço é aberto. Se você selecionar Editar da parte superior do bloco de gráfico de métricas, a folha de editar gráfico abre, mas não exibir as opções para editar as métricas.

- Ele não poderá exibir os recursos de computação ou armazenamento no portal do administrador. A causa desse problema é um erro durante a instalação da atualização que faz com que a atualização a ser informada incorretamente como bem-sucedido. Se esse problema ocorrer, entre em contato com o Atendimento Microsoft para obter assistência.

- Você pode ver um painel em branco no portal. Para recuperar o painel de controle, selecione o ícone de engrenagem no canto superior direito do portal e, em seguida, selecione **restaurar as configurações padrão**.

- Excluir resultados de assinaturas do usuário em recursos órfãos. Como alternativa, primeiro exclua os recursos do usuário ou o grupo de recursos inteiro e exclua assinaturas de usuário.

- Você não pode exibir as permissões para sua assinatura usando os portais de pilha do Azure. Como alternativa, use o PowerShell para verificar as permissões.

- No painel do portal de administração, o bloco de atualização não pode exibir informações sobre atualizações. Para resolver esse problema, clique no bloco para atualizá-lo.

- No portal de administração, você poderá ver um alerta crítico para o *Microsoft.Update.Admin* componente. O nome do alerta, descrição e correção toda a exibição como:  
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


#### <a name="marketplace"></a>Marketplace
- Usuários podem procurar o marketplace completo sem uma assinatura e podem ver itens administrativos como planos e ofertas. Esses itens não estão funcionando para os usuários.



#### <a name="compute"></a>Computação
- As configurações de escala para conjuntos de escala de máquinas virtuais não estão disponíveis no portal. Como alternativa, você pode usar [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). Por causa das diferenças de versão do PowerShell, você deve usar o `-Name` parâmetro em vez de `-VMScaleSetName`.

- Quando você cria uma conjunto de disponibilidade no portal do indo para **novo** > **de computação** > **conjunto de disponibilidade**, você pode criar apenas um conjunto de disponibilidade com um domínio de falha e o domínio de atualização de 1. Como alternativa, ao criar uma nova máquina virtual, crie a disponibilidade definida usando o PowerShell, CLI, ou de dentro do portal.

- Ao criar máquinas virtuais no portal do usuário de pilha do Azure, o portal exibe um número incorreto de discos de dados que pode se conectar a uma série D VM. Todas as séries de D VMs com suporte podem acomodar quantos discos de dados como a configuração do Azure.

- Quando uma imagem de VM Falha ao ser criado, um item com falha que você não pode excluir pode ser adicionado para a folha de computação de imagens VM.

  Como alternativa, crie uma nova imagem VM com um VHD fictício que pode ser criado pelo Hyper-V (New-VHD-caminho C:\dummy.vhd-fixa - SizeBytes 1 GB). Esse processo deve corrigir o problema que impede a exclusão do item com falha. Em seguida, 15 minutos depois de criar a imagem fictícia, você poderá com êxito excluí-la.

  Você pode tentar, em seguida, faça download novamente a imagem VM que falhou anteriormente.

-  Se uma extensão em uma implantação de VM de provisionamento leva muito tempo, os usuários devem permitir que o tempo limite de provisionamento em vez de tentar parar o processo para desalocar ou exclua a máquina virtual.  

- <!-- 1662991 --> Não há suporte para o diagnóstico de VM do Linux na pilha do Azure. Quando você implanta uma VM do Linux com o diagnóstico VM habilitado, a implantação falhará. A implantação também falhará se você habilitar as métricas básicas de VM do Linux por meio de configurações de diagnóstico.  


#### <a name="networking"></a>Rede
- Depois que uma máquina virtual é criada e associada a um endereço IP público, não é possível desassociar essa VM de endereço IP. Dissociação parece estar funcionando, mas o endereço IP público atribuído anteriormente permanecerá associado à VM original.

  No momento, você deve usar apenas novos endereços IP públicos para novas VMs que você criar.

  Esse comportamento ocorre mesmo se você reatribuir o endereço IP para uma nova VM (conhecido como um *permuta de VIP*). Todas as futuras tentativas de conexão por esse resultado do endereço IP em uma conexão à VM originalmente associado e não para o novo.



- A pilha do Azure oferece suporte a um único *gateway de rede local* por endereço IP. Isso é verdadeiro em todas as assinaturas de locatários. Após a criação da primeira conexão de gateway local de rede, subsequente tenta criar um recurso de gateway de rede local com o mesmo endereço IP são bloqueados.

- Em uma rede Virtual que foi criado com uma configuração de servidor DNS de *automáticas*, alteração de uma falha de servidor DNS personalizado. As configurações atualizadas não são propagadas para as VMs nessa rede virtual.

- Pilha do Azure não oferece suporte à adição de interfaces de rede adicionais para uma instância de VM depois que a VM for implantada. Se a VM requer mais de uma interface de rede, eles devem ser definidos no momento da implantação.

- <!-- 2096388 --> Você não pode usar o portal de administração para atualizar as regras para um grupo de segurança de rede. 

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

- <!-- IS, ASDK --> Caracteres especiais, incluindo espaços e pontos, não há suporte para o **família** nome quando você cria uma SKU para os provedores de recursos do SQL e MySQL.

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
