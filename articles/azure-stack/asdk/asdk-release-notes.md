---
title: Notas de versão do Kit de desenvolvimento de pilha do Microsoft Azure | Microsoft Docs
description: Problemas conhecidos do Kit de desenvolvimento de pilha do Azure, correções e aprimoramentos.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2018
ms.author: brenduns
ms.reviewer: misainat
ms.openlocfilehash: 2aa6663ae598b32979411fd10e7bb8a2eacd21de
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/16/2018
---
# <a name="azure-stack-development-kit-release-notes"></a>Notas de versão do Kit de desenvolvimento de pilha do Azure
Essas notas de versão fornecem informações sobre problemas conhecidos no Kit de desenvolvimento de pilha do Azure, correções e aprimoramentos. Se você não tiver certeza de qual versão você está executando, você poderá [usar o portal para verificar](.\.\azure-stack-updates.md#determine-the-current-version).

> Mantenha-se atualizado com o que há de novo no ASDK assinando o [ ![RSS](./media/asdk-release-notes/feed-icon-14x14.png)](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#) [feed](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#).


## <a name="build-201805131"></a>Criar 20180513.1

### <a name="new-features"></a>Novos recursos 
Esta compilação inclui os seguintes aprimoramentos e correções para a pilha do Azure.  

- <!-- 1759172 - IS, ASDK --> **More granular administrative subscriptions**. With version 1804 and later, the Default Provider subscription is now complemented with two additional subscriptions. The additions facilitate separating the management of core infrastructure, additional resource providers, and workloads. The following three subscriptions are available:
  - *Padrão de assinatura do provedor de*. Use essa assinatura para somente a infraestrutura básica. Não implante recursos ou provedores de recursos nesta assinatura.
  - *Assinatura de medição*. Use essa assinatura para implantação do provedor de recursos. Os recursos implantados nesta assinatura não serão cobrados.
  - *Assinatura de consumo*. Use essa assinatura para qualquer outra carga de trabalho que você deseja implantar. Os recursos implantados aqui são cobrados preços de uso normal.


### <a name="fixed-issues"></a>Problemas corrigidos
- <!-- IS, ASDK -->  In the admin portal, you no longer have to refresh the Update tile before it displays information. 

- <!-- 2050709 - IS, ASDK -->  You can now use the admin portal to edit storage metrics for Blob service, Table service, and Queue service.

- <!-- IS, ASDK --> Under **Networking**, when you click **Connection** to set up a VPN connection, **Site-to-site (IPsec)** is now the only available option. 

- **Várias correções de** de desempenho, estabilidade, segurança e sistema operacional que é usado pela pilha do Azure

<!-- ### Changes  --> 
### <a name="additional-releases-timed-with-this-update"></a>Versões adicionais atingiu o tempo com essa atualização  
A seguir agora está disponíveis, mas não requerem a atualização da pilha do Azure 1804.
- **Atualizar para o pacote de monitoramento do Microsoft Azure pilha System Center Operations Manager**. Uma nova versão (1.0.3.0) do pacote Microsoft System Center Operations Manager monitoramento para a pilha do Azure está disponível para [baixar](https://www.microsoft.com/download/details.aspx?id=55184). Com esta versão, você pode usar entidades de serviço quando você adiciona uma implantação de pilha do Azure conectada. Esta versão também apresenta uma experiência de gerenciamento de atualização que permite que você realizar uma ação de correção diretamente de dentro do Operations Manager. Também há novos painéis que exibem provedores de recursos, unidade de escala e nós de unidade de escala.

- **Novo Azure pilha Admin PowerShell versão 1.2.12**.  O Azure PowerShell pilha 1.2.12 agora está disponível para instalação. Esta versão fornece comandos para todos os provedores de recursos de administração gerenciar a pilha do Azure.  Com esta versão, alguns tipos de conteúdo serão substituído do GitHub de ferramentas de pilha do Azure [repositório](https://github.com/Azure/AzureStack-Tools). 

   Para obter detalhes de instalação, execute o [instruções](.\.\azure-stack-powershell-install.md) ou [ajuda](https://docs.microsoft.com/powershell/azure/azure-stack/overview?view=azurestackps-1.2.12) conteúdo para o módulo do Azure pilha 1.2.12. 

- **Versão da referência de Rest de API do Azure pilha inicial**. A referência de API para todas as provedor de recursos do Azure pilha administrador agora está publicada.

### <a name="known-issues"></a>Problemas conhecidos
 
#### <a name="portal"></a>Portal
- <!-- TBD - IS ASDK --> The ability [to open a new support request from the dropdown](.\.\azure-stack-manage-portals.md#quick-access-to-help-and-support) from within the administrator portal isn’t available. Instead, use the following link:     
    - Para o Kit de desenvolvimento de pilha do Azure, use https://aka.ms/azurestackforum.    

- <!-- 2403291 - IS ASDK --> You might not have use of the horizontal scroll bar along the bottom of the admin and user portals. If you can’t access the horizontal scroll bar, use the breadcrumbs to navigate to a previous blade in the portal by selecting the name of the blade you want to view from the breadcrumb list found at the top left of the portal.
  ![Navegação estrutural](media/asdk-release-notes/breadcrumb.png)

- <!-- TBD -  IS ASDK --> Deleting user subscriptions results in orphaned resources. As a workaround, first delete user resources or the entire resource group, and then delete user subscriptions.

- <!-- TBD -  IS ASDK --> You cannot view permissions to your subscription using the Azure Stack portals. As a workaround, use PowerShell to verify permissions.

-   <!-- TBD -  IS ASDK --> In the admin portal, you might see a critical alert for the Microsoft.Update.Admin component. The Alert name, description, and remediation all display as:  
    - *Erro - o modelo para o FaultType ResourceProviderTimeout está ausente.*

    Esse alerta pode ser ignorado. 

#### <a name="compute"></a>Computação
- <!-- TBD -  IS ASDK --> Scaling settings for virtual machine scale sets are not available in the portal. As a workaround, you can use [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). Because of PowerShell version differences, you must use the `-Name` parameter instead of `-VMScaleSetName`.

- <!-- TBD -  IS ASDK --> When you create virtual machines on the Azure Stack user portal, the portal displays an incorrect number of data disks that can attach to a DS series VM. DS series VMs can accommodate as many data disks as the Azure configuration.

- <!-- TBD -  IS ASDK --> When a VM image fails to be created, a failed item that you cannot delete might be added to the VM images compute blade.

  Como alternativa, crie uma nova imagem VM com um VHD fictício que pode ser criado pelo Hyper-V (New-VHD-caminho C:\dummy.vhd-fixa - SizeBytes 1 GB). Esse processo deve corrigir o problema que impede a exclusão do item com falha. Em seguida, 15 minutos depois de criar a imagem fictícia, você poderá com êxito excluí-la.

  Você pode tentar, em seguida, faça download novamente a imagem VM que falhou anteriormente.

- <!-- TBD -  IS ASDK --> If provisioning an extension on a VM deployment takes too long, users should let the provisioning time-out instead of trying to stop the process to deallocate or delete the VM.  

- <!-- 1662991 - IS ASDK --> Linux VM diagnostics is not supported in Azure Stack. When you deploy a Linux VM with VM diagnostics enabled, the deployment fails. The deployment also fails if you enable the Linux VM basic metrics through diagnostic settings. 

#### <a name="networking"></a>Rede
- <!-- 1766332 - IS, ASDK --> Under **Networking**, if you click **Create VPN Gateway** to set up a VPN connection, **Policy Based** is listed as a VPN type. Do not select this option. Only the **Route Based** option is supported in Azure Stack.

- <!-- 2388980 -  IS ASDK --> After a VM is created and associated with a public IP address, you can't disassociate that VM from that IP address. Disassociation appears to work, but the previously assigned public IP address remains associated with the original VM.

  No momento, você deve usar apenas novos endereços IP públicos para novas VMs que você criar.

  Esse comportamento ocorre mesmo se você reatribuir o endereço IP para uma nova VM (conhecido como um *permuta de VIP*). Todas as futuras tentativas de conexão por esse resultado do endereço IP em uma conexão à VM originalmente associado e não para o novo.

- <!-- 2292271 - IS ASDK --> If you raise a Quota limit for a Network resource that is part of an Offer and Plan that is associated with a tenant subscription, the new limit is not applied to that subscription. However, the new limit does apply to new subscriptions that are created after the quota is increased. 

  Para contornar esse problema, use um plano de complemento para aumentar a cota de rede quando o plano já está associado uma assinatura. Para obter mais informações, consulte como [disponibilizar um plano de complemento](.\.\azure-stack-subscribe-plan-provision-vm.md#to-make-an-add-on-plan-available).

- <!-- 2304134 IS ASDK --> You cannot delete a subscription that has DNS Zone resources or Route Table resources associated with it. To successfully delete the subscription, you must first delete DNS Zone and Route Table resources from the tenant subscription. 


- <!-- 1902460 -  IS ASDK --> Azure Stack supports a single *local network gateway* per IP address. This is true across all tenant subscriptions. After the creation of the first local network gateway connection, subsequent attempts to create a local network gateway resource with the same IP address are blocked.

- <!-- 16309153 -  IS ASDK --> On a Virtual Network that was created with a DNS Server setting of *Automatic*, changing to a custom DNS Server fails. The updated settings are not pushed to VMs in that Vnet.
 
- <!-- TBD -  IS ASDK --> Azure Stack does not support adding additional network interfaces to a VM instance after the VM is deployed. If the VM requires more than one network interface, they must be defined at deployment time.


#### <a name="sql-and-mysql"></a>SQL e MySQL 
- <!-- TBD - ASDK --> The database hosting servers must be dedicated for use by the resource provider and user workloads. You cannot use an instance that is being used by any other consumer, including App Services.

- <!-- IS, ASDK --> Special characters, including spaces and periods, are not supported in the **Family** name when you create a SKU for the SQL and MySQL resource providers. 

#### <a name="app-service"></a>Serviço de Aplicativo
- <!-- TBD -  IS ASDK --> Users must register the storage resource provider before they create their first Azure Function in the subscription.

- <!-- TBD -  IS ASDK --> In order to scale out infrastructure (workers, management, front-end roles), you must use PowerShell as described in the release notes for Compute.
 
#### <a name="usage"></a>Uso  
- <!-- TBD -  IS ASDK --> Usage Public IP address usage meter data shows the same *EventDateTime* value for each record instead of the *TimeDate* stamp that shows when the record was created. Currently, you can’t use this data to perform accurate accounting of public IP address usage.

<!-- #### Identity -->






## <a name="build-201803291"></a>Criar 20180329.1

### <a name="new-features-and-fixes"></a>Novos recursos e correções
Os novos recursos e correções lançadas para a pilha do Azure versão sistemas integrados 1803 aplicar o Kit de desenvolvimento de pilha do Azure. Consulte o [novos recursos](.\.\azure-stack-update-1803.md#new-features) e [problemas foram corrigidos](.\.\azure-stack-update-1803.md#fixed-issues) seções do Azure pilha 1803 atualizar as notas de versão para obter detalhes.  
> [!IMPORTANT]    
> Alguns dos itens listados no **novos recursos** e **problemas foram corrigidos** seções são relevantes apenas para sistemas de pilha do Azure integradas.

### <a name="changes"></a>Alterações
- A maneira de alterar o estado de uma oferta recém-criado de *privada* para *pública* ou *encerrado* foi alterado. Para obter mais informações, consulte [criar uma oferta](.\.\azure-stack-create-offer.md). 


### <a name="known-issues"></a>Problemas conhecidos
 
#### <a name="portal"></a>Portal
- A capacidade de [para abrir uma nova solicitação de suporte na lista suspensa](.\.\azure-stack-manage-portals.md#quick-access-to-help-and-support) de dentro do administrador do portal não está disponível. Em vez disso, use o seguinte link:     
    - Para o Kit de desenvolvimento de pilha do Azure, use https://aka.ms/azurestackforum.    

- <!-- 2050709 --> In the admin portal, it is not possible to edit storage metrics for Blob service, Table service, or Queue service. When you go to Storage, and then select the blob, table, or queue service tile, a new blade opens that displays a metrics chart for that service. If you then select Edit from the top of the metrics chart tile, the Edit Chart blade opens but does not display options to edit metrics.  

- Você verá um **ativação necessária** alerta de aviso que o aconselha a registrar seu Kit de desenvolvimento de pilha do Azure. Esse comportamento é esperado.

- Excluir resultados de assinaturas do usuário em recursos órfãos. Como alternativa, primeiro exclua os recursos do usuário ou o grupo de recursos inteiro e exclua assinaturas de usuário.

- Você não pode exibir as permissões para sua assinatura usando os portais de pilha do Azure. Como alternativa, use o PowerShell para verificar as permissões.

- No painel do portal de administração, o bloco de atualização não pode exibir informações sobre atualizações. Para resolver esse problema, clique no bloco para atualizá-lo.

-   No portal de administração, você poderá ver um alerta crítico para o componente Microsoft.Update.Admin. O nome do alerta, descrição e correção toda a exibição como:  
    - *Erro - o modelo para o FaultType ResourceProviderTimeout está ausente.*

    Esse alerta pode ser ignorado. 



#### <a name="marketplace"></a>Marketplace
- Os usuários podem procurar o marketplace completo sem uma assinatura e podem ver os itens administrativos como planos e ofertas. Esses itens não estão funcionando para os usuários.
 
#### <a name="compute"></a>Computação
- As configurações de escala para conjuntos de escala de máquinas virtuais não estão disponíveis no portal. Como alternativa, você pode usar [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). Por causa das diferenças de versão do PowerShell, você deve usar o `-Name` parâmetro em vez de `-VMScaleSetName`.

- Ao criar máquinas virtuais no portal do usuário de pilha do Azure, o portal exibe um número incorreto de discos de dados que podem ser anexados a uma VM da série DS. As VMs da série DS podem acomodar quantos discos de dados como a configuração do Azure.

- Quando uma imagem de VM Falha ao ser criado, um item com falha que você não pode excluir pode ser adicionado para a folha de computação de imagens VM.

  Como alternativa, crie uma nova imagem VM com um VHD fictício que pode ser criado pelo Hyper-V (New-VHD-caminho C:\dummy.vhd-fixa - SizeBytes 1 GB). Esse processo deve corrigir o problema que impede a exclusão do item com falha. Em seguida, 15 minutos depois de criar a imagem fictícia, você poderá com êxito excluí-la.

  Você pode tentar, em seguida, faça download novamente a imagem VM que falhou anteriormente.

-  Se uma extensão em uma implantação de VM de provisionamento leva muito tempo, os usuários devem permitir que o tempo limite de provisionamento em vez de tentar parar o processo para desalocar ou exclua a máquina virtual.  

- <!-- 1662991 --> Linux VM diagnostics is not supported in Azure Stack. When you deploy a Linux VM with VM diagnostics enabled, the deployment fails. The deployment also fails if you enable the Linux VM basic metrics through diagnostic settings. 


#### <a name="networking"></a>Rede
- Em **rede**, se você clicar em **Conexão** para configurar uma conexão VPN, **VNet para VNet** está listado como um tipo de conexão possíveis. Não selecione essa opção. Atualmente, apenas o **Site a site (IPsec)** opção tem suporte.

- Depois que uma máquina virtual é criada e associada a um endereço IP público, não é possível desassociar essa VM de endereço IP. Dissociação parece estar funcionando, mas o endereço IP público atribuído anteriormente permanecerá associado à VM original.

  No momento, você deve usar apenas novos endereços IP públicos para novas VMs que você criar.

  Esse comportamento ocorre mesmo se você reatribuir o endereço IP para uma nova VM (conhecido como um *permuta de VIP*). Todas as futuras tentativas de conexão por esse resultado do endereço IP em uma conexão à VM originalmente associado e não para o novo.



- A pilha do Azure oferece suporte a um único *gateway de rede local* por endereço IP. Isso é verdadeiro em todas as assinaturas de locatários. Após a criação da primeira conexão de gateway local de rede, subsequente tenta criar um recurso de gateway de rede local com o mesmo endereço IP são bloqueados.

- Em uma rede Virtual que foi criado com uma configuração de servidor DNS de *automáticas*, alteração de uma falha de servidor DNS personalizado. As configurações atualizadas não são propagadas para as VMs nessa rede virtual.
 
- Pilha do Azure não oferece suporte à adição de interfaces de rede adicionais para uma instância de VM depois que a VM for implantada. Se a VM requer mais de uma interface de rede, eles devem ser definidos no momento da implantação.



#### <a name="sql-and-mysql"></a>SQL e MySQL 
- Pode levar até uma hora antes dos usuários podem criar bancos de dados em um novo SQL ou MySQL SKU.

- O banco de dados de servidores de hospedagem deve ser dedicado para uso pelas cargas de trabalho de usuário e provedor de recursos. Você não pode usar uma instância que está sendo usada por outro consumidor, incluindo serviços de aplicativos.

- <!-- IS, ASDK --> Special characters, including spaces and periods, are not supported in the **Family** or **Tier** names when you create a SKU for the SQL and MySQL resource providers.

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






## <a name="build-201803021"></a>Criar 20180302.1

### <a name="new-features-and-fixes"></a>Novos recursos e correções
Consulte o [novos recursos e correções](.\.\azure-stack-update-1802.md#new-features-and-fixes) sistemas integrados de seção das notas de versão de atualização do Azure pilha 1802 pilha do Azure.

> [!IMPORTANT]    
> Alguns dos itens listados no **novos recursos e correções** seção são relevantes apenas para sistemas de pilha do Azure integradas.


### <a name="known-issues"></a>Problemas conhecidos
 
#### <a name="portal"></a>Portal
- A capacidade de [para abrir uma nova solicitação de suporte na lista suspensa](.\.\azure-stack-manage-portals.md#quick-access-to-help-and-support) de dentro do administrador do portal não está disponível. Em vez disso, use o seguinte link:     
    - Para o Kit de desenvolvimento de pilha do Azure, use https://aka.ms/azurestackforum.    

- <!-- 2050709 --> In the admin portal, it is not possible to edit storage metrics for Blob service, Table service, or Queue service. When you go to Storage, and then select the blob, table, or queue service tile, a new blade opens that displays a metrics chart for that service. If you then select Edit from the top of the metrics chart tile, the Edit Chart blade opens but does not display options to edit metrics.  

- Você verá um **ativação necessária** alerta de aviso que o aconselha a registrar seu Kit de desenvolvimento de pilha do Azure. Esse comportamento é esperado.

- Excluir resultados de assinaturas do usuário em recursos órfãos. Como alternativa, primeiro exclua os recursos do usuário ou o grupo de recursos inteiro e exclua assinaturas de usuário.

- Você não pode exibir as permissões para sua assinatura usando os portais de pilha do Azure. Como alternativa, use o PowerShell para verificar as permissões.

- No painel do portal de administração, o bloco de atualização não pode exibir informações sobre atualizações. Para resolver esse problema, clique no bloco para atualizá-lo.

-   No portal de administração, você poderá ver um alerta crítico para o componente Microsoft.Update.Admin. O nome do alerta, descrição e correção toda a exibição como:  
    - *Erro - o modelo para o FaultType ResourceProviderTimeout está ausente.*

    Esse alerta pode ser ignorado. 

- No portal de administração e no portal do usuário, a folha de visão geral não é carregado quando você seleciona a folha de visão geral de contas de armazenamento que foram criados com uma versão mais antiga de API (exemplo: 2015-06-15). 

  Como alternativa, use o PowerShell para executar o **ResourceSynchronization.ps1 início** script para restaurar o acesso para os detalhes da conta de armazenamento. [O script está disponível no GitHub]( https://github.com/Azure/AzureStack-Tools/tree/master/Support/scripts)e deve ser executada com as credenciais de administrador de serviço no host do kit de desenvolvimento se você usar o ASDK.  

- O **a integridade do serviço** folha Falha ao carregar. Quando você abre a folha de integridade do serviço no portal do administrador ou o usuário, Azure pilha exibirá um erro e não carregar as informações. Este comportamento é esperado. Embora você possa selecionar e abrir o serviço de integridade, esse recurso não está disponível, mas será implementado em uma versão futura da pilha do Azure.

#### <a name="health-and-monitoring"></a>Monitoramento e integridade
No portal de administração de pilha do Azure, você poderá ver um alerta crítico com o nome **pendentes expiração do certificado externo**.  Esse alerta pode ser ignorado e afetar as operações do Kit de desenvolvimento de pilha do Azure. 


#### <a name="marketplace"></a>Marketplace
- Os usuários podem procurar o marketplace completo sem uma assinatura e podem ver os itens administrativos como planos e ofertas. Esses itens não estão funcionando para os usuários.
 
#### <a name="compute"></a>Computação
- As configurações de escala para conjuntos de escala de máquinas virtuais não estão disponíveis no portal. Como alternativa, você pode usar [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). Por causa das diferenças de versão do PowerShell, você deve usar o `-Name` parâmetro em vez de `-VMScaleSetName`.

- A pilha do Azure oferece suporte ao uso somente VHDs de tipo fixo. Algumas imagens oferecidas por meio do marketplace na pilha do Azure usam VHDs dinâmicos, mas aqueles foram removidos. Redimensionamento de uma máquina virtual (VM) com um disco dinâmico anexado a ele deixa a VM em um estado de falha.

  Para atenuar esse problema, exclua a máquina virtual sem excluir o disco da VM, um blob VHD em uma conta de armazenamento. Em seguida, converter o VHD de um disco dinâmico em um disco fixo e, em seguida, recrie a máquina virtual.

- Ao criar máquinas virtuais no portal do usuário de pilha do Azure, o portal exibe um número incorreto de discos de dados que podem ser anexados a uma VM da série DS. As VMs da série DS podem acomodar quantos discos de dados como a configuração do Azure.

- Quando uma imagem de VM Falha ao ser criado, um item com falha que você não pode excluir pode ser adicionado para a folha de computação de imagens VM.

  Como alternativa, crie uma nova imagem VM com um VHD fictício que pode ser criado pelo Hyper-V (New-VHD-caminho C:\dummy.vhd-fixa - SizeBytes 1 GB). Esse processo deve corrigir o problema que impede a exclusão do item com falha. Em seguida, 15 minutos depois de criar a imagem fictícia, você poderá com êxito excluí-la.

  Você pode tentar, em seguida, faça download novamente a imagem VM que falhou anteriormente.

-  Se uma extensão em uma implantação de VM de provisionamento leva muito tempo, os usuários devem permitir que o tempo limite de provisionamento em vez de tentar parar o processo para desalocar ou exclua a máquina virtual.  

- <!-- 1662991 --> Linux VM diagnostics is not supported in Azure Stack. When you deploy a Linux VM with VM diagnostics enabled, the deployment fails. The deployment also fails if you enable the Linux VM basic metrics through diagnostic settings. 


#### <a name="networking"></a>Rede
- Em **rede**, se você clicar em **Conexão** para configurar uma conexão VPN, **VNet para VNet** está listado como um tipo de conexão possíveis. Não selecione essa opção. Atualmente, apenas o **Site a site (IPsec)** opção tem suporte.

- Depois que uma máquina virtual é criada e associada a um endereço IP público, não é possível desassociar essa VM de endereço IP. Dissociação parece estar funcionando, mas o endereço IP público atribuído anteriormente permanecerá associado à VM original.

  No momento, você deve usar apenas novos endereços IP públicos para novas VMs que você criar.

  Esse comportamento ocorre mesmo se você reatribuir o endereço IP para uma nova VM (conhecido como um *permuta de VIP*). Todas as futuras tentativas de conexão por esse resultado do endereço IP em uma conexão à VM originalmente associado e não para o novo.

-   O recurso de encaminhamento de IP é visível no portal, no entanto, habilitar o encaminhamento IP não tem nenhum efeito. Ainda não há suporte para esse recurso.

- A pilha do Azure oferece suporte a um único *gateway de rede local* por endereço IP. Isso é verdadeiro em todas as assinaturas de locatários. Após a criação da primeira conexão de gateway local de rede, subsequente tenta criar um recurso de gateway de rede local com o mesmo endereço IP são bloqueados.

- Em uma rede Virtual que foi criado com uma configuração de servidor DNS de *automáticas*, alteração de uma falha de servidor DNS personalizado. As configurações atualizadas não são propagadas para as VMs nessa rede virtual.
 
- Pilha do Azure não oferece suporte à adição de interfaces de rede adicionais para uma instância de VM depois que a VM for implantada. Se a VM requer mais de uma interface de rede, eles devem ser definidos no momento da implantação.



#### <a name="sql-and-mysql"></a>SQL e MySQL 
- Pode levar até uma hora antes dos usuários podem criar bancos de dados em um novo SQL ou MySQL SKU.

- O banco de dados de servidores de hospedagem deve ser dedicado para uso pelas cargas de trabalho de usuário e provedor de recursos. Você não pode usar uma instância que está sendo usada por outro consumidor, incluindo serviços de aplicativos.

- <!-- IS, ASDK --> Special characters, including spaces and periods, are not supported in the **Family** name when you create a SKU for the SQL and MySQL resource providers.

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

