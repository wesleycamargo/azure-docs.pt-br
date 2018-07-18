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
ms.date: 06/20/2018
ms.author: brenduns
ms.reviewer: misainat
ms.openlocfilehash: bbd9bb0d56dd61fd0a32531ac425a1dbc1aa8923
ms.sourcegitcommit: 1438b7549c2d9bc2ace6a0a3e460ad4206bad423
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/20/2018
ms.locfileid: "36295784"
---
# <a name="azure-stack-development-kit-release-notes"></a>Notas de versão do Kit de desenvolvimento de pilha do Azure  
Essas notas de versão fornecem informações sobre problemas conhecidos no Kit de desenvolvimento de pilha do Azure, correções e aprimoramentos. Se você não tiver certeza de qual versão você está executando, você poderá [usar o portal para verificar](.\.\azure-stack-updates.md#determine-the-current-version).

> Mantenha-se atualizado com o que há de novo no ASDK assinando o [ ![RSS](./media/asdk-release-notes/feed-icon-14x14.png)](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#) [feed](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#).

## <a name="build-11805147"></a>Criar 1.1805.1.47

> [!TIP]  
> Com base nos comentários dos clientes, há uma atualização para o esquema de versão em uso para a pilha do Microsoft Azure. Começando com essa atualização, 1805, o novo esquema melhor representa a versão atual da nuvem.  
> 
> O esquema de versão é agora *Version.YearYearMonthMonth.MinorVersion.BuildNumber* onde os conjuntos de segundo e terceiro indicam a versão e a versão. Por exemplo, 1805.1 representa o *lançamento para fabricação* versão 1805 (RTM).  


### <a name="new-features"></a>Novos recursos 
Esta compilação inclui os seguintes aprimoramentos e correções para a pilha do Azure.  

- <!-- 2297790 - IS, ASDK --> **A pilha do Azure agora inclui um *Syslog* cliente** como um *recurso de visualização*. Esse cliente permite que o encaminhamento de logs de auditoria e segurança relacionada à infraestrutura de pilha do Azure, um Syslog server ou segurança informações e eventos (SIEM) software de gerenciamento externo a pilha do Azure. No momento, o cliente de Syslog só dá suporte a conexões UDP não autenticadas através da porta padrão 514. A carga de cada mensagem Syslog é formatada em comum formato de evento (CEF). 

  Para configurar o cliente de Syslog, use o **SyslogServer conjunto** cmdlet exposta no ponto de extremidade com privilégios. 

  Com essa visualização, você poderá ver os seguintes três alertas. Quando for apresentada pela pilha do Azure, esses alertas incluem *descrições* e *correção* orientação. 
  - TÍTULO: Integridade do código desativado  
  - TÍTULO: Integridade do código no modo de auditoria 
  - TÍTULO: Conta de usuário criada

  Enquanto esse recurso estiver no modo de visualização, ele não deve ser usado em ambientes de produção.   


### <a name="fixed-issues"></a>Problemas corrigidos
- Corrigimos o problema que bloqueados [abrir uma nova solicitação de suporte na lista suspensa](.\.\azure-stack-manage-portals.md#quick-access-to-help-and-support) de dentro do portal de administração. Essa opção agora funciona conforme o esperado. 

- **Várias correções de** de desempenho, estabilidade, segurança e sistema operacional que é usado pela pilha do Azure


<!-- ### Changes  --> 


<!--   ### Additional releases timed with this update  -->


### <a name="known-issues"></a>Problemas conhecidos
 
#### <a name="portal"></a>Portal
- <!-- 2551834 - IS, ASDK --> Quando você seleciona **visão geral** para portais de administrador ou o usuário, as informações de uma conta de armazenamento a *Essentials* painel não será exibido.  O painel do Essentials exibe informações sobre a conta como seu *grupo de recursos*, *local*, e *ID da assinatura*.  Outras opções de visão geral são acessadas, como *serviços* e *monitoramento*, bem como as opções para *abrir no Gerenciador de* ou *excluir conta de armazenamento* .  

  Para exibir as informações disponíveis, use o [azureRMstorageaccount Get](https://docs.microsoft.com/powershell/module/azurerm.storage/get-azurermstorageaccount?view=azurermps-6.2.0) cmdlet do PowerShell. 

- <!-- 2551834 - IS, ASDK --> Quando você seleciona **marcas** para uma conta de armazenamento, o administrador ou o usuário portais, as informações de falha ao carregar e não exibe.  

  Para exibir as informações disponíveis, use o [AzureRmTag Get](https://docs.microsoft.com/powershell/module/azurerm.tags/get-azurermtag?view=azurermps-6.2.0) cmdlet do PowerShell.

- <!-- TBD - IS ASDK --> Não use os novos tipos de assinatura administrativo de *medição assinatura*, e *assinatura consumo*. Esses novos tipos de assinatura foram introduzidos com versão 1804 mas ainda não estão prontos para uso. Você deve continuar a usar o *provedor padrão* tipo de assinatura.  

- <!-- 2403291 - IS ASDK --> Você não pode ter o uso da barra de rolagem horizontal na parte inferior dos portais de administrador e usuário. Se você não pode acessar a barra de rolagem horizontal, use a trilha para navegar até um blade anterior no portal do selecionando o nome da folha você deseja exibir na lista de trilha de navegação na parte superior esquerda do portal.
  ![Navegação estrutural](media/asdk-release-notes/breadcrumb.png)

- <!-- TBD -  IS ASDK --> Excluir resultados de assinaturas do usuário em recursos órfãos. Como alternativa, primeiro exclua os recursos do usuário ou o grupo de recursos inteiro e exclua assinaturas de usuário.

- <!-- TBD -  IS ASDK --> Você não pode exibir as permissões para sua assinatura usando os portais de pilha do Azure. Como alternativa, use o PowerShell para verificar as permissões.


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

  Ambos os alertas podem ser ignorados com segurança e serão fechada automaticamente ao longo do tempo.  

- <!-- 2392907 – ASDK -->   Você pode ver um *crítico* alerta para **baixa capacidade de memória**. Esse alerta tem a seguinte descrição: *a região consumiu mais de 95,00% da memória disponível. Criar máquinas virtuais com grandes quantidades de memória pode falhar.*

  Esse alerta pode ser gerado quando o Azure pilha incorretamente contas para uso de memória no kit de desenvolvimento da pilha do Azure.  

  Esse alerta pode ser ignorado e o problema não tem efeito sobre o posicionamento de máquinas virtuais. 

- <!-- 2368581 - IS. ASDK --> Um operador de pilha do Azure, se você receber um alerta de memória insuficiente e máquinas virtuais de locatário Falha na implantação com um *erro de criação de malha VM*, é possível que o carimbo de pilha do Azure está fora da memória disponível. Use o [Planne de capacidade de pilha do Azure](https://gallery.technet.microsoft.com/Azure-Stack-Capacity-24ccd822) para entender melhor a capacidade disponível para suas cargas de trabalho. 


#### <a name="compute"></a>Computação
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


- <!-- TBD -  IS ASDK --> As configurações de escala para conjuntos de escala de máquinas virtuais não estão disponíveis no portal. Como alternativa, você pode usar [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). Por causa das diferenças de versão do PowerShell, você deve usar o `-Name` parâmetro em vez de `-VMScaleSetName`.

- <!-- TBD -  IS ASDK --> Ao criar máquinas virtuais no portal do usuário de pilha do Azure, o portal exibe um número incorreto de discos de dados que podem anexar uma série D VM. Todas as séries de D VMs com suporte podem acomodar quantos discos de dados como a configuração do Azure.

- <!-- TBD -  IS ASDK --> Quando uma imagem de VM Falha ao ser criado, um item com falha que você não pode excluir pode ser adicionado para a folha de computação de imagens VM.

  Como alternativa, crie uma nova imagem VM com um VHD fictício que pode ser criado pelo Hyper-V (New-VHD-caminho C:\dummy.vhd-fixa - SizeBytes 1 GB). Esse processo deve corrigir o problema que impede a exclusão do item com falha. Em seguida, 15 minutos depois de criar a imagem fictícia, você poderá com êxito excluí-la.

  Você pode tentar, em seguida, faça download novamente a imagem VM que falhou anteriormente.

- <!-- TBD -  IS ASDK --> Se uma extensão em uma implantação de VM de provisionamento leva muito tempo, os usuários devem permitir que o tempo limite de provisionamento em vez de tentar parar o processo para desalocar ou exclua a máquina virtual.  

- <!-- 1662991 - IS ASDK --> Não há suporte para o diagnóstico de VM do Linux na pilha do Azure. Quando você implanta uma VM do Linux com o diagnóstico VM habilitado, a implantação falhará. A implantação também falhará se você habilitar as métricas básicas de VM do Linux por meio de configurações de diagnóstico. 

#### <a name="networking"></a>Rede
- <!-- TBD - IS ASDK --> Você não pode criar rotas definidas pelo usuário no portal do administrador ou usuário. Como alternativa, use [Azure PowerShell](https://docs.microsoft.com/azure/virtual-network/tutorial-create-route-table-powershell).

- <!-- 1766332 - IS, ASDK --> Em **rede**, se você clicar em **criar Gateway de VPN** para configurar uma conexão VPN, **com base na política** está listado como um tipo VPN. Não selecione essa opção. Somente o **rota com base em** opção tem suporte na pilha do Azure.

- <!-- 2388980 -  IS ASDK --> Depois que uma máquina virtual é criada e associada a um endereço IP público, não é possível desassociar essa VM de endereço IP. Dissociação parece estar funcionando, mas o endereço IP público atribuído anteriormente permanecerá associado à VM original.

  No momento, você deve usar apenas novos endereços IP públicos para novas VMs que você criar.

  Esse comportamento ocorre mesmo se você reatribuir o endereço IP para uma nova VM (conhecido como um *permuta de VIP*). Todas as futuras tentativas de conexão por esse resultado do endereço IP em uma conexão para a máquina virtual original e não para o novo.


- <!-- 2292271 - IS ASDK --> Se você gerar um limite de cota para um recurso de rede que faz parte de uma oferta e o plano está associado uma assinatura de locatário, o novo limite não é aplicado a essa assinatura. No entanto, o novo limite se aplicam a novas assinaturas que são criadas depois que a cota é aumentada. 

  Para contornar esse problema, use um plano de complemento para aumentar a cota de rede quando o plano já está associado uma assinatura. Para obter mais informações, consulte como [disponibilizar um plano de complemento](.\.\azure-stack-subscribe-plan-provision-vm.md#to-make-an-add-on-plan-available).

- <!-- 2304134 IS ASDK --> Você não pode excluir uma assinatura que tem recursos de tabela de rotas associados a ele ou zona DNS. Para a assinatura foi excluída com êxito, exclua primeiro os recursos de zona DNS e a tabela de rotas da assinatura de locatário. 


- <!-- 1902460 -  IS ASDK --> A pilha do Azure oferece suporte a um único *gateway de rede local* por endereço IP. Isso é verdadeiro em todas as assinaturas de locatários. Após a criação da primeira conexão de gateway local de rede, subsequente tenta criar um recurso de gateway de rede local com o mesmo endereço IP são bloqueados.

- <!-- 16309153 -  IS ASDK --> Em uma rede Virtual que foi criado com uma configuração de servidor DNS de *automáticas*, alteração de uma falha de servidor DNS personalizado. As configurações atualizadas não são propagadas para as VMs nessa rede virtual.
 
- <!-- TBD -  IS ASDK --> Pilha do Azure não oferece suporte à adição de interfaces de rede adicionais para uma instância de VM depois que a VM for implantada. Se a VM requer mais de uma interface de rede, eles devem ser definidos no momento da implantação.


#### <a name="sql-and-mysql"></a>SQL e MySQL 
- <!-- TBD - ASDK --> O banco de dados de servidores de hospedagem deve ser dedicado para uso pelas cargas de trabalho de usuário e provedor de recursos. Você não pode usar uma instância que está sendo usada por outro consumidor, incluindo serviços de aplicativos.

- <!-- IS, ASDK --> Caracteres especiais, incluindo espaços e pontos, não há suporte para o **família** nome quando você cria uma SKU para os provedores de recursos do SQL e MySQL. 

#### <a name="app-service"></a>Serviço de Aplicativo
- <!-- 2352906 - IS ASDK --> Os usuários devem registrar o provedor de recursos de armazenamento antes de criar sua primeira função do Azure na assinatura.

- <!-- TBD - IS ASDK --> Para expandir a infraestrutura (os operadores, gerenciamento, funções de front-end), você deve usar o PowerShell conforme descrito nas notas de versão para a computação.  

- <!-- TBD - IS ASDK --> Serviço de aplicativo só pode ser implantado no *assinatura do provedor padrão* neste momento. Em uma atualização futura, o serviço de aplicativo será implantado para a nova *medição assinatura* que foi introduzido no Azure 1804 de pilha. Quando a medição é suportada para uso, todas as implantações existentes serão migradas para esse novo tipo de assinatura.

#### <a name="usage"></a>Uso  
- <!-- TBD -  IS ASDK --> Dados de medição de uso do endereço de IP público uso mostram a mesma *EventDateTime* valor para cada registro em vez do *TimeDate* carimbo que mostra quando o registro foi criado. No momento, você não pode usar esses dados para realizar as estatísticas precisas de uso de endereço IP público.

<!-- #### Identity -->



## <a name="build-201805131"></a>Criar 20180513.1

### <a name="new-features"></a>Novos recursos 
Esta compilação inclui os seguintes aprimoramentos e correções para a pilha do Azure.  

- <!-- 1759172 - IS, ASDK --> **Novas assinaturas administrativas**. Com 1804 há dois novos tipos de assinatura disponíveis no portal. Esses novos tipos de assinatura são além da assinatura do provedor padrão e visíveis em novas instalações de pilha do Azure começando com a versão 1804. *Não use esses novos tipos de assinatura com esta versão do Azure pilha*. Podemos anunciou a disponibilidade para usar esses tipos de assinatura com uma atualização futura. 

  Esses novos tipos de assinatura são visíveis, mas faz parte de uma alteração maior para proteger a assinatura do provedor padrão e para tornar mais fácil implantar recursos compartilhados, como servidores de hospedagem SQL. 

  Os tipos de três assinatura agora disponíveis são:  
  - Padrão de assinatura do provedor: continuar a usar este tipo de assinatura. 
  - Assinatura de medição: *não use este tipo de assinatura.*
  - Assinatura de consumo: *não use este tipo de assinatura*

### <a name="fixed-issues"></a>Problemas corrigidos
- <!-- IS, ASDK -->  No portal de administração, você não precisa atualizar o bloco de atualização antes de exibir informações. 

- <!-- 2050709 - IS, ASDK -->  Agora você pode usar o portal de administração para editar as métricas de armazenamento para o serviço Blob, o serviço de tabela e o serviço de fila.

- <!-- IS, ASDK --> Em **rede**, quando você clica em **Conexão** para configurar uma conexão VPN, **Site a site (IPsec)** agora é a única opção disponível. 

- **Várias correções de** de desempenho, estabilidade, segurança e sistema operacional que é usado pela pilha do Azure

<!-- ### Changes  --> 
### <a name="additional-releases-timed-with-this-update"></a>Versões adicionais atingiu o tempo com essa atualização  
A seguir agora está disponíveis, mas não requerem a atualização da pilha do Azure 1804.
- **Atualizar para o pacote de monitoramento do Microsoft Azure pilha System Center Operations Manager**. Uma nova versão (1.0.3.0) do pacote Microsoft System Center Operations Manager monitoramento para a pilha do Azure está disponível para [baixar](https://www.microsoft.com/download/details.aspx?id=55184). Com esta versão, você pode usar entidades de serviço quando você adiciona uma implantação de pilha do Azure conectada. Esta versão também apresenta uma experiência de gerenciamento de atualização que permite que você realizar uma ação de correção diretamente de dentro do Operations Manager. Também há novos painéis que exibem provedores de recursos, unidade de escala e nós de unidade de escala.

- **Novo Azure pilha Admin PowerShell versão 1.3.0**.  O Azure PowerShell pilha 1.3.0 agora está disponível para instalação. Esta versão fornece comandos para todos os provedores de recursos de administração gerenciar a pilha do Azure.  Com esta versão, alguns tipos de conteúdo serão substituído do GitHub de ferramentas de pilha do Azure [repositório](https://github.com/Azure/AzureStack-Tools). 

   Para obter detalhes de instalação, execute o [instruções](.\.\azure-stack-powershell-install.md) ou [ajuda](https://docs.microsoft.com/powershell/azure/azure-stack/overview?view=azurestackps-1.3.0) conteúdo para o módulo do Azure pilha 1.3.0. 

- **Versão da referência de Rest de API do Azure pilha inicial**. O [referência de API para todos os provedores de recursos de administração do Azure pilha](https://docs.microsoft.com/rest/api/azure-stack/) agora está publicada. 

### <a name="known-issues"></a>Problemas conhecidos
 
#### <a name="portal"></a>Portal
- <!-- TBD - IS ASDK --> A capacidade de [para abrir uma nova solicitação de suporte na lista suspensa](.\.\azure-stack-manage-portals.md#quick-access-to-help-and-support) de dentro do administrador do portal não está disponível. Em vez disso, use o seguinte link:     
    - Para o Kit de desenvolvimento de pilha do Azure, use https://aka.ms/azurestackforum.    

- <!-- 2403291 - IS ASDK --> Você não pode ter o uso da barra de rolagem horizontal na parte inferior dos portais de administrador e usuário. Se você não pode acessar a barra de rolagem horizontal, use a trilha para navegar até um blade anterior no portal do selecionando o nome da folha você deseja exibir na lista de trilha de navegação na parte superior esquerda do portal.
  ![Navegação estrutural](media/asdk-release-notes/breadcrumb.png)

- <!-- TBD -  IS ASDK --> Excluir resultados de assinaturas do usuário em recursos órfãos. Como alternativa, primeiro exclua os recursos do usuário ou o grupo de recursos inteiro e exclua assinaturas de usuário.

- <!-- TBD -  IS ASDK --> Você não pode exibir as permissões para sua assinatura usando os portais de pilha do Azure. Como alternativa, use o PowerShell para verificar as permissões.

-   <!-- TBD -  IS ASDK --> No portal de administração, você poderá ver um alerta crítico para o componente Microsoft.Update.Admin. O nome do alerta, descrição e correção toda a exibição como:  
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
- Os usuários podem procurar o marketplace completo sem uma assinatura e podem ver os itens administrativos como planos e ofertas. Esses itens não estão funcionando para os usuários.
 
#### <a name="compute"></a>Computação
- <!-- TBD -  IS ASDK --> As configurações de escala para conjuntos de escala de máquinas virtuais não estão disponíveis no portal. Como alternativa, você pode usar [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). Por causa das diferenças de versão do PowerShell, você deve usar o `-Name` parâmetro em vez de `-VMScaleSetName`.

- <!-- TBD -  IS ASDK --> Ao criar máquinas virtuais no portal do usuário de pilha do Azure, o portal exibe um número incorreto de discos de dados que podem ser anexados a uma VM da série DS. As VMs da série DS podem acomodar quantos discos de dados como a configuração do Azure.

- <!-- TBD -  IS ASDK --> Quando uma imagem de VM Falha ao ser criado, um item com falha que você não pode excluir pode ser adicionado para a folha de computação de imagens VM.

  Como alternativa, crie uma nova imagem VM com um VHD fictício que pode ser criado pelo Hyper-V (New-VHD-caminho C:\dummy.vhd-fixa - SizeBytes 1 GB). Esse processo deve corrigir o problema que impede a exclusão do item com falha. Em seguida, 15 minutos depois de criar a imagem fictícia, você poderá com êxito excluí-la.

  Você pode tentar, em seguida, faça download novamente a imagem VM que falhou anteriormente.

- <!-- TBD -  IS ASDK --> Se uma extensão em uma implantação de VM de provisionamento leva muito tempo, os usuários devem permitir que o tempo limite de provisionamento em vez de tentar parar o processo para desalocar ou exclua a máquina virtual.  

- <!-- 1662991 - IS ASDK --> Não há suporte para o diagnóstico de VM do Linux na pilha do Azure. Quando você implanta uma VM do Linux com o diagnóstico VM habilitado, a implantação falhará. A implantação também falhará se você habilitar as métricas básicas de VM do Linux por meio de configurações de diagnóstico. 

#### <a name="networking"></a>Rede
- <!-- 1766332 - IS, ASDK --> Em **rede**, se você clicar em **criar Gateway de VPN** para configurar uma conexão VPN, **com base na política** está listado como um tipo VPN. Não selecione essa opção. Somente o **rota com base em** opção tem suporte na pilha do Azure.

- <!-- 2388980 -  IS ASDK --> Depois que uma máquina virtual é criada e associada a um endereço IP público, não é possível desassociar essa VM de endereço IP. Dissociação parece estar funcionando, mas o endereço IP público atribuído anteriormente permanecerá associado à VM original.

  No momento, você deve usar apenas novos endereços IP públicos para novas VMs que você criar.

  Esse comportamento ocorre mesmo se você reatribuir o endereço IP para uma nova VM (conhecido como um *permuta de VIP*). Todas as futuras tentativas de conexão por esse resultado do endereço IP em uma conexão para a máquina virtual original e não para o novo.

- <!-- 2292271 - IS ASDK --> Se você gerar um limite de cota para um recurso de rede que faz parte de uma oferta e o plano está associado uma assinatura de locatário, o novo limite não é aplicado a essa assinatura. No entanto, o novo limite se aplicam a novas assinaturas que são criadas depois que a cota é aumentada. 

  Para contornar esse problema, use um plano de complemento para aumentar a cota de rede quando o plano já está associado uma assinatura. Para obter mais informações, consulte como [disponibilizar um plano de complemento](.\.\azure-stack-subscribe-plan-provision-vm.md#to-make-an-add-on-plan-available).

- <!-- 2304134 IS ASDK --> Você não pode excluir uma assinatura que tem recursos de tabela de rotas associados a ele ou zona DNS. Para a assinatura foi excluída com êxito, exclua primeiro os recursos de zona DNS e a tabela de rotas da assinatura de locatário. 


- <!-- 1902460 -  IS ASDK --> A pilha do Azure oferece suporte a um único *gateway de rede local* por endereço IP. Isso é verdadeiro em todas as assinaturas de locatários. Após a criação da primeira conexão de gateway local de rede, subsequente tenta criar um recurso de gateway de rede local com o mesmo endereço IP são bloqueados.

- <!-- 16309153 -  IS ASDK --> Em uma rede Virtual que foi criado com uma configuração de servidor DNS de *automáticas*, alteração de uma falha de servidor DNS personalizado. As configurações atualizadas não são propagadas para as VMs nessa rede virtual.
 
- <!-- TBD -  IS ASDK --> Pilha do Azure não oferece suporte à adição de interfaces de rede adicionais para uma instância de VM depois que a VM for implantada. Se a VM requer mais de uma interface de rede, eles devem ser definidos no momento da implantação.


#### <a name="sql-and-mysql"></a>SQL e MySQL 
- <!-- TBD - ASDK --> O banco de dados de servidores de hospedagem deve ser dedicado para uso pelas cargas de trabalho de usuário e provedor de recursos. Você não pode usar uma instância que está sendo usada por outro consumidor, incluindo serviços de aplicativos.

- <!-- IS, ASDK --> Caracteres especiais, incluindo espaços e pontos, não há suporte para o **família** nome quando você cria uma SKU para os provedores de recursos do SQL e MySQL. 

#### <a name="app-service"></a>Serviço de Aplicativo
- <!-- TBD -  IS ASDK --> Os usuários devem registrar o provedor de recursos de armazenamento antes de criar sua primeira função do Azure na assinatura.

- <!-- TBD -  IS ASDK --> Para expandir a infraestrutura (os operadores, gerenciamento, funções de front-end), você deve usar o PowerShell conforme descrito nas notas de versão para a computação.
 
#### <a name="usage"></a>Uso  
- <!-- TBD -  IS ASDK --> Dados de medição de uso do endereço de IP público uso mostram a mesma *EventDateTime* valor para cada registro em vez do *TimeDate* carimbo que mostra quando o registro foi criado. No momento, você não pode usar esses dados para realizar as estatísticas precisas de uso de endereço IP público.

<!--
#### Identity
-->



#### <a name="downloading-azure-stack-tools-from-github"></a>Baixar ferramentas de pilha do Azure do GitHub
- Ao usar o *invoke-webrequest* das ferramentas de cmdlet do PowerShell para baixar a pilha do Azure do Github, você receberá um erro:     
    -  *Invoke-webrequest: A solicitação foi anulada: não foi possível criar o canal seguro de SSL/TLS.*     

  Esse erro ocorre devido a uma substituição de suporte do GitHub recente das Tlsv1 e Tlsv1.1 padrões de criptografia (o padrão para o PowerShell). Para obter mais informações, consulte [aviso de remoção de padrões de criptografia fraca](https://githubengineering.com/crypto-removal-notice/).

<!-- #### Identity -->



## <a name="build-201803021"></a>Criar 20180302.1

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

- <!-- 2050709 --> No portal de administração, não é possível editar as métricas de armazenamento para o serviço Blob, o serviço de tabela ou o serviço de fila. Quando você vá para o armazenamento e, em seguida, selecione o blob, tabela ou bloco de serviço de fila, uma nova folha que exibe um gráfico de métricas para esse serviço é aberto. Se você selecionar Editar da parte superior do bloco de gráfico de métricas, a folha de editar gráfico abre, mas não exibir as opções para editar as métricas.  

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

- No portal de administração de pilha do Azure, você poderá ver um alerta crítico com o nome **pendentes expiração do certificado externo**.  Esse alerta pode ser ignorado e afetar as operações do Kit de desenvolvimento de pilha do Azure. 


#### <a name="marketplace"></a>Marketplace
- Os usuários podem procurar o marketplace completo sem uma assinatura e podem ver os itens administrativos como planos e ofertas. Esses itens não estão funcionando para os usuários.
 
#### <a name="compute"></a>Computação
- As configurações de escala para conjuntos de escala de máquinas virtuais não estão disponíveis no portal. Como alternativa, você pode usar [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). Por causa das diferenças de versão do PowerShell, você deve usar o `-Name` parâmetro em vez de `-VMScaleSetName`.

- Ao criar máquinas virtuais no portal do usuário de pilha do Azure, o portal exibe um número incorreto de discos de dados que podem ser anexados a uma VM da série DS. As VMs da série DS podem acomodar quantos discos de dados como a configuração do Azure.

- Quando uma imagem de VM Falha ao ser criado, um item com falha que você não pode excluir pode ser adicionado para a folha de computação de imagens VM.

  Como alternativa, crie uma nova imagem VM com um VHD fictício que pode ser criado pelo Hyper-V (New-VHD-caminho C:\dummy.vhd-fixa - SizeBytes 1 GB). Esse processo deve corrigir o problema que impede a exclusão do item com falha. Em seguida, 15 minutos depois de criar a imagem fictícia, você poderá com êxito excluí-la.

  Você pode tentar, em seguida, faça download novamente a imagem VM que falhou anteriormente.

-  Se uma extensão em uma implantação de VM de provisionamento leva muito tempo, os usuários devem permitir que o tempo limite de provisionamento em vez de tentar parar o processo para desalocar ou exclua a máquina virtual.  

- <!-- 1662991 --> Não há suporte para o diagnóstico de VM do Linux na pilha do Azure. Quando você implanta uma VM do Linux com o diagnóstico VM habilitado, a implantação falhará. A implantação também falhará se você habilitar as métricas básicas de VM do Linux por meio de configurações de diagnóstico. 


#### <a name="networking"></a>Rede
- Em **rede**, se você clicar em **Conexão** para configurar uma conexão VPN, **VNet para VNet** está listado como um tipo de conexão possíveis. Não selecione essa opção. Atualmente, apenas o **Site a site (IPsec)** opção tem suporte.

- Depois que uma máquina virtual é criada e associada a um endereço IP público, não é possível desassociar essa VM de endereço IP. Dissociação parece estar funcionando, mas o endereço IP público atribuído anteriormente permanecerá associado à VM original.

  No momento, você deve usar apenas novos endereços IP públicos para novas VMs que você criar.

  Esse comportamento ocorre mesmo se você reatribuir o endereço IP para uma nova VM (conhecido como um *permuta de VIP*). Todas as futuras tentativas de conexão por esse resultado do endereço IP em uma conexão para a máquina virtual original e não para o novo.



- A pilha do Azure oferece suporte a um único *gateway de rede local* por endereço IP. Isso é verdadeiro em todas as assinaturas de locatários. Após a criação da primeira conexão de gateway local de rede, subsequente tenta criar um recurso de gateway de rede local com o mesmo endereço IP são bloqueados.

- Em uma rede Virtual que foi criado com uma configuração de servidor DNS de *automáticas*, alteração de uma falha de servidor DNS personalizado. As configurações atualizadas não são propagadas para as VMs nessa rede virtual.
 
- Pilha do Azure não oferece suporte à adição de interfaces de rede adicionais para uma instância de VM depois que a VM for implantada. Se a VM requer mais de uma interface de rede, eles devem ser definidos no momento da implantação.



#### <a name="sql-and-mysql"></a>SQL e MySQL 
- Pode levar até uma hora antes dos usuários podem criar bancos de dados em um novo SQL ou MySQL SKU.

- O banco de dados de servidores de hospedagem deve ser dedicado para uso pelas cargas de trabalho de usuário e provedor de recursos. Você não pode usar uma instância que está sendo usada por outro consumidor, incluindo serviços de aplicativos.

- <!-- IS, ASDK --> Caracteres especiais, incluindo espaços e pontos, não há suporte para o **família** ou **camada** nomes quando você cria uma SKU para os provedores de recursos do SQL e MySQL.

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





